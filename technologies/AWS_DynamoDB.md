# Amazon DynamoDB
Dynamo DB is a noSQL database technology for AWS. Unlike AWS RDS, is it not a wrapper service for other database technologies, it is standalone.

## Management
Each AWS account has access to one DynamoDB database instance per region, within this database, many tables are stored.

The database can be accessed in one of three main ways; the AWS console, AWS CLI, or AWS SDK. Each method provides the opportunity to perform table and record management.

## Data Storage
### Records
The data records in DynamoDB are JSON encoded.

An example record may look like the following:

```
{
  "name": "Barry",
  "age": "34",
  "height_cm": 180,
  "dead": false
}
```

As demonstrated, DynamoDB supports three data types: Strings, Numbers, and Booleans.

### Tables
As with most databases, records are held in tables.

An important concept behind table structure is keys. There are two keys that DynamoDB uses; hash keys, and range keys:

 * Hash keys, as expected, are a hash of a particular field and acts as a primary key for the record - as a result, this key must be unique else subsequent writes to the database with that key will be treated as an update operation where the old record will be replaced.
 * Range keys operate on continuous data. They are primarily present to support querying on the field (See: [Queries](#Queries)).

An unusual feature of DynamoDB is the optionality of non-key fields. That is, whilst the fields specified at table-creation-time are required in all records, additional fields may be added or omitted as desired at any time.

## Data Retrieval
### Queries
Queries in DynamoDB operate on key fields. If we wish to query a record based on a hash key, we can check for equality. If we wish to query on a range key, we can assess comparisons to other values.

Important to note is that, if a hash key is present in a table, is *must* be included in any query performed on that table. This results in inflexibility of querying in DynamoDB as there is no concept of a wildcard.

### Scans
A scan is essentially a query where all data is returned. If we scan a table, will will get every records that the table contains. This is useful if you with to perform advanced filtering client-side as the querying capabilities of DynamoDB are quite limited.

### Global Secondary Indexes
A shortcoming mentioned in [Queries](#Queries) is that the hash key of a table must be included in any queries made on a table. A workaround for this is to use a Global Secondary Index (GSI).

A GSI acts, in some way, as duplicate of a table but with different key fields specified. This way, we may remove or change the hash key such that a troublesome field (from a querying perspective) can be demoted to a regular field. A hash key in a GSI does not have to be unique as the true data constraints are dictated by the "real" table. Therefore, we can do something such as set the hash key to a date and query the date to pull everything from a particular day. This wouldn't work without a GSI as we would have to specify the unique hash key in the *real* table and therefore could pull at most one record.

## Resources
* https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html
