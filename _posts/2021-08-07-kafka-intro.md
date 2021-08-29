---
layout : post
title : "What is Apache Kafka?"
categories : [kafka]
published : true
---
Note from read ["Everything you need to know about Kafka in 10 minutes"][1]

### Apache Kafka® is an event streaming platform. What does that mean?

*  To **publish (write)** and **subscribe to (read)** streams of events
*  To **store** streams of events durably and reliably for as long as you want.
*  To **process** streams of events as they occur or retrospectively.


Kafka is a distributed system consisting of **servers and clients** that communicate via a high-performance **TCP network protocol**.

**Servers:** Kafka is run as a cluster of one or more servers that can span multiple datacenters or cloud regions.

**Clients:** They allow you to write distributed applications and microservices that read, write, and process streams of events ...


### Main Concepts and Terminology

*  **Event:** An event records the fact that "something happened" in the world or in your business. It is also called record or message in the documentation. **When you read or write data to Kafka, you do this in the form of events.** Conceptually, an event has a key, value, timestamp, and optional metadata headers

*  **Producers:** Producers are those client applications that **publish (write)** events to Kafka.

*  **Consumers:** consumers are those that **subscribe to (read** and process) these events.

*  **Topics:** Very simplified, a topic is similar to a folder in a filesystem, and the events are the files in that folder.

*  **Partitioned:** Topics are partitioned, meaning a topic is spread over a number of "buckets" located on different Kafka brokers


Events with the same event key (e.g., a customer or vehicle ID) are written to the same partition,

[1]: https://kafka.apache.org/intro "Kafka Intro"
