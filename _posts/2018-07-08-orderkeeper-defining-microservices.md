---
published: true
title: OrderKeeper - Defining microservices
layout: post
categories: orderkeeper microservices architecture
---

One of the first challenges when using a microservices architecture is analysing the business domain of the system to be implemented and identifying which features/operations could be identified as separate microservices.

## Requirements
* Each microservice should serve a clear business requirement and avoid implementing logic for multiple operations within a business domain
* Each microservice owns its own data model and logic
* Enable consistency between microservices (when sharing common identities)
* Find balance between fully decoupled microservices and performance

## OrderKeeper microservices

![microservices]({{ "/assets/orderkeeper_microservices_high_level.png" | absolute_url }})

### Customer Management
This microservice will be reponsible for customer management in terms of maintaining details of customers, and will be the service responsible to acquire/modify/add/delete customer details

### Order Management
This service will be responsible for managing all operations related to orders. This service will be called when creating and acquiring orders. It will be receiving order requests and when an order is placed, notify all 'stakeholder' services.

### Catalog Product Management
This service will be the repository and access point of products, both sales and buy products.

### Supplier Management
This is responsible to hold all supplier data. This could be merged with customer management module since we are essentially exposing the same operation on a different 'type' of data. 


The above solution is just a first try to break the OrderKeeper System into microservices. This might not be the best approach but it is something to begin with. As this project progresses, changes might appear and applied through refactoring. 

This is a learning process and I can see the challenges already at this first stage of identifying the microservices, and this is a great exercise to find out at a later stage, what might be wrong in this initial thought process and how to avoid it  


Next Post: Internal Architecture of each microservice and service interaction.