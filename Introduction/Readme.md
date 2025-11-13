# What is System Design

System design is the process of defining the stucture and the elements of a sofware application, and the relation between them. It focuses on the high-level design of a software system, including the architecture and components.

Components examples : **API Gateway, Database, Web app, Microservices, Cache …**

# Scalability vs Performence

**Performance** is the speed at which a system processes a **single** operation (e.g., latency, throughput).

**Scalability** is a system's ability to **maintain** that performance under **increased** load by adding resources.

- **Latency** is the amount of time it takes for a system to responde to a single request
- **Throughput** is the amount of request a system can handle at the same time (req/sec)

Generally, you should aim for maximal throughput with acceptable latency

# CAP theorem

Consestency, Availability and Partition Tolerence, In any distributed system, you can **only** have 2 of those 3 concept, You must choose between **CP  or AP**

- **Consistency :** Every node sees the same data at the same time
- Availability : Every request gets a response, even if it's not the latest data.
- **Partition Tolerance** : The system continues to operate despite arbitrary partitioning due to network failures
- CP : The system stops responding (becomes unavailable) during a network failure to ensure all nodes have the same, correct data.
- AP : The system keeps responding during a network failure, even if some nodes return stale or different data.

## Consistency Patterns:

In a distrubuted system, We need to think about data consistency accros different nodes, and its impact on latency and availability. There are three patterns:

1. **Strong Consistency** : The updates on data is replicated in a synchronous way, **it will be immediately visible to any subsequent read operations.** But the tradeoff is that the system will not be **highly available and has high latency (Financial Systems)**
2. **Weak Consistency :**  The updates on the data may or may not be reflected on the recent reads, **but it also allows for high availability and low latency (Games)**
3. **Eventual Consistency : Updates will** be eventually visible to any subsequent read operations. This can lead to inconsistencies between different versions of the data, but it also allows for a high level of availability and low latency (Social media)

## Availability Patterns

Those are some architectural approches to insure that a system is availabile, accessible and operationnal even in the face of failures.

**Fail-Over Pattern:** 

there's a primary component handling the workload, and a secondary component waiting in the wings and If it goes down, the secondary component is automatically activated to ensure uninterrupted service.

- Active - Active : Both systems are active and handles requests
- Active - Passive : The main handles all requests, if it goes down the passive one takes on directly

**Replication Pattern : (storing multiple copies of data across different locations)**

- Master - Master : both systems can serve as both the primary and secondary components. They can handle both read and write operations
- Master - slave : The master handles everything, while the slave only save and replicates data from the master.