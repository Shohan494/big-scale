# big-scale


Overview

This article talks about eleven terminologies that are relevant in system design, especially for large-scale distributed systems. In each section, we also dive a bit into what solutions there are to achieve them. For a more detailed explanation of each topic, please check the reading list at the bottom of this article.
1. Reliability

The system should continue to work correctly (performing the correct function at the desired level of performance) even in the face of adversity (hardware or software faults, software errors, and even human errors). In simple words, “continuing to work correctly, even when things go wrong.” The following are the major three aspects that affect reliability:
How to achieve

    Redundancy: Redundancy is the practice of duplicating critical components or systems in order to increase reliability. For example, you might have multiple servers running the same application, or multiple network connections to ensure that data can always be transmitted. This approach is to remove every single point of failure.
    Fault-tolerance: Fault-tolerance refers to the ability of a system to continue functioning even in the face of component failures. This can be achieved through techniques such as replication, where multiple copies of data are maintained to ensure that it is always available, or through failover, where secondary systems are automatically brought online when primary systems fail.
    Error handling: Error handling is the practice of anticipating and handling errors that occur in a system. This can involve techniques such as retrying failed operations, failing fast to minimize the impact of failures, or handling errors in a way that prevents cascading failures.
    Testing: Testing is critical for ensuring that a system is reliable. This includes unit testing, integration testing, and stress testing to ensure that a system can handle a variety of conditions and scenarios.
    Monitoring: Monitoring is the practice of collecting data on the performance and behavior of a system in order to detect and diagnose problems. This can involve monitoring metrics such as response time, error rate, and throughput, as well as monitoring logs and traces to identify specific issues.
    Disaster recovery: Disaster recovery refers to the ability of a system to recover from catastrophic failures, such as a natural disaster or a cyber attack. This can involve practices such as data backup and restore, or having a secondary system in a separate location that can take over in the event of a major failure.

Achieving reliability in a distributed system is a complex and ongoing process that requires careful design, implementation, and testing. It also requires a culture of vigilance and continuous improvement, where reliability is a top priority and failures are viewed as opportunities to learn and improve.
2. Scalability

Scalability is the property of a system to handle a growing amount of work by adding resources to the system. As the system grows (in data volume, traffic volume, or complexity), there should be reasonable ways of dealing with that growth. It also refers to the capability of a system to grow and manage increased demand. A system that can continuously evolve to support a growing amount of work is scalable.
How to achieve

    Horizontal scaling: Adding more nodes to the system to handle increased traffic or workload.
    Vertical scaling: Increasing the resources (e.g., CPU, memory) of individual nodes to handle increased traffic or workload.
    Load balancing: Distributing incoming requests or traffic across multiple nodes to avoid overloading any one node.
    Partitioning: Splitting data or functionality across multiple nodes to distribute workload and avoid bottlenecks.
    Caching: Storing frequently used data in a local cache to reduce the number of requests that need to be processed by the system.
    Asynchronous communication: Using messaging or event-driven architectures to decouple components and improve scalability and fault tolerance.
    Statelessness: Designing components to be stateless, so that they can be easily replicated or scaled horizontally without worrying about maintaining state.
    Auto-scaling: Using automation to automatically scale the number of nodes up or down based on traffic or workload.

It’s worth noting that achieving scalability is often not a one-time event, but an ongoing process that requires monitoring and adjusting as the system and its traffic evolve over time.
Four golden rules for scalability

    Clones (horizontal scaling)

    Public servers are hidden behind a load balancer, it evenly distributes load (requests from your users) onto your group/cluster of application servers.
    Every server contains exactly the same codebase and does not store any user-related data, like sessions or profile pictures, on local disc or memory.
    Sessions need to be stored in a centralized data store that is accessible to all your application servers. It can be an external database or an external persistent cache, like Redis.
    Use tools such as Capistrano to deploy code to each server.

2. Database

    Master-slave replication (read from slaves, write to master) and upgrade your master server by adding RAM, RAM, and more RAM.
    Sharding, denormalization, and SQL tuning
    Denormalize right from the beginning and include no more Joins in any database query. You can stay with MySQL, and use it as a NoSQL database, or you can switch to a better and easier to scale NoSQL databases like MongoDB or CouchDB. Joins will now need to be done in your application code.

3. Cache

    Cache here usually means in-memory caches like Memcached or Redis (Redis includes extra database-features like persistence and the built-in data structures like lists and sets). Please never do file-based caching, it makes cloning and auto-scaling of your servers just a pain.
    A cache is a simple key-value store and it should reside as a buffering layer between your application and your data storage. Whenever your application has to read data it should at first try to retrieve the data from your cache. Redis can do several hundreds of thousands of READ operations per second when being hosted on a standard server.
    2 patterns of caching data:
    1. Cached Database Queries: Whenever you do a query to your database, you store the result dataset in cache. A hashed version of your query is the cache key.
    2. Cached Objects: see your data as an object like you already do in your code (classes, instances, etc.). Let your class assemble a dataset from your database and then store the complete instance of the class or the assembled dataset in the cache. Some ideas of objects to cache:

1. user sessions (never use the database!)
2. fully rendered blog articles
3. activity streams
4. user<->friend relationships

4. Asynchronism

    Do the time-consuming work in advance and serve the finished work with a low request time. It is to turn dynamic content into static content. Pages of a website, maybe built with a massive framework or CMS, are pre-rendered and locally stored as static HTML files on every change. Often these computing tasks are done on a regular basis.
    Handle tasks asynchronously. The basic idea is to have a queue of tasks or jobs that a worker can process such as by using RabbitMQ, Kafka, ActiveMQ, or a simple Redis list.

3. Availability

Availability refers to the ability of a distributed system to continue operating and serving requests, even in the face of failures or network disruptions. Achieving availability is important because users expect the system to be available and responsive at all times, and downtime or unavailability can have serious consequences.

    Measured by the percentage of time that a system remains operational under normal conditions.
    A reliable system is available.
    An available system is not necessarily reliable.
    A system with a security hole is available when there is no security attack.

How to achieve

    Redundancy: Building redundancy into the system by replicating components, nodes, or data across multiple locations. If one component fails, another can take over and continue to provide service. Redundancy can be implemented at different levels, from hardware redundancy to application-level redundancy.
    Load balancing: Distributing incoming requests or traffic across multiple nodes to avoid overloading any one node. Load balancing can be done using techniques such as round-robin load balancing, least-connections load balancing, or IP hash load balancing.
    Failover: Implementing a failover mechanism that can automatically switch to a backup component or node when a failure occurs. This can be done using techniques such as heartbeat monitoring or leader election.
    Isolation: Isolating components from each other so that a failure in one component does not affect others. This can be done using techniques such as containerization or microservices.
    Graceful degradation: Designing the system to gracefully degrade its performance when components fail, rather than completely stopping or crashing. For example, a web server might continue to serve cached pages or a database might switch to a read-only mode when a failure occurs.
    Monitoring: Monitoring the system and its components to detect failures as soon as they occur. This allows the system to quickly respond to failures and limit their impact.
    Auto-scaling: Using automation to automatically scale the number of nodes up or down based on traffic or workload. This can help ensure that the system has enough capacity to handle incoming requests.

It’s worth noting that achieving availability often involves trade-offs with other system properties, such as consistency and partition tolerance. For example, increasing redundancy and failover mechanisms can improve availability but may impact consistency. It’s important to carefully consider the requirements of your system and its users when designing an availability strategy.
4. Fault tolerance

Fault tolerance refers to a system’s ability to continue operating despite one or more component failures or malfunctions. Achieving fault tolerance is important because failures are inevitable in complex distributed systems, and a failure in one component can quickly ripple through the entire system if not properly addressed.
How to achieve

    Redundancy: Building redundancy into the system by replicating components, nodes, or data across multiple locations. If one component fails, another can take over and continue to provide service. Redundancy can be implemented at different levels, from hardware redundancy to application-level redundancy.
    Monitoring: Monitoring the system and its components to detect failures as soon as they occur. This allows the system to quickly respond to failures and limit their impact.
    Self-healing: Building self-healing mechanisms into the system that can detect and automatically recover from failures. This can include techniques such as automatic node replacement or data replication.
    Graceful degradation: Designing the system to gracefully degrade its performance when components fail, rather than completely stopping or crashing. For example, a web server might continue to serve cached pages or a database might switch to a read-only mode when a failure occurs.
    Failover: Implementing a failover mechanism that can automatically switch to a backup component or node when a failure occurs. This can be done using techniques such as heartbeat monitoring or leader election.
    Isolation: Isolating components from each other so that a failure in one component does not affect others. This can be done using techniques such as containerization or microservices.

It’s worth noting that achieving fault tolerance can come at a cost in terms of complexity, performance, and resources. It’s important to balance the benefits of fault tolerance against the costs and trade-offs involved in implementing it.
5. Consistency

Consistency refers to the property of a distributed system where all nodes or replicas of data have the same view of that data at any given point in time. Achieving consistency is important because it ensures that all nodes have accurate and up-to-date data, even as updates and modifications are being made to the data.
How to achieve

    Strong consistency: Ensuring that all nodes see the same view of data at all times. This can be achieved using techniques such as distributed transactions or consensus algorithms like Paxos or Raft. However, strong consistency can come at a cost in terms of performance and availability.
    Eventual consistency: Allowing nodes to have temporarily different views of data, but ensuring that they eventually converge to the same view. This can be achieved using techniques such as version vectors, conflict resolution, or reconciliation. Eventual consistency is often used in systems where strong consistency is not feasible or practical.
    Read-your-writes consistency: Ensuring that a node always sees the updates or modifications that it has made to the data. This can be achieved using techniques such as quorum-based reads or causal consistency.
    Consistency models: Defining a consistency model that specifies the level of consistency required for different types of data or operations. For example, some data might require strong consistency while others might only need eventual consistency.
    Replication: Replicating data across multiple nodes to ensure that all nodes have access to the same data. Replication can be done using techniques such as master-slave replication, multi-master replication, or leader-based replication.

It’s worth noting that achieving consistency can be a challenging task in distributed systems, as there are often trade-offs to be made between consistency, availability, and partition tolerance (i.e., the ability of a system to continue operating even when nodes are temporarily disconnected from one another). It’s important to carefully consider the requirements of your system and its users when designing a consistency strategy.
6. Partition tolerance

Partition tolerance is the property of a distributed system that allows it to continue operating and providing service, even in the face of network partitions or communication failures between nodes. This means that the system can handle situations where nodes are temporarily disconnected from each other or when messages between nodes are delayed or lost.

Achieving partition tolerance is a crucial aspect of building large-scale distributed systems, as it ensures that the system can continue to function even in the face of network disruptions, which are inevitable in any large-scale distributed system.
How to achieve

    Replication: One way to achieve partition tolerance is through data replication. By replicating data across multiple nodes, the system can continue to operate and serve requests even if some of the nodes are temporarily disconnected from the others.
    Quorum-based systems: In a quorum-based system, data is divided into partitions, and a certain number of nodes are required to agree on updates to the data before they can be considered valid. This allows the system to continue to operate even if some nodes are temporarily disconnected.
    Consensus algorithms: Consensus algorithms, such as the Paxos or Raft algorithms, allow a distributed system to reach agreement on a value, even in the presence of failures and network partitions.
    Sharding: In a sharded system, data is partitioned across multiple nodes. Each node is responsible for serving requests for a particular subset of the data. This allows the system to continue operating even if some nodes are temporarily disconnected.
    Asynchronous communication: By using asynchronous communication between nodes, the system can continue to operate even if messages are delayed or lost. This can be achieved through techniques such as message queues, event-driven architectures, and publish-subscribe systems.
    Partition-aware clients: Clients can be made partition-aware by using techniques such as consistent hashing, which ensures that clients always direct requests to the correct node, even in the face of network partitions.

It’s important to note that achieving partition tolerance often involves trade-offs with other system properties, such as consistency and availability. For example, ensuring partition tolerance through data replication may impact consistency, while ensuring partition tolerance through quorum-based systems may impact availability. It’s important to carefully consider the requirements of your system and its users when designing a partition tolerance strategy.
7. Load balancing

Load balancing is a technique used in distributed systems to distribute incoming requests or traffic across multiple nodes or servers. The goal of load balancing is to ensure that no single node is overwhelmed with too many requests, and that each request is handled by the most appropriate node. This helps to improve performance, scalability, and availability of the system.
How to achieve

    Round-robin: In a round-robin load balancing strategy, incoming requests are distributed in a cyclic order among the available nodes. This ensures that each node receives an equal number of requests.
    Least-connections: In a least-connections load balancing strategy, incoming requests are distributed to the node with the least number of active connections. This ensures that nodes are not overloaded with too many active connections.
    IP Hash: In an IP hash load balancing strategy, the source IP address of the incoming request is used to determine which node should handle the request. This ensures that requests from the same IP address are always handled by the same node, which can be useful for maintaining session state or caching.
    Content-based: In a content-based load balancing strategy, the content of the incoming request is used to determine which node should handle the request. For example, requests for static content might be directed to a different set of nodes than requests for dynamic content.
    Geo-based: In a geo-based load balancing strategy, incoming requests are directed to the node closest to the user, based on their geographic location. This can help to reduce latency and improve performance.
    Load balancer as a service: There are also load balancer as a service (LBaaS) providers that can automatically distribute incoming traffic across multiple nodes based on customizable algorithms. This can be particularly useful for cloud-based deployments, where nodes are constantly being added or removed.

It’s important to note that load balancing is often used in conjunction with other techniques for improving the performance, scalability, and availability of distributed systems, such as caching, replication, and failover. Choosing the right load balancing strategy for your system depends on the specific needs of your application and its users.
8. Data replication

Data replication is the process of creating and maintaining multiple copies of the same data across multiple nodes in a distributed system. The purpose of data replication is to improve performance, availability, and fault tolerance, as well as to ensure that data is not lost in the event of a node failure.
How to achieve

    Master-slave replication: In a master-slave replication strategy, one node is designated as the master node, and all write requests are sent to this node. The master node then propagates changes to the slave nodes, which are responsible for serving read requests. This strategy ensures that all nodes have a consistent copy of the data.
    Master-master replication: In a master-master replication strategy, all nodes are capable of handling both write and read requests. Each node writes changes to its local copy of the data, which is then propagated to the other nodes. This strategy can improve performance and availability, but it requires careful coordination to avoid conflicts and inconsistencies.
    Multi-master replication: In a multi-master replication strategy, all nodes are capable of handling both write and read requests, and each node can independently update the data. This strategy can improve performance and availability, but it requires complex conflict resolution algorithms to ensure consistency.
    Sharding: In a sharded system, data is partitioned across multiple nodes, and each node is responsible for serving requests for a particular subset of the data. This allows for horizontal scaling and can improve performance, but it also requires careful coordination to ensure consistency across nodes.
    Asynchronous replication: In an asynchronous replication strategy, changes to the data are propagated to the other nodes at a later time, rather than immediately. This can improve performance and reduce the impact of network latency, but it can also introduce the risk of data inconsistencies.
    Consistency models: Different consistency models, such as strong consistency or eventual consistency, can be used to ensure that all nodes have a consistent view of the data. This can involve trade-offs between performance, availability, and consistency.

It’s important to note that data replication is not a one-size-fits-all solution, and the appropriate replication strategy depends on the specific needs of your application and its users. Careful consideration should be given to the trade-offs between performance, availability, and consistency, as well as the impact on network bandwidth and storage requirements.
9. Caching

Caching is the process of storing frequently accessed data in a cache, which is a fast and easily accessible storage layer that sits between the client and the main data source. The purpose of caching is to improve performance by reducing the number of requests to the main data source and to reduce the latency of data access.
How to achieve

    In-memory caching: In-memory caching stores frequently accessed data in memory, which allows for fast access and low latency. This is the most common type of caching, and can be implemented using technologies such as Redis or Memcached.
    Distributed caching: Distributed caching involves storing cached data across multiple nodes in a distributed system, which can help to improve performance and availability. This can be implemented using technologies such as Hazelcast or Apache Ignite.
    Content delivery networks (CDNs): CDNs are a type of distributed caching that is specifically designed for serving static content, such as images, videos, and files. CDNs store copies of content on multiple servers located in different geographic regions, which allows for faster access and reduced latency.
    Application-level caching: Application-level caching involves caching frequently accessed data within the application itself, rather than relying on a separate caching layer. This can be useful for reducing the number of requests to a database and can be implemented using frameworks such as Spring or Hibernate.
    Query caching: Query caching involves caching the results of frequently executed queries, which can help to improve performance and reduce the load on the database. This can be implemented using technologies such as Ehcache or Hibernate.

It’s important to note that caching is not a one-size-fits-all solution, and the appropriate caching strategy depends on the specific needs of your application and its users. Careful consideration should be given to the size and lifespan of the cache, the frequency of updates to the data, and the trade-offs between performance and consistency. Additionally, cache invalidation strategies should be put in place to ensure that stale data is not served to clients.
10. Asynchronous communication

Asynchronous communication is a way of sending and receiving messages between components or services without blocking the execution of the sender or the receiver. In other words, the sender can continue with its work without waiting for a response, and the receiver can process the message whenever it is available.
How to achieve

    Message queues: Message queues provide a way to send and receive messages between services asynchronously. The sender sends a message to a queue, and the receiver processes the message when it is available in the queue. Popular message queue systems include Apache Kafka, RabbitMQ, and Amazon SQS.
    Publish/Subscribe model: In this model, the sender publishes a message to a topic, and all subscribers who are interested in that topic receive the message. This allows for asynchronous communication and decouples the sender and receiver. Popular publish/subscribe systems include Apache Kafka and Amazon SNS.
    Callbacks: In this approach, the sender makes a request to the receiver and provides a callback function that the receiver can invoke when it has finished processing the request. The sender can continue with its work while waiting for the callback. This is often used in web development, where a user makes a request to a server and provides a callback function that is invoked when the server finishes processing the request.
    Asynchronous API: Asynchronous API is an API that is designed to work in an asynchronous manner. Rather than returning a value directly, an asynchronous API typically returns a Promise or a Future object, which represents the eventual result of the operation. The caller can then use the Promise to receive the result or handle any errors that occur.

To implement asynchronous communication, you need to choose an appropriate approach and use a communication protocol that supports asynchronous communication. Many modern communication protocols, such as HTTP/2 and WebSocket, support asynchronous communication. You also need to make sure that your components are designed to handle asynchronous communication, such as by using non-blocking I/O and event-driven programming models. Finally, you need to handle errors and timeouts gracefully, since the receiver may not respond immediately or at all.
11. Monitoring and instrumentation

Monitoring and instrumentation are essential aspects of building and operating large-scale distributed systems. In general, monitoring refers to the practice of collecting data on the performance, health, and behavior of a system, while instrumentation refers to the process of adding code to a system in order to collect this data.
How to achieve

    Metrics: Metrics are a way to measure the behavior of a system over time. Common metrics include response time, error rate, and throughput. In order to collect metrics, you need to instrument your code by adding hooks or probes that capture relevant data.
    Logging: Logging is a way to capture events that occur in a system over time. Logs can be used for debugging, troubleshooting, and auditing. To capture logs, you need to instrument your code by adding logging statements that capture relevant data.
    Tracing: Tracing is a way to follow the path of a request through a system. By instrumenting your code with tracing, you can get a detailed view of how requests are handled, which can be useful for troubleshooting and performance optimization.
    Distributed tracing: Distributed tracing is a way to trace requests as they move through multiple services in a distributed system. By instrumenting each service with tracing, you can get a detailed view of how requests are handled across the entire system.

To achieve monitoring and instrumentation, you need to choose an appropriate tool or framework that supports the type of data you want to collect. Many frameworks, such as Spring Boot and Micronaut, include built-in support for metrics, logging, and tracing. You also need to make sure that your instrumentation is lightweight and does not add too much overhead to your system. Finally, you need to make sure that you have a way to store and analyze the data you collect, such as using a monitoring tool like Prometheus, Grafana, Datadog, or Splunk.
References

    Designing Data-Intensive Applications
    educative
    System Design Cheatsheet
    System Design Interview

Other Topics for System Design

    System Design — Load Balancing
    System Design — Caching
    System Design — Sharding / Data Partitioning
    System Design — Indexes
    System Design — Proxies
    System Design — Message Queues
    System Design — Redundancy and Replication
    System Design — SQL vs. NoSQL
    System Design — CAP Problem
    System Design — Consistent Hashing
    System Design — Client-Server Communication
    System Design — Storage
    System Design — Other Topics


