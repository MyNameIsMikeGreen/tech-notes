# ActiveMQ
 Bus technology

## Data Structures

### Queues
* One-in, one-out
* May have many producers
* Only one consumer
* Once read off of the queue, message is gone

#### Dead Letter Queues (DLQ)
If the application reading the message off of the queue (listening) returns a non-200 response code (Behind the scenes it is using HTTP protocols to communicate), then the message is put into an error state. If configured to, redelivery may be attempted If the maximum amount of redelivery attempts has been reached (Or there is no redelivery policy at all), the message will end up on the DLQ.

When on the DLQ, the message can be manually retried (using the web console) and it will be placed back onto the queue that is came from.

### Topics

### Virtual Topics

* One-in, (potentially) many-out
* May have many producers
* May have many consumers
* Only is removed from the topic once all the consumers have read it.
  * But, the message is not visible to the consumer that has read it.
* The topic from the perspective of the producer is named `VirtualTopic.[name]`
* The topic from the perspective of the consumer is named `Consumer.[consumer_name].VirtualTopic.[name]`
