
keypoint
Cloud native and microservices are currently predominant architecture style
Several large cloud providers dominate the commercial market
Cloud migration transforms the on site IT infrastructures
Designing microservice architecture is an iterative process to discover regarding interaction, size of services, dealing with transactions


# 1 Cloud Architectures

## 1.1 Basis

Resource Pooling

Dynamic Scalability
Elastic Resource Capacity
Service Load Balancing
Cloud Bursting
Elastic Disk Provisioning
Redundant Storage
Multi cloud

### 1.1.1 Resource Pooling

Resource pools = identical IT resources grouped and maintained by system that automatically ensures synchronization
- Physical server pools: networked servers with OS, applications ready for use
- Virtual server pools: configured using templates chosen by consumer during provisioning
- Network pools: preconfigured network devices, e.g. virtual firewalls or physical network switches for redundant connectivity, load balancing, …
- CPU pools: ready to be allocated to virtual servers, typically broken down into cores


Example
A 1/ A 2 comprised of the same IT resources as Pool A, but in different quantities

![](image/Pasted%20image%2020241212101246.png)

### 1.1.2 Extending Scaling Architecture with Pooling

![](image/Pasted%20image%2020241212101447.png)

### 1.1.3 Service Load Balancing Architecture

- Variant of workload distribution architecture specifically designed for scaling cloud services
- Redundant deployments of cloud services with load balancing
- Implementation mechanisms
    - Usage Monitor: monitoring service instances and IT resource consumption at runtime
    - Resource Cluster: active-active cluster groups incorporated to balance workloads across different members of the cluster
    - Replication: generate cloud service in support of load balancing requirements

An active-active cluster refers to a setup in high availability (HA) systems where multiple nodes or servers in a cluster are simultaneously active and processing workloads. This configuration ensures efficient resource utilization, scalability, and fault tolerance.
- **Improved Performance**: Concurrent processing increases throughput.
- **Fault Tolerance**: Redundancy ensures continuous operation despite individual node failures.
- **Optimal Resource Utilization**: All nodes contribute actively, reducing idle resources.
- **Scalability**: Easier to expand by adding more nodes.

### 1.1.4 Cloud Bursting Architecture

Dynamic scaling (bursts out) of on premises workload into off premise cloud when predefined capacity thresholds reached → Provides consumers with option of cloud based IT resources to meet higher demands

Approach
1. Scaling listener monitors usage of Service A (on prem) and redirects Consumer C’s request to A’s redundant implementation (Service A, off prem) once A’s threshold exceeded (1)
2. Resource replication system keeps state management databases synchronized (2)

![](image/Pasted%20image%2020241212102056.png)

### 1.1.5 Advanced Cloud Architectures

- Mostly used for creating resilient architectures with minimal downtime
- Problem
    - Creating and hosting multiple virtual servers with ==one hyperviso==r introduces unwanted dependency → any failure condition affecting a hypervisor can cascade to virtual servers
- Solution
    - Building high availability cluster of hypervisors across multiple physical servers. 就是 多个 hypervisor 
- Mechanisms
    - Redundancy
    - Heartbeats: system messages between hypervisors, between hypervisors and virtual servers, and between hypervisors and virtualization infrastructure management (VIM)
    - Live VM migration: relocating virtual servers or virtual server instances at runtime

#### 1.1.5.1 Hypervisor Clustering Architecture

Goal: High availability cluster/hypervisors across multiple physical servers

If heartbeat detects unavailable hypervisor/ underlying server → virtual servers moved to another server/hypervisor
1. Hypervisors installed on physical servers A, B, C
2. Virtual servers created by the hypervisor
3. Shared cloud storage contains virtual server configuration files accessible by all hypervisors
4. Hypervisor cluster enabled on all three physical server hosts via a central VIM
5. VIM chooses physical server as new host for virtual servers → Virtual server live migrated to new location (restarting may be needed)


virtualization infrastructure management (VIM)

![](image/Pasted%20image%2020241212103154.png)


#### 1.1.5.2 Virtual Server Clustering Architecture

- Deployment of virtual server clusters on physical hosts running hypervisors
    - Virtual servers instantiated on top of separate physical hosts running hypervisors
    - Virtual infrastructure with virtual server clusters configured for e.g. big data analytics, SOA, advanced container management platforms
- Example
    - Physical Servers A, B, C running hypervisors allowing multiple virtual servers to be hosted (Virtual servers configured by resource cluster mechanism into clusters of virtual servers )

![](image/Pasted%20image%2020241212103506.png)


### 1.1.6 Nondisruptive Service Relocation Architecture

- Cloud service can become unavailable for many reasons, for example 
    - runtime usage demands exceed processing capacity
    - maintenance update mandates a temporary outage
    - permanent migration to a new physical server host
- Temporarily unavailable cloud service is a problem, even if the outage is planned
- ==Nondisruptive service: predefined event triggers service duplication or service migration at runtime to avoid any disruption==
    - Cloud service activity temporarily diverted 使转向 to another hosting environment at runtime by adding a duplicate implementation onto a new host
    - Requests temporarily redirected to a duplicate implementation
- Live migration is the key
    - ==New cloud service guaranteed to be successfully receiving and responding to consumer requests before the original cloud service implementation is deactivated or removed==

---

Nondisruptive Service Relocation: Procedure
![](image/Pasted%20image%2020241212104526.png)

Monitoring workload (1), 
predefined threshold reached (2), 
listener signals VIM to initiate relocation (3), 
Live VM migration for runtime relocation (4)
Second copy of virtual server and its hosted cloud service created via destination hypervisor on Physical Server B (5)
State of virtual servers synchronized (6). 
First server removed from A after requests successfully exchanged with B (7). 
New requests only to B (8)


### 1.1.7 Resilient Disaster Recovery Architecture

Disaster recovery strategies
- Create secondary remote location with redundant copies of all critical enterprise resources to take over in case of major catastrophe at original location
- copies placed in a remote location and synchronized with their original copies
- ready to replace originals

Example
- Organization uses resource replication to create duplicate virtual instances of its physical infrastructure in a public cloud
- Storage replication synchronizes on premises data with duplicates in cloud


![](image/Pasted%20image%2020241212104957.png)


# 2 Cloud Platforms: IaaS vs. PaaS

PaaS offers higher abstraction level compared to IaaS
- Less development/maintenance effort
- Less flexibility, high provider dependence

![](image/Pasted%20image%2020241212110458.png)


Higher abstraction level allows more variable / expensive pricing models for providers

Service usage can be charged
- by time
- per query (e.g., for database services)
- per message (e.g., for message queues)
- per CPU usage (e.g., for request triggered applications)

Enables interesting scenarios for consumers
- Deployed applications can have very low operational costs until they become popular

## 2.1 Azure 


Azure Programming Model (1/4)

Azure is based on the ==component and role==

- Azure applications separated into logical components
    - Each component is assigned to a role
    - Multiple instances of the same component might exist
- Azure programming model offers three roles
    - Web role: Components facing the outer world
    - Accepting requests via HTTP, e.g. Web sites/services
    - Worker role: Components doing background tasks
    - VM role: Legacy components
    - Component outside of Azure’s programming model



Example: Azure application according to the programming model
![](image/Pasted%20image%2020241212110929.png)


## 2.2 AWS

Sample Service: Elastic Map/Reduce (EMR)

![](image/Pasted%20image%2020241212112153.png)

![](image/Pasted%20image%2020241212111855.png)


Amazon runs preconfigured systems on EC2
- User submits/monitors jobs through web interface
- Dedicated VMs per user
- No direct access to the VMs (in contrast to IaaS)

![](image/Pasted%20image%2020241212111920.png)


1. Customer submits job through web interface with following job specification
    1. Location of input data on Amazon S3
    2. Framework, job code, user libraries, parameters, …
    3. Number of virtual machines to run the job on
    4. Type of virtual machines to run the job on
    5. Designated output location on Amazon S3
2. Requested virtual machines are started on EC2
    1. Pricing starts to apply
    2. Boot of EC2 instances with preconfigured systems and start of these
    3. Worker nodes automatically contact master
3. Job runs on rented VMs
    1. Input read from Amazon S3
    2. Customer can monitor execution progress through web interface
    3. Output saved to Amazon S3
4. After job completion, EC2 instances are automatically shut down
    1. Job completed after all distributed tasks have finished
    2. All VMs must remain allocated until that point in time
    3. Intermediate data might be required for potential recovery
    4. Shutdown also marks end of billing period


Executing Sequences of Jobs
- Deployment model requires to store initial input/final output on Amazon S3
    - Violates principle to keep data local to computation, so increased effort to move data to/from virtual machines
    - Yet storage nodes can be independently scaled
- EMR also allows to execute sequences of jobs
    - Initial input/final output still stored on Amazon S3
    - Intermediate results between jobs stored in HDFS
    - Data kept at least local between two, e.g., MR jobs

Data Stored in S3
- Data stored in S3 in between jobs
    - Pro: independent number of storage/compute nodes and less expensive than a permanent HDFS cluster
    - Contra: data locality
    - Thus, good if you only run jobs every now and then

![](image/Pasted%20image%2020241212112337.png)


Auto Scaling with EMR
- Rules for dynamically scaling of core and task nodes based on metrics
- Example: scale up number of workers when less than 15% of memory available for a five minute period
![](image/Pasted%20image%2020241212112515.png)

## 2.3 Google Cloud 

- Projects in GCP used to allocate all resources as management entity of the services
    - Characterized by parameters such as permissions, settings, and other meta data for applications
    - Communication between resources in a project is allowed
    - Access to resources used by other projects using a shared VPC or Network Peering
    - Unique project ID can never be reused for other projects even if the initial project is deleted
    - Resource names in the same project must be unique
    - Each project is assigned to a billing account

- Modes to access GCP services
    - Google Cloud Console: web based user interface
    - gcloud command line tool: interacting with GCP services using the command line
    - Cloud SDK: set of tools for developing and deploying applications to GCP
    - Google Cloud API: access GCP services programmatically
    - Cloud Marketplace: pre configured and ready to use solutions be to integrated and managed
    - Cloud Shell: command line interface for running GCP commands directly in a web browser


- Compute Services
    - Compute Engine (GCE): Infrastructure as a Service (IaaS) run VMs in Google’s data centers
    - Kubernetes Engine (GKE): Managed Kubernetes service for deploying, managing, and scaling containerized applications
- Storage Services
    - Cloud Storage: Scalable and durable object storage
    - Cloud SQL: Fully managed relational DB service for MySQL, PostgreSQL, SQL Server
    - Cloud Spanner: Globally distributed, strongly consistent, horizontally scalable DB service
- Database Services
    - Bigtable: Fully managed, scalable NoSQL DB for large analytical and operational workloads
    - Firestore : Serverless, NoSQL document database for web, mobile, and server development
- Developer Tools
    - Cloud Build: CI/CD platform
    - Cloud Source Repositories: Private Git repositories hosted on GCP
- Networking Services
    - Virtual Private Cloud: Networking service for creating and managing virtual private networks
    - Cloud Load Balancing, Cloud DNS: Automated, scalable, distributed load balancing / DNS service
- Big Data and Analytics
    - BigQuery : Serverless data warehouse for fast SQL queries on large datasets
    - Dataflow: Stream and batch processing service for real time data processing
    - Dataproc : Managed Apache Spark and Hadoop service for running big data analytics
    - AI: Services for building, testing, deploying ML models (Vision AI, NLP with pre trained models)
- Identity and Security
    - Identity and Access Management: Security and access control for permissions and policies
    - Cloud Identity Aware Proxy: Identity based access control for applications running on GCP


![](image/Pasted%20image%2020241212184120.png)


![](image/Pasted%20image%2020241212184130.png)


# 3 Cloud Migration


## 3.1 Why Migrating Systems into the Cloud?

Organizations migrate infrastructures and applications from own data centers (on premise) to cloud providers (off premise) expecting some benefits
- Optimized costs: savings in resource, maintenance, and real estate costs
- Flexibility and scalability: pay per use but also cover demand spikes
- Enhanced security: public clouds are more valuable targets, but have SOTA built in security features and specialized cloud security tools
- Compliance offerings to meet requirements needed in highly regulated industries like finance, healthcare, and government
- Provider responsible for backup , recovery, and failover
- Simplified management and monitoring of on premises data center and cloud resources (single Screen)

## 3.2 6Rs of Cloud Migration

- Framework by AWS for organizations to shift workloads to cloud platforms
- Each option with different balance of complexity, cost, and cloud optimization
    - Rehost (Lift and Shift): straightforward migrating apps and data with minimal changes
    - Re-platform (Lift, Tinker, and Shift): apps modified partly for exploiting cloud capabilities, but no re-architecture
    - Rearchitect, Refactoring (Redesign for Cloud): significant modifications to fully exploit the advantages of cloud native technologies and services
    - Repurchase (Drop and Shop): moving to different product (cloud native or SaaS)
    - Retire: Removing no longer useful IT assets, migrating only the necessary systems
    - Retain (Keep as It Is): no migration of workloads based on specific business, technical, or regulatory factors (e.g. data privacy)

![](image/Pasted%20image%2020250112222345.png)


---

Retain & Optimize
- Retention: viable option when applications
    - tightly bound to legacy hardware, cannot be easily moved or
    - regulatory and compliance requirements not yet addressed by cloud environments
    - Pros: Compliance, stability, cost management (avoids unnecessary migration costs for systems that are not cloud ready or beneficial to move)
    - Cons: Missed opportunities, complexity of a separate environment to manage
- Repurchase: existing outdated applications replaced by cloud native solutions
    - reducing operational burden of maintaining legacy systems
- Retire: Applications outdated, redundant, or no longer deliver business value
    - Identifying such assets is challenging: Comprehensive inventory of all IT assets / applications and usage statistics analysis to identify rarely / never used assets

Lift & Shift
- Mostly implemented as rehosting → organizations move existing on premises applications to cloud without redesigning the architecture
    - Quick migration (time constraints/cost pressures) and fast scaling operations rapidly without immediate concern for cloud native features
    - Pros: Fast, cost effective (avoiding major redesigns), simple (reduces complexity in migration process)
    - Cons: Poor optimization, technical debt (keep legacy issues and inefficiencies)
- How to lift & shift
    - Assessment: which workloads to shift? Compatible with provider's environment?
    - Planning: Migration plan, resource allocation, scheduling, risk mitigation strategies
    - Preparation, migration: automation tools to be used for migration and possible rollback?
    - Testing: Ensure functionality and performance match expectations
    - Optimizing resources to take advantage of the cloud's scalability and elasticity

Re-architect (Re Factor)
- Re-building applications needing scalability, resilience, or features from cloud native architecture → Often used for transforming businesses through the cloud
    - Pros: Full optimization (cloud native features for performance, scalability, resilience), long term savings, innovation (e.g. microservices, serverless)
    - Cons: High initial cost (time, resources, expertise), complexity of restructuring and transition risks, potential for greater impact on business downtimes
- How to re architect?
    - Assessment: In depth analysis to determine feasibility and requirements for rearchitecting
    - Strategic planning: Roadmap for transition (timelines, expected benefits, resource allocation)
    - Rebuilding architecture: cloud native practices, transforming monolithic in microservices
    - Implementation: Develop and deploy rearchitected application using CI/CD pipelines
    - Testing and iterative optimization

## 3.3 cloud native architecture

- What is a cloud native architecture?
    - Complex systems decomposed into services that can be independently tested and deployed on containerized runtime (microservice or service oriented architecture)
    - Applications use standard platform provided services such as DBMS, blob storage, messaging, CDN, SSL, etc.
    - Standardized cloud platform automates operation (deployment, autoscaling, configuration, monitoring, alerting) → access on demand by all development teams
    - Standardized OS, middleware, language specific stacks provided to developers
    - Cross functional teams responsible for entire software delivery life cycle of each service


Cloud native: Principles and practices
- Microservices architecture principles and practices recommended by Google
    - Every service with own DB schema to remove dependencies
    - Services communicate through public APIs only: no “back door” access, or services talking directly to other services’ databases
    - Services responsible for backwards client compatibility: Team building and operating a service makes sure that updates don’t break the service
    - Standard way to run services: Developers can run any subset of production services on own workstations with one command → easily test and debug locally
    - Observability: Teams have access to tools/data to trace, understand, diagnose infrastructure problems in production, including interactions between services
    - Service level objectives: expectations on how the service will perform, mitigation plans for service interruption

Cloud native: Local and remote development loops
- Local development: Speed up an inner development loop and provide the developers with sets of tools to get fast feedback on the impact of local code changes
- Remote Development: Pull request (PR) kicks off remote development loop reduce the time to validate and test the PR via CI, scan for vulnerabilities, and performing other supporting activities
![](image/Pasted%20image%2020250112224134.png)



Cloud native architecture elements
![](image/Pasted%20image%2020250112224355.png)


Operational Excellence
- Automate everything
    - Infrastructure as Code (IaC): minimizing errors during environment provisioning and deployment by using code artifacts (Azure Resource Manager, AWS CloudFormation, Terraform, etc
- Monitor everything
    - Information about application and environment behavior, usage patterns
    - Challenging: consistent monitoring across entire stack
- Document everything
    - Cloud native applications built by many teams → documentation crucial, as every member must understand how to use each services, how environment is defined and provisioned
    - Documentation should be done automatically and not manually
- Incremental changes
    - Changes to environment and application should be incremental and reversible
- Design for failure
    - How to design fault tolerant applications and which processes need to activated in case of failure

## 3.4 Fundamentals of Cloud Native Applications

Difference between cloud native applications and monolithic application
- Handling of state (session state, application and configuration data, etc
- Orchestration of Services
- Reaction to failures


Stateful vs. Stateless
- Handling of state (session state, application and configuration data, etc.)
    - Traditional applications stateful (application state commonly stored in compute instance)
    - Cloud native: stateless by nature → state externalized
- Example
    1. Client sends request to application
    2. LB routes request to Application Instance 0, which reads and writes the state to external state store
    3. Instance 0 failure → LB sends the requests from client to Application Instance 2
    4. Instance 2 reads state for the initial request from the external state store → client not affected by failure

![](image/Pasted%20image%2020250112224741.png)


Event-driven patterns for communication
- Organizing requests across loosely coupled services
- Each service isolated, autonomous, responsible for managing its own state
- Example
    1. Client request to application routed through LB to SvcA , which requests data from messaging system
    2. SvcB independently sends its data to eventing system
    3. SvcC independently sends its data to eventing system
    4. SvcA picks up the data from the eventing system and sends it back to the client

![](image/Pasted%20image%2020250112225248.png)



Handling Failures
- Traditional architectures try to minimize failures using redundancy, e.g. database clustering
- Cloud native architectures expect failures and implement mechanisms to deal with them, whereas
- Small services and stateless means
    - Quick recovery after failure (restart) or
    - Scale out in case of performance issues
    - Fast live migration in case of failed platform
    - Roll back, if update malfunctioning

## 3.5 Microservice Architecture

12 factor methodology for cloud native
![](image/Pasted%20image%2020250112214213.png)


![](image/Pasted%20image%2020250112214227.png)


Microservice Architecture
- Distributed, decoupled: each service in own container / VM
    - Extreme decoupling “wastes” resources, affects performance (many network calls)
- Bounded context
    - Each service models a domain or workflow
    - Each service includes everything necessary to operate within the application including classes, other subcomponents, and database schemas
- “Microservice” is a label , not a requirement (micro)
    - Architects struggle to find the correct granularity and make the mistake of too small services, which require (too) many communication links between the services
    - finde the best balance between size of service and the network 

Microservices: How to find the right size (Granularity)
- Ideas, what to use to determine the granularity:
    - Purpose: Ideally, each microservice is functionally cohesive 团结的，有凝聚力的, contributing one significant behavior on behalf of the overall application.
- Transactions: entities that need to cooperate in a transaction build a good service boundary
    - transactions cause issues in distributed architectures → design the system to avoid
- Choreography编舞: set of services with excellent domain isolation yet extensive communication needs
    - bundling these services into larger service to avoid overhead may be a better design choice
- Data isolation: Using relational databases to unify values within a system (single source of truth) is no great option when distributing data
    - Identifying one domain as the source of truth for some fact and coordinating with it to retrieve values or using database replication or caching to distribute information
- Iteration is the only way to ensure good service design
    - Architects rarely discover perfect granularity, dependencies, and communication overhead on first pass → Iterating over options gives a good chance for refining the design


---
Topology of the microservices architecture style
![](image/Pasted%20image%2020250112214333.png)

---

Operational Reuse
- Microservices prefer duplication to coupling
- How architects handle architecture parts that benefit from coupling (e.g. monitoring, logging, …)?
- Challenges
    - How can be ensured that each service team implements monitoring?
    - Who is responsible for upgrading to the new version of monitoring tool?
- Sidecar pattern in microservices
    - Handles all operational concerns that benefit from coupling
    - Shared infrastructure team updates sidecar → each microservices receives that new functionality
![](image/Pasted%20image%2020250112215804.png)

---

Service Plane and Service Mash
- Sidecar content (included services) is known to all teams
- Each sidecar wires into service plane, which forms consistent interface to each service
- Service mesh allows unified control across the architecture for logging and monitoring and allows developers holistic access to services
- Green tiles: application, blau: proxy 

![](image/Pasted%20image%2020250112215901.png)



---

Choreography
- No central (permanent) mediator 
    - First service called becomes a mediator 中介物 and coordinates the other services (front controller pattern) 
    - added complexity to every service
- Trade offs
    - Pros: Preserving highly decoupled philosophy of the architecture style
    - Cons: Common problems like error handling and coordination become more complex to handle

![](image/Pasted%20image%2020250112221137.png)

---

Orchestration
- Dedicated, permanent service responsible to coordinate the call and get all information for particular customer
- Trade offs
    - Pros: Less coordination overhead
    - Cons: Breaking distribution as domain workflows are inherently 固有地 coupled

![](image/Pasted%20image%2020250112221354.png)

---

`Saga(*) Pattern`
The term "SAGA" does not stand for an acronym but is borrowed from storytelling, symbolizing a sequence of events that make up a complete narrative.
- Don’t do transactions in microservices → fix granularity instead!
- If not possible other way ->  saga pattern 
    - Sequence of local transactions, where each local transaction updates DB and publishes a message to trigger next local transaction
    - Local transaction fails → saga executes compensating transactions to undo changes made by preceding local transactions
- Typical implementation
    - Each request enters a pending state until the mediator indicates overall success
    - Complex design for asynchronous requests 
    - Significant coordination traffic at network level

![](image/Pasted%20image%2020250112221844.png)

