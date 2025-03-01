#### Latency Vs Throughput
- Latency refers to the amount of time it takes for a system to respond to a request. 
- Throughput refers to the number of requests that a system can handle at the same time.
- ==Generally, you should aim for maximal throughput with acceptable latency.==
---
#### CAP Theorem
- A distributed system can support only two of the following characteristics:
	- [[#Consistency]]
	- [[#Availability]]
	- [[#Partition Tolerance]]
![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020241124151440.png)
##### Consistency
-  all nodes see the same data at the same time
- client perceives that a set of operations has occurred all at once
	- even though there are many operations behind the scenes, client sees only sum of them

- More like Atomic in ACID transaction properties
##### Availability
- A non-failing node will return a reasonable response within a reasonable amount of time (no error or timeout).
- Every operation must terminate in an intended response
##### Partition Tolerance
- partition - parts of distributed system that can't communicate at the time
- the system continues to operate despite arbitrary message loss
- Operations will complete, even if individual components are unavailable

##### [following_are_in_this_link](https://robertgreiner.com/cap-theorem-revisited/)

One such [_fallacy of distributed computing_](http://en.wikipedia.org/wiki/Fallacies_of_Distributed_Computing?ref=robertgreiner.com) is that networks are reliable. They aren't. Networks and parts of networks go down frequently and unexpectedly. Network failures _happen to your system_ and you don't get to choose when they occur.

Given that networks aren't completely reliable, you must tolerate partitions in a distributed system, period. Fortunately, though, you get to choose what to do when a partition does occur. According to the CAP theorem, this means we are left with two options: Consistency and Availability.

- **CP** - Consistency/Partition Tolerance - Wait for a response from the partitioned node which could result in a timeout error. The system can also choose to return an error, depending on the scenario you desire. Choose Consistency over Availability when your business requirements dictate atomic reads and writes.

- **AP** - Availability/Partition Tolerance - Return the most recent version of the data you have, which could be stale. This system state will also accept writes that can be processed later when the partition is resolved. Choose Availability over Consistency when your business requirements allow for some flexibility around when the data in the system synchronizes. Availability is also a compelling option when the system needs to continue to function in spite of external errors (shopping carts, etc.)

- [Consistency patterns](https://cs.fyi/guide/consistency-patterns-week-strong-eventual)
	- Strong consitency
		- After an update is made to the data, it will be immediately visible to any subsequent read operations. The data is replicated in a synchronous manner, ensuring that all copies of the data are updated at the same time.
	- Weak Consistency
		- After an update is made to the data, it is not guaranteed that any subsequent read operation will immediately reflect the changes made. The read **may or may not** see the recent write.
	- Eventual Consistency
		- Eventual consistency is a form of Weak Consistency. After an update is made to the data, it will be eventually visible to any subsequent read operations. The data is replicated in an asynchronous manner, ensuring that all copies of the data are eventually updated.
- [Availability patterns](https://learn.microsoft.com/en-us/azure/well-architected/reliability/design-patterns#availability)
	-  <- TODO

#### Availability patterns - Replication

- Replication is an availability pattern that involves having multiple copies of the same data stored in different locations. In the event of a failure, the data can be retrieved from a different location. There are two main types of replication: Master-Master replication and Master-Slave replication.

- **Master-Master replication:**
	- In this type of replication, multiple servers are configured as “masters,” and each one can accept read and write operations. This allows for high availability and allows any of the servers to take over if one of them fails. However, this type of replication can lead to conflicts if multiple servers update the same data at the same time, so some conflict resolution mechanism is needed to handle this.
- **Master-Slave replication:** 
	- In this type of replication, one server is designated as the “master” and handles all write operations, while multiple “slave” servers handle read operations. If the master fails, one of the slaves can be promoted to take its place. This type of replication is simpler to set up and maintain compared to Master-Master replication.

- More relevant to data availability

#### Availability patterns - Fail-Over

- Availability is often quantified by uptime (or downtime) as a percentage of time the service is available. Availability is generally measured in number of 9s—a service with 99.99% availability is described as having four 9s.
- ![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250110112055.png)
- https://www.filecloud.com/blog/2015/12/architectural-patterns-for-high-availability/
#### Availability in parallel vs in sequence

If a service consists of multiple components prone to failure, the service’s overall availability depends on whether the components are in sequence or in parallel.

##### In sequence
- Overall availability decreases when two components with availability < 100% are in sequence:
```
Availability (Total) = Availability (Foo) * Availability (Bar)
```
If both `Foo` and `Bar` each had 99.9% availability, their total availability in sequence would be 99.8%.
##### In parallel
- Overall availability increases when two components with availability < 100% are in parallel:
```
Availability (Total) = 1 - (1 - Availability (Foo)) * (1 - Availability (Bar))
```
- If both `Foo` and `Bar` each had 99.9% availability, their total availability in parallel would be 99.9999%.
