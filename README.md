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

![alt text](https://github.com/LipikaM/kafka-event-driven-architecture/blob/main/cluster_architecture.jpg?raw=true)
