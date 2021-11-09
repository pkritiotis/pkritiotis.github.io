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

# Preword

In the last month, I wanted to start a side-project in `go` to expose myself to more features of the language and get some hands-on look on concurrency. Taking an opportunity from a discussion at work, I have started working on an implementation of a generic outbox pattern package in go. Although a simple pattern in concept, it has considerable complexity that I found interesting to share. Here it is then!

# Introduction

In the microservices world, and in distributed systems in general, the inter-communication between backend services is commonly achieved asynchronously through the exchange of events. The asynchronous communication via events and the architecture to support it comes with a long list of complicated challenges to resolve, and one of them is the messaging reliability.

Sometimes it is crucial to ensure that local service storage operations go hand in hand with message delivery, otherwise we may end in an inconsistent state with local changes applied while external services never got notified.

The outbox patterns resolves this messaging reliability issue by tackling scenarios in which operations should be done atomically through the use of an RDBMS.

# A detailed look into the problem

Let's look at an example of a typical scenario in services:
1. The service receives a create/update request via a HTTP API
2. The handler processes the request and stores the created/updated entity in a persistent storage
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


That's it! However, while the outbox pattern is simple as a concept, its implementation is considerably complex depending on the requirements. The next section analyzes the implementation challenges.

# Implementation Challanges

## Generic Purpose or Specific RDBMS and Message Broker

## Storing Messages to the Outbox Table

Approach A:  Store the raw data components of the message to be sent when saving the data within the outbox table
Pros: Fast RDBMS specific
Cons: Requires knowledge of the dispatcher implementationa and probably the message broker technology to know how to serialize the data

Approach B: 
Pros: Provide an outbox service that callers can use. No need to know about the database provider beforehand 
Cons: encoding and generic model

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


Notes:
- The outbox pattern is usually accompanied with the Unit of Work pattern to support grouping business database operations with the message storage in on single transaction
- The outbox pattern works with RDBMS since typically NoSQL databases don't support transactions
