# Understanding Microservices
This repo is about introducing and documenting what Microservices mean, their advantages and the things one should be wary of while implementing them. I have referred to a variety of courses and videos to arrive at this content.

# Introduction
Most web applications are bundled into a single executable Jar/War/Ear file that contains the application code packaged. This bundled package then serves the client pages along with the services that perform business logic and database connection configurations. This type of application architecture where everything is bundled together is described as a Monolithic architecture. Note that typically the database is not part of this bundle and also the inside the bundle the code and resources could be organized well in terms of module. But having organized code/resources in a bundle does not mean it is not Monolithic - **it still is a Monolithic, because it presents a single point of failure** and also, each of the components which are modularized inside the bundle could not be scaled seperately - you either scale the Monolith or you do not scale it at all.

This approach has worked well over the years. In fact most applications still follow this way of working. Most web based applications or even commercial applications like SAP, Salesforce etc, are either hosted on cloud or on local premise and their demand is more or less stable. In other words, there wont be very huge spikes when it comes to the usability of these applications. This however, is a problem for applications which need to scale parts of it as per demand.

To be more precise, if you consider products/websites like Amazon or Netflix, their demand has sharp spikes at certain times. Amazon offers Great Indian Sale to Indian geography where the products are offerred at a high discount. Netflix too had similar StreamFest option where people could see its content without any fee. Events like these spark surge in usage of these applications. **As an example, in 2019, during Amazon sale, 56 billion extra requests are received to Amazon's database.** Source: https://analyticsindiamag.com/how-big-techs-are-powering-big-billion-day-sales/

This level of surge means that the team at Amazon should be prepared for such huge spikes as well in advance. In other words, their application has to scale to support users. Interesting to note that an average user waits for 2 seconds ( source: https://www.researchgate.net/publication/220893869_A_Study_on_Tolerable_Waiting_Time_How_Long_Are_Web_Users_Willing_to_Wait ) for an application result and leaves the page after that. So this means this is a serious issue which products like these need to consider.

**Amazon uses around 100 Microservices to show a web page.**

## Examples
Twitter moved from RoR monolith to Microservices.
Facebook from PHP monolith to Microservices.
Buzzfeed moved to Microservices - https://www.youtube.com/watch?v=ZjEJWrbz-Eo
And finally, Netflix moved from Java monolith to Microservices.

## Problem 1: Scaling

In the above examples, it is interesting to note that both Amazon website itself and Netflix are hosted on AWS cloud. You can scale your applications on cloud. But if your application itself is a Monolith, you are losing an important advantage.

Going back to the previous example, on those sale days, chances are, most people are window shopping, ordering items or placing return of purchased items. There may not be such similar increase in people writing comments or giving feedback about the products. In other words, if Amazon were built as a monolith, it may have seperate modules for order management, user wishlist management, review & rating system, comment system, an ML recommendation system,  customer service chatbot system etc bundled into a single war/jar/ear file. If a scale of the application is needed, all of the modules would also scale. So there is no option to scale everything except review & rating system and comment system. Scaling them up is an unavoidable side affect of scaling the application itself.

## Problem 2: Testing

Since all the modules are part of the same deliverable, no matter how docile a change is done to the rating system, there could always be a chance that the ML recommendation system be thrown into regression.

## Problem 3: Single point of failure

Since all the modules are part of the same deliverable, if there is a problem in the Jar/War/Ear in one of the modules, its crash would render the application useless. In essence the failure is global -> Single point of failure.

# Enter Microservices

One solution to solving this problem is via Microservices. In our example, instead of having a single bundled file, if we have seperate deliverables for each of the sub-systems then it would help in scaling only that system as per need as well as any change to it being testable easier. This is the way of working of applications that leverage Microservices architecture.

To be more format, Microservices are small, loosely coupled applications or services that can fail independently, scale independently while the rest of the system is unaffected. Another example is on the week having February 14th, most dating applications need to give better performance when trying to load user profiles, pictures etc that the rest of the year. And also, during this time, the customer support sub-system need not scale.

## Principles of Microservices

### Communication b/w Microservices

Microservices should communicate via Message buses / queues like Kafka or RabbitMQ. This way, they would not have hard dependency between them and delivers a system that is loosely coupled.

### Responsiblity of a Microservice

Each Microservice has to be responsible for a single system of the entire application. Ex: The rating system should be a Microservice, the ML product suggestion engine should be another seperate Microservice etc.

### Autonomy vs Code/Data Reusability

The main need for Microservices is to prevent a single point of failure for the applications. If two Microservices share code or if they are tightly dependent on each other, it increases the coupling of the system which would make the application hard to maintain. So always prefer autonomy to reusability. This also helps them to fail independently to each other - example, if the rating system goes down / crashes, it should not and will not halt the ML recommendation system from functioning. This is another way of saying that there should not be any single point of failure.

## Benefits

1.Different parts of the system could be written in different languages. Ex: ML recommendation system in Python, Product profile page in NodeJs, Rating and comments section in Java. The different Microservices can use their own suitable database.
2.Creates an architecture that is highly scalable.
3.Reduces the risk of introducing breaking changes.
4.Smaller, manageable code base for each system which is represented by its Microservice.
5.If there is a memory leak, it will only impact that one service instance, leaving the rest of the application unaffected.
6.No long term committment to a single stack - most ML recommendations are best in Python, backends stable in Java etc.
7.If one Microservice needs to have more RAM memory for doing calculations like cryptography, you can scale them at wish, onto computation heavy EC2 clusters.

## Antipatterns
1. Database replication is needed when developing solutions via Microservices. Otherwise it introduces a single point of failure.
2. Not all applications need to be broken into Microservices - there is no right or wrong software architecture pattern. It is specific to the characteristics of the application that you are developing.

# Components of a Microservice based application

## REST API
APIs exposed over the internet which can be accessed via REST standard.
CRUD operations in REST API world are leveraged by the HTTP methods - POST, GET, PUT/PATCH, DELETE respectively.
The response is typically JSON, but could also be XML.
Most applications offer REST API for integration with other services/products.

## API Gateway
Acts as a reverse proxy and handles all the incoming requests from the end users onto the application.
Forwards the request to the appropriate Microservice.
Can also handle authentications, ssl terminations and load balancing.
Prevents the exposure of the Microservices directly to the end users.

## Event Bus
In a decoupled Microservices arcitecture, Microservices communicate with each other by placing messages onto a bus.
This is the publisher/subscribe design pattern.

## Securing Microservices
Using OAuth 2.0 which prevents credential sharing.
Authentication to be done at API Gateway.
When implementing custom authentication schemes, prefer JWT.

# Data Management
CQRS - Command ( alter data ) Query ( retrieve data ) Responsibility Segregation
SAGA Pattern - when a business scenario needs data from two or more Microservices. Choreography or Orchestration based approaches.

# Key Practises
## Logging
Events published to event bus
Response times
Exceptions
Login activities and access token requests

## Monitoring
Uptime of Microservices
Average response time
Resource consumptino of each Microservices
Success and failure ratio of Microservices
Access Frequency of client requests

## Documentation
API and Design documentation - what all are available for consumption
Depoyment view of architecture
Network and port allocations
Dependencies

## Container Technologies
Containerization of the application is the best option for deployment of Microservices.
1. Docker
2. LXC Linux containers

An orchestration engine is also a very good addition.
1. Kubernetes
2. Docker Swarm
3. OpenShift
4. Amazon Elastic Container Service
5. Azure Kubernetes Services

## Tools and Technologies
Sprint Boot, Helidon
.Net Core
Molecular - NodeJS

## Service Discovery - Determining what services are available for communication
1. Consul
2. Apache Zookeeper
3. Etcd

## API Gateways
1. Kong
2. Ambassador
3. Amazon AWS API Gateway
4. Azure Application Gateway
5. Spring Cloud Gateway

## Event Bus
1. Kafka - perfect fit.
2. RabbitMQ
3. Azure Service But
4. Amazon Simple Queue Service
5. Google Cloud Pub/Sub

## Logging Tools
1. Fluentd
2. Kibana
3. Logstash
4. Suro

## Monitoring Tools
1. Grafana
2. Prometheus

## Documentation Tools
1. Swagger UI
2. Apiary
3. Readme.io
4. Slate
5. Gelato
6. Aglio

## Testing Tools
1. Postman
2. Hoverfly
3. Pact
4. Gatling
5. REST-Assured

# Converting a Monolithic to a Microservice
1. Identify all the different features the current application provides.
2. Define new set of Microservices ( by taking into account the design principles ) for the functionality offerred above.
3. Pick a non-critical Microservice for the first implementation and design it RESTfully.
4. Implement an API Gateway between user interface and Microservices.
5. Connect to database avoiding single point of failure.
6. Implement event bus and few critical micro services and define the events during which one Microservice needs to interact with the other one.

# Resources
- https://www.youtube.com/watch?v=nTtK8mZWLkg&list=PLzERW_Obpmv81N-F8yBowb_QHvf9ISG7y
- ![image](https://user-images.githubusercontent.com/42272776/120099843-578d6100-c15b-11eb-8336-02fad47e356a.png)
- https://www.youtube.com/watch?v=o36vWQCRrp0

# More details

## CQRS & Event Sourcing
- Maintain seperate READ and WRITE stores so that the scaling can happen as needed.
- In Event Sourcing, a complete log of every state change is captured in an event store.
  - This helps in improving WRITE performance - append only log.
  - This helps in recreating objects by replaying the events in the event store.
