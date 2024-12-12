
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
    - Resource Cluster: active active cluster groups incorporated to balance workloads across different members of the cluster
    - Replication: generate cloud service in support of load balancing requirements

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


## 1.2 Cloud Platforms: IaaS vs. PaaS

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

## 1.3 Azure 


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


## 1.4 AWS

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



