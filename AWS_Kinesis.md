# Structure
* The Kinesis family contains Streams (The core component), Firehose (For extremely large data production), and Analytics.
* AWS service that can handle very high throughput of streamed data.

## Shards
* A stream is made up of many shards.
* A shard is a unit of capacity for a stream.
  * As the number of shards increases, as does the computational capability of the stream. The stream can handle a greater number of consumers, more data, and has lower overall latency.
  * Each shard is capable of ingesting up to 1MB or 1000 records per second
* Each shard has a hash key range which determines which records it will handle.

### Shard Iterators
* Used to get data from a stream.
  * As the name suggests, this iterates through the data in the stream.
  * Each iterator lives for only a short time and are therefore disposable.
  * Iterators can return the details of the next iterator.

## Records
* Each set of data and metadata submitted to a Kinesis stream is known as a record.
* A record, as a minimum, contains:
  * The data blob
    * Kinesis does not care what this data is, it can be anything, or formatted in any way.
    * 1MB is a strict size limit.
  * A partition key
    * This is some producer-provided string which, when hashed, is used to determine which shard will process the record.
  * A sequence number
    * An auto-generated field containing a unique key to identify the record.
* Stored in the order in which they are received.
* Typically stored for 24 hours before being automatically deleted.
  * However the retention period can be extended if required.

# Examples

## AWS SDK PutRecords Example

```
AmazonKinesisClientBuilder clientBuilder = AmazonKinesisClientBuilder.standard();

clientBuilder.setRegion(regionName);
clientBuilder.setCredentials(credentialsProvider);
clientBuilder.setClientConfiguration(config);

AmazonKinesis kinesisClient = clientBuilder.build();

PutRecordsRequest putRecordsRequest  = new PutRecordsRequest();
putRecordsRequest.setStreamName(streamName);
List <PutRecordsRequestEntry> putRecordsRequestEntryList  = new ArrayList<>();
for (int i = 0; i < 100; i++) {
    PutRecordsRequestEntry putRecordsRequestEntry  = new PutRecordsRequestEntry();
    putRecordsRequestEntry.setData(ByteBuffer.wrap(String.valueOf(i).getBytes()));
    putRecordsRequestEntry.setPartitionKey(String.format("partitionKey-%d", i));
    putRecordsRequestEntryList.add(putRecordsRequestEntry);
}

putRecordsRequest.setRecords(putRecordsRequestEntryList);
PutRecordsResult putRecordsResult  = kinesisClient.putRecords(putRecordsRequest);
System.out.println("Put Result" + putRecordsResult);
```

## AWS Command Line

* Add data to a stream

  `aws kinesis put-record --stream-name [name] --partition-key [key_string] --data [data_string]`

* Delete an existing stream

  `aws kinesis delete-stream --stream-name [name]`

* Get shard name

  `aws kinesis describe-stream --stream-name [name]`

* Get shard iterator to be used to get data

  `aws kinesis get-shard-iterator --shard-id [shard_name] --shard-iterator-type TRIM_HORIZON --stream-name [name]`

* Get records from shard

  `aws kinesis get-records --shard-iterator [shard_iterator]`

# Additional Resources
* https://aws.amazon.com/kinesis/data-streams/getting-started/
