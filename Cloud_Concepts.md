Questions:
```
Know the key differences between cloud computing and previous generations of distributed systems.
Design MapReduce programs for a variety of problems.
Know how Hadoop schedules jobs.

Why are gossip and epidemic protocols fast and reliable?
What is the most efficient way for cloud computing systems to detect failures of servers?
How is grid computing related to cloud computing?

What is the difference between how Napster clients and Gnutella clients search for files?
What is the difference between Gnutella and FastTrack?
What is BitTorrent’s tit for tat mechanism?
What is consistent hashing?
Why are DHTs efficient in searching?
How does Chord route queries?
How does Pastry route queries?
How does Kelips route queries?
What is churn in P2P systems?
How does Chord maintain correct neighbors in spite of failures and churn?


Why are key-value/NoSQL systems popular today?
How does Cassandra make writes fast?
How does Cassandra handle failures?
What is the CAP theorem?
What is eventual consistency?
What is a quorum?
What are the different consistency levels in Cassandra?
How do snitches work in Cassandra?
Why is time synchronization hard in asynchronous systems?
How can you reduce the error while synchronizing time across two machines over a network?
How does HBase ensure consistency?
What is Lamport causality?
Can you assign Lamport timestamps to a run?
Can you assign vector timestamps to a run?

```


- Cloud: a Cloud consists of a lot of storage resources with compute cycles located nearby
- Data center: a single site cloud: 
  - servers grouped into rack
  - a rack is a unit of several servers which typically sahre the same power and a top of the rack switch
  - this top of the rack switches are connected via a network topology
  
- Doubling period: same money
  - storage: 12 months
  - bandwidth: 9m; Tbps now
  - CPU: 18m (Moore's law)
  
- Modern cloud features:
  - Massive scale
  - on-demand access: pay as you go
  - Data intensive nature: I/O focus
  - New Cloud Programming Paradigms: MapReduce, NoSQL

- Operating System
  - UI to hardware(device drivers)
  - provide abstraction(process, file system)
  - Resource manager(scheduler)
  - Means of communication(networking)

- A distributed system is a collection of entities, each of which is autonomous, programmable, asynchronous and failure-prone, and which communicate through an unreiable communication medium.
  - challenges
    - Failure: normal
    - Scalability: 1000s machine/ TB data
    - Asynchrony: clock skew and clock drift
    - Concurrency: 1000s machine to access same data

- MapReduce
  - map: processes each record sequentially and independently
  - reduce: processes set of all records in batches
    - by partitioning keys
  - barrier between the 2 phrase
  - Fault-Tolerance
    - node heartbeat
    - Straggler: slowest slows entire; Speculative Execution: replicating slow taks, complete task when first copy finish
    - Locality: same machine > same rack > anywhere

- Gossip
  - Muliticast problem: Group of node(with a piece of info) = proccess
    - problems: sender -> protocol -> nodes 
      - Nodes may crash
      - packets maybe dropped
      - 1000's of nodes
    - approach
      - Centralized: one to many: simplest: UDP/TCP packets
      - Tree-Based: IPmulticast, SRM, RMTP, TRAM, TMTP; Tree setup and maintaince
        - a spanning tree aming nodes; use either ACKs or Negative ack(NAKs) to repair multicasts not received
        - SRM(Scalable Reliable Multicast): use NAKs, add random delays, uses exponential backoff(double wait time everytime) to avoid NAK storms
        - RMTP(Reliable Multicast Transport Protocol): ACKs, only sent to designated receivers, which then re-transmit missing multicasts
      - Third: Epidemic, Infected nodes select B(2) random targets(gossip fan out nodes) -> Gossip msg(UDP) -> nodes 
   - Model:
     - Push gossip
       - Once you have a multicast msg, you start gossiping about it
       - Multiple msg? Gossip a random subset of them, or recently-received onces, or higher priority ones
     - Pull
       - Periodically poll a few randomly selected processes for new multicast msg that you haven't received
       - Get those msg
       - faster in the second half of gossip spread
     - Hybrid
   - Analysis:
     - low latency: clog(n) rounds
     - reliability: most nodes receive msg
     - lightweight: each node transmits <= cblog(n) msg
     - Fault-tolerance
       - package loss
       - node failure
   - Impl:
     - EC2, S3: exchange msg
     - Cassandra: maintain membership lists
     - NNTP: news

- Membership
  - failure detector & dissemination mechanism
    - rate of failure of 1 machine: 120 months => MTTF(mean time to failure)
  - Process 'group'-based system
    - Cloud/DataCenter
    - Replicated servers(P2P)
    - Distributed DB
  - Crash-stop/Fail-stop process failure
  - Virtual synchrony(strong)/gossip(weakly) paradigm
  - Property:
    - **Completeness**: each failure is detected, => 100%
    - Accuracy: no mistake detection
    - Spead: time to first detection
    - Scale: equal load, msg load
  - Centralized Heartbeating: hotspot
  - Ring Heartbeating: neighbors
  - All to All Heartbeating: send to all, mark if timeout of wait
    - Gossip-style heartbeating:
      - Nodes periodically gossip their membership list(address, heartbeat counter, Time(local)), merge latest
      - On receipt, the local membership list is updated
      - thredhold: no increased for more tahn T sec, mark as failed; clean up membership entry time(ping-pong prob);
  - Swim Failure Detector
    - During protocol period(T time unit), random pick node to ping, gert ACK back then fine
    - If no ACK, ping indirect node to send ping to that node agaion <= second chance
    - Time-bound completeness: round robin pinging => permutate
  - Infection-style dissemination
- Grid computing: RAMs model, a DAGs of jobs
  - HPC(High Computation-intensive computing), often federated
  - diff sites run same task
  - 5 stages
    - init
    - stage in
    - execute 
    - stage out
    - publish
 - Computation intensive, so massively parellel
 - Globus Protocol: external allocation, wide area transfer; => Single sign-on
 - intra-site: HTCondor
  
- P2P systems
  - uses:
    - First distributed system on scalability
    - key-value stores uses P2P chord hashing
  - Napster
    - client as peer, quartlly
    - centralized server store meta only, <filename, id_address, port> tuples
  - Gnutella
    - client as a server, store nearby peers info => overlaid graph
    - payload: TTL decrement per node; <<7/10
    - QueryHit routed back from the path
    - use HTTP
    - push: direct or path(behind firewall)
    - ping/pong: 50% traffic; membership
    - 70% freeload: only download
  - Fasttrack:(kazaa)
    - healthier <- supernode, promoted by reputaion(upload/connectivity)
  - BitTorrent
    - incentive for node to provide good download rate
    - tracker(receives, heartbeat)(torrent)
    - choking: limit num of neighbors(best) to upload <= 5
  - DHT(distributed): above systems sort of
    - performance concerns
      - Load balancing
      - Fault-tolerance
      - Efficiency of lookup and inserts
      - Locality: closer by
  - P2P with Provable Properties
  - chord
    - log(n)
    - Consistent Hashing: SHA-1 => 160bit str, truncated to m b
    - peer in ring: know successor's IP
    - Finer table: WFB
    - rounting: #
  - Pastry
    - Routing tables based prefix mathcing: prefix for distance
    - O(logN) lookup
  - Kelips
    - O(1) lookup
    - k "affinity groups"
  
- NoSQL(Not only SQL)
  - workloads
    - Data: large and unstructured
    - Lots of randoim reads and writes
    - Sometimes write-heavy
    - Foreigh keys rarely needed
    - Join infrequent
  - need:
    - speed
    - avoid SPoF
    - low TCO(total cost of opt)
    - fewer sys admin
    - incremental scalability
      - scale up: more powerful machine
      - scale out: more COTS machines
  - API:
    - get
    - put
  - Tables
    - column families: Cassandra
    - Table: HBase
    - Collection: MongoDB
  - Column oriented
    - RDBMS: store a row together
    - NoSQL a column together WFB
    - fast range search within a col
      - all blog_id updated within past month => search in last_updated col, fetch blog_id <= no ned fetch other col
  - Cassandra
    - uses a Ring-based DHT but without finger tables or routing
    - Key: server mapping is the partiioner <= coordinator
    - Data replacement
      - Simple Strategy
        - Random Paritioner: chord-like hash partitioning
        - Byte Ordered: assign ranges of keys to servers <= range queries
      - Network Topo
        - 2/3 replica per DC
        - per DC: first replica placed according to partitioner; then go clockwise until hit a diff rack
    - Maps: IPs to racks and DCs. Configured in cassandra.yaml file
    - Write:
      - lock free and fast
      - Client sends write to one coordinator node(if per key, ensure serilaized) in cluster
      - Coordinator uses Partitioner to send query to all replica nodes responsible for key
      - When X replicas respond, coordinator returns ans ack to the client
      - always writable: Hinted handoff machanism
        - If any replica is down, coordinator writes to all other and keeps the write locally untill down replica back up
        - When all replica down, the coordinator buffer writes
        - one ring per DC, Zookeeper, Paxos
      - write at replica
        - log it in disk commit log(failure recovery)
        - make change to Memtable(in-memory cache, write-back(faster than write through))
        - when memtabke is full or old, flush to disk
          - data fiile: SSTable(Sorted String Table)
            - periodically compaction
          - Index file: SSTable of key and its position in data sstable
          - Bloom filter(for efficient search): not in
            - compact way of representing a set of items
            - possibly false positive, never false negative
            - key => multiple hash function -> one large Bit Map as 1
      - delete: don't del right away
        - mark tombstone
        - compaction will do delete eventually
      - Read
        - Coordinator contact X replicas
        - return latest-timestamped value
        - also init read repair to eventually consistent
      - Memebership
        - any node can be coordnator
        - all contains list
      - Suspicion mechanisams to adaptively set the timeout based on underlying network and failure behavior
      - In practice, PHI = 5 => 10-15 sec
      - Speed: > 50G, avg:
        - MySQL: r 350ms, w 300ms
        - Cassandra: r 15ms, w 0.12ms 
      - CAP
        - Cassandra: Eventual(weak) consistency, Availabliity, Partition-tolerance
          - BASE: Basically Available Soft-state(memeory) Eventual Consistency
        - RDBMS: Strong consistency over availablity partition
          - ACID
      - Consistency Level:
        - ANY: any server: fastest
        - ALL: all replica: string consistency
        - ONE: at least one replica,  cannot tolerate a failure
        - QUORUM: replica across DC
          - = majority, >50%
          - faster than ALL, still strong consistency
          - R = read replica count, W = write replica count: W+R > N; W > N/2
            - W1R1: very few writes and reads
            - W=N, R= 1: read-heavy
            - W/R= N/2 + 1: wrtie heavy
            - W1, R=N; write heavy, mostly one client to write
  - HBase
    - blob-based
    - API:
      - get/put
      - Scan(row range, filter) - range queies
      - MultiPut
    - HLOg: write-ahead log: Write to HLog before writing to MenStore, helps revocer
    - Master cluster-slave cluster
    - Zookeeper to cooridnate among cluster
- CAP
  - Consistency: same data
    - Eventual  Strong
    - models:
      - Per key sequential: all opt have a global order
      - CRDTs: commutated writes,same result
      - Red-blue:
        - blue ops in any order
        - red in same order
      - Strong:
        - Linearizability: instananeously
        - Sequential[Lamport]
        - NewSQL: Spanner
  - Availablity: all time
    - Reads/writes complete reliably and quickly
    - SLA
  - Partition-tolerance
  - Tradeoff
    - Consistency, Availability: RDNMS
    - Partition, Availability: Cassandra, RIAK, Dynamo, Voldemort
    - Partition, Consisitency: HBASE, HyperTable, BigTable, Spanner
- Time and Ordering
  
- Lamport Timestamps
