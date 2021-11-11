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

In the distributed services world, there are two ways to achieve inter-communication between two services; synchronously and asynchronously. Synchronously is most commonly achieved through HTTP calls while asynchronous communication is achieved through the exchange of events via a message broker. Event driven architectures focus on asynchronous communication since it can provide numerous benefits including scalability, decoupling, and processing time. However asynchronous communication introduces multiple challenges that have to be resolved to provide a robust system and one of them is the messaging delivery reliability.

In many scenarios services need to process a request in which they need to perform a local operation and publish a message to be consumed by subscribers. In this type of scenarios we usually need to ensure that both the local operations and the message delivery have succeeded. If one of them did not succeed then we should return to the initial state of the service  otherwise we may end in an inconsistent state in which local changes applied while external services never got notified.

The outbox patterns resolves this messaging reliability issue by tackling scenarios in which database operations and message delivery should be done atomically through the use of an RDBMS.

# A detailed look into the problem

Let's look at an example of a typical scenario in a microservice:
1. The service receives an entity-related request via a HTTP API
2. The handler processes the request and stores the updated entity in a persistent storage
3. Then it publishes an event containing the latest snapshot of the entity, or a command/action to be triggered
4. If the storage and the event/command publishing is successful, return a successful response

***What happens if the message broker is not available at the time of the message delivery***
\- We can rollback the change we just applied on the database
\- But what if the database is not available at that time? Or what if the program somehow crashes?

Well, we end up in an inconsistent state. The datavase will have the entity changes stored, while the external subscribers will never get notified.

How do we resolve this?
-> Steps 2 and 3 have to be done in an *atomic* fashion. And this is where the outbox pattern comes to save us!

# The outbox pattern

The outbox pattern achieves atomicity in scenarios that we need to perform operations in a persistent storage and then publish a message in a single atomic operation by taking advantage of RDBMS transactions.

## How does it work?

The outbox pattern consists of two main components
1. The `outbox` database table
  This is a table located in the local database that holds the outbound messages to be sent.
2. The `message dispatcher`
  This is a background worker that observes and publishes the entries of the `outbox` table to the designated message broker

The flow:
1. The request handler prepares and commits the business database operations and the message publishing operation in a single atomic database transaction. As part of this atomic transaction, the message to be delivered is stored in the `outbox` table
  - If the transaction fails at this point, we notify the client that something went wrong. No harm here, neither the business database operations are stored nor the message is published.
2. The `message dispatcher` **observes** the entries in the database, delivers unsent messages to the message broker and marks them as delivered.
  - If the message delivery operation is not successful the dispatcher will retry later following a retrial policy

That's it. Very simple but very powerful at the same time. Let's explore the implementation details in the next section.

# Implementation

While the outbox pattern is simple as a concept, its implementation can become considerably complicated depending on the requirements.

In the following subsections we explore the implementation components of the outbox pattern and explain the challenges and approaches that we can follow depending on the requirements.

## A detailed look into the flow
Firstly, let's explore how the outbox works in more detail.
The outbox implementation that we will be looking at will support:
1. Retrials
2. Retention
3. 

The flow would look like this:
1. The service, as part of the transaction stores the encoded message to be sent in the outbox table in an `unprocessed` state
2. A single instance dispatcher periodically checks for new entries by querying the `outbox` table for `unprocessed` entities
3. For all unprocessed entries
  1. The dispatcher decodes the message
  2. Sends the message to the message broker
  3. If the sending was successful, mark the message as `Processed`

Let's take a look at the details of this implementation



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
This column is mostly straightforward, we need to send some data to the message broker. 
The important thing to note here is that since the message to be sent can hold any type of data, the data should be serialized/encoded in a predefined way that is known to the message dispatcher.
Depending on how generic we want the outbox implementation to be we could introduce different types of encoding that we can represent with another column that we can name `encoding_type`. The important thing is that the dispatcher should support deconding the message data.

### State
The `state` column is used to control the flow of the message.
Every message starts in the `Undelivered` state, it proceeds to the `Delivered` state when it is delivered successfully.
It can also end up in a `Discarded` state if the message is discarded. This scenario can occur when the dispatcher has tried to send the message multiple times, and it exceeded the maximum retrials threshold.

### Retrial Policy
The `number of attempts` column is required to ensure that we have not exceeded the maximum retrial threshold. The `last attempt time` column is required to ensure that the maximum attempt duration is not exceeded either.

### Delivery/Discard Time
This column is required for retention purposes. As the number of messages to be sent grows, we need to apply a retention policy which is based on time. Using this column we can , for example, periodically delete the 'completed' entries older than X months.


## Storing Messages to the Outbox Table

The easiest part of the outbox pattern implemenation is storing messages to be sent to the outbox table.

We have two main approaches that an implementation can follow:
1. Allow consumers of the outbox to directly write to the outbox table by following the guidelines of the schema
  - Requires knowledge of the dispatcher implementation and probably the message broker technology to know how to encode the data
  - Can be heavily customized to the requirements of the RDBMS and Message Broker capabilities that can increase efficiency
2. Provide an sdk utility, an outbox storage service that accepts the data and the database transaction information and manages the storage of a predefined behind the scenes
  - Can handle encoding and provide a generic message data model that can be applicable for any RDBMS and Message Broker Technology
  - Can be less efficient because of its generic nature

## Dispatcher Implementation

General:
 Specific Database vs Any Database
  pros and cons
  implementation complexity
 Specific Broker vs Any Broker
  model

Observing Patterns:
- scraping
  - database features
- polling

## Error Handling
errors errors errors
  retrials
  break or kill
  discarded messages
  notifications

## Concurrency
distributed workers
  only one instance
    consensus
    election
  concurrency

## Retention Policy


## Idempotency and Order of Messages

## An outbox library - Generic Purpose or Specific RDBMS and Message Broker
The first choice we have to make when implementing the outbox library is whether

Notes:
- The outbox pattern is usually accompanied with the Unit of Work pattern to support grouping business database operations with the message storage in on single transaction
- The outbox pattern works with RDBMS since typically NoSQL databases don't support transactions
