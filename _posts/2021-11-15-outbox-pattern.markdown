---
layout: single
title:  "Outbox pattern - The why, the what, and the how"
date:   2021-11-15 18:00:00 +0300
tags: golang software-patterns
header:
  image: assets/images/outbox.png
toc: true
---

This article presents the outbox pattern, when to use it and the its implementation complexity.

# Introduction

In the microservices world, one of the recommended approaches of tackling communication is using an event driven architecture.  It common for services to expose a Web API that accepts HTTP/gRPC requests to add or modify an entity. The requirement of such an operation is to validate the request, and add/update the entity within a persistent storage. In event-driven architectures it is also required to publish an event to a message broker containing the last snapshot of the added/changed entity, so that external services can have the latest snapshot of that entity.
The challenge in such an operation is the achievement of atomicity. The entity persistence and the message publishing should either succeed or fail. If we don't 


# The outbox pattern

high level description
What is it?

the atomic operation
awesome unit of work and that's it

the service
abstract the details

the dispatcher
dispatcher periofic scraping


# Implementation Challanges

scraping/polling
database features
polling


errors errors errors
  retrials
  break or kill
  discarded messages
  notifications

distributed workers
  only one instance
    consensus
    election
  concurrency