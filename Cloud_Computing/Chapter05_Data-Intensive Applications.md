
data-intensive applications : a large pool of cluster nodes 

Analytics Cluster Setup
● Analytics clusters often shared by multiple users and applications
● Shared via resource management and distributed file systems

![](image/Pasted%20image%2020241127122433.png)


![](image/Pasted%20image%2020241127122517.png)



# 1 Distributed Storage

Distributed file systems and databases (e.g. on GFS) typically running on commodity clusters
Fault tolerance and parallel access through replication of data blocks


![](image/Pasted%20image%2020241127122736.png)


## 1.1 Google File System[

GFS designed to meet the following criteria
- Scalability: Must store hundreds of terabytes and support for large commodity clusters (1000s of servers)
- Hihh performance: High throughput (over low latency)
- Fault tolerance: Compensate individual server failures

Google‘s workload characteristics
- Individual files are expected to be big (MBs to GBs)
- Billions of files must be managed by the file system
- Most files are written only once, then read sequentially


### 1.1.1 Google File System Design

GFS is not a POSIX-compliant file system
- Limited support for random writes (expected to happen rarely), data can be efficiently appended instead
- Sits on top of regular file systems, but provides own API to users

![](image/Pasted%20image%2020241127123304.png)




### 1.1.2 Google File System Architecture

● GFS follows master-worker architecture
● Master stores metadata

● Workers store actual data
■ Called chunk server
■ One chunk server per node

● Files are split into fixed-sized chunks
■ Identified by GUIDs
■ Replicated across machines

![](image/Pasted%20image%2020241127123412.png)


- Let’s assume, we have file X with a size of 150 MB
    - 一个file 被分为 多个 chunk 
- Default chunk size is 64 MB
    - File is stored in three chunks
    - Chunks are stored across the available chunk servers
        - 同一个 chunk 可以分布在 多个 server 上 , 每个 server 的 chunk 都是一样的 , 他们的 是 consistent 


![](image/Pasted%20image%2020241127124240.png)



### 1.1.3 Reading File X From GFS

Let’s assume client wants to read X starting at MB 100
1. Client knows chunk size, converts offset to chunk index
2. Client contacts master with filename and chunk index
3. Master returns chunk handle and list of replicas
4. Client sends read request to closest replica (Chunk Server 3 )
5. Client caches chunk information so further reads of the same chunk require no more client-master interaction (until cached information expires)

Note: Master not involved in actual data transfer



### 1.1.4 Design Considerations for the GFS Master

Master 本身不存储 file 的 content , 只存储 file 怎么被分割的 和 个个 chunk 和 chunk server 的信息 (metadata )

Central master has global knowledge of the file system
- Simplifies design and reduces response times
- Easy garbage collection and data reorganization

Metadata is kept in main memory for performance, each chunk consumes 64 bytes metadata

Chunk size is an important parameter of GFS as it determines
metadata that fits into memory
metadata exchanged between master and client

#### 1.1.4.1 What Happens When the Master Fails?

Metadata is crucial to the functionality of the FS
所以 metadata 会被自动 replicated to remote machine 

GFS knows three types of metadata
- Namespace mappings (files, directories)
- File-to-chunk mappings
- Replica locations

Namespace/file-to-chunk mappings are written to log
- **Operation log** is persistent on master‘s hard disk
- Also replicated to remote machine

Replica locations are requested from chunk servers when the master (re-)starts or new chunk server joins

#### 1.1.4.2 Operation Log

Serves as logical timeline that defines the order of concurrent operations
- Files and chunks (as well as their version) are all uniquely and eternally identified by their logical creation times
- Changes to the file system are only made visible to clients after the log has been flushed locally and remotely

==Master can recover file system state by replaying log==

==When log grows beyond certain size the master creates snapshot of metadata to improve startup times==


### 1.1.5 GFS Leases and Mutation Order

Mutation 是 write/append 操作

- A mutation (write/append) is performed at all the chunk‘s replicas
    - 同一个 chunk 可以分布在 多个 server 上 , 每个 server 的 chunk 都是一样的 , 他们的 是 consistent 
- Master grants chunk lease 租约 to one replica to ensure consistent mutation order across all other replicas
    - Replica with lease is called primary
    - 但是他们其中 有一个 primary chunk , (with chunk lease). 其他的 都是 chunk with replicas, 他们之间会自动数据同步, 来保证 data consistency 
- Primary chooses serial order for mutations
    - All other replicas follow this order
    - 当 Mutation (write/append 操作) 发生的时候, Primary chooses serial order for mutations. 按照这个 mutation order 去同步数据到所有的 replica 中 

Global mutation order defined by selected primary

---

**GFS Control/Data Flow for Writes/Appends**

![](image/Pasted%20image%2020241127130627.png)

Mutation 是 write/append 操作

1. Request primary replica (位于 primary chunk 上面) , list of replicas from master
2. Master‘s response
3. Data transfer to arbitrary replica server 数据先写入一个 replica server 
4. Client send Mutation request to primary replica, primary determines order . primary replica,  确定 Mutation' order 
5. Replicas apply mutations in primary‘s serial order
6. Ack. to primary
7. Ack. to client


### 1.1.6 GFS Support for Atomic Appends

Append data  就是 在 chunk3 的某个位置 上面加数据,  这个位置有 Offset 来表示

Append is writing to a file at specific offset
- Offset for reads specified by client
- Challenging for writes in presence of concurrency, e.g. Two applications concurrently specify to write record at offset Y → One record would be destroyed
- Applications depend on atomic append for no loss of data

![](image/Pasted%20image%2020241127131319.png)

- Special GFS operation to append data in atomic units
    - Append procedure
        - 1 Client specifies append operation and data (but no offset!)
        - 2 Primary serializes requests, chooses offsets
        - 3 Returns offsets to clients after data has been appended
- Data appended to next chunk if size exceeded otherwise
    - Rest of previous chunk filled with padding bits
- Client retries operation if append fails at any replica
    - Appended data may occur more than once in some replicas
    - Replicas are not guaranteed to be bitwise identical!
- Operation guarantees that data is appended at least once

## 1.2 Hadoop Distributed File System (HDFS)

- Distributed filesystem inspired by GFS: All data is stored in blocks, replicated on multiple nodes
- Each node is a Linux compute node with hard disks
- One NameNode maintains the file locations, many (1000+) DataNodes store actual data
- Any piece of data is available if at least one data node with a replica is up and running
- Rack-optimized by default:
    - one replica written locally
    - second in same rack
    - third replica in another rack
- Uses large block size, 128 MB is the default, designed for batch processing
- Optimized for scalability and “write once, read often”
- Applications need to be re-engineered to only do sequential writes

Example systems working on top of HDFS
- HBase (Hadoop Database), a database system with only sequential writes, inspired by Google
- MapReduce, a batch processing system
- Pig and Hive, data mining tools
- Mahout, a set of machine learning libraries
- Spark and Flink, dataflow systems


### 1.2.1 HDFS Architecture

![](image/Pasted%20image%2020241127132112.png)



粉色的为 node, 绿色为 block 

NameNode= single computer that maintains the namespace (metadata) of the filesystem
- Keeps all metadata in memory, writes logs, and does periodic snapshots to the disk
- High Availability HDFS allows for multiple NameNodes


Rules
- Replica writes are done in a pipelined fashion and reads are done from the nearest replica to optimize network usage
- Replication and block placement: file’s replication factor can be changed dynamically (default is 3, 默认 同一个block 有三个 replicas in 3 different node )
- Block placement is rack-aware (by default, when racks are configured)



