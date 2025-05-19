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