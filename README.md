# System_Design
Resource - (https://roadmap.sh/system-design?fl=1)

## What is System Design?

System design is the process of defining the elements of a system, as well as their interactions and relationships, in order to satisfy a set of specified requirements.

It involves taking a problem statement, breaking it down into smaller components and designing each component to work together effectively to achieve the overall goal of the system. This process typically includes analyzing the current system (if any) and determining any deficiencies, creating a detailed plan for the new system, and testing the design to ensure that it meets the requirements. It is an iterative process that may involve multiple rounds of design, testing, and refinement.

In software engineering, system design is a phase in the software development process that focuses on the high-level design of a software system, including the architecture and components.

It is also one of the important aspects of the interview process for software engineers. Most of the companies have a dedicated system design interview round, where they ask the candidates to design a system for a given problem statement. The candidates are expected to come up with a detailed design of the system, including the architecture, components, and their interactions. They are also expected to discuss the trade-offs involved in their design and the alternatives that they considered.

## How To: System Design?

There are several steps that can be taken when approaching a system design:

* **Understand the problem**: Gather information about the problem you are trying to solve and the requirements of the system. Identify the users and their needs, as well as any constraints or limitations of the system.
* **Identify the scope of the system**: Define the boundaries of the system, including what the system will do and what it will not do.
* **Research and analyze existing systems**: Look at similar systems that have been built in the past and identify what worked well and what didn't. Use this information to inform your design decisions.
* **Create a high-level design**: Outline the main components of the system and how they will interact with each other. This can include a rough diagram of the system's architecture, or a flowchart outlining the process the system will follow.
* **Refine the design**: As you work on the details of the design, iterate and refine it until you have a complete and detailed design that meets all the requirements.
* **Document the design**: Create detailed documentation of your design for future reference and maintenance.
* **Continuously monitor and improve the system**: The system design is not a one-time process, it needs to be continuously monitored and improved to meet the changing requirements.

#### Additional Resources
[How to approach System Design?](https://github.com/donnemartin/system-design-primer#how-to-approach-a-system-design-interview-question)

## Performance vs Scalability

A service is scalable if it results in increased performance in a manner proportional to resources added. Generally, increasing performance means serving more units of work, but it can also be to handle larger units of work, such as when datasets grow.

Another way to look at performance vs scalability:

* If you have a performance problem, your system is slow for a single user.
* If you have a scalability problem, your system is fast for a single user but slow under heavy load.

#### Additional Resources
* [Scalability, Availability & Stability Patterns](https://www.slideshare.net/slideshow/scalability-availability-stability-patterns/4062682)
* [A Word on Scalability](https://www.allthingsdistributed.com/2006/03/a_word_on_scalability.html)
* [Performance vs Scalability](https://blog.professorbeekums.com/performance-vs-scalability/)

## Latency vs Throughput

Latency and throughput are two important measures of a system's performance. Latency refers to the amount of time it takes for a system to respond to a request. Throughput refers to the number of requests that a system can handle at the same time.

Generally, you should aim for maximal throughput with acceptable latency.

#### Additional Resources
* [System Design: Latency vs Throughput](https://cs.fyi/guide/latency-vs-throughput) 
* [Understanding Latency versus Throughput](https://community.cadence.com/cadence_blogs_8/b/fv/posts/understanding-latency-vs-throughput)

## Availability vs Consistency

Availability refers to the ability of a system to provide its services to clients even in the presence of failures. This is often measured in terms of the percentage of time that the system is up and running, also known as its uptime.

Consistency, on the other hand, refers to the property that all clients see the same data at the same time. This is important for maintaining the integrity of the data stored in the system.

In distributed systems, it is often a trade-off between availability and consistency. Systems that prioritize high availability may sacrifice consistency, while systems that prioritize consistency may sacrifice availability. Different distributed systems use different approaches to balance the trade-off between availability and consistency, such as using replication or consensus algorithms.

## CAP Theorem

According to CAP theorem, in a distributed system, you can only support two of the following guarantees:

* **Consistency** - Every read receives the most recent write or an error
* **Availability** - Every request receives a response, without guarantee that it contains the most recent version of the information
* **Partition Tolerance** - The system continues to operate despite arbitrary partitioning due to network failures

Networks aren't reliable, so you'll need to support partition tolerance. You'll need to make a software tradeoff between consistency and availability

### CP - consistency and partition tolerance
Waiting for a response from the partitioned node might result in a timeout error. CP is a good choice if your business needs require atomic reads and writes.

### AP - availability and partition tolerance
Responses return the most readily available version of the data available on any node, which might not be the latest. Writes might take some time to propagate when the partition is resolved.

AP is a good choice if the business needs to allow for [eventual consistency](https://github.com/donnemartin/system-design-primer#eventual-consistency) or when the system needs to continue working despite external errors.

#### Additional Resources
* [CAP FAQ](https://github.com/henryr/cap-faq)
* [CAP Theorem: Rivisited](https://robertgreiner.com/cap-theorem-revisited/)
* [A plain english introduction to CAP Theorem](http://ksat.me/a-plain-english-introduction-to-cap-theorem)

## Consistency Patterns

Consistency patterns refer to the ways in which data is stored and managed in a distributed system, and how that data is made available to users and applications. There are three main types of consistency patterns:

* Strong consistency - After an update is made to the data, it will be immediately visible to any subsequent read operations. The data is replicated in a synchronous manner, ensuring that all copies of the data are updated at the same time.
* Weak consistency - After an update is made to the data, it is not guaranteed that any subsequent read operation will immediately reflect the changes made. The read may or may not see the recent write.
* Eventual Consistency - Eventual consistency is a form of Weak Consistency. After an update is made to the data, it will be eventually visible to any subsequent read operations. The data is replicated in an asynchronous manner, ensuring that all copies of the data are eventually updated.

Each of these patterns has its own advantages and disadvantages, and the choice of which pattern to use will depend on the specific requirements of the application or system.

#### Additional Resources
* [Consistency Patterns in Distributed Systems](https://cs.fyi/guide/consistency-patterns-week-strong-eventual)

## Availability Patterns

Availability is measured as a percentage of uptime, and defines the proportion of time that a system is functional and working. Availability is affected by system errors, infrastructure problems, malicious attacks, and system load. Cloud applications typically provide users with a service level agreement (SLA), which means that applications must be designed and implemented to maximize availability.

#### Additional Resources
* [Availability Patterns](https://learn.microsoft.com/en-us/azure/well-architected/reliability/design-patterns#availability)

## Fail-Over

Failover is an availability pattern that is used to ensure that a system can continue to function in the event of a failure. It involves having a backup component or system that can take over in the event of a failure.

In a failover system, there is a primary component that is responsible for handling requests, and a secondary (or backup) component that is on standby. The primary component is monitored for failures, and if it fails, the secondary component is activated to take over its duties. This allows the system to continue functioning with minimal disruption.

Failover can be implemented in various ways, such as active-passive, active-active, and hot-standby.

### Active-Passive

With active-passive fail-over, heartbeats are sent between the active and the passive server on standby. If the heartbeat is interrupted, the passive server takes over the active's IP address and resumes service.

The length of downtime is determined by whether the passive server is already running in 'hot' standby or whether it needs to start up from 'cold' standby. Only the active server handles traffic.

Active-passive failover can also be referred to as master-slave failover.

### Active-Active

In active-active, both servers are managing traffic, spreading the load between them.

If the servers are public-facing, the DNS would need to know about the public IPs of both servers. If the servers are internal-facing, application logic would need to know about both servers.

Active-active failover can also be referred to as master-master failover.

### Disadvantage of failover
* Fail-over adds more hardware and additional complexity.
* There is a potential for loss of data if the active system fails before any newly written data can be replicated to the passive.

#### Additional Resources
* [Fail Over Pattern - High Availability](https://www.filecloud.com/blog/2015/12/architectural-patterns-for-high-availability/)


## Replication

Replication is an availability pattern that involves having multiple copies of the same data stored in different locations. In the event of a failure, the data can be retrieved from a different location. There are two main types of replication: Master-Master replication and Master-Slave replication.

* Master-Master replication: In this type of replication, multiple servers are configured as "masters," and each one can accept read and write operations. This allows for high availability and allows any of the servers to take over if one of them fails. However, this type of replication can lead to conflicts if multiple servers update the same data at the same time, so some conflict resolution mechanism is needed to handle this.

* Master-Slave replication: In this type of replication, one server is designated as the "master" and handles all write operations, while multiple "slave" servers handle read operations. If the master fails, one of the slaves can be promoted to take its place. This type of replication is simpler to set up and maintain compared to Master-Master replication.

#### Additional Resources
* [Replication: Availability Pattern](https://github.com/donnemartin/system-design-primer#replication)

## Availability in Numbers

Availability is often quantified by uptime (or downtime) as a percentage of time the service is available. Availability is generally measured in number of 9s--a service with 99.99% availability is described as having four 9s.

### 99.9% Availability - Three 9s:

| Duration          | Acceptable       |
| :---------------- | :-----------     | 
| Downtime per year |  8h 41min 38s    |
| Downtime per month|  43min 28 s      |    
| Downtime per week |  10min 4.8s      |
| Downtime per day  |  1min 26s        | 


### 99.99% Availability - Four 9s:

| Duration          | Acceptable     |
| :---------------- | :-----------   | 
| Downtime per year |  52min 9.8s    |
| Downtime per month|  4min 21s      |    
| Downtime per week |  1min 0.5s     |
| Downtime per day  |  8.6s          | 

### Availability in paralle vs in sequence

If a service consists of multiple components prone to failure, the service's overall availability depends on whether the components are in sequence or in parallel.

#### In sequence

Overall availability decreases when two components with availability < 100% are in sequence:

    Availability (Total) = Availability (Foo) * Availability (Bar)

If both Foo and Bar each had 99.9% availability, their total availability in sequence would be 99.8%.

#### In parallel

Overall availability increases when two components with availability < 100% are in parallel:

    Availability (Total) = 1 - (1 - Availability (Foo)) * (1 - Availability (Bar))

If both Foo and Bar each had 99.9% availability, their total availability in parallel would be 99.9999%.

#### Additional Resources
* [Availability in System Design](https://www.enjoyalgorithms.com/blog/availability-system-design-concept)

## Background Jobs

Background jobs in system design refer to tasks that are executed in the background, independently of the main execution flow of the system. These tasks are typically initiated by the system itself, rather than by a user or another external agent.

Background jobs can be used for a variety of purposes, such as:
* Performing maintenance tasks: such as cleaning up old data, generating reports, or backing up the database.
* Processing large volumes of data: such as data import, data export, or data transformation.
* Sending notifications or messages: such as sending email notifications or push notifications to users.
* Performing long-running computations: such as machine learning or data analysis.

### Event Driven

Event-driven invocation uses a trigger to start the background task. Examples of using event-driven triggers include:

* The UI or another job places a message in a queue. The message contains data about an action that has taken place, such as the user placing an order. The background task listens on this queue and detects the arrival of a new message. It reads the message and uses the data in it as the input to the background job. This pattern is known as asynchronous message-based communication.
* The UI or another job saves or updates a value in storage. The background task monitors the storage and detects changes. It reads the data and uses it as the input to the background job.
* The UI or another job makes a request to an endpoint, such as an HTTPS URI, or an API that is exposed as a web service. It passes the data that is required to complete the background task as part of the request. The endpoint or web service invokes the background task, which uses the data as its input.

### Schedule Driven

Schedule-driven invocation uses a timer to start the background task. Examples of using schedule-driven triggers include:

* A timer that is running locally within the application or as part of the application's operating system invokes a background task on a regular basis.
* A timer that is running in a different application, such as Azure Logic Apps, sends a request to an API or web service on a regular basis. The API or web service invokes the background task.
* A separate process or application starts a timer that causes the background task to be invoked once after a specified time delay, or at a specific time.

Typical examples of tasks that are suited to schedule-driven invocation include batch-processing routines (such as updating related-products lists for users based on their recent behavior), routine data processing tasks (such as updating indexes or generating accumulated results), data analysis for daily reports, data retention cleanup, and data consistency checks.

### Returning Results

Background jobs execute asynchronously in a separate process, or even in a separate location, from the UI or the process that invoked the background task. Ideally, background tasks are "fire and forget" operations, and their execution progress has no impact on the UI or the calling process. This means that the calling process does not wait for completion of the tasks. Therefore, it cannot automatically detect when the task ends.

#### Additional Resources
* [Background Jobs - Best Practices](https://learn.microsoft.com/en-us/azure/architecture/best-practices/background-jobs)


## Domain Name System (DNS)

A Domain Name System (DNS) translates a domain name such as www.example.com to an IP address.

DNS is hierarchical, with a few authoritative servers at the top level. Your router or ISP provides information about which DNS server(s) to contact when doing a lookup. Lower level DNS servers cache mappings, which could become stale due to DNS propagation delays. DNS results can also be cached by your browser or OS for a certain period of time, determined by the time to live (TTL).

* NS record (name server) - Specifies the DNS servers for your domain/subdomain.
* MX record (mail exchange) - Specifies the mail servers for accepting messages.
* A record (address) - Points a name to an IP address.
* CNAME (canonical) - Points a name to another name or CNAME (example.com to www.example.com) or to an A record.

Services such as CloudFlare and Route53 provide managed DNS services. Some DNS services can route traffic through various methods:

* Weighted Round Robin
  * Prevent traffic from going to servers under maintenance
  * Balance between varying cluster sizes
  * A/B testing
* Latency Based
* Geolocation Based

#### Additional Resources
* [Comparing Load Balancing Algorithms](https://www.jscape.com/blog/load-balancing-algorithms)
* [Choosing a routing policy](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html#routing-policy-latency)
* [What is DNS? | How DNS works](https://www.cloudflare.com/learning/dns/what-is-dns/)

## Content Delivery Networks (CDN)

A content delivery network (CDN) is a globally distributed network of proxy servers, serving content from locations closer to the user. Generally, static files such as HTML/CSS/JS, photos, and videos are served from CDN, although some CDNs such as Amazon's CloudFront support dynamic content. The site's DNS resolution will tell clients which server to contact.

Serving content from CDNs can significantly improve performance in two ways:
* Users receive content from data centers close to them
* Your servers do not have to serve requests that the CDN fulfills

### Pull CDNs

Pull CDNs grab new content from your server when the first user requests the content. You leave the content on your server and rewrite URLs to point to the CDN. This results in a slower request until the content is cached on the CDN.

A time-to-live (TTL) determines how long content is cached. Pull CDNs minimize storage space on the CDN, but can create redundant traffic if files expire and are pulled before they have actually changed. Sites with heavy traffic work well with pull CDNs, as traffic is spread out more evenly with only recently-requested content remaining on the CDN.

### Push CDNs

Push CDNs receive new content whenever changes occur on your server. You take full responsibility for providing content, uploading directly to the CDN and rewriting URLs to point to the CDN. You can configure when content expires and when it is updated. Content is uploaded only when it is new or changed, minimizing traffic, but maximizing storage.

Sites with a small amount of traffic or sites with content that isn't often updated work well with push CDNs. Content is placed on the CDNs once, instead of being re-pulled at regular intervals.

#### Additional Resources
* [The Differences Between Push And Pull CDNs](https://www.travelblogadvice.com/technical/the-differences-between-push-and-pull-cdns/)
* [Brief about Content delivery network](https://en.wikipedia.org/wiki/Content_delivery_network)

## Load Balancers

Load balancers distribute incoming client requests to computing resources such as application servers and databases. In each case, the load balancer returns the response from the computing resource to the appropriate client.

Load balancers are effective at:
* Preventing requests from going to unhealthy servers
* Preventing overloading resources
* Helping to eliminate a single point of failure

Load balancers can be implemented with hardware (expensive) or with software such as HAProxy. Additional benefits include:
* **SSL termination** - Decrypt incoming requests and encrypt server responses so backend servers do not have to perform these potentially expensive operations
  * Removes the need to install X.509 certificates on each server
* **Session persistence** - Issue cookies and route a specific client's requests to same instance if the web apps do not keep track of sessions

### Disadvantages of load balancer
* The load balancer can become a performance bottleneck if it does not have enough resources or if it is not configured properly.
* Introducing a load balancer to help eliminate a single point of failure results in increased complexity.
* A single load balancer is a single point of failure, configuring multiple load balancers further increases complexity.

### Load Balancer vs Reverse Proxy
* Deploying a load balancer is useful when you have multiple servers. Often, load balancers route traffic to a set of servers serving the same function.
* Reverse proxies can be useful even with just one web server or application server, opening up the benefits described in the previous section.
* Solutions such as NGINX and HAProxy can support both layer 7 reverse proxying and load balancing

### Disadvantages of Reverse Proxy
* Introducing a reverse proxy results in increased complexity.
* A single reverse proxy is a single point of failure, configuring multiple reverse proxies (ie a failover) further increases complexity.

### Load Balancing Algorithms

A load balancer is a software or hardware device that keeps any one server from becoming overloaded. A load balancing algorithm is the logic that a load balancer uses to distribute network traffic between servers (an algorithm is a set of predefined rules).

There are two primary approaches to load balancing. Dynamic load balancing uses algorithms that take into account the current state of each server and distribute traffic accordingly. Static load balancing distributes traffic without making these adjustments. Some static algorithms send an equal amount of traffic to each server in a group, either in a specified order or at random.

### Layer 7 Load Balancing

Layer 7 load balancers look at the application layer to decide how to distribute requests. This can involve contents of the header, message, and cookies. Layer 7 load balancers terminate network traffic, reads the message, makes a load-balancing decision, then opens a connection to the selected server. For example, a layer 7 load balancer can direct video traffic to servers that host videos while directing more sensitive user billing traffic to security-hardened servers.

At the cost of flexibility, layer 4 load balancing requires less time and computing resources than Layer 7, although the performance impact can be minimal on modern commodity hardware.

### Layer 4 Load Balancing

Layer 4 load balancers look at info at the transport layer to decide how to distribute requests. Generally, this involves the source, destination IP addresses, and ports in the header, but not the contents of the packet. Layer 4 load balancers forward network packets to and from the upstream server, performing Network Address Translation (NAT).

### Horizontal Scaling

Load balancers can also help with horizontal scaling, improving performance and availability. Scaling out using commodity machines is more cost efficient and results in higher availability than scaling up a single server on more expensive hardware, called Vertical Scaling. It is also easier to hire for talent working on commodity hardware than it is for specialized enterprise systems.

### Disadvantages of horizontal scaling

* Scaling horizontally introduces complexity and involves cloning servers
  * Servers should be stateless: they should not contain any user-related data like sessions or profile pictures
  * Sessions can be stored in a centralized data store such as a database (SQL, NoSQL) or a persistent cache (Redis, Memcached)
* Downstream servers such as caches and databases need to handle more simultaneous connections as upstream servers scale out.

#### Additional Resources
* [Scalability for Dummies](https://cs.fyi/guide/scalability-for-dummies)
* [NGINX Architecture](https://blog.nginx.org/blog/inside-nginx-how-we-designed-for-performance-scale)
* [HAProxy Architecture Guide](https://www.haproxy.org/download/1.2/doc/architecture.txt)
* [What is a Reverse Proxy?](https://www.f5.com/glossary/reverse-proxy)
* [Types of load balancing algorithms](https://www.cloudflare.com/learning/performance/types-of-load-balancing-algorithms/)

## Application Layer

Separating out the web layer from the application layer (also known as platform layer) allows you to scale and configure both layers independently. Adding a new API results in adding application servers without necessarily adding additional web servers. The single responsibility principle advocates for small and autonomous services that work together. Small teams with small services can plan more aggressively for rapid growth.

![application layer architecture](./files/images/application_layer.png "Application Layer Architecture")

### Disadvantages
* Adding an application layer with loosely coupled services requires a different approach from an architectural, operations, and process viewpoint (vs a monolithic system).
* Microservices can add complexity in terms of deployments and operations.

### Microservices

Related to the "Application Layer" discussion are microservices, which can be described as a suite of independently deployable, small, modular services. Each service runs a unique process and communicates through a well-defined, lightweight mechanism to serve a business goal. 1

Pinterest, for example, could have the following microservices: user profile, follower, feed, search, photo upload, etc.

#### Additional Resources
* [Introduction to architecting systems for scale.](https://lethain.com/introduction-to-architecting-systems-for-scale/#platform_layer) -- ***IMPORTANT***
* [Introduction to Microsevices](https://aws.amazon.com/microservices/)

## Databases

Picking the right database for a system is an important decision, as it can have a significant impact on the performance, scalability, and overall success of the system. Some of the key reasons why it's important to pick the right database include:

* **Performance:** Different databases have different performance characteristics, and choosing the wrong one can lead to poor performance and slow response times.
* **Scalability:** As the system grows and the volume of data increases, the database needs to be able to scale accordingly. Some databases are better suited for handling large amounts of data than others.
* **Data Modeling:** Different databases have different data modeling capabilities and choosing the right one can help to keep the data consistent and organized.
* **Data Integrity:** Different databases have different capabilities for maintaining data integrity, such as enforcing constraints, and can have different levels of data security.
* **Support and maintenance:** Some databases have more active communities and better documentation, making it easier to find help and resources.

### SQL vs noSQL

SQL databases, such as MySQL and PostgreSQL, are best suited for structured, relational data and use a fixed schema. They provide robust ACID (Atomicity, Consistency, Isolation, Durability) transactions and support complex queries and joins.

NoSQL databases, such as MongoDB and Cassandra, are best suited for unstructured, non-relational data and use a flexible schema. They provide high scalability and performance for large amounts of data and are often used in big data and real-time web applications.

The choice between SQL and NoSQL depends on the specific use case and requirements of the project. If you need to store and query structured data with complex relationships, an SQL database is likely a better choice. If you need to store and query large amounts of unstructured data with high scalability and performance, a NoSQL database may be a better choice.

### Relational Database Management System (RDBMS)

#### Replication
Replication is the process of copying data from one database to another. Replication is used to increase availability and scalability of databases. There are two types of replication: master-slave and master-master.

* **Master-slave Replication:** The master serves reads and writes, replicating writes to one or more slaves, which serve only reads. Slaves can also replicate to additional slaves in a tree-like fashion. If the master goes offline, the system can continue to operate in read-only mode until a slave is promoted to a master or a new master is provisioned.

* **Master-master Replication:** Both masters serve reads and writes and coordinate with each other on writes. If either master goes down, the system can continue to operate with both reads and writes.

#### Sharding
Sharding distributes data across different databases such that each database can only manage a subset of the data. Taking a users database as an example, as the number of users increases, more shards are added to the cluster.

Similar to the advantages of federation, sharding results in less read and write traffic, less replication, and more cache hits. Index size is also reduced, which generally improves performance with faster queries. If one shard goes down, the other shards are still operational, although you'll want to add some form of replication to avoid data loss. Like federation, there is no single central master serializing writes, allowing you to write in parallel with increased throughput.

#### Federation
Federation (or functional partitioning) splits up databases by function. For example, instead of a single, monolithic database, you could have three databases: forums, users, and products, resulting in less read and write traffic to each database and therefore less replication lag. Smaller databases result in more data that can fit in memory, which in turn results in more cache hits due to improved cache locality. With no single central master serializing writes you can write in parallel, increasing throughput.

#### Denormalization
Denormalization attempts to improve read performance at the expense of some write performance. Redundant copies of the data are written in multiple tables to avoid expensive joins. Some RDBMS such as PostgreSQL and Oracle support materialized views which handle the work of storing redundant information and keeping redundant copies consistent.

Once data becomes distributed with techniques such as federation and sharding, managing joins across data centers further increases complexity. Denormalization might circumvent the need for such complex joins.

#### SQL Tuning
SQL tuning is the attempt to diagnose and repair SQL statements that fail to meet a performance standard. It's important to benchmark and profile to simulate and uncover bottlenecks.

* Benchmark - Simulate high-load situations with tools such as ab.
* Profile - Enable tools such as the slow query log to help track performance issues.

### NoSQL

#### Key-Value Store
A key-value store generally allows for O(1) reads and writes and is often backed by memory or SSD. Data stores can maintain keys in lexicographic order, allowing efficient retrieval of key ranges. Key-value stores can allow for storing of metadata with a value.

Key-value stores provide high performance and are often used for simple data models or for rapidly-changing data, such as an in-memory cache layer. Since they offer only a limited set of operations, complexity is shifted to the application layer if additional operations are needed.

#### Document Store
document store is centered around documents (XML, JSON, binary, etc), where a document stores all information for a given object. Document stores provide APIs or a query language to query based on the internal structure of the document itself. Note, many key-value stores include features for working with a value's metadata, blurring the lines between these two storage types.

Based on the underlying implementation, documents are organized by collections, tags, metadata, or directories. Although documents can be organized or grouped together, documents may have fields that are completely different from each other.

#### Wide Column Store
A wide column store's basic unit of data is a column (name/value pair). A column can be grouped in column families (analogous to a SQL table). Super column families further group column families. You can access each column independently with a row key, and columns with the same row key form a row. Each value contains a timestamp for versioning and for conflict resolution.

Google introduced Bigtable as the first wide column store, which influenced the open-source HBase often-used in the Hadoop ecosystem, and Cassandra from Facebook. Stores such as BigTable, HBase, and Cassandra maintain keys in lexicographic order, allowing efficient retrieval of selective key ranges.

#### Graph 
In a graph database, each node is a record and each arc is a relationship between two nodes. Graph databases are optimized to represent complex relationships with many foreign keys or many-to-many relationships.

Graphs databases offer high performance for data models with complex relationships, such as a social network. They are relatively new and are not yet widely-used; it might be more difficult to find development tools and resources. Many graphs can only be accessed with REST APIs.



#### Additional Resources
* [Scaling up to your first 10 million users](https://www.youtube.com/watch?v=kKjm4ehYiMs) -- ***Video***
* [SQL vs noSQL: The Differences](https://www.sitepoint.com/sql-vs-nosql-differences/)
* [SQL vs noSQL Databases: What's the difference](https://www.ibm.com/think/topics/sql-vs-nosql)
* [noSQL vs SQL Databases](https://www.mongodb.com/resources/basics/databases/nosql-explained/nosql-vs-sql)
* Shrading
  * [The Coming of the Shard](https://highscalability.com/an-unorthodox-approach-to-database-design-the-coming-of-the/)
* SQL Tuning
  * [Introduction to SQL Tuning](https://docs.oracle.com/en/database/oracle/oracle-database/23/tgsql/introduction-to-sql-tuning.html)
* [Introduction to NoSQL](https://www.youtube.com/watch?v=qI_g07C_Q5I) -- ***Video***

## Caching

Caching is the process of storing frequently accessed data in a temporary storage location, called a cache, in order to quickly retrieve it without the need to query the original data source. This can improve the performance of an application by reducing the number of times a data source must be accessed.

There are several caching strategies:
* Refresh Ahead
* Write-Behind
* Write-through
* Cache Aside

Also, you can have the cache in several places, examples include:
* Client Caching
* CDN Caching
* Web Server Caching
* Database Caching
* Application Caching

#### Additional Resources
* [Cache Strategies](https://medium.com/@mmoshikoo/cache-strategies-996e91c80303)