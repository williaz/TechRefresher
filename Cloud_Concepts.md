
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
    
    
    
- Lamport Timestamps
