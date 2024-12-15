



![](image/Pasted%20image%2020241215182634.png)


# 1 MapReduce

System published by Google in 2004
- Introduces MapReduce programming model
- Illustrates how model helps to meet the discussed requirements (sequential code, fault tolerance, …) 

Google used MapReduce for various things
- Process crawled  网络爬虫 documents, logs
- Computing inverted indices

Publication has spawned 产卵 significant research activities (Among the most cited CS research papers in last 15 years)

System has been replaced by successors by now





## 1.1 The MapReduce Programming Model

- Based on second-order functions map and reduce
    - Inspired by functional programming language Lisp 口齿不清；咬舌发音
- Map and reduce take first-order functions as input
    - Specify signature of the first-order function
    - Specify how data is passed to first-order function
- ==MapReduce operates on a key-value (KV) model==
    - ==Data is passed as KV pairs through the system==
    - ==Developers specify how to build KV pairs from input data==

The MapReduce programming model is a framework for processing and generating large datasets in parallel across distributed systems. It was introduced by Google and is widely used in big data systems to handle tasks that require scalable and fault-tolerant data processing.
MapReduce is based on two main functions: Map and Reduce. Both are user-defined operations that process data in key-value pairs. The framework automatically handles task scheduling, data distribution, and fault tolerance.

---

The Map Function
- Signature: (k1, v1) →list(k2, v2)
- Guarantees to the first-order function:
    - First-order function is invoked once for each KV pair
    - Can produce a` [0,*]` KV pairs as output
- Useful for projections, selection, …

- **Input**: A dataset split into key-value pairs (k1, v1​).
- **Output**: A transformed set of intermediate key-value pairs (k2, v2).
- **Purpose**: The Map function processes and filters the input data, transforming it into intermediate results suitable for aggregation by the Reduce function.



![](image/Pasted%20image%2020241215183819.png)


---

Shuffle Phase
- **Input**: A transformed set of intermediate key-value pairs (k2, v2). 
- Output: Intermediate key-value pairs grouped by key (k2​, list[v2​]).

---

The Reduce Function
- Signature: (k2, list(v2)) →list(k3, v3)
- Guarantees to the first-order function:
    - All KV-pairs with the same key are presented to the same invocation of the first-order function
- Useful for aggregation, grouping, …

- **Input**: Intermediate key-value pairs grouped by key (k2​, list[v2​]).
- **Output**: A final set of aggregated or summarized key-value pairs (k3,v3​).
- **Purpose**: The Reduce function combines all the intermediate values associated with the same key to produce the final result.

![](image/Pasted%20image%2020241215183927.png)

---

Execution Workflow 
High-Level View on a MapReduce Program
![](image/Pasted%20image%2020241215185040.png)

More complex programs can be created by combining individual MapReduce jobs
![](image/Pasted%20image%2020241215190240.png)




- **Input Splitting**:
    - The input data is divided into chunks and distributed across a cluster of nodes.
- **Map Phase**:
    - The Map function is applied to each input split, producing intermediate key-value pairs.
- **Shuffle and Sort**:
    - Intermediate key-value pairs are grouped by key and distributed to the appropriate reducers. This step ensures that all values associated with the same key are sent to the same Reduce task.
- **Reduce Phase**:
    - The Reduce function processes the grouped intermediate key-value pairs, producing the final output.
- **Output Storage**:
    - The final results are written to distributed storage, often in a format suitable for downstream processing.

--- 

Characteristics of the MapReduce Model
1. **Scalability**:
    - Designed to handle petabytes of data by distributing tasks across hundreds or thousands of nodes.
2. **Fault Tolerance**:
    - If a task fails, the system can reassign it to another node.
3. **Data Locality**:
    - Tasks are executed on nodes where the data resides to minimize data transfer.
4. **Parallelism**:
    - Tasks are processed in parallel, improving throughput.

## 1.2 Example: Word Count

**Problem**: Count the frequency of each word in a large dataset.

**Steps**:
1. **Map Phase**:
    - Input: Lines of text.
    - Map function emits key-value pairs: `("word", 1)` for each word.
    - Example: `"hello world"` → `[("hello", 1), ("world", 1)]`.
2. **Shuffle and Sort**:
    - Group by key: `[("hello", [1, 1, ...]), ("world", [1, 1, ...])]`.
3. **Reduce Phase**:
    - Sum the values for each key: `("hello", total_count)`.
4. **Output**:
    - `[("hello", 5), ("world", 3), ...]`.

![](image/Pasted%20image%2020241215190024.png)


![](image/Pasted%20image%2020241215190031.png)

![](image/Pasted%20image%2020241215190044.png)

![](image/Pasted%20image%2020241215190051.png)



## 1.3 MapReduce Implementation

Assumptions
- Designed for large sets of shared-nothing nodes
- Expects distributed file system
    - Every node can potentially read every part of the input, but local data is preferred (computation pushed to data)
- Individual nodes can fail

![](image/Pasted%20image%2020241215190443.png)


---

Design
- MapReduce follows master-worker pattern
- Master
    - Job scheduling
    - Load balancing
    - Monitoring of worker nodes and failure handling
- Workers
    - Execution of map and reduce functions
    - Reading and storing of data
    - Periodically reporting availability to master node




## 1.4 Distributed Execution of MapReduce Program

### 1.4.1 MapReduce Terminology

- Map function
    - User-defined first-order function that specifies what happens to the data in a job‘s map phase
- Mapper
    - Worker process invoking map function on each KV pair
- Reduce function
    - User-defined first-order function that specifies what happens to the data in a job‘s reduce phase
- Reducer
    - Worker process invoking reduce function on KV pair groups

### 1.4.2 Workflow 

1. Client partitions input file into input splits Splits define maximal scale-ou
    1. Typical split size is 16-64 MB
    2. Independent of GFS block size
2. Client submits job to master
    1. Includes code of map/reduce functions and list of input splits (file boundaries)
    2. Master tries to find free resources to schedule mappers/reducers
3. Mapper started for each input split
    1. Executing the user’s map function on each KV pair in input split
    2. Multiple mappers per server possible
    3. Servers with locally available replicas preferred (data locality)
    4. Intermediate output of mappers is partitioned by intermediate key, stored on server’s local hard disk
4. Reducers pull data from mappers over network: Data is grouped by key, passed to user’s reduce function

![](image/Pasted%20image%2020241215192459.png)

![](image/Pasted%20image%2020241215192516.png)

![](image/Pasted%20image%2020241215192845.png)


## 1.5 MapReduce Refinement: Combiners

- Can be used to locally aggregate intermediate results
    - Executed after the mappers, before partitions go to disk
    - Useful to unburden network
    - Example: If intermediate KV pair <For, 1>, <For, 1> is created by the same mapper, the combiner aggregates it to <For, 2>, without combiner <For, (1,1)> is shipped
- Applicability of combiner depends on job
    - Sometimes local aggregation destroys correctness of results
    - Example: Reduce function shall compute a median


## 1.6 MapReduce Fault Tolerance and Limitations

Fault Tolerance
- Scenario 1: Mapper process fails
    - Master detects failure through missing status report
    - Mapper is restarted on different node, continues with reading from GFS
- Scenario 2: Reducer process fails
    - Again, detected through missing status report
    - Reducer is restarted on different node, pulls intermediate results for its partition from mappers again
- Scenario 3: Entire worker node fails
    - Master re-schedules lost mappersand reducers
- Already finished mappers may be restarted to re-compute lost intermediate results

Limitations
- MapReduce is powerful but has two major limitations
    - Assumes finite input (batch processing only)
    - Data between stages and jobs goes to file system for fault tolerance
- Limitation of finite input prevents stream processing
    - Difficult to respond to events without large delays, e.g. click streams, IT systems monitoring …
- Constraint to write to GFS especially costly for iterative algorithms e.g. graph processing, machine learning, …


# 2 MapReduce Evolutions

- Improved programming model with ==rich operator and feature set==
    - Joins, groupBys, filters, iterations, and windows besides map and reduce operators
- This simplifies a wide array of computations, including:
    - Relational batch jobs
    - Iterative machine learning and graph processing
    - Continuous stream processing

![](image/Pasted%20image%2020241215195321.png)

- Intermediate results ==kept in memory==
    - Instead of writing them to disk
    - Can speed up iterative programs by 100x

![](image/Pasted%20image%2020241215195338.png)


- Generalized processing engine that handles streams
    - Using a batch engine but small batches (called “Microbatching”) → illusion of continuous processing
    - ==Using a streaming engine== → Continuously deployed dataflows, batch becomes a special case of streaming
- Stream processing requires re-definition of operators
    - e.g. classic reduce function does not make sense
    - Instead:==window operators== (e.g. all tuples of last 5 sec.)


# 3 Apache Spark

- General purpose 2ndgeneration data processing framework with support for in-memory datasets and processing
- Open-source project, started as research project at UC Berkeley
- Apache top-level project since the beginning of 2014
    - Large user and developer base
    - Part of the platforms of Cloudera, Hortonworks, and IBM

## 3.1 Spark Stack
● At Spark’s core are parallel transformations of Resilient Distributed Datasets (RDDs)  对困境有承受力的，有复原力的；（物质或物体）有弹性的，有回弹力的
● Libraries built on-top of Spark for popular use cases

![](image/Pasted%20image%2020241215200148.png)

## 3.2 Resilient Distributed Datasets (RDDs)

- RDDs are distributed read-only collections of objects
    - partitioned and distributed across nodes
    - in-memory (intermediate results are not exchanged via disk like in MapReduce)
- Bulk operations (e.g. Map, Reduce, and Join) transform RDDs in parallel on workers
    - ![](image/Pasted%20image%2020241215200326.png)
- RDDs are logical (lazy and ephemeral 短暂的；（主指植物）短生的，短命的 )
    - partitions of a dataset are materialized on demand when they are used
    - an RDD contains enough information to compute it starting from data in reliable storage
- Fault-tolerance through linage  行数；排列；按行数支付
    - Affected partitions are recomputed on failures (but partitions can depend on all partitions of preceding RDDs →can be expensive )
    - No overhead in failure-free case
- Caching: users can give explicit hints that datasets should be kept in-memory
    - Enables faster iterative jobs and interactive analysis
        - ![](image/Pasted%20image%2020241215201000.png)
    - Model data kept in-memory across iterations!


## 3.3 Spark Streaming

Spark processes continuous inputs in Microbatches
- Arriving input is processed in small batches
- Operations like aggregations and joins use these batches as their windows

![](image/Pasted%20image%2020241215201032.png)


# 4 Apache Flink

- Open-source project thatunifies batch and streamprocessing in one engine
- Started as joint research project from TU Berlin,HU Berlin, and HPI Potsdam
- Apache top-level project since beginning of 2015
    - Well-integrated in big data ecosystem
    - Active user and developer community worldwide


## 4.1 FlinkStack

Flink’score is a streaming dataflow engine that supports both batch and stream processing
![](image/Pasted%20image%2020241215202047.png)



## 4.2 Apache Flink Execution Model

- A job consists of:
    - A directed acyclic graph (DAG) of operators and
    - Intermediate streams of data records and control events flowing through the DAG of operators
- Pipelined/Streaming engine
 ![](image/Pasted%20image%2020241215202154.png)



- Follows a master-worker paradigm
    - Job Manager keeps track of all Task Managers and entire job execution
- Execution resources are defined through Task Slots
    - A Task Manager will have one or more Task Slots
    - Typically, there is one Task Slot per core

![](image/Pasted%20image%2020241215202250.png)



Deployment example on a cluster with 2 Task Managers with 3 slots each
![](image/Pasted%20image%2020241215202359.png)

- Complete DAG of operators are deployed distributed on all Task Manager
    - Task Slot executes a pipeline of tasks (operators)
    - Often execute successive operators concurrently


## 4.3 Performance Comparison of Streaming Approaches

- Microbatches: Processing of small batches of tuples
- “Real” Streaming: Tuple-wise processing (lower latencies possible, but might cost throughput)

![](image/Pasted%20image%2020241215202623.png)


