MongoDB
=========

# Overview
MongoDB is a NoSQL document database. It primarily focuses on speed and flexibility over availability and guaranteed data integrity.

## Components

### Document
MongoDB stores data as JSON-like objects known as documents.

The structure of a document need not be defined ahead of time; by default, a document can take any shape that the user desires. As MongoDB's BSON (Binary-JSON) format is used to represent documents, the individual fields it is supported to use a superset of the standard JSON data types:

* All standard JSON types
* Date
* Code
* Timestamp
* etc

The only requirement of a MongoDB document is that it contains an `_id` field containing some unique value. This acts as a primary key. The MongoDB instance will create and populate this field with an `ObjectID` value for the user by default, it does not need to be explicitly defined.

Schemas for documents are not enforced, but they can be if desired. If no schema is set, documents may have no resemblance to one another at all, and this is perfectly valid.

### Collection
If a database were like a file-system, a collection would be a folder. A collection, as the name suggests, is simply a grouping of related documents.

When querying, a user is most often querying a collection to find documents contained within it matching certain criteria.

### Database
A database is simply a set of related collections within a MongoDB instance. The term database in Mongo is the same as in most other DBMS technologies.

# Architecture
## Replica Sets
### Data Replication
To guard against failures in any single instance, MongoDB operates a concept of **replica sets**. A replication set involves duplicating data held in the primary database into one or more secondary databases. Therefore, if the primary database becomes unavailable for some reason, the data it held has not been lost as one of the secondary databases can take its place.

In addition to guarding against failures of technology, replica sets can be utilised to guard against failures of humans. Secondary servers can be configured as **delayed secondaries**. This means that they are out of sync with the primary by some specified time (e.g. 1 hour behind). This offers the opportunity to restore from this secondary in the event that a user mistakenly executes some unintended otherwise-irreversible change.

#### Promoting a Secondary
If a secondary database needs to be promoted to become the primary database (the database the applications communicate with) then there needs to exist a mechanism of selecting which secondary should be chosen in the event that multiple secondaries are within the cluster. There are several options to choose from in MongoDB to achieve this:

* Secondary servers vote for which secondary server they believe to be the most appropriate.
  * For this, the number of secondary servers in the cluster must be at least 3 and must be odd. Otherwise there is a chance of no majority vote.
* An **arbiter node** is provisioned.
  * This node takes the place of a secondary node for the purposes of elections but does not perform any other duties.

### Sharding
Sharding is the concept of splitting a large set of data into multiple parts and assigning to responsibility for each part to a different processor. In MongoDB, this can be achieved by creating multiple replica sets.

Each replica set is made up of one primary and one or more secondary servers, just like in the non-sharding configuration. Data is divided up based on an indexed field. If the value of the indexed field falls between the bounds for one particular replica set, then that replica set is the one assigned to process it.

In the background, MongoDB has a **balancer** process which rebalances the threshold values for sharding as it deems necessary to promote even distribution of work across the replica sets.

In order for the client application to know which server to communicate with on any given document, it makes requests to one of a set of **config servers** which hold the sharding rules and the details of the servers in the cluster. The client, through its use of **mongos** will have cached these details so that the config servers are not contacted for every request.

# Usage
As most DBMSs do, mongo provides a command-line tool for interacting with a MongoDB instance. This tool is aptly-named `mongo`. This section will demonstrate several of the common commands that will be used within this and within similar MongoDB clients.

## Navigation
* `show dbs`
  * Shows databases managed by the connected MongoDB instance.
* `use [DATABASE]`
  * Sets the database that subsequent actions will be performed on.
  * If the database does not yet exist, will also create it.
  * The database will not appear in the `show dbs` output if it does not yet contain a collection.
* `show collections`
  * Shows collections within the active database.
* `[SOME_COMMAND].pretty()`
  * Prettifies the output of a command containing JSON.

## Create
* `db.createCollection('[COLLECTION]')`
  * Creates a new collection.
* `db.[COLLECTION].insert([DOCUMENT])`
  * Inserts a new document with the content specified into the collection.
* `db.[COLLECTION].insertMany([ [DOCUMENT1], [DOCUMENT2], ... ])`
  * As above, but inserts multiple documents at once.
  * This command takes a list of MongoDB JSON documents using standard JSON list syntax (i.e. `[]`).

## Read
* `db.[COLLECTION].find()`
  * Finds all documents in the collection.
* `db.[COLLECTION].find( {[FIELD_NAME]: [VALUE]} )`
  * Finds all documents matching the pattern provided.
* `db.[COLLECTION].find().sort( {[FIELD_NAME]: 1} )`
  * Finds all documents in the collection and then sorts them by `[FIELD_NAME]`.
  * `1` = ascending.
  * `2` = descending.
* `db.[COLLECTION].find().limit(2)`
  * Finds at most 2 documents matching the find condition (if any).
* `db.[COLLECTION].find().forEach(function(doc) {[SOME_JAVASCRIPT]} )`
  * Performs a Javascript function on each document returned from the find command.
  * For example: `function(doc) {print("This is my document: " + doc)}`

## Update
* `db.[COLLECTION].update( [MATCH_CONDITION], [NEW_DOCUMENT] )`
  * `[MATCH_CONDITION]` in this case is very similar to the condition passed to `find()`. It can be any structure that can be used to describe a unique document in the collection. Most commonly, the `_id` field would be used here (e.g. `{_id: [SOME_ID]}`).
  * `[NEW_DOCUMENT]` is what will replace the existing document. If not using any MongoDB operator (See below), this will completely replace the existing document and is therefore no different to deleting and inserting.
* `db.[COLLECTION].update( [MATCH_CONDITION], { $set: { [EXISTING_FIELD]: [NEW_VALUE] } } )`
  * In contrast to the example above, the set operator can be used to update only certain fields within the found document, leaving all other fields intact.

### Operators
Operators are fields prefixed with `$` in MongoDB commands indicating complex or dynamic behaviour. There are different built-in operators to achieve different things. They offer us the opportunity to not simply work with static blobs of JSON when updating, but allow us access to individual fields within documents and their values.

* `$set: { [EXISTING_FIELD]: [NEW_VALUE] }`
  * Seen previously.
  * Sets a new value to an existing field.
  * Leaves the rest of the document intact.
* `$inc: { [EXISTING_FIELD]: 5 }`
  * Increments an existing integer field by 5.
* `$rename: { [EXISTING_FIELD]: [NEW_FIELD_NAME] }`
  * Renames a field.
  * All values remain intact.


## Delete

# Resources
* https://www.mongodb.com/
* [Understanding MongoDB - Frank Kane - YouTube](https://www.youtube.com/watch?v=UFVFIKduXpo)
* [Understanding MongoDB (2020 Update) - Frank Kane - YouTube](https://www.youtube.com/watch?v=HWZRio7ukrk)
* [MongoDB Crash Course - Traversy Media - YouTube](https://www.youtube.com/watch?v=-56x56UppqQ)
