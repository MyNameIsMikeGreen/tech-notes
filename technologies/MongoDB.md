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

### Collection
If a database were like a file-system, a collection would be a folder. A collection, as the name suggests, is simply a grouping of related documents.

When querying, a user is most often querying a collection to find documents contained within it matching certain criteria.

### Database
A database is simply a set of related collections within a MongoDB instance. The term database in Mongo is the same as in most other DBMS technologies.

# Architecture
## Data Replication
To guard against failures in any single instance, MongoDB operates a concept of **replica sets**. A replication set involves duplicating data held in the primary database into one or more secondary databases. Therefore, if the primary database becomes unavailable for some reason, the data it held has not been lost as one of the secondary databases can take its place.

In addition to guarding against failures of technology, replica sets can be utilised to guard against failures of humans. Secondary servers can be configured as **delayed secondaries**. This means that they are out of sync with the primary by some specified time (e.g. 1 hour behind). This offers the opportunity to restore from this secondary in the event that a user mistakenly executes some unintended otherwise-irreversible change.

### Promoting a Secondary
If a secondary database needs to be promoted to become the primary database (the database the applications communicate with) then there needs to exist a mechanism of selecting which secondary should be chosen in the event that multiple secondaries are within the cluster. There are several options to choose from in MongoDB to achieve this:

* Secondary servers vote for which secondary server they believe to be the most appropriate.
  * For this, the number of secondary servers in the cluster must be at least 3 and must be odd. Otherwise there is a chance of no majority vote.
* An **arbiter node** is provisioned.
  * This node takes the place of a secondary node for the purposes of elections but does not perform any other duties.



# Resources
* [Understanding MongoDB - Frank Kane - YouTube](https://www.youtube.com/watch?v=UFVFIKduXpo)
