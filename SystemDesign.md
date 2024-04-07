System Design

2/8. SQL NoSQL Database

SQL:

DB tabular view, data has structured relations, ACID transactions supports(supports concurrency)

Impedance mismatch: Database schema is different from in-memory computing model, to build an in-memory model, we may need to read from multiple tables.

NoSQL:

For semi structured or unstructured data. No strong data integrity. 

Scalable: no relations between tables, easier to sclae horizontally. 
Simple design: we can store data in a document, no need to read from nultiple table to build in-memory object
Supports for semi/unstructured data
COST: many open source solutions

Key-Value DB: key is a primary key, value can be anything like a complex object. DynamoDB, Redis, Memcached
Usage examples: Social media user profiles whre user id or names can be the key, and the profile is the value.
Or Real time recommadations where user id or product id is the key, and recommendations are the values.

Document DB: store data that is in the format of xml, json, bson. MongoDB, Google Cloud Firestore
Examples in e-commerce platform, each product could have many attributes

Graph DB, suitable for social network where nodes have relationships. Neo4J

Columnar DB: store data in columns instead of rows, strong for large data aggregation and analytics Cassandra, Amazon RedShift

For service to be high available, high scalable, and performant

Replication: Keep multiple copies of the data into different nodes geographically distributed

Sync vs Async for Primary and secondary 
1) Sync: wait for all nodes to complete, what if one of the node failed or network failure, not performant
2) Async: return immediately after primary node completes, but may lose data if the parmary node failed and the data is not fully replicated to secondary nodes.
Multi leader replication: 
Peer to peer replication: Quorum


Partitioning/Sharding:

Horizontal sharding: 
1) key range based
cons: 
1. hard to perform range query on non-partitioning key,hard to query by date if partiotion key is customerId
2. could result in uneven distribution

2) hash based
cons:
1) does not work well if server is down or new server is added

3) consistent hashing with virtual nodes
Implement in a hash ring, server and node are hashed and then mapped to the ring
Basic CH solves the problems of migrating lot of data. But the server nodes may still be unevenly distributed.
Introduce virtual nodes to solve this.


What is a preference list??


Available Reailable <think of self check station as example, available may not be reailable>Scalable 

latency throughput

2/9 

2/16

cache

can be added at multiple layers

memory read nanoseconds, hard drive millseconds

cache strategy/patterns
![](/Users/pacheng/Downloads/Xnip2024-02-29_10-34-01.jpg)

cash-aside:
application will read from db and then insert/update cache. Cache is not updated by db directly.
If cache fails, the whole service can run just application layer will need to read from db.

read/write through:
read heavy(news feed)
client - app - cache -db
cache is talking to the db, cache has the respobislibity of updating db
cons: another layer of delay
maybe we cab pre-laod the cache


write around cache:
application will write to the db directly, but application will read from cache.
for write heavy service because we don not write to cache,

write back/async mode:
application will write to the cache and accumulate, cache will do a batch write to the db to persist.
good for heavy write.
cons: what if cache layer break, lose all the writes.
all the write is added to some log?


read ahead


Write ahead logs

Write-Ahead Logging (WAL) is a standard method for ensuring data integrity. Briefly, WAL's central concept is that changes to data files (where tables and indexes reside) must be written only after those changes have been logged, that is, after WAL records describing the changes have been flushed to permanent storage. If we follow this procedure, we do not need to flush data pages to disk on every transaction commit, because we know that in the event of a crash we will be able to recover the database using the log: any changes that have not been applied to the data pages can be redone from the WAL records.


oauth

how would you prevent hacker attacks to run out of your uuid?
can anonymous user also shorten url? can they analyze the usages of their shortened urls?

Sorted String table, bloom filter, bit map

API Gateway, Load Balancer, Index on SQL and NoSQL

Available zone/region?

Is is possible to add indexes on NoSQL database non-key field?

data base read replica?

what is API key?
As you must've noticed, we're using an API key to prevent abuse of our services. Using this API key we can limit the users to a certain number of requests per second or minute. This is quite a standard practice for developer APIs and should cover our extended requirement.

SQL database index:

SQL Server creates a unique index on the primary key columns. This index includes all key columns in the case of a composite key.
This is called primary index or clustered/clustering index.

B-trees are one of the most commonly used data structures for clustering indexes. They are balanced tree structures that store keys in sorted order, making them efficient for range queries as well as point lookups. B-trees maintain a balanced structure, ensuring relatively uniform access times for all elements.
B+ trees have additional features such as separate leaf nodes containing actual data records (or pointers to data records), while non-leaf nodes only contain keys for navigation. 

Secondary index
Used in a database to facilitate faster retrieval of data based on columns other than the primary key. 

bloom filter

prredicate query expensive
index on short url filed

on disk index of db 
LSM tree + SS Table, write heavy
B tree, reads heavy

push or pull cache

write back 
write through, two phase commits
write around, 

analytics

put the information into kafka, to analytics side
instead of sedning kafka write each request we could aggregate the request and then send/flush them as a batch write

stream process
in-mem message broker
log-based message broker write ahead log

kafka spark streaming mini batch


how to delete 
delete from db then delete from cache or delete cache first?
延时双删
? binlog

## HTTP Status code

### 1xx

This is informational code and intrim. You probably will not see these codes.

### 2xx

Successful

- 200 ok request was successful
- 201 correct
- 202 received Request has been accepted for processing, but the processing has not been finished yet. The request may or may not be completed when the processing eventually takes place.


### 3xx
Redirection

- 301 Moved permanently, all the future requirement will be directed to the given url
- 302 Found should look at another url

### 4xxx
Client error

- 400 Bad request could not understand the request because of invalid syntax.
- 403 forbidden, the client request has been rejected because the client does not have rights to access the content. Unlike a 401 error, the client's identity is known to the server, but since they are not authorized to view the content, giving the proper response is rejected by the server.
- 404 Not found, Resource trying to query is not found 
- 429 too many requests, rate limiter/throttle

### 5xx
Server error

- 500 internal server error, a generic message
- 502  the server received an invalid response while working as a gateway to handle the response.
- 503 service unavailable, server is down and can not handle requests.

## HTTP methods

what is API gatway?
API gateway is a fully managed service that supports rate limiting, SSL termination, authentication, IP whitelisting, servicing static content, etc. For now, we only need to know that the API gateway is a middleware that supports rate limiting.

Stateless vs Stateful
sticky session?  not advisable because it is neither scalable nor flexible. 

Redis
centralized cache?
Rate-limiter multiple rate limit services, how to sync?? Redis??


## Message Queues
can the requests in the queue get lost?

### producer consumer

FIFO MQ, will be blocked if some of the message execution failed
unordered MQ, retry Q/dead Q

1 to 1 producer and consumer
1 to n pub to sub

### pub sub

publisher
input channel
message broker
output channel
subscriber

decouple, easy to scale


A log-based message broker is a type of messaging system that uses an append-only log as the underlying data structure to store and distribute messages. In a log-based message broker, messages are written sequentially to the log and are subsequently read by consumers in the same order they were written. This architecture is inspired by the log-structured storage engines commonly used in distributed databases and file systems.

In-memory message brokers store messages primarily in memory rather than on disk. These brokers offer low-latency message delivery and high throughput by avoiding disk I/O operations. They are suitable for scenarios where message processing speed is critical, such as real-time data analytics and low-latency messaging applications. Examples include Redis Pub/Sub and Apache Ignite.

what is event and message?

### Index

### hash index
hash map in memroy, less space
key -> the actual row/ the addrs of the row
O(1) read and write, but range query is O(N)

### b-tree
Self balancing tree data structure.  Each node in a B-tree can store multiple keys and values. The keys within a node are sorted and are used to guide the search operation to the correct subtree that may contain the desired key-value pair.

can store more index,
supports range query
read is fast, (logN)
write might be slow because of write ahead log and maybe update the child index and traverse back. If a node becomes too full after insertion (exceeding the maximum number of keys allowed), it is split into two nodes, maintaining the tree's balance.

### LSM tree + SS table
LSM Log-Structured Merge-tree tree in memeory balanced binary search tree, 
in mem so read and write can be fast
not durable(solved by WAL), 
but mem is less space(solved by SS Table)

Whne LSM tree grows very big, purge it to the disk as Sorted string table.
sorted because the LSM tree is binary search tree.

Create a spare index for the ss table for faster query.Cna also use bloom filter.

To save space, we could merge ss tables in the background. 

To read a key, the system first checks the memtable, then the most recent SSTables on disk, and so on, until the key is found or all tables have been checked. Bloom filters are often used to quickly determine whether an SSTable contains a given key without fully scanning the table, thus optimizing read performance.

![](/Users/pacheng/Downloads/Hash_BTree_LSMTree.jpg)

[B tree VS LSM Tree](https://tikv.github.io/deep-dive-tikv/key-value-engine/B-Tree-vs-Log-Structured-Merge-Tree.html)

## Replicaiton

### Single leader replication
All the write requests go to the leader. Read can be served by any node.
Pro: There is no write confict. 
Con:
Low write throughput, good for read heavy/low write application. 
And since there is only one write node, it could cause single point of failure.
(If the write leader is down, and we have to pick up a new leader. 
What's more, if the new leader is chosen and the old leader comes up. This is called split brain.
Can be solved in distributed concensus.)


### Multi leader replication
There are multiple write leaders. The write request can be served by any leader.
Pro: Very high write throughput. Large geographical areas.
Con: Write conflicts.
1. last write wins. not good as time/clock is not realiable.
2. use version vector to dertermine causality or concurrent write. 
If detected concurrent writes, one way is to store all the conflicts aka storing sblings, the customer can resolve.
The other option is to use CRDT, database merging the conflicts for you.


### Leaderless replication
There is no leader is the system. Cna write to any or any set of the nodes and then read from a set of nodes.
Quorum is required here.
Pro: 
Reletively high write throughput
Quorum writes/reads

Cons:
Reletively high read latency
Write conflicts
1. Read repair, when seeing a successful read and an unsuccessful one, fix the unsuccessful one.
2. Anti entropy. Merge only the difference with merkel tree.

## Consistency

### Strong consistency
A read from any replica would return the most up to date values.

1. Consensus algorithm: Paxos, Raft. Consensus algorithms play a central role in achieving strong consistency in distributed systems. 
These algorithms ensure that a majority of nodes must agree on the order of operations before they are committed

2. Sync replication: Before acknowledging a write operation to the client, the data is replicated to all replicas and the write is committed. 
This approach ensures that all replicas have the latest data before any acknowledgment is sent back to the client, thus maintaining strong consistency.

3. Transaction management: Implementing transaction management mechanisms like multi-version concurrency control (MVCC) or two-phase commit (2PC) protocols ensures that transactions are executed atomically, consistently, isolated, and durably (ACID properties). These mechanisms guarantee that transactions are executed serially, maintaining strong consistency. 2PC

4. Version Vectors or Lamport Timestamps: Using version vectors or Lamport timestamps to track the version of data across replicas ensures that all replicas can converge to a consistent state by resolving conflicts based on these versions. This approach helps to ensure that the order of operations is preserved across all replicas, maintaining strong consistency.

5. Examples:

Distributed Databases: Systems like Google Spanner use synchronized clocks (TrueTime) and a two-phase commit protocol to achieve global strong consistency.

Distributed Coordination Services: Tools like Apache ZooKeeper, which uses Zab, a consensus protocol, to maintain strong consistency across its distributed configuration service.

### Eventual consistency
Sequential consitency

Linearizable consistency


## Good write ups for replicaiton

https://www.brianstorti.com/replication/

https://robertheaton.com/2020/04/06/systems-design-for-advanced-beginners/

https://kousiknath.medium.com/system-design-design-a-geo-spatial-index-for-real-time-location-search-10968fe62b9c

## Sharding

1. Range based sharding 
Similar keys are on the same db, no need to read through network
Not evenly distributed. Maybe some shard is still overloaded.
This is good for range based query if you use sharding key to query.

2. Hash range sharding
Reletively evenly distributed data.
No data locality for range queries. Adding secondary index or global secondary index.

### Distributed transactions
Cross partition writes
Global secondary indexes

2 phase commit
problems: 
If cordinate nodes went down. The receiver hold the key for the row.
If receiveer node went down, coordinator would continue sending data over and over again.

### linearizable
linearizable database, the writes are ordered.
Linearizability = Strong Consistency
Once a write is committed to one replica for a given object, all reads (regardless of replica) of that object afterwards will show the results of the write (not be stale).  To a user, it seems as if there is just one copy of the data.

Single leader replication: replication log
Multi leader/leaderless: version vector or Lamport clock.

### linearizable vs serilizable
http://www.bailis.org/blog/linearizability-versus-serializability/


There's a great explanation by Peter Bailis here:

Linearizability versus Serializability
"In plain English, under linearizability, writes should appear to be instantaneous. Imprecisely, once a write completes, all later reads (where “later” is defined by wall-clock start time) should return the value of that write or the value of a later write. Once a read returns a particular value, all later reads should return that value or the value of a later write."

"Serializability is a guarantee about transactions, or groups of one or more operations over one or more objects. It guarantees that the execution of a set of transactions (usually containing read and write operations) over multiple items is equivalent to some serial execution (total ordering) of the transactions."

## MongoDB vs Cassandra

### MongoDB
A document database.

Within a shard, use single leader replication.
B Tree based storage engine, better for read.

MongoDB uses the shard key to distribute the collection's documents across shards. 
The shard key is either a single indexed field or multiple fields covered by a compound index that determines the distribution of the collection's documents among the cluster's shards.

1. MongoDB vs RDBM

```
Pros:
Flexible schema
The ability to design an application with data locality in mind via a highly nested document structure

Con:
Lack of normalized data may require having to change data in multiple places
```

2. MongoDB vs Cassandra

```
pros:
More expressive document data model
Although with a bit of tinkering on a clustering key you can effectively organize Cassandra rows in a subcollection type of ordering
The ability to query data using complicated secondary indexes, as well as making operations (both writes and queries) spanning multiple shards
B-Tree architecture is better for read throughput

cons:
Use of single leader replication and B-Tree as opposed to LSM trees and leaderless replication means that write throughput does not scale linearly to the number of nodes in the cluster
```

### Cassandra
A wide column database. A row can have any number of columns of any type. But it must have primary key column.
Within a table, a row is uniquely identified by its primary key, and hence all tables must define a single PRIMARY KEY.

Partition key: It is the first component of the primary key definition. It can be a single column or, using an additional set of parenthesis, can be multiple columns. A table must have at least one partition key.

Clustering key: The columns are the columns that follow the partition key in the primary key definition. The order of those columns define the clustering order. Sometimes it is also called sort key??

1. Leaderless Replication:
configurable Quorums. Use read repair and anti entropy(Merkle tree) to fix. Wrote conflicts: Last write wins. Riak use CRDT.

2. Partitioning:
partition key determins which node to send the data to. 

3. Storage engine:
LSM Tree + SS Table, so very fast writes, and read is slower.

4. Index:
The main method uses the ***partition key*** defined for a table, and is called primary indexing.  Often, however, a query must use another column of a table to select the rows desired, and secondary indexing is required. Secondary index is local index not global index.

Global index span all the shards. Fast query if partition key is not provided. Write is could be slower as in needs to update global index and update is more complex because it involves multiple shards.

Local index is index that exists within the single shard. Write is faster but read is slower.

https://cassandra.apache.org/doc/latest/cassandra/developing/cql/indexing/indexing-concepts.html

### HBase

1. Replication
2. Index
3. Sharding
4. Storage engine

## Stream processing

Message broker

```
-Decoupling: the producer and consumer. They don't need to be online at the same time. They don't have to be close. They can be language or database indepandent.

-Load Balance between producer and consumer.

-System is more scalable
```

### exactly once symantics
To ensure at least once and at most once at the same time
1) At least once: Consider disk persistence(wriet-ahead log), replication, or consumer ack
2) At most once: Two phase commit, or idempotence

### two types of message brokers

#### In memory 
1. Round robin delivery, out of order processing unless with fan-out.
2. Messages are deleted if they are successfully processed. Can not replay them.

#### Log based
1. Messages are stored in the disk in the order of arriving at the queue. Consumer will consume at the same order.
2. Messages are durable. Not deleted and can be replayed.