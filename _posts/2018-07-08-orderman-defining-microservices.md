---
published: false
title: Orderman - Defining microservices
layout: post
categories: orderman microservices architecture
---

One of the first challenges when using a microservices architecture is analysing the business domain of the system to be implemented and identifying which features/operations could be identified as separate microservices.

## Requirements
* Each microservice should serve a clear business requirement and avoid implementing logic for multiple operations within a business domain
* Each microservice owns its own data model and logic
* Enable consistency between microservices (when sharing common identities)
* Find balance between fully decoupled microservices and performance

## OrderMan microservices

![microservices]({{ "/assets/orderman_microservices_high_level.png" | absolute_url }})