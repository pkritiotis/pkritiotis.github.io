---
layout: single
title:  "Outbox pattern - The why, the what, and the how"
date:   2021-11-15 18:00:00 +0300
tags: golang software-patterns
header:
  image: assets/images/outbox.png
toc: true
---

This article presents the outbox pattern, a communication/messaging pattern used in event-driven distributed systems. We will examine the problem that the outbox pattern solves, the way it works and the implementation complexity.

# Introduction

In the past few weeks as I have started experimenting with a generic implementation of the outbox pattern in go. I started this project as an opportunity to see how one can generically support multiple data providers and message brokers given the limitations of explicit interfaces and generics(not for long) in go. While the 'generic' factor is indeed a challenge, I was very impressed by the considerable complexity of the implementation of the outbox pattern especially when you compare it with the simplicity of the concept. Hence the motivation to write this article.

# Asynchronous Message Delivery Reliability in Distributed Systems

In the distributed services world, there are two types to communicate between services: synchronously and asynchronously. Synchronous communication is most commonly achieved through HTTP or gRPC calls. Asynchronous communication is achieved through the exchange of events via a message broker. Event driven architectures focus on asynchronous communication since it can provide numerous benefits including scalability, decoupling, and processing time. However, this asynchronous nature introduces multiple challenges that have to be resolved to provide a robust system. 

One of these challenges in event-driven architecture is the guaranteed **messaging delivery** after the completion of a local operation. We cannot be sure that at the time we need to deliver a message, that the message broker will be available to process the request. In some cases it is essential to perform a number of local operations and then notify external services by sending a message to the message broker. And we want to do this reliably.

Let's look at an example of a typical scenario in a microservice:
<TODO Add Diagram>

1. The service receives an entity-related request via a HTTP API
2. The handler processes the request and stores the updated entity in a persistent storage
3. Then it publishes an event containing the latest snapshot of the entity
4. If the storage and the event/command publishing is successful, return a successful response

*** - What happens if the message broker is not available at the time of the message delivery***

<TODO Add Diagram>

\- We can rollback the change we just applied on the database

*** \- But what if the database is not available at that time? Or what if the program somehow crashes?***
<TODO Add Diagram>

Well, we end up in an inconsistent state. The database will have the entity changes stored, while the external subscribers will never get notified.

How do we resolve this?
-> Steps 2 and 3 have to be done in an *atomic* fashion. And this is where the outbox pattern comes to save us!

# The outbox pattern

The outbox pattern achieves atomicity in scenarios that we need to perform operations in a persistent storage and then publish a message. It does this in a single atomic operation by taking advantage of RDBMS transactions.

## How does it work?

The outbox pattern consists of two main components
1. The `outbox` database table
  This is a table located in the local database that holds the outbound messages to be sent.
2. The `message dispatcher`
  This is a background worker that observes and publishes the entries of the `outbox` table to the designated message broker

<TODO Add Diagram>

The flow:
1. When the service receives a request, the request handler prepares and commits the business database operations and the message publishing operation **in a single atomic database transaction**. As part of this atomic transaction, the message to be delivered is stored in the `outbox` table
  - If the transaction fails at this point, we notify the client that something went wrong. No harm here, neither the business database operations are stored nor the message is published.
2. The `message dispatcher` **observes** the entries in the database, delivers the required messages to the message broker and marks them as delivered.
  - If the message delivery operation is not successful the dispatcher will retry later following a retrial policy


T**he outbox pattern guarantees *at least once* delivery**. This is because we can still have a failure after the message broker delivery, when the dispatcher tries to update the database record. While this is a generally best practice[^TODO idempotency], **consumers of messages published by services that use the outbox pattern should definitely be idempotent**. They should expect that they may receive the same message and should handle this without any issues.

That's it. Very simple but very powerful at the same time. Let's explore the implementation details in the next section.

# Implementation

While the outbox pattern is simple as a concept, its implementation can become considerably complicated.
Depending on the requirements of the system that needs the outbox pattern we have some mandatory and some optional requirements.

**Mandatory Requirements**\
1. Retrial policy
  - The main purpose of the outbox pattern is to ensure message delivery reliability. While the database transaction guarantees atomicity in local and message storage operations we also need to ensure that the message is delivered successfully as well even if the message broker is unavailable for some time.
  - A typical retrial policy will include 
    - maximum number of attempts ( how many times should the dispatcher attempt to delivere the message before it gives up)
    - time between attempts ( how much time should the dispatcher wait between attempts )
    - maximum total duration of retrials ( from the time that we received the message request, how long does it make sense to retry sending the message )
2. Retention policy
  - Messages are stored for reliability purposes, not for auditing purposes. For this reason, we need to ensure that we don't leave the outbox table expanding forever and never delete any records
  - A typical retention policy includes a time duration threshold to keep the records in the database

**Optional Requirements**\
1. Order of messages
  - While a strict order of message delivery might be required in some cases, there are some systems that do not have this requirement
2. Concurrent delivery of messages
  - This of course depends on the order of messages requirement. If it is not needed the solution can be parallelized to reduce the delivery time by delivering multiple messages at the same time

In the following subsections we explore the implementation components of the outbox pattern and explain the challenges and approaches that we can follow depending on the requirements.


## The `outbox` table schema
The `outbox` table, at a bare minimum needs to hold:
1. Message Data to be sent
2. State of the message
  - Processed
  - Unprocessed
  - Discarded
3. Retrial Policy columns
  - Number of Attempts
  - Last Attempt Time
4. Error Description
  - Last Attempt Error
5. Delivery/Discard Datetime

### Message to be sent
This column is mostly straightforward, it stores the data that we need to send to the message broker. 
The important thing to note here is that since the message to be sent can hold any type of data, the data should be serialized/encoded in a predefined way that is known to the message dispatcher.
Depending on how generic we want the outbox implementation to be, we could introduce different types of encoding that we can represent with an additional column that we can name `encoding_type`. The important thing is that the dispatcher should support deconding the message data.

### State
The `state` column is used to control the flow of the message.
Every message starts in the `Undelivered` state, it proceeds to the `Delivered` state when it is delivered successfully.
<todo diagram>
It can also end up in a `Discarded` state if the message is discarded. This scenario can occur when the dispatcher has tried to send the message multiple times, and it exceeded the maximum retrials threshold.

### Retrial Policy
The `number of attempts` column is required to ensure that we have not exceeded the maximum retrial threshold. The `last attempt time` column is required to ensure that the maximum attempt duration is not exceeded either.
<todo diagram>

### Delivery/Discard Time
This column is required for retention purposes. As the number of messages to be sent grows, we need to apply a retention policy which is based on time. Using this column we can , for example, periodically archive or delete the 'completed' entries older than X months.


## Storing Messages to the Outbox Table

The most straightforward part of the outbox pattern implemenation is storing messages to be sent to the outbox table.

We have two main approaches that an implementation can follow:
1. Allow consumers of the outbox to directly write to the outbox table by following the guidelines of the schema
  - Requires knowledge of the dispatcher implementation and probably the message broker technology to know how to encode the data
  - Can be heavily customized to the requirements of the RDBMS and Message Broker capabilities that can increase efficiency
2. Provide an sdk utility, an outbox storage service that accepts the data and the database transaction information and manages the storage of a predefined behind the scenes
  - Can handle encoding and provide a generic message data model that can be applicable for any RDBMS and Message Broker Technology
  - Can be less efficient because of its generic nature

For both of these approaches we can use the Unit of Work pattern. This will ensure that both business operations and the outbox pattern will be commited as a single unit.

## Dispatcher Implementation

The message dispatcher is the heart of the outbox pattern. The dispatcher is responsible to 
1. Observe the outbox table for new messages
2. Manage the outbox records lifecycle
3. Deliver the messages to the message broker
4. Handle errors
5. Apply the message retrial policy

### Message Observation

The message dispatcher has two main ways to 'observe' new messages
1. Polling
  - Periodically query the `outbox` table to find `Undelivered` messages
  - Applicable for any database
2. Transaction log tailing
  - Continuously monitor the database transaction log for new `outbox` table entries and directly triggre the processing once an entry appears
  - Only applicable for databases that provide an interface to monitor transaction logs such as MySQL and Postgres
  - Requires extra complexity for implementing a retrial policy

### Dispatcher Flow/Algorithm

The flow looks like this:
1. The service, as part of the transaction stores the encoded message to be sent in the outbox table in an `Undelivered` state
2. A single instance of the dispatcher periodically checks for new entries by querying the `outbox` table for `Undelivered` entities that meet the retrial policy rules
3. For all unprocessed entries
  1. Decode the message
  2. Send the message to the message broker
  3. Increase the number of attempts, update the last attempt datetime and update the state
     1. If the sending was successful, mark the message as `Delivered`
     2. If it failed leave it as `Undelivered`

### Message broker specific or generic

Different message brokers might have different message data requirements and this is something that should be considered when designing an outbox implementation.
Fortunately most of them have typical requirements such as a message **key**, **payload** and **headers** and that's it. If we need more advanced features per message broker while also supporting multiple message broker provider, we need to provide an extensibility mechanism and the foundation for this to be supported. A generic `Message` interface that can be broker-specific can do the job here.

### Error handling
While the main algorithm section provides a generic description of the flow, it is not bulletproof. Since we have dependencies on the database provider and the message broker that are external services we need to account for possible failures.
- If a message delivery fails due to message broker unavailability and I have more in the queue should I try to process them?
  - Probably not. We need to track errors and depending on the error type (i.e. network) try later.
- If I try to save the updated state to the database and it is not reachable how can I proceed?
  - A solution would be to sleep for some brief duration and retry? The database is a crucial component to manage the state and therefore it doesn't make sense to proceed with other records if the database is not reachable.
- What happens with discarded message that exceed retrial times?
  - The dispatcher has tried to sent a message X times and failed. So it has discarded the message. Should we notify someone about these types of messages to take a closer look?

These are scenarios that are not uncommon and it is important to determine how our implementation deals with them in each case.

### Coordinating Multiple Dispatcher Instances

High availability is a requirement in distributed systems and this applies to the dispatcher too. While the above algorithm describes an environment where a single instance of a background worker is only running, it is probable that we will have multiple instances running at the same time. 

Although the message consumers, should be indepotent, we should not of course waste deliveries by having all dispatcher workers sending the same messages. 
In an environment that manages multiple instance of an outbox dispatcher, we have typical distributed system consensus challenges in which we should only allow one instance of a worker to process/deliver an `outbox` record.
In these type of scenarios, we have two ways we can approach these:
1. Multiple instances deliver concurrently different records
  - this approach does not maintain the order of messages which can be a mandatory requirements depending on the use case
  - with this approach we need a *locking mechanism* in place
    - An instance of a dispatcher locks the messages that it will process
    - If the lock was successful then proceed with the delivery
    - Once finished unlock the record
  - the problem of locking can get quite complex when handling error scenarios, for example when an instance failed to unlock the message
    - mechanisms need to be introduced to unlock stale messages periodically
2. Only one instance - the leader - processes and delivers messages
  - Guarantees the order of messages
  - Requires a consensus stage in which all instances elect a leader]
  - Can be complicated to implement

## Retention Worker
 In addition to the dispatcher, we also need a retention worker that will be running periodically deleting/archiving events older than X months. This can be part of the dispatcher's algorithm. 

 For the retention worker we could use a cronjob that executre periodically every x days.

# Conclusion

We have seen the outbox pattern, a very common pattern found in event-driven systems.
As we have seen, it is a very powerful tool to ensure message delivery reliability when we need to group database operations and message delivery as a unit and commit them atomically.

While simple as a concept it can be complicated to design and implement as there are a lot of edge cases and decisions to make that affect its performance and extensibility.