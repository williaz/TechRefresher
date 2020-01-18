
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





- Lamport Timestamps
