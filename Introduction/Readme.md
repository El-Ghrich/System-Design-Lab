# What is System Design

System design is the process of defining the stucture and the elements of a sofware application, and the relation between them. It focuses on the high-level design of a software system, including the architecture and components.

Components examples : **API Gateway, Database, Web app, Microservices, Cache …**

## Scalability vs Performence

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

# Background Jobs

Background Jobs are operations that run asynchronously, without blocking the user or requiring their interaction.

Background jobs can be initiated in several different ways. They fall into one of the following categories:

- [**Event-driven triggers**](https://learn.microsoft.com/en-us/azure/architecture/best-practices/background-jobs#event-driven-triggers). The task is started in response to an event, typically an action taken by a user or a step in a workflow.
- [**Schedule-driven triggers**](https://learn.microsoft.com/en-us/azure/architecture/best-practices/background-jobs#schedule-driven-triggers). The task is invoked on a schedule based on a timer. This might be a recurring schedule or a one-off invocation that is specified for a later time.

### [Even-driven Triggers](https://learn.microsoft.com/en-us/azure/architecture/best-practices/background-jobs#event-driven-triggers)

Examples of using event-driven triggers include:

- The UI or another job places a message in a **queue.** The background task listens on this queue and detects the arrival of a new message. It reads the message and uses the data in it as the input to the background job. This pattern is known as [asynchronous message-based communication](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/architect-microservice-container-applications/asynchronous-message-based-communication).
- The UI or another job makes a request to an endpoin
- …

Typical examples of tasks that are suited to event-driven invocation include image processing, workflows, sending information to remote services, sending email messages, and provisioning new users in multitenant applications.

### [Schedule-driven Triggers](https://learn.microsoft.com/en-us/azure/architecture/best-practices/background-jobs#schedule-driven-triggers)

Examples of using event-driven triggers include:

- A timer that is running locally within the application
- A timer that is running in a different application, such as Azure Logic Apps, sends a request to an API or web service on a regular basis
- …

Examples of tasks that are suitable for this kind of triggers :

- Updating related-products lists for users based on their recent behavior
- Updating indexes or generating accumulated results
- data consistency checks
- …

### **Returning Results**

Background jobs execute asynchronously in a separate process, or even in a separate location, from the UI or the process that invoked the background task. Ideally, background tasks are "fire and forget" operations, and their execution progress has no impact on the UI or the calling process. This means that the calling process does not wait for completion of the tasks. Therefore, it cannot automatically detect when the task ends.

# DNS

A Domain Name System (DNS) translates a domain name such as [www.example.com](http://www.example.com/) to an IP address.

DNS is hierarchical, with a few authoritative servers at the top level. Your router or ISP (INTERNET SERVICE PROVIDER) provides information about which DNS server(s) to contact when doing a lookup. Lower level DNS servers cache mappings, which could become stale due to DNS propagation delays. DNS results can also be cached by your browser or OS for a certain period of time, determined by the [time to live (TTL)](https://en.wikipedia.org/wiki/Time_to_live).

- **NS record (name server)** - Specifies the DNS servers for your domain/subdomain.
- **MX record (mail exchange)** - Specifies the mail servers for accepting messages.
- **A record (address)** - Points a name to an IP address.
- **CNAME (canonical)** - Points a name to another name or `CNAME` (example.com to [www.example.com](http://www.example.com/)) or to an `A` record.