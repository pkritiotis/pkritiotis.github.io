---
layout: single
title:  "Outbox pattern in go"
date:   2022-05-29 19:00:00 +0300
tags: software-patterns golang
toc: true
---

This article presents an implementation of the **outbox pattern** in go.

# Introduction

Last November, I wrote an article on the outbox pattern and it's implementation challenges. You can check it [here](https://pkritiotis.io/outbox-pattern-implementation-challenges/). While I like the theory of software patterns, I like implementing them even more. In this post we will see an implementation of a go package that implemenets the outbox pattern.

# Let's dive into it 

The source code of the library we will be looking at in this post is [here](https://github.com/pkritiotis/go-outbox).

## Features and approach

As explained in my previous post on the outbox pattern, the implementation can get very complicated depending on the features that we need to support.
The features supported by `go-outbox` are:
- Send messages via periodic polling with a configurable period duration
- Extensible store for pesisting the outbox messages
- Extensible message broker for publishing the messages
- An optional, simple retrial policy that allows discarding messages after a configurable number of attempts

## Designing the outbox library

### Structure
Since this is an implementation of a very specific purpose library, the structure is kept very simple

We essentially have three packages:
- Outbox
- Store
- Kafka

#### `outbox`

The outbox contains the building blocks of the package
```tree
├── broker.go
├── broker_mock.go
├── dispatcher.go
├── dispatcher_test.go
├── go.mod
├── go.sum
├── outbox.go
├── outbox_test.go
├── record_processor.go
├── record_processor_mock.go
├── record_processor_test.go
├── record_unlocker.go
├── record_unlocker_mock.go
├── record_unlocker_test.go
├── store.go
└── store_mock.go
```

The outbox package is the core of the library and it provides the logic of publishing and processing outbox messages.
The public api of the library contains
- The following stucts
  - `Outbox`
    - Provides the capability of sending `Message` objects to the `outbox` store.
  - `Message`
    - The `Message` is the model that represents an outbox message
  - `Dispatcher`
    - Is a background worker that processes the outbox messages based on the provided `DispatcherSettings`
  - `DispatcherSettings`
    - Contains the configurable settings that can be modified as per the consumer needs
  -  `RetrialPolicy`
     -  Contains the retrial settings of the dispatcher
- Two main interfaces
  - `Store`
    - Represents a persistent storage implementation that can be used for storing/retrieving outbox records
  - `MessageBroker`
    - Represents the message broker implementation that will be used to publish the outbox messages