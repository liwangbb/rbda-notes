# Lecture 2 - Big Data Storage

Big Data is when **the size of the data itself** becomes part of the Problem.

One 10 TB HDD < 100 HDD < 100 HDD distributed across 20 computers due to **CPU limitation**.

## Big Data storage concepts
* Big Data analytics uses **high scalable distributed technologies and frameworks**.
* To store Big Data datasets, **often in multiple copies**, to achieve **cost-effective** and **highly scalable** storage. 
* Concepts: 
  * Clusters, distributed file systems, relational database management systems (RDBMS), NoSQL databases, sharding, replication, CAP theorem, ACID, and BASE.

### Clusters

A cluster is a tightly coupled **collection of servers ("nodes")**. 

These servers are **connected together via a network** to work as a single unit.

Each node in the cluster has its **own dedicated resources**, such as CPU, memory, and storage.

A cluster can **excute a job** by **splitting it into small tasks** and **distributing their excution onto different computers** that belong to the cluster.

> Cluster -> Racks -> Nodes(servers) -> CPU, Memory, Storage

### Distributed File Systems
A file is **the most basic unit of storage** to store data.

A file system (FS) is the method of **organizing files** on a storage device.

A distributed file systen (DFS) is a file system that can **store large files spread across multiple nodes of a cluster** in a cluster.

> Examles: Google File System (GFS), Hadoop Distributed File System (HDFS), Amazon S3, and Azure Blob Storage.

## Database Systems for Big Data

### Relational Database Management Systems (RDBMS)
A relational database management system (RDBMS) is a product that presents a view of data as **a collection of rows and columns**.

SQL (structured query language) is the standard language for **querying** and **maintaining** the database.

A transaction symbolizes **a unit of work** that is performed against a database, and treated in a **coherent and reliable way** independent of other transactions.

### NoSQL Databases
A NoSQL database is a **non-relational database** that provides a mechanism for **storage and retrieval of data** that is **highly scalable, fault-tolerant** and specifically designed to house **semi-structured** and **unstrcuted** data.
> Examples: Key-Value stores, Document stores, Wide-Column stores, Graph databases. 
> 
> Examples: Redis, MongoDB, Cassandra, Pregel.

## Distributed Data Management Strategies

### Sharding
Sharding is the process of **horizonally partitioning** a large dataset into a collection of smaller, more manageable pieces called **shards**.

Each shard is stored on a **seperate node**, and is responsible for **only the data stored on that node**.

All shards share **the same schema**, but each shard contains a **subset of the data**.

#### How does sharding work in practice?
1. Each shard can **independently** service reads and writes for the **specific subset** of data that it is responsible for.
2. Depending on the query, data may need to be fetched from **multiple shards**.

#### Benefits of sharding
* Horizontal scalability: Sharding allows for **scaling out** by adding more nodes to the cluster, which can help to **distribute the load** and improve performance.
* Sharding provides **partial tolerance towards failure**.

#### Concerns of sharding
* Queries requiring data from multiple shards can be **slower** will impose performance panalty.
* To mitigate such performance penalty, **data locality** keeps commonly accessed data in the same shard.

### Replication
Replication stores multiple copies of a dataset on multiple nodes.

#### Methods of replication
* Master-slave replication
* Peer-to-peer replication

##### Master-slave replication
All data is written to a **master node**. Once saved, the data is replicated over to multiple **slave nodes**.
* **Write requests**, including insert update and delete, occur on the **master node**.
* **read requests** can occur on either the **master node** or any of the **slave nodes**.
> Ideal for **read intensive loads**. Growing read demands can be managed by **horizontal scaling** to add more slave nodes.

Writes are **consistent**.
* All writes are coordinated by the **master node**.
* However, **write performance will suffer** as the amount of writes increases.

If the **master node fails**,
* Reads are still possible from the **slave nodes**.
* Writes are **not possible** until a new master node is reestablished.

Recover:
* From a **backup**.
* Choose a new master node from the **slave nodes**.

##### Peer-to-peer replication
All nodes operate at the **same level**. Each peer is **equally capable** of handling read and write requests. Each **write** is copied to **all peers**.

Concern: **read/write inconsistency**.

Strategies:
* Pessimistic concurrency is a **proactive** strategy, using **locking** to ensure that only one update to a record can occur at a time. However, this is **detrimental to availability** since the database record being updated remains unavailable to other users until the lock is released.
* Optimistic concurrency is a **reactive** strategy, that **does not use locking**. Instead, it **allows inconsistency** to occur with knowledge that **eventually** consistency will be achieved after all updates have propagated. 

### Combined Approaches for Data Distribution
* Sharding and master-slave replication: Each node acts both as a **master** and a **slave** for **different shards**.
* Sharding and peer-to-peer replication: Each node contains **replicas** of two different **shards**. 

## Theoretical Foundations and Design Principles

### CAP Theorem
A distributed system may **wish** to provide three guarantees:
* **C**onsistency: a read from any nodes results in the **same, most recently written** data across multiple nodes.
* **A**vailability: a read/write request is **always be ackowledged** in the form of a success or failure, regardless of the state of the system.
* **P**artition tolerance: the database system can **tolerate communication outages** that split the cluster into multiple silos and can still **service read/write requests**.
> The CAP theorem states that a distributed system can only provide **two** of the three guarantees at any given time.

### Database Design Principles

#### ACID
ACID is a **traditional** database design principle on **transaction management**.
* **A**tomicity: ensures that all transactions will always succeed or fail **completely**.
* **C**onsistency: ensures that only data that **conforms to the constraints of the database schema** can be written to the database.
* **I**solation: ensures that the results of a transaction are **not visible** to other transactions until the transaction is committed.
* **D**urability: ensures the results of a transaction are **permanent**, regardless of any system failures.
> Traditional databases leverages pessimistic concurrency controls (i.e., locking) to ensure ACID compliance. Database system providing traditional ACID guarantees choose **consistency** over **availability**.

#### BASE
BASE is a database design principle leveraged by many **distributed** systems.
* **B**asically **A**vailable: the system is **always available** to service read/write requests, even if the data is not consistent.
* **S**oft state: the system may be in a **temporary inconsistent state** at any given time, but will eventually become consistent.
* **E**ventual consistency: the system will **eventually become consistent** after all updates have propagated.
> When a database supports BASE, it favors **availability** over **consistency**. BASE leverages optimistic concurrency by relaxing the strong consistency constraints mandated by the ACID properties.

#### ACID vs BASE Comparison
ACID ensures **immediate consistency at the expense of availability** due to record locking. 
BASE emphasizes **availability over immediate consistency**.