Kafka
=========

# Overview
Apache Kafka is a data streaming technology utilising the producer-consumer paradigm. It is primarily intended to store and transmit event messages in real-time between multiple producers and consumers. It is a key technology used to facilitate event-driven architectures.

In a basic use-case, a producer will place an event message onto a stream whenever a significant state change occurs. There will be one or many consumers listening for these messages on the stream and they will read these messages. As this is a stream rather than a message queue, the messages are not removed once read as there is no explicit intended recipient when placing onto the stream, it will remain for any consumer to read when they are processing events from that timestamp.

Consumers are usually monitoring the stream live (like watching live TV), or they may have started the stream processing from a timestamp in the past and are processing messages beginning from that time (like watching TV on-demand). Eventually, these "on-demand" consumers would likely catch up to the live timestamp and would then be processing the event messages in real-time just like the other consumers.

# Key Components

## Kafka Cluster
May be a self-managed local cluster of related Kafka instances or a cloud-hosted cluster.

### Broker
* An individual logical machine that works with other brokers to make up the larger cluster.
  * Could be a server, or a container, or a VM, or any other type of capable computing machine.
* They each have their own instance of Kafka running and their own storage volume(s) to manage.
* If using a fully-managed cloud-based Kafka service, brokers are mostly abstracted away.

### Topic
* A logical stream of related messages.
* There is no limit to the number of topics that can be created/used.

### Partition
* The data within a topic is persistent, therefore it needs to physically live somewhere on a broker (on disk). However, performing this storage has a computational cost and eventually, once the topic becomes large enough (with increased frequency of messages and with increased number of consumers and producers), it becomes infeasible for a single broker to manage a topic.
* Effective scaling of topics in Kafka is accomplished using partitioning.
* A topic is broken up into multiple partitions and each partition managed by a different broker, spreading the burden.
* The concept of partitions introduces important notes about the relative order of messages in a topic. See [Message Ordering](#message-ordering) for more details.
* The Kafka cluster automatically places partitions on brokers when a topic is created. However, it does not rebalance the locations of partitions as time goes on.
  * This can mean that certain brokers may get overloaded with work if it happens to be in charge of multiple busy partitions. Any rebalancing is something that the developer must manage themselves if using a self-managed system. When using a cloud platform, it is likely that the provider will manage rebalancing themselves.

#### Replication
* Without an additional mechanism, should a broker go down, it would take the partition and all of its messages with it.
* To combat unreliability of brokers, partitions are replicated across multiple brokers.
* One broker will be the *leader* for a partition and it will have several *followers*.
  * Typically, there are 2 followers for each leader.
* The follower's job is to scrape the leader for its messages and store an identical copy.
  * This is done in as close to real-time as possible.
* Should the leader go down, one of the followers will be promoted to become the new leader.

### Segment
* At the lowest level, Kafka is ultimately storing messages it receives in log files on partitions.
* Instead of storing one gigantic log file for a partition, Kafka stores it as a set of rolling log files.
* This has the added benefit of making it simple to implement TTLs on topics as the Kafka system can simply delete old segments.
* Unless fine tuning a Kafka configuration, most people do not need to care about segments as the details of log storage are mostly abstracted away.

## Producer
* An application which writes event messages to a topic (or multiple topics).
* The Kafka cluster sends an acknowledgement back to the producer if the message has been received successfully.
* Producers are completely de-coupled from consumers, they have no visibility of what will be reading from the topics it writes to.
* Kafka producers can be written using many languages
  * Java is the primary language and is the main language supported by the Apache Foundation.
  * Python, C++, .NET, and JMS official libraries are also available.
  * There are several community libraries for development with other languages.
  * Where there are no native libraries available, Kafka messages can be sent using REST.
  * A command-line tool can be used for producing messages for testing.

## Consumer
* An application which reads event messages from a topic (or multiple topics).
* Consumers are completely de-coupled from producers, they have no visibility of what wrote to the topics that it reads from.
* Uses a polling mechanism to check whether the broker has any further messages since some timestamp (offset) for a given topic.
* The offset that a consumer is up to us stored by both the consumer itself in-memory and also by the cluster itself in a special consumer offsets topic.
  * This means that, if the consumer "forgets" where it got to in a topic (perhaps the consumer was restarted), it can check against the cluster's consumer offsets topic to find out where it left off.
* As with producers, similar libraries and tools exist to consume Kafka messages in a variety of ways.

## Consumer Groups
* Logically similar consumers can be put into *consumer groups*.
* Between them, messages from a topic will only be consumed by a group once.
  * This is acheived by uniquely allocating each consumer within the group a different partition to read from.
* An example of a consumer group could be an application with multiple instances deployed to Kubernetes. Each instance would belong to the same consumer group as other instances of the same application.

## Zookeeper

>**NOTE:** Following *KIP-500*, Zookeeper will no longer be necessary to use Kafka.

Zookeeper is an application used by Kafka to manage distributed state and store metadata. It facilitates the cluster having an accepted concensus for various activities such as topic leadership and replication configurations.

## Message
* A message represents an event. It is the atomic entity created by a producer, placed onto a topic, and read by a consumer. It is made up of multiple parts.

### Headers
A set of key-value pairs representing metadata for the payload. It shouldn't typically be used to extend the payload itself, but rather provide information *about* the payload which may be useful for the consumers. The concept of these headers is very similar to the concept of HTTP headers.

### Key
* Under normal circumstances, the key is what defines which partition a producer sends a message to. Messages with the same key will be sent to the same partition.
* There is no specific structure necessary for the key. It may be some general string, it may be an integer, it can even be a serialised object if that is desired.
  * A custom partitioning strategy may choose to ignore the key for partition selection but this is uncommon.
* If no key is provided, messages will be sent to one of the topic's partitions in a round-robin approach.

### Value
The actual payload that the producer wants to share. Most likely this value is going to be some serialised domain object.

### Timestamp
This represents the time that the event occurred. This can either be written explicitly by the consumer or, if it has been left blank, will be set by the broker to be the time it was ingested.

# Message Ordering
* In practice, even though we conceptually write to a topic, a producer is actually writing to a specific partition which manages part of that topic.
* When writing, because of the log nature of kafka topic storage, we are appending to the end of a log file for each partition, anything already written is undisturbed.
* Therefore, the order of messages is relative only within a specific partition.
  * i.e. if multiple producers are writing to a topic with multiple partitions, the end of one partition's log file may be at a different timestamp to another partition's log file (perhaps one broker is simply slower than the other), so if these files were simultaneously read by one consumer, the messages may appear out of order - but if reading from the log of a single partition, the messages will always appear in perfect relative order.
* If we want to guarantee that some specific set of messages will be stored and consumed in order, we must ensure that they are written to the same partition.
  * For example, if we have a topic for storing temperatures and we have many IoT devices reporting their current temperature, we may want to ensure that each individual device's temperatures are stored cronologically within the topic.
* To guarantee that similar messages will be handled by the same partition, we can set those messages to each have the same key.
  * Using the above example, we may set the key for one device's messages to be `device-1` (instead of, for example, being random). This way, all of the messages from this device will be sent to whichever partition is registered as being in chage of messages with the key `device-1` (or, more technically, whatever `device-1` is hashed to).

# Resources
* https://kafka.apache.org/
* https://kafkasummit.io/
* [Fundamentals of Apache Kafka Course - Confluent - YouTube](https://www.youtube.com/watch?v=-DyWhcX3Dpc&list=PLa7VYi0yPIH2PelhRHoFR5iQgflg-y6JA)
