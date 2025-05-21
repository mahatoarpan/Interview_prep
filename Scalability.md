# Chapter 1: Scalability

### Scalability Lecture at Harvard - [Youtube video](https://www.youtube.com/watch?v=-W9F__D3oY4)

#### Topics covered:
1. Vertical scaling - [Blog](https://hypermode.com/blog/vertical-scale)
2. Horizontal scaling
3. Horizontal v/s Vertical Scaling - [Blog](https://dev.to/somadevtoo/horizontal-scaling-vs-vertical-scaling-in-system-design-3n09)
4. Load Balancing - [Blog](https://aws.amazon.com/what-is/load-balancing/)
5. Sticky Sessions (Session Persistence) - [Blog](https://medium.com/@aditimishra_541/sticky-sessions-an-in-depth-overview-4ec19bd7e22a)
6. Caching - [Blog](https://medium.com/@abhishekranjandev/caching-in-system-design-an-in-depth-exploration-b51e2c2e4dbd)
7. Database Replication - [Blog](https://medium.com/@pulkitent/system-design-database-replication-part-1-3dcf4a300db1)
8. Database Partition - [Blog](https://medium.com/@pulkitent/system-design-database-sharding-part-1-e1779e84da7a)


### Scalability for Dummies - [Blog](https://web.archive.org/web/20221030091841/http://www.lecloud.net/tagged/scalability/chrono)

#### Topics Covered:
1. Clones
2. Databases
3. Caches
4. Asynchronism


### Performance vs scalability

A service is scalable if it results in increased performance in a manner proportional to resources added. Generally, increasing performance means serving more units of work, but it can also be to handle larger units of work, such as when datasets grow.

Another way to look at performance vs scalability:

* If you have a performance problem, your system is slow for a single user.
* If you have a scalability problem, your system is fast for a single user but slow under heavy load.

A word on scalability - [Blog](https://www.allthingsdistributed.com/2006/03/a_word_on_scalability.html)

Scalability, availability and stability pattenrs - [PPT](https://www.slideshare.net/slideshow/scalability-availability-stability-patterns/4062682)

### Laterncy vs throughput

Latency is the time to perform some action or to produce some result.

Throughput is the number of such actions or results per unit of time.

Generally, you should aim for maximal throughput with acceptable latency.

Understanding Latency versus Throughput - [Blog](https://community.cadence.com/cadence_blogs_8/b/fv/posts/understanding-latency-vs-throughput)

### Availability vs consistency

In a distributed computer system, you can only support two of the following guarantees:

* Consistency - Every read receives the most recent write or an error
* Availability - Every request receives a response, without guarantee that it contains the most recent version of the information
* Partition Tolerance - The system continues to operate despite arbitrary partitioning due to network failures

*Networks aren't reliable, so you'll need to support partition tolerance. You'll need to make a software tradeoff between consistency and availability.*

#### CP - consistency and partition tolerance
Waiting for a response from the partitioned node might result in a timeout error. CP is a good choice if your business needs require atomic reads and writes.

#### AP - availability and partition tolerance
Responses return the most readily available version of the data available on any node, which might not be the latest. Writes might take some time to propagate when the partition is resolved.

AP is a good choice if the business needs to allow for eventual consistency or when the system needs to continue working despite external errors.

CAP Theorem - [Youtube](https://www.youtube.com/watch?v=k-Yaq8AHlFA)

CAP Theorem: Revisited - [Blog](https://robertgreiner.com/cap-theorem-revisited/)

CAP FAQ - [Github](https://github.com/henryr/cap-faq)

### Consistency Patterns

With multiple copies of the same data, we are faced with options on how to synchronize them so clients have a consistent view of the data. Recall the definition of consistency from the CAP theorem - Every read receives the most recent write or an error.

#### Weak Consistency

    After a write, reads may or may not see it. A best effort approach is taken.

    This approach is seen in systems such as memcached. Weak consistency works well in real time use cases such as VoIP, video chat, and realtime multiplayer games. For example, if you are on a phone call and lose reception for a few seconds, when you regain connection you do not hear what was spoken during connection loss.

#### Eventual Consistency

    After a write, reads will eventually see it (typically within milliseconds). Data is replicated asynchronously.

    This approach is seen in systems such as DNS and email. Eventual consistency works well in highly available systems.

#### Strong Consistency

    After a write, reads will see it. Data is replicated synchronously.

    This approach is seen in file systems and RDBMSes. Strong consistency works well in systems that need transactions.

Transactions across data centers - [Youtube](https://www.youtube.com/watch?v=srOgpXECblk)

### Availability Patterns

There are two complementary patterns to support high availability: fail-over and replication.

#### Fail-over

#### 1. Active-passive
    With active-passive fail-over, heartbeats are sent between the active and the passive server on standby. If the heartbeat is interrupted, the passive server takes over the active's IP address and resumes service.

    The length of downtime is determined by whether the passive server is already running in 'hot' standby or whether it needs to start up from 'cold' standby. Only the active server handles traffic.

    Active-passive failover can also be referred to as master-slave failover.

#### 2. Active-active
    In active-active, both servers are managing traffic, spreading the load between them.

    If the servers are public-facing, the DNS would need to know about the public IPs of both servers. If the servers are internal-facing, application logic would need to know about both servers.

    Active-active failover can also be referred to as master-master failover.

#### Disadvantages of fail-over:
1. Fail-over adds more hardware and additional complexity.
2. There is a potential for loss of data if the active system fails before any newly written data can be replicated to the passive.

#### Availability in numbers

Availability is often quantified by uptime (or downtime) as a percentage of time the service is available. Availability is generally measured in number of 9s--a service with 99.99% availability is described as having four 9s.

**99.9% availability - three 9s**

| Duration | Accepted Downtime |
| --- | --- |
| Downtime per year | 8h 45min 57s |
| Downtime per month | 43m 49.7s |
| Downtime per week | 10m 4.8s |
| Downtime per day | 1m 26.4s |


**99.99% availability - four 9s**

| Duration | Accepted Downtime |
| --- | --- |
| Downtime per year | 52min 35.7s |
| Downtime per month | 4m 23s |
| Downtime per week | 1m 5s |
| Downtime per day | 8.6s |


#### Availability in parallel vs in sequence 

If a service consists of multiple components prone to failure, the service's overall availability depends on whether the components are in sequence or in parallel.

#### In sequence
Overall availability decreases when two components with availability < 100% are in  sequence

    Availability (Total) = Availability (A) * Availability (B)

If both `A` and `B` each have 99.9% availability, their total availability in sequence would be 99.8%.

#### In parallel
Overall availability increases when two components with availability < 100% are in paralle.

    Availability (Total) = 1 -  (1 - Availability (A)) * (1 - Availability (B))

If both `A` and `B` each have 99.9% availability, their total availability in parallel would be 99.9999%.