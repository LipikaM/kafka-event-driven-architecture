# kafka-event-driven-architecture

Traditionally there are two kinds of messaging models:

## message queues: 
where a consumer read from the queue. 

Advantage: The advantage of this messaging type is the scalability. If you need more workers to handle the events you simply add more consumers.

Disadvantage: One event is consumed just by one consumer. If you want two different consumers to get the same event, you can’t.

## publish-subscriber: 
where a consumer subscribes to a publisher for getting some kind of events. Using this model more consumer could get the same data. But, since all the messages goes to all the consumers, you can’t parallelize the work. 

Advantage: the events are distributed to more consumers.

Disadvantage: it is not scalable.

Apache Kafka is a distributed streaming platform capable of handling trillions of events per day. Considering it's design, it gives you both the advantages of a queue message and a publish-subscriber service.

## Kafka is used for:

- If you need to move streams of data around in your system in real-time between applications.
- If you need to identify events that happens in your system and then do something or trigger some workflow based on those events or streams of data.

![alt text](https://github.com/LipikaM/kafka-event-driven-architecture/blob/main/cluster_architecture.jpg?raw=true)

Kafka stores the events in topics. A topic is a logical split among the data, like a category. For example: all the events corresponding to a customer request can be kept on a dedicated topic called customer-requests and all the events corresponding to a payment received can be held on other topic called payment-received.  So, if you want to consume just the customer request then you subscribe to customer-requests topic. This looks like having multiple message queues, one queue per category.

Internally, a topic can be split into partitions. A partition is an ordered, immutable sequence of records. 

## Producers
Producers publish data into topics. Beside this, the producer is also responsible for selecting in which topic partition to put the data. By default, Kafka will calculate the key’s hashcode and uses it to select the partitions.

## Consumers
A consumer subscribes to a topic and then it will start to receive data from that topic. Consumers can be define in a consumer-group (which is just a label). But, using this consumer-group is a critical point for scaling the load. The relation between a consumer-group and a kafka topic is like a publish-subscriber service, where the subscriber is the entire consumer-group. 

So, all the data from a topic will be distributed to all the consumer groups which are subscribed to that specific topic. Right now, we have all the advantages of a publish-subscriber service, were all the data is distributed to all the subscribers. 

## Scalability
The data in a topic is splitted into many partitions, and the consumer-groups consists of different consumers (which can be different processes on different machines).

Kafka ensures that a partition will be consumed by only a single consumer per consumer-group. A consumer can consume data from zero, one, or more partitions. And now, inside a consumer-group, we have a messaging queue, where a consumer goes to a queue and gets one element. And that’s how we obtained scalability. You need more workers? Increase the number of consumer and partitions. Ideally it will be a one-to-one mapping.And this is how Kafka combines both the advantages of a messaging queue and a publish subscriber, eliminating their disadvantages. The data could be distributed to more consumers without sacrificing the scalability.  All this was obtained with the concepts of partitions and consumer group.

## Message Ordering

Kafka doesn’t guarantee the order of the data in a topic, but it does guarantee the order of the data per partition, and, as a partition is consumed by a single consumer per consumer-group, you have the guarantee that those events are consumed in the right order. This is in contrast with a messaging queue, where, even if the events are kept in order, since many consumers get the data concurrently you can’t guarantee the processing order.

## Data Distribution

A Kafka cluster can consist of multiple servers, where each server handles a fair share of partitions. But, additionally, the partitions could be replicated to more than one server. 

Each partition has a leader which is responsible for populating and handling the read request for that partition. But, since the partition is replicated, if the leader dies, some other follower will become the new leader so no data will be lost.

As such, this kind of distribution guarantees that for a topic with a replication factor N, a Kafka Cluster will tolerate N-1 fails without any data loss.

## Storage

In Kafka, the consumers are decoupled from the producers. Producers publish data and consumers come and control what data they want to consume, by specifying the message offset, and how often. As such, Kafka becomes a storage service. The data stays in the cluster for a configured retention time, no matter if the data was read by some consumers or not.

Moreover, since the partitions are replicated for failover, data is implicit replicated. So, like I said before, with a replication factor N, Kafka guarantees no data loss for a maximum of N-1 failures. 

References: https://dzone.com/articles/microservices-event-driven-architecture-and-kafka

