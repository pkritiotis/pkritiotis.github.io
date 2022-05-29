---
layout: single
title:  "Implementing the Outbox pattern in go"
date:   2022-05-29 16:00:00 +0300
tags: software-patterns golang
toc: true
---

This article presents the design and implementation of the **outbox pattern** in go.

# Introduction

Last November, I wrote an article on [what the outbox pattern is and its implementation challenges](https://pkritiotis.io/outbox-pattern-implementation-challenges/). If you are unfamiliar with the outbox pattern, I suggest reading this post first.

As noted in the above post, to implement the outbox pattern, you need to make several decisions about:
- Message Observation
- Dispatcher Coordination Approach - Guarantee the order of messages or not
- Retrial Policy & Retention

In this blog post, I explain how I designed and implemented the outbox pattern in go and the approach I followed on the above properties.

# Source Code 

The source code of the library we will be looking at in this post is [here](https://github.com/pkritiotis/go-outbox).

# Features

<p align="center">
  <img alt="whattodo" src="/assets/images/lamp.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

The design of this sample `outbox` implementation follows the below approach:
- Message Observability
  - **Polling** via configurable interval
- Dispatcher Coordination
  - Supports **message locking** to prevent concurrent message publishing from different dispatcher instances
  - **The order of messages is not guaranteed** if the dispatcher runs on more than one instances
- Error handling
  - Supports an **optional maximum attempts threshold**. If the threshold is exceeded, the message is discarded
- Outbox Store extensibility
  - Supports an extensible design through which we can **integrate with different SQL providers**
- Message Broker extensibility
  - Supports an extensible design through which we can **integrate with different message brokers**

What this package does not support:
- Does **NOT** support leader instance between multiple dispatcher instances (leader election)
- Does **NOT** support guaranteed order of messages when there are more than one dispatcher instances
- The message broker interface can be a bit limited depending on the number of features that we need to support from the message broker

# Design

<p align="center">
  <img alt="whattodo" src="/assets/images/write.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

Let's check how the project is structured and go through the core components of the package.
## Project Structure
Since this is an implementation of a very specific-purpose library, the structure is kept very simple using three main directories:
- Outbox - Main Package
  - Manages the outbox messages, including the functionality of pushing messages to the outbox table and then processing and publishing the messages to the message broker
- Store
  - Contains the packages of different SQL providers that implement the functionality around persisting and retrieving outbox messaging capabilities
- Message Broker
  - Contains the packages of different message broker providers that implement the functionality around publishing messages to the corresponding broker

## Core components

![Image](../assets/images/outbox-implementation-Core-Components.png)

The core components of this implementation are:
1. Publisher
   - Stores the provided message to the store
2. Dispatcher
   - Manages the execution of the RecordUnlocker, Record Processor, and the Cleanup Worker 
3. Store
   - Provides an interface that is used for interacting with an SQL store
4. Message Broker
   - Provides an interface that is used for interacting with a message broker

### Publisher

The `outbox.Publisher` struct is initialized using a `Store`. This allows the publisher to work with any SQL provider implementing the `Store`.

```go
//Publisher encapsulates the publishing functionality of the outbox pattern
type Publisher struct {
	store Store
	time  time.Provider //time abstraction for testing purposes
	uuid  uuid.Provider //uuid abstraction for testing purposes
}
```

The only exported method is `Send`, which accepts a `Message` and a SQL transaction.

The responsibility of this method is:
1. Transform the `Message` into a `Record`
2. Use the `Store` to save the entry within the provided transaction.

The `Message` struct encapsulates the information needed to send a message via a message broker. The attributes of the message, in this case, are limited. Depending on the use case, highly configurable message brokers may need additional details when sending messages.

```go
//Message encapsulates the contents of the message to be sent
type Message struct {
	Key     string
	Headers []MessageHeader
	Body    []byte
	Topic   string
}

//Send stores the provided Message within the provided sql.Tx
func (o Publisher) Send(msg Message, tx *sql.Tx) error {
	newID := o.uuid.NewUUID()
	record := Record{
		ID:          newID,
		Message:     msg,
		State:       PendingDelivery,
		CreatedOn:   o.time.Now().UTC(),
	}

	return o.store.AddRecordTx(record, tx)
}
```

### Dispatcher

The `Dispatcher` is a background worker that manages the outbox messages based on the provided `DispatcherSettings`.

```go
// RetrialPolicy contains the retrial settings
type RetrialPolicy struct {
	MaxSendAttemptsEnabled bool
	MaxSendAttempts        int
}

// DispatcherSettings defines the set of configurations for the dispatcher
type DispatcherSettings struct {
	ProcessInterval     time.Duration
	LockCheckerInterval time.Duration
	MaxLockTimeDuration time.Duration
	RetrialPolicy       RetrialPolicy
}

//Dispatcher initializes and runs the outbox dispatcher
type Dispatcher struct {
	recordProcessor processor
	recordUnlocker  unlocker
	recordCleaner	cleaner
	settings        DispatcherSettings
}
```

The responsibilities of the `Dispatcher` are:
1. Periodically spawn a new `recordProcessor` to check for eligible messages and publish them to the corresponding message broker
2. Periodically spawn a new `recordUnlocker` to check for stale records that have been locked for a long duration (configurable)
3. Periodically spawn a new `recordCleaner` to remove old messages (configurable duration)
4. Interrupt the ongoing routines if it receives a message from the provided `done` channel
5. Communicate all errors of the workers in the provided `error` channel

```go
//Run periodically checks for new outbox messages from the Store, sends the messages through the MessageBroker
//and updates the message status accordingly
func (d Dispatcher) Run(errChan chan<- error, doneChan <-chan struct{}) {
	// [...]

	go d.runRecordProcessor(errChan, doneProc)
	go d.runRecordUnlocker(errChan, doneUnlock)
	go d.runRecordCleaner(errChan, doneClean)
}
```

#### Record Processor

The `recordProcessor` is the heart of the dispatcher and uses the provided `Store` and `MessageBroker` to 
1. Check and lock the unprocessed entries, so another instance of the processor does not process the same records
2. Retrieves the records that have been locked with the dispatcher's unique identifier(`machineID`)
3. Tries to publish the messages using and updating the record states
4. Unlocks the records

```go
//ProcessRecords locks unprocessed messages, tries to deliver them and then unlocks them
func (d defaultRecordProcessor) ProcessRecords() error {
	err := d.lockUnprocessedEntities()
	defer d.store.ClearLocksByLockID(d.machineID)
	if err != nil {
		return err
	}
	records, err := d.store.GetRecordsByLockID(d.machineID)
	if err != nil {
		return err
	}
	if len(records) == 0 {
		return nil
	}

	return d.publishMessages(records)
}
```

The actual publishing of the message follows this algorithm:

For each record:
1. Update the records fields to indicate a new publishing attempt
2. Try to publish the message
   - If the publishing succeeded
      -  update the record's details and continue to the next record
   - If the publishing failed
      -  update the record's details (failed attempt) and exit

The code:
```go
func (d defaultRecordProcessor) publishMessages(records []Record) error {

	for _, rec := range records {
		// Send message to message broker
		now := d.time.Now().UTC()
		rec.LastAttemptOn = &now
		rec.NumberOfAttempts++
		err := d.messageBroker.Send(rec.Message)

		// If an error occurs, remove the lock information, update retrial times and continue
		if err != nil {
			rec.LockedOn = nil
			rec.LockID = nil
			errorMsg := err.Error()
			rec.Error = &errorMsg
			if d.retrialPolicy.MaxSendAttemptsEnabled && rec.NumberOfAttempts == d.retrialPolicy.MaxSendAttempts {
				rec.State = MaxAttemptsReached
			}
			dbErr := d.store.UpdateRecordByID(rec)
			if dbErr != nil {
				return fmt.Errorf("Could not update the record in the db: %w", dbErr)
			}

			return fmt.Errorf("An error occurred when trying to send the message to the broker: %w", err)
		}

		// Remove lock information and update state
		rec.State = Delivered
		rec.LockedOn = nil
		rec.LockID = nil
		rec.ProcessedOn = &now
		err = d.store.UpdateRecordByID(rec)

		if err != nil {
			return fmt.Errorf("Could not update the record in the db: %w", err)
		}
	}
	return nil
}
```

### Store
The `Store` provides an interface that is used for interacting with a SQL store

```go
//Store is the interface that should be implemented by SQL-like database drivers to support the outbox functionality
type Store interface {
	//AddRecordTx stores the message within the provided database transaction
	AddRecordTx(record Record, tx *sql.Tx) error
	//GetRecordsByLockID returns the records by lockID
	GetRecordsByLockID(lockID string) ([]Record, error)
	//UpdateRecordLockByState updates the lock of all records with the provided state
	UpdateRecordLockByState(lockID string, lockedOn time.Time, state RecordState) error
	//UpdateRecordByID updates the provided the record
	UpdateRecordByID(message Record) error
	//ClearLocksWithDurationBeforeDate clears the locks of records with a lock time before the provided time
	ClearLocksWithDurationBeforeDate(time time.Time) error
	//ClearLocksByLockID clears all records locked by the provided lockID
	ClearLocksByLockID(lockID string) error
}
```

The main entity of the Store is the `Record`
```go
//Record represents the record that is stored and retrieved from the database
type Record struct {
	ID               uuid.UUID
	Message          Message
	State            RecordState
	CreatedOn        time.Time
	LockID           *string
	LockedOn         *time.Time
	ProcessedOn      *time.Time
	NumberOfAttempts int
	LastAttemptOn    *time.Time
	Error            *string
}

//RecordState is the State of the Record
type RecordState int

const (
	//PendingDelivery is the initial state of all records
	PendingDelivery RecordState = iota
	//Delivered indicates that the Record is already Delivered
	Delivered
	//MaxAttemptsReached indicates that the message is not Delivered, but the max attempts are reached so it shouldn't be delivered
	MaxAttemptsReached
)
```

The `RecordState` indicates the state of each record. It is needed by the record processor to know whether it is processed, delivered, or if its max attempts have been reached

![image](../assets/images/outbox-implementation-State%20Machine.png)

### `MessageBroker`

The `MessageBroker` is a straightforward interface that can be implemented by different providers to publish messages to a message broker.
```go
//MessageBroker provides an interface for message brokers to send Message objects
type MessageBroker interface {
	Send(message Message) error
}
```

## Using the go-outbox package

To use the sample outbox package implementation, you need to:
1. Create the outbox table
2. Use the publisher to send your message
3. Run the dispatcher worker

For a complete example of a MySQL outbox using a Kafka broker check the example [here](https://github.com/pkritiotis/go-outbox/blob/main/examples/mySQL-Kafka/)

### Create the outbox table
To use this outbox library, you first need to create the outbox table that is required for storing the messages. 

The following script creates the outbox table in a `MySQL` database
```sql
CREATE TABLE outbox (
        id varchar(100) NOT NULL,
        data BLOB NOT NULL,
        state INT NOT NULL,
        created_on DATETIME NOT NULL,
        locked_by varchar(100) NULL,
        locked_on DATETIME NULL,
        processed_on DATETIME NULL,
        number_of_attempts INT NOT NULL,
        last_attempted_on DATETIME NULL,
        error varchar(1000) NULL
)
```
## Sending a message via the `outbox.Publisher` service
```go
type SampleMessage struct {
	message string
}
func main() {
  
  //Setup the mysql store
	sqlSettings := mysql.Settings{
		MySQLUsername: "root",
		MySQLPass:     "a123456",
		MySQLHost:     "localhost",
		MySQLDB:       "outbox",
		MySQLPort:     "3306",
	}
	store, err := mysql.NewStore(sqlSettings)
	if err != nil {
		fmt.Sprintf("Could not initialize the store: %v", err)
		os.Exit(1)
	}
  
  // Initialize the outbox
	publisher := outbox.NewPublisher(store)
	db, _ := sql.Open("mysql",
		fmt.Sprintf("%v:%v@tcp(%v:%v)/%v?parseTime=True",
			sqlSettings.MySQLUsername, sqlSettings.MySQLPass, sqlSettings.MySQLHost, sqlSettings.MySQLPort, sqlSettings.MySQLDB))
  // Open the transaction
	tx, _ := db.BeginTx(context.Background(), nil)
  // Encode the message in a string format
	encodedData, _ := json.Marshal(SampleMessage{message: "ok"})
  
  // Send the message within the tx
	publisher.Send(outbox.Message{
		Key:   "sampleKey",
		Body:  encodedData,
		Topic: "sampleTopic",
	}, tx)
  
	tx.Commit()
}
```
## Running the outbox dispatcher
The dispatcher can run on the same or on a different application instance that uses the outbox.
Once the dispatcher starts, it will periodically check for new outbox messages and push them to the kafka broker
```go
func main() {
  
  //Setup the mysql store
	sqlSettings := mysql.Settings{
		MySQLUsername: "root",
		MySQLPass:     "a123456",
		MySQLHost:     "localhost",
		MySQLDB:       "outbox",
		MySQLPort:     "3306",
	}
	store, err := mysql.NewStore(sqlSettings)
	if err != nil {
		fmt.Sprintf("Could not initialize the store: %v", err)
		os.Exit(1)
	}
  
  // Setup the kafka message broker
	c := sarama.NewConfig()
	c.Producer.Return.Successes = true
	broker, err := kafka.NewBroker([]string{"localhost:29092"}, c)
	if err != nil {
		fmt.Sprintf("Could not initialize the message broker: %v", err)
		os.Exit(1)
	}
  
  // Initialize the dispatcher	
	settings := outbox.DispatcherSettings{
		ProcessInterval:     20 * time.Minute,
		LockCheckerInterval: 600 * time.Minute,
		MaxLockTimeDuration: 5 * time.Minute,
	}
  
  d := outbox.NewDispatcher(store, broker, settings, "1")
  // Run the dispatcher
	errChan := make(chan error)
	doneChan := make(chan struct{})
	d.Run(errChan, doneChan)
	defer func() { doneChan <- struct{}{} }()
	err = <-errChan
	fmt.Printf(err.Error())
}
```

# Exploring Areas of Improvement and Alternatives

Having seen the above implementation, it's important to note that there is a great number of improvements and alternative approaches that we could follow. 

This depends on what we want to achieve with our outbox implementation. And I believe this is why implementing a fully flexible, generic outbox package is very difficult without sacrificing complexity and performance.

<p align="center">
  <img alt="whattodo" src="/assets/images/fix.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 


Let's check some of the areas of improvement and alternative approaches.

## Guaranteeing the order of messages
The outbox package presented in this post does not guarantee the correct order of messages if we have multiple dispatcher instances. 

If we want to support such a feature, we could introduce some logic to ensure that the order of messages is guaranteed by ensuring that only one dispatcher runs at a time. This could be done using a leader election algorithm that would probably need to be orchestrated by an external service. 
Another rough solution would be to have an additional storage location through which dispatchers have to acquire a lock before running. Only one dispatcher runs at a time based on whether the run is already logged. This could be done using a cache store or an extra table in the existing `Store`.

## Retrial Policy
The retrial capabilities of this implementation are limited to the maximum number of attempts per message.

We could also include other retrial settings such as *time between attempts* and *maximum attempt duration*.

## Supporting multiple message brokers
The `Message` struct has several primary fields that could not be adequate for complex message brokers for which we need further configuration.

We could enhance the design of the Broker by providing broker-specific message attributes. We could do this by enhancing the Message struct. We could also delete the Message abstraction and use broker-specific messages that are only serialized and deserialized by the outbox package as unknown objects.

## Logging & Observability

This outbox implementation contains very basic logging indicating when the dispatcher has run and when it stopped or had an error. 

An implementation targeting critical systems would need to be more observable. For example, we could enhance the logging capabilities to use configurable logging levels. We could also improve the observability by providing metrics about the number of messages processed, time spent, etc.

# Conclusion
An outbox pattern implementation has many alternative options, which is why we can have many implementation flavors.

We have seen how we can implement a simple package of the outbox pattern in go that uses a polling mechanism and provides an extensible design to support multiple stores and message brokers. 

What we've seen in this post is not a generic outbox solution that meets every requirement that a complex application might have. There is room for improvement and alternatives in approaching the different parts of the implementation.

I hope that this post provides the foundation for understanding how the outbox pattern and serves as the base of something more complicated. Clone, fork, experiment, and build your custom outbox pattern with it!