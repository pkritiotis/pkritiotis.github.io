---
layout: single
title:  "Implementing the Outbox pattern in go"
date:   2022-05-29 19:00:00 +0300
tags: software-patterns golang
toc: true
---

This article presents an implementation of the **outbox pattern** in go.

# Introduction

Last November, I wrote an article on the outbox pattern and its implementation challenges. You can check it [here](https://pkritiotis.io/outbox-pattern-implementation-challenges/). 

In this post we will go through a sample implementation of the outbox pattern in go. 

We will be looking at the package's features and and how it approaches the outbox pattern and dig into the design and usage details.

# Source Code 

The source code of the library we will be looking at in this post is [here](https://github.com/pkritiotis/go-outbox).

# Features

The design of this sample `outbox` implementation follows the below approach:
- Message Observability
  - **Polling** via configurable interval
- Dispatcher Coordination
  - Supports **message locking** to prevent concurrent message publishing from different dispatcher instances
  - **The order of messages is not guaranteed** if the dispatcher runs on more than one instances
- Error handling
  - Supports an **optional maximum attempts threshold**. If the threshold is exceeded, the message is discarded
- Outbox Store extensibility
  - Supports an extensible design through which we can **integrate with different sql providers**
- Message Broker extensibility
  - Supports an extensible design through which we can **integrate with different message brokers**

What this package **does not** support:
- Single instance running dispatcher (leader election)
- Guaranteed order of messages when there are more than one dispatcher instances
- The message broker interface can be a bit limited depending on the number of features that we need to support from the message broker

# Design

## Project Structure
Since this is an implementation of a very specific-purpose library, the structure is kept very simple using three packages:
- Outbox
  - Manages the outbox messages incuding the functionality of pushing messages to the outbox table and then processing and publishing the messages to the message broker
- Store
  - Contains the packages of different providers that implement the functinality around persisting and retrieving outbox messaging capabilities
- Message Brokers
  - Contains the packages of different providers that implement the functionality around publishing messages to thee corresponding broker

## Core components

![Image](../assets/images/outbox-implementation_Core-Components.png)

The core components of this implementation are:
1. Publisher
   - Stores the provided message to the store
2. Dispatcher
   - Manages the execution of the RecordUnlocker, Record Processor and the Cleanup Worker 
3. Record Processor
   - Checks the Store for new messages, and publishes the eligible messages to the message broker
4. Record Unlocker
   - Unlocks records that have been locked for a specific configurable duration
5. Cleanup Worker
   - Removes old records from the Store 
6. Store
   - Provides an interface that is used for interacting with an sql store
7. Message Broker
   - Provides an interface that is used for interacting with a message broker

### Publisher

The `Publisher` struct is initialized using a `Store`. This allows the publisher to work with any sql provider that implements the `Store`.

```go
//Publisher encapsulates the publishing functionality of the outbox pattern
type Publisher struct {
	store Store
	time  time.Provider //time abstraction for testing purposes
	uuid  uuid.Provider //uuid abstraction for testing purposes
}
```

The only exported method is `Send` that accepts a `Message` and a sql transaction.

The responsibility of this method is to transform the `Message` to a `Record` and use the `Store` to save the entry within the provided transaction
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
		LockID:      nil,
		LockedOn:    nil,
		ProcessedOn: nil,
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
	settings        DispatcherSettings
}
```

The responsibilities of the `Dispatcher` are:
1. Periodically spawn a new `recordProcessor` to check for eligible messages and publish them to the corresponding message broker
2. Periodically spawn a new `recordUnlocker` to check for stale records that have been locked for a long duration (configurable)
3. Periodically spawn a new `cleanupWorker` to remove old messages (configurable duration)
4. Interrupt the ongoing routines if it receives a message from the provided `done` channel
5. Communicate all errors of the workers in the provided `error` channel

```go
//Run periodically checks for new outbox messages from the Store, sends the messages through the MessageBroker
//and updates the message status accordingly
func (d Dispatcher) Run(errChan chan<- error, doneChan <-chan struct{}) {
	doneProc := make(chan struct{}, 1)
	doneUnlock := make(chan struct{}, 1)

	go func() {
		<-doneChan
		doneProc <- struct{}{}
		doneUnlock <- struct{}{}
	}()

	go d.runRecordProcessor(errChan, doneProc)
	go d.runRecordUnlocker(errChan, doneUnlock)
}

// runRecordProcessor processes the unsent records of the store
func (d Dispatcher) runRecordProcessor(errChan chan<- error, doneChan <-chan struct{}) {
	ticker := time.NewTicker(d.settings.ProcessInterval)
	for {
		log.Print("Record processor Running")
		err := d.recordProcessor.ProcessRecords()
		if err != nil {
			errChan <- err
		}
		log.Print("Record Processing Finished")

		select {
		case <-ticker.C:
			continue
		case <-doneChan:
			ticker.Stop()
			log.Print("Stopping Record processor")
			return
		}
	}
}
```

### Record Processor

The `recordProcessor` uses the provided `Store` and `MessageBroker` to 
1. Check and lock the unprocessed entries so another instance of the processor does not process the same records
2. Retrieves the records that have been locked with its unique identifier(`machineID`)
3. Tries to publish the messages using and updates the record states
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

The actual publishing of message implements the following algorithm:

Foreach record:
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
The `Store` provides an interface that is used for interacting with an sql store

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
	//Delivered indicates that the Records is already Delivered
	Delivered
	//MaxAttemptsReached indicates that the message is not Delivered but the max attempts are reached so it shouldn't be delivered
	MaxAttemptsReached
)
```

The `RecordState` indicates the state of each record so that the processor will know whether it is processed, delivered or if its max attempts have been reached

![image](../assets/images/outbox-implementation-State%20Machine.png)

### `MessageBroker`

The `MessageBroker` is a very simple interface that can be implemented by different providers to publish messages to a message broker.
```go
//MessageBroker provides an interface for message brokers to send Message objects
type MessageBroker interface {
	Send(message Message) error
}
```

## Usage

For a full example of a mySQL outbox using a Kafka broker check the example [here](https://github.com/pkritiotis/go-outbox/blob/main/examples/mySQL-Kafka/main.go)

### Prerequisite: Create the outbox table
In order to use this outbox library you first need to create the outbox table that is required for storing the messages. 

The following script creates the outbox table in a mySQL database
```mysql
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
## Sending a message via the `Publisher` service
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
## Starting the outbox dispatcher
The dispatcher can run on the same or different instance of the application that uses the outbox.
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

# Possible modifications and improvements

## Guaranteeing the order of messages
We could introduce some logic to ensure that the order of messages is guaranteed by ensuring that only one dispatcher runs at a time. This could be done using a leader election algorithm that would probably need to be orchestrated by an external service. 

## Retrial Policy
We could also include further retrial settings such as *time between attempts* and *maximum attempt duration* to provide even better retrial capabilities.

# Conclusion

We have seen how we can implement a simple package of the outbox pattern in go. 

This package does not provide leader election capabilities or guaranteed order of messages when running multiple dispatchers, but it includes some cool features such as locking of messages and an extensible design to use any sql provide or message broker that we may need.