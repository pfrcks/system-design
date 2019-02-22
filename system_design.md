# System Design Basics

<!-- toc -->

- [How to approach a system design problem](#how-to-approach-a-system-design-problem)
- [Load Balancer](#load-balancer)
    + [Types of Load Balances](#types-of-load-balances)
    + [Load Balancing Algorithms](#load-balancing-algorithms)
- [Caching](#caching)
    + [Types of Cache](#types-of-cache)
    + [Cache Invalidation](#cache-invalidation)
    + [Cache Eviction Policy](#cache-eviction-policy)
- [Sharding(Data Partitioning)](#shardingdata-partitioning)
    + [Partioning Methods](#partioning-methods)
    + [Partitioning Criteria](#partitioning-criteria)
    + [Common problems](#common-problems)
- [Indexes](#indexes)
- [Proxies](#proxies)
- [Queues](#queues)
- [Redundancy/Replication](#redundancyreplication)
- [SQL vs NoSQL](#sql-vs-nosql)
    + [NoSQL Types](#nosql-types)
    + [Difference](#difference)
    + [What to use when](#what-to-use-when)
- [CAP - Consistency, Availability, Partition Tolerance](#cap---consistency-availability-partition-tolerance)
- [Consistent Hashing](#consistent-hashing)
- [Long-Polling vs WebSockets vs Server-Sent Events](#long-polling-vs-websockets-vs-server-sent-events)
    + [AJAX Polling](#ajax-polling)
    + [HTTP Long Polling](#http-long-polling)
    + [Websockets](#websockets)
    + [Server-Sent Events (SSEs)](#server-sent-events-sses)
    + [Scaling](#scaling)
    + [Reliability](#reliability)
- [Case Studies](#case-studies)
- [Design a logging system](#design-a-logging-system)
- [Collection](#collection)
- [Transportation](#transportation)
- [Storage](#storage)
    + [Duration](#duration)
    + [Volume :](#volume-)
    + [Access](#access)
- [Analysis](#analysis)
- [Alerting](#alerting)
- [Building from scratch](#building-from-scratch)
- [Testing in Production](#testing-in-production)
- [Types(Based on effect)](#typesbased-on-effect)
    + [No change](#no-change)
    + [Experiment with Users](#experiment-with-users)
    + [Change with Service](#change-with-service)
- [Types of TiP](#types-of-tip)
- [Types of Production Testing(based on phase)](#types-of-production-testingbased-on-phase)
    + [Deploy](#deploy)
    + [Release](#release)
    + [Post Release](#post-release)
- [OOD](#ood)
    + [Sudoku](#sudoku)
    + [BlackJack](#blackjack)
- [Hotel Reservation](#hotel-reservation)

<!-- tocstop -->

## How to approach a system design problem
 * Requirements clarifications
 * System interface definition : Defining APIs
 * Back of the envelope estimation
 * Define data model : data flow, databases
 * High level design
 * Detailed desing
 * Identify and resolve bottlenecks

## Load Balancer

To utilize full scalability and redundancy, we can try to balance the load at each layer of the system. We can add LBs at three places:

* Between the user and the web server
* Between web servers and an internal platform layer, like application servers or cache servers
* Between internal platform layer and database.

#### Types of Load Balances

Smart Clients, Hardware, and Software

#### Load Balancing Algorithms

Least Connection, Least response time, least bandwidth, round robin, weighted round robin, ip hash

## Caching

Load balancing helps you scale horizontally across an ever-increasing number of servers, but caching will enable you to make vastly better use of the resources you already have, as well as making otherwise unattainable product requirements feasible. Caches take advantage of the locality of reference principle: recently requested data is likely to be requested again.

#### Types of Cache

* Application server cache
* Distributed Cache
* Global Cache
* CDN

#### Cache Invalidation

* Write through cache: data is written into the cache and the corresponding database at the same time. minimizes the risk of data loss, this scheme has the disadvantage of higher latency for write operations.
* Write-around cache: This technique is similar to write through cache, but data is written directly to permanent storage, bypassing the cache. disadvantage that a read request for recently written data will create a “cache miss” and must be read from slower back-end storage and experience higher latency.
* Write-back cache: Under this scheme, data is written to cache alone, and completion is immediately confirmed to the client. The write to the permanent storage is done after specified intervals or under certain conditions.

#### Cache Eviction Policy

LIFO, FIFO, LRU, MRU, LFU, RR

## Sharding(Data Partitioning)

Data partitioning (also known as sharding) is a technique to break up a big database (DB) into many smaller parts. It is the process of splitting up a DB/table across multiple machines to improve the manageability, performance, availability and load balancing of an application. The justification for data sharding is that, after a certain scale point, it is cheaper and more feasible to scale horizontally by adding more machines than to grow it vertically by adding beefier servers.

#### Partioning Methods

* Horizontal : In this scheme, we put different rows into different tables. if the value whose range is used for sharding isn’t chosen carefully, then the partitioning scheme will lead to unbalanced servers.
* Vertical : divide our data to store tables related to a specific feature to their own server.  main problem with this approach is that if our application experiences additional growth, then it may be necessary to further partition a feature specific DB across various servers
* Directory based : A loosely coupled approach to work around issues mentioned in above schemes is to create a lookup service which knows your current partitioning scheme and abstracts it away from the DB access code.

#### Partitioning Criteria

* Key/Hash based
* List partitioning
* Round robin
* Composite


#### Common problems

* Joins and denormalization
* Referential Integrity
* Rebalancing

## Indexes

The goal of creating an index on a particular table in a database is to make it faster to search through the table and find the row or rows that we want. Indexes can be created using one or more columns of a database table, providing the basis for both rapid random lookups and efficient access of ordered records.

an index is a data structure that can be perceived as a table of contents that points us to the location where actual data lives. The trick with indexes is that we must carefully consider how users will access the data. In the case of data sets that are many terabytes in size but with very small payloads (e.g., 1 KB), indexes are a necessity for optimizing data access. Finding a small payload in such a large dataset can be a real challenge since we can’t possibly iterate over that much data in any reasonable time. Furthermore, it is very likely that such a large data set is spread over several physical devices—this means we need some way to find the correct physical location of the desired data. Indexes are the best way to do this.

The downside is that indexes make it slower to add rows or make updates to existing rows for that table since we not only have to write the data but also have to update the index. So adding indexes can increase the read performance, but at the same time, decrease the write performance.  To reiterate, adding indexes is about improving the performance of search queries.

## Proxies

A proxy server is an intermediary piece of hardware/software that sits between the client and the back-end server. Another advantage of a proxy server is that its cache can serve a lot of requests.

For example, we can collapse the same (or similar) data access requests into one request and then return the single result to the user; this scheme is called collapsed forwarding.

Another great way to use the proxy is to collapse requests for data that is spatially close together in the storage (consecutively on disk).

## Queues

Queues are used to effectively manage requests in a large-scale distributed system.

## Redundancy/Replication

## SQL vs NoSQL

Relational databases are structured and have predefined schemas, like phone books that store phone numbers and addresses. Non-relational databases are unstructured, distributed and have a dynamic schema

#### NoSQL Types

* Key value stores : redis
* Document databases : MongoDB
* Wide-Column db : Instead of ‘tables,’ in columnar databases we have column families, which are containers for rows. Unlike relational databases, we don’t need to know all the columns up front, and each row doesn’t have to have the same number of columns. Columnar databases are best suited for analyzing large datasets - big names include Cassandra and HBase.
* Graph Databases: These databases are used to store data whose relations are best represented in a graph. Neo4j

#### Difference

Storage, Schema, Querying, Scalability(SQL databases are vertically scalable, i.e., by increasing the horsepower (higher Memory, CPU, etc.) of the hardware, which can get very expensive.NoSQL databases are horizontally scalable, meaning we can add more servers easily in our NoSQL database infrastructure to handle large traffic. Any cheap commodity hardware or cloud instances can host NoSQL databases, thus making it a lot more cost-effective than vertical scalin), ACID(Atomicity, Consistency, Isolation, Durability)

#### What to use when

SQL -> ACID, data structured and unchanging
NoSQL -> large volume of data with no structure, rapid development

## CAP - Consistency, Availability, Partition Tolerance

## Consistent Hashing

Normal hashing is not horizontally scalable, may not be load balanced(if data is not uniformly balanced) -- soln = consistent hashing

In Consistent Hashing when the hash table is resized (e.g. a new cache host is added to the system), only k/n keys need to be remapped, where k is the total number of keys and n is the total number of servers.

Here’s how consistent hashing works:

* Given a list of cache servers, hash them to integers in the range.
* To map a key to a server,
	* Hash it to a single integer.
	* Move clockwise on the ring until finding the first cache it encounters.
	* That cache is the one that contains the key.

To handle load balancing, we add “virtual replicas” for caches. Instead of mapping each cache to a single point on the ring, we map it to multiple points on the ring, i.e. replicas. This way, each cache is associated with multiple portions of the ring.

## Long-Polling vs WebSockets vs Server-Sent Events

#### AJAX Polling

Problem with Polling is that the client has to keep asking the server for any new data. As a result, a lot of responses are empty creating HTTP overhead.

#### HTTP Long Polling

A variation of the traditional polling technique that allows the server to push information to a client, whenever the data is available. With Long-Polling, the client requests information from the server exactly as in normal polling, but with the expectation that the server may not respond immediately. That’s why this technique is sometimes referred to as a “Hanging GET”.


#### Websockets

WebSocket provides Full duplex communication channels over a single TCP connection. It provides a persistent connection between a client and a server that both parties can use to start sending data at any time. The client establishes a WebSocket connection through a process known as the WebSocket handshake. If the process succeeds, then the server and client can exchange data in both directions at any time. The WebSocket protocol enables communication between a client and a server with lower overheads, facilitating real-time data transfer from and to the server.

#### Server-Sent Events (SSEs)

Under SSEs the client establishes a persistent and long-term connection with the server. The server uses this connection to send data to a client. If the client wants to send data to the server, it would require the use of another technology/protocol to do so. SSEs are best when we need real-time traffic from the server to the client or if the server is generating data in a loop and will be sending multiple events to the client.


#### Scaling

Horizontal vs. Vertical Scaling: Horizontal scaling means that you scale by adding more servers into your pool of resources whereas Vertical scaling means that you scale by adding more power (CPU, RAM, Storage, etc.) to an existing server.

#### Reliability

If a system is reliable, it is available. However, if it is available, it is not necessarily reliable. In other words, high reliability contributes to high availability, but it is possible to achieve a high availability even with an unreliable product by minimizing repair time and ensuring that spares are always available when they are needed

## Case Studies

* Assuming a 70% capacity model (we don’t want to go above 70% of the total capacity of our storage system)
* How can we build more intelligent cache? If we go with 80-20 rule, i.e., 20% of daily read volume for photos is generating 80% of traffic which means that certain photos are so popular that the majority of people reads them. This dictates we can try caching 20% of daily read volume of photos and metadata.
* Assuming a modern server can handle 50K concurrent connections at any time, we would need 10K such servers.
* HBase is a column-oriented key-value NoSQL database that can store multiple values against one key into multiple columns. HBase is modeled after Google’s BigTable and runs on top of Hadoop Distributed File System (HDFS). HBase groups data together to store new data in a memory buffer and once the buffer is full, it dumps the data to the disk This way of storage not only helps storing a lot of small data quickly but also fetching rows by the key or scanning ranges of rows. HBase is also an efficient database to store variable size data, which is also required by our service.


## Design a logging system

## Collection

Applications create logs in different ways, some log through syslog, others log directly to files. f you are using a file replication based approach where files are replicated to a central server on a fixed schedule, then you can only inspect logs as frequently as the replication runs.


## Transportation

For example, Scribe, nsq and Kafka, require clients to log data via their API. Typically, application code is written to log directly to these sources which allows them to reduce latency and improve reliability.
Logstash, Heka, fluentd and Flume provide a number of input sources but also support natively tailing files and transporting them reliably. These are a better fit for more general log collection.

## Storage

#### Duration
S3 > HDFS/Cassandra/MongoDB > Redis
#### Volume :
 The storage system you chose should allow you to scale-out horizontally if your data volume will be large.
#### Access
AWS Glacier or tape backup can take hours to load a file. These don’t work if you need log access for production troubleshooting. If you plan to do more interactive data analysis, storing log data in ElasticSearch or HDFS may allow you work with the raw data more effectively. Some log data is so large that it can only be analyzed in more batch oriented frameworks. The defacto standard is this case is Apache Hadoop along with HDFS.
## Analysis
If you are storing log data in HDFS, Hive or Pig might help analyzing the data easier than writing native MapReduce jobs.

If you need a UI for analysis, you can store parsed log data in ElasticSearch and use a front-end such as Kibana or Graylog2 to query and inspect the data. The log parsing can be handled by Logstash, Heka or applications logging with JSON directly. This approach allows more real-time, interactive access to the data but is not really suited for a mass batch processing.
## Alerting
Most log data is not interesting but errors almost always indicate a problem. It’s much more effective to have the logging system email or notify respective parties when errors ocurr instead of having someone repeatedly watch for the events. There are several services that solely provide application error logging such as Sentry or HoneyBadger. These can also aggregate repetitve exceptions which can give you and idea of how frequently an error is occuring.

## Building from scratch

- As we know, the log is an ordered, immutable sequence of messages. Messages are atomic, meaning they can’t be broken up.
- The log can be played back from any arbitrary position. With position, we normally refer to a logical message timestamp rather than a physical wall-clock time, such as an offset into the log.
- With the above in mind, our log starts to look an awful lot like an actual logging file, but instead of timestamps and log messages, we have offsets and opaque data messages.
- Recall that we need to support a few different access patterns: looking up messages by offset and also truncating the log using a variety of different retention policies.
- To account for this, we break up the log file into chunks. In Kafka, these are called segments. In NATS Streaming, they are called slices. Each segment is a new file. At a given time, there is a single active segment, which is the segment messages are written to.
- Segments are defined by their base offset, i.e. the offset of the first message stored in the segment. In Kafka, the files are also named with this offset.
- Alongside each segment file is an index file that maps message offsets to their respective positions in the log segment. In Kafka, the index uses 4 bytes for storing an offset relative to the base offset and 4 bytes for storing the log position
- Ideally, the data written to the log segment is written in protocol format. That is, what gets written to disk is exactly what gets sent over the wire. This allows for zero-copy reads.
- To do that, we replicate the data. Replication can also be a means for increasing scalability, but for now we’re only looking at this through the lens of high availability.
- gossip/multicast protocols and consensus protocols. The former includes things like epidemic broadcast trees, bimodal multicast, SWIM, HyParView, and NeEM. These tend to be eventually consistent and/or stochastic. The latter, which I’ve described in more detail here, includes 2PC/3PC, Paxos, Raft, Zab, and chain replication. These tend to favor strong consistency over availability.
- Since ordering is an important property of a log, consistency becomes important for a replicated log. This more or less rules out the stochastic and eventually consistent options, leaving us with consensus-based replication.
- There are essentially two components to consensus-based replication schemes: 1) designate a leader who is responsible for sequencing writes and 2) replicate the writes to the rest of the cluster.
- we need the leader to be dynamic. It turns out leader election is a well-understood problem, so we’ll get to this in a bit.
- Once a leader is established, it needs to replicate the data to followers. In general, this can be done by either waiting for all replicas or waiting for only a quorum (majority) of replicas.
- Leader maintains an in-sync replica set (ISR) consisting of all the replicas which are fully caught up with the leader. This is every replica, by definition, at the beginning.
- The leader writes messages to a write-ahead log (WAL). Messages written to the WAL are considered uncommitted or “dirty” initially. The leader only commits a message once all replicas in the ISR have written it to their own WAL.
- The leader also maintains a high-water mark (HW) which is the last committed message in the WAL.
- The remaining two questions pertaining to replication are how do we keep things fast and how do we ensure data is durable?
- A key part of scaling any kind of data-intensive system is the ability to partition the data. Partitioning is how we can scale a system linearly, that is to say we can handle more load by adding more nodes. We make the system horizontally scalable.
- Kafka was designed this way from the beginning. Topics are partitioned and ordering is only guaranteed within a partition. These partitions allow us to distribute reads and writes across a set of brokers. In Kafka, the log is actually the partition.
- pecifically, how do we scale to a large number of consumers? In Kafka and NATS Streaming, reads (and writes) are only served by the leader
- with an immutable log, there are no stale or phantom reads. Unlike a database, we can loosen our requirements a bit. Whereas a database is typically mutable, with a log, we’re only appending things. From a consumer’s perspective, a replica is either up-to-date with the leader or in the process of catching up, but in either case, if we read all of the records, we should end up in the same state
- With push, it can be a tricky balance to ensure full utilization of the consumer. We might use a backoff protocol like additive increase/multiplicative decrease, widely known for its use in TCP congestion control, to optimize utilization.
- With push, we must decide whether to send a message immediately or wait to accumulate more messages before sending. This is a decision pertaining to latency versus throughput.
- There are two ways to track position in the log: have the server track it for consumers or have consumers track it themselves. Again, there are trade-offs with this, namely between API simplicity, server complexity, performance, and scalability.
- We can store the offsets themselves directly in the log. As of 0.9, this is what Kafka does.
- But by storing offsets in the log, they are treated just like any other write to a Kafka topic, which scales quite well (offsets are stored in an internal Kafka topic called consumer\_offsets partitioned by consumer group; there is also a special read cache for speeding up the read path).
- There are a number of advantages to this approach. We get fault-tolerance and durability due to the fact that our log is already fault-tolerant and durable as designed earlier. We get consistent reads again due to our replication scheme.

## Testing in Production

- The core techniques for testing in production encompass active and passive monitoring (involving real data and synthetic transactions), experimentation (both controlled and uncontrolled) with real users, and stress tests to monitor system response.
- Techniques such as telemetry, or diagnostics around software usage, bring readability into the outcomes of testing in production. While active monitoring focuses more on user-generated outcomes, passive monitoring relates to test effort from the test teams with synthetic data. So an example of active monitoring would be if you engage a beta crowd team of users to provide you feedback, whereas passive monitoring is when the test team initiates monitoring either by itself or through the rest of the product teams. T
- Experimentation through a controlled form often involves techniques such as A/B testing to gauge user feedback on specific scenarios, while uncontrolled experimentation relates to beta testing and crowdsourced testing. Stress tests in a live environment need to be closely monitored, especially ones performed during peak load seasons for the application.


## Types(Based on effect)

#### No change

- Data Mining
- User Performance Testing
- Environment Validation

#### Experiment with Users

- Canary
- A/B testing : (Desing, Controlled Flight test)
- Dogfood and beta

#### Change with Service

- Synthetic tests in prod
- User scenario execution
- Load testing
- Destructive testing


## Types of TiP

- Canary Testing
- Controlled test flight
- A/B testing
- Synthetic User Testing
- Fault Injection
- Recovery testing

## Types of Production Testing(based on phase)

#### Deploy

- Intgeration testing : Test components in pairs or so. Ensure effects not persistent, only reads, if writes : whitelist, if no writes: discard in application layers, mark db entry as one triggered by test run. Server mesh architecture for stubbing of side effects. Since everything is handled by proxy, egress and ingress can then be modified to deal with test runs
- Shadow/Dark Traffic/Mirroring : Capture traffic on prod and then replay on soon to be prod. Can happen in real time or can be done asynch. Downside with entire traffic shadowing -> requires 2x space. Writing to DB is again an issue(a prod one). Shadowing is best of statless services with stateful responses stubbed out
- Tap Compare : Multicast traffic and compare. Not fire and forget like shadow testing.
- Load Testing : Pass x% of load, non blocking requests are generated and tested. traffic sent to smaller cluster. can do redlining as well. remove backends from load balancer. this is good for running actual traffic
- Config Tests : Check configs, canarying, no global rollout : Common config, statically typed, canary, easy to revert, maintain good configs

#### Release

- Canarying : dogfood, beta and so on
- Monitoring : core metrics to track, eg, increase in error rate, increase in latency etc.
- Exception Tracking : sentry, traces variables etc
- Traffic Shaping : 1->100%

#### Post Release

- Feature Flagging/Dark Launch : disciplined pruning must be done.
- A/B Testing
- Profiling
- Teeing : replay traffic on non prod cluster for debugg purposes. maybe for more verbosity
- Logs/Events/Metrics
- Chaos Engineering

## OOD

#### Sudoku
For base classes of a solver, I see a good start with Cell, ValidationRegion, Board, and Pattern as your main classes.
Cell: Has the current value of the cell, the remaining possible values of the cell, and if the cell is fixed or not.
ValidationRegion: Has references to the appropriate 9 Cells on the Board. This class doesn't really need to know if it is representing horizontal, vertical, or a square regions, because the rules are the same. This class has a validate() method to verify that the current state of the region is possible.
Board: Has the entire layout of Cells, and initializes the fixed ValidationRegions appropriately by passing the appropriate Cells by reference. It also has a solve method that applies Patterns in a pre-defined order until a solution is reached or it is determined that no solution is possible (brute-force pattern must therefore be the last ditch effort).
Pattern: Abstract class that has an apply(Board) method that applies the given pattern to the specified board object (removes possibilities from Cells and sets them when it knows there is only one possibility left). From Sudoku Dragon - Sudoku Strategy, you'll likely implement patterns like OneChoicePattern, SinglePossibilityPattern, OnlySquareRule, etc.
https://github.com/AndrewBaliushin/Stanford-CS108-Object-Oriented-System-Design/blob/master/HW3Sudoku/hw3DBSudoku/src/sudoku/Sudoku.java

#### BlackJack
```python
from collections import namedtuple
from itertools import product
from random import shuffle
from typing import List

Card = namedtuple('Card', ('rank', 'suit'))


class Deck:
    card_ranks = []
    card_suits = []

    def __init__(self) -> None:
        self.cards = []
        self.refresh_deck()

    def refresh_deck(self) -> int:
        self.cards = map(Card, product(self.card_ranks, self.card_suits))

    def shuffle(self) -> None:
        shuffle(self.cards)

    def draw_card(self) -> Card:
        return self.cards.pop()


class FrenchDeck(Deck):
    card_ranks = ['Ace', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K']
    card_suits = ['Hearts', 'Spades', 'Clubs', 'Diamonds']


class Game:
    def __init__(self, deck: Deck) -> None:
        raise NotImplementedError

    def card_value(self, card: Card) -> int:
        raise NotImplementedError

    def hand_value(self, hand: List[Card]) -> int:
        raise NotImplementedError

    def play(self) -> None:
        raise NotImplementedError
```

## Hotel Reservation
```python
class Bookable(object):
    def is_available(self, booking_class):
        return self.get_items(booking_class) > 0

    def make_reservation(self, customer, booking_class):
        if self.is_available(booking_class):
            self.decrement_items(booking_class)
            customer.customer_record['Wallet'] += self.get_price(booking_class)
            customer.customer_record['Booking ID'][self.get_class_name()] = self.get_id_prefix() + str(random.randrange(10, 1000, 2))

    def get_class_name(self):
        return self.__class__.__name__

    def get_items(self, booking_class):
        raise NotImplementedError

    def get_price(self, booking_class):
        raise NotImplementedError

    def decrement_items(self, booking_class):
        raise NotImplementedError

    def get_id_prefix(self):
        raise NotImplementedError


class Airline(Bookable):
    CLASS_BUSINESS = 'Business Class'
    CLASS_FIRST = 'First Class'
    CLASS_PREMIUM_ECONOMY = 'Premium Economy'
    CLASS_ECONOMY = 'Regular Economy'

    def __init__(self, airline_name):
        super(Airline, self).__init__()
        self.airline_name = airline_name
        self.seats = {
            self.CLASS_BUSINESS: 50,
            self.CLASS_FIRST: 50,
            self.CLASS_PREMIUM_ECONOMY: 100,
            self.CLASS_ECONOMY: 150,
        }
        self.prices = {
            self.CLASS_BUSINESS: 2500,
            self.CLASS_FIRST: 2000,
            self.CLASS_PREMIUM_ECONOMY: 1800,
            self.CLASS_ECONOMY: 1500,
        }

    def get_items(self, booking_class):
        return self.seats[booking_class]

    def decrement_items(self, booking_class):
        self.seats[booking_class] -= 1

    def get_id_prefix(self):
        return 'AIR'

    def get_price(self, booking_class):
        return self.prices[booking_class]<Paste>
```

```java
// Java code skeleton to design an online hotel
// booking system.
Enums:

public enum RoomStatus {
    EMPTY
        NOT_EMPTY;
}

public enum RoomType {
    SINGLE,
    DOUBLE,
    TRIPLE;
}

public enum PaymentStatus {
    PAID,
    UNPAID;
}

public enum Facility {
    LIFT;
    POWER_BACKUP;
    HOT_WATERR;
    BREAKFAST_FREE;
    SWIMMING_POOL;
}

class User {

    int userId;
    String name;
    Date dateOfBirth;
    String mobNo;
    String emailId;
    String sex;
}

// For the room in any hotel
class Room {

    int roomId; // roomNo
    int hotelId;
    RoomType roomType;
    RoomStatus roomStatus;
}

class Hotel {

    int hotelId;
    String hotelName;
    Adress adress;

    // hotel contains the list of rooms
    List<Room> rooms;
    float rating;
    Facilities facilities;
}

// a new booking is created for each booking
// done by any user
class Booking {
    int bookingId;
    int userId;
    int hotelId;

    // We are assuming that in a single
    // booking we can book only the rooms
    // of a single hotel
    List<Rooms> bookedRooms;

    int amount;
    PaymentStatus status_of_payment;
    Date bookingTime;
    Duration duration;
}

class Address {

    String city;
    String pinCode;
    String state;
    String streetNo;
    String landmark;
}

class Duration {

    Date from;
    Date to;

}

class Facilities {

    List<Facility> facilitiesList;
}
Copy Code
Let me explain about the classes and the relationships among themselves.



The enums defined here are self-explanatory. The classes User, Room, and Address also self-explanatory. The class Facilities contains a list of facilities (enum) that the hotel provides. We can add more facilities in the Facility enum if required. The duration class has two attributes “from” and “to”, which is obvious.

Now, the class “Hotel” contains:
1. List of rooms (Room class) // this is the list of rooms the hotel has
2. Address class // its address
3. Facilities class // the facilities it has

The class “Booking” contains:
1. User // information about the
2. Hotel // Information about the hotel
3. List of rooms
4. PaymentStatus etc.
```

