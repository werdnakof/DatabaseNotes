# Parallel Databases

### I/O bottleneck

| Type      | Capacity            | Latency      |
|-----------|---------------------|--------------|
| Registers | 10^1 bytes          | 1 cycle      |
| L1        | 10^4 bytes          | <5 cycles    |
| L2        | 10^5 bytes          | 5-10 cycles  |
| RAM       | 10^9 - 10^10 bytes  | 20-30 cycles |
| Disk      | 10^11 - 10^12 bytes | 10^6 cycles  |

- Time to file access (latency) is high on disks, parallelism can speed up file access on multiple disks
 
### Parallel Architectures

**Shared Memory**
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/shared-memory-arch.png?raw=true)
Properties:
- less complex software
- limited scalability
- single buffer, more consistent
- single database storage

**Shared Disc**
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/shared-disc-arch.png?raw=true)
(S - switch)
- avoid memory bottleck by introducing buffer for each processor
- incoherence issues: same page can be in more than one buffer ("page" means "virtual page" (i.e. a chunk of virtual address space)
- requires global lock

**Share Nothing**
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/shared-nothing-arch.png?raw=true)
- each processor has unique part of the data
- 1 page for each buffer (for each processor), no incoherence
- needs distributed deadlock
- need multiphase commit protocol
- break SQL queries into multi sub queries

(The followings focus on share nothing only)

Challenges with **share nothing**
- data partition
- balancing partitions
- spliting queries
- avoiding distributed deadlock
- concurrency control
- node failure

# Parallel Query Processing

Within in dbms application, there are multiple processes i.e. coordinator process (C) and worker processes (W).

 ![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/dbms-in-nodes.png?raw=true)

and each coordinator can issue query to dbms in other nodes.

**Inter-query parallelism**: involves executing different requests simultaneously on separate CPUs. Each request (task) runs on a single thread and executes on a single processor.

**intra-query parallelism**:  involves having more than one CPU handle a **single request** (not mulitple requests) simultaneously, so that **portions of the query** are computed in parallel on multi-processor hardware. Processing of these portions is handled by the Exchange algorithm. See  [Exchange algorithm (Exchange)](http://dcx.sybase.com/1200/en/dbusage/queryopt-exchange.html).

- **intra-operator (horizontal, JOINs)**: 
	- operator split into independent operators instances, 
	  which perform the operation on different subsets of data on seperate processor
	- e.g. JOIN, the two sub relations can be carried out seperately before join
- **inter-operator (vertical)**: operations overlapped
	- pipeline data from one stage to the next without materialisation
	- e.g. SELECT -> PROJECT -> ... (each stage can be excuted in different process)
- **bushy (independent)**: 
	- subtrees in query plan excuted concurrently

 ![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/intra-query.png?raw=true)
 - To achieve this sort of parallelism, the data needs to be partitioned across servers.
 - e.g. a RELATION containing 1000 tuples is spread across 10 nodes, the time to search for a tuple has been reduced by a factor of 10, given we know which node to access beforehand (see next bullet point)
 - Choice of partition types affords different parallel query processing approaches:
	 1. **Range partition** (e.g. each partition contains a specific range e.g. a-h, i-p or 1-25, 26-50)
	 2. **Hash partition** (e.g. hash a key to access by index, but we don't know where tuple will be in which partition)
	 3. **schema partition** (by tables)

- Require **rebalancing data** in partitions to maintain efficiency

# Data Shipping vs Query Shipping
Assume we have 10000 tuples for a relation
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/data-shipping-2.png?raw=true)
**Query Shipping**: we can simply retrieve all tuples from every single partition, and carry out the necessary operations on all the tuples on the coordinator node. Like this:
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/data-shipping.png?raw=true)
$t_{1-4}$ are the partitions of the database
Very slow, coordinator node needs to filter a lot of tuples.

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/query-shipping.png?raw=true)
**Query Shipping**: we try to push as much of the operators to each partition, shifting all the processing to each partition node (parallel)
(Network traffic is also minimised)
In practise, a mixture of query shipping and data shipping are employed

# Inter Query Paralleism
Allows operators with producer-consumer dependency to be excuted concurrently
- results produced by producer are pipelined directly to consumer
- consumer can start before producer finishes
- no need to materialization

**Exchange Operator**: a type of operator which is inserted in-between steps of a query to 1) pipeline results between inter/intra-operations 2) coordinate streams. This provides supports to both vertical and horizontal. For example:
```
SELECT county, SUM(order_item)
FROM customer, order
WHERE order.customer_id=customer_id
GROUP BY county
ORDER BY SUM(order_item)
```
Without exchange operator, it would look like:
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/exchange-op-1.png?raw=true)
with exchange operator:
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/exchange-op-2.png?raw=true)
So exchange operators sit between **multiple** producers and consumers.

# Bushy Parallelism
todo

# Parallel Query Processing
Types of parallel query operators:
- Enquiry
- Collocated Join
- Directed Join
- Broadcast join
- repartitioned join

# Reliability in Parallel Databases
Uses **Two Phase Commit** (**2PC**)
- similar to **Two Phase Locking** for transactions, we try to enforce ACID properties on all the database nodes.
- C, I and D are already perserved when we use Transations
- A is not, hence we need **2PC**

## Two Phase Commit
2 forms of transactions are introduced:
- **Global Transaction**
- **Local Transaction** which global transaction is decomposed into
Global is managed by the **_coordinator ( C )_** node 
Locals are managed by **_participants ( P )_** nodes

**Phase 1**:
- C sends \<prepare T> to Ps
- Ps response with either \<vote-commit T> or \<vote-abort T>
- C will wait for Ps to response within a time period

**Phase 2**
- if all Ps return \<vote-commit T>, C send \<commit T> to all Ps
- Ps respond with \<ack T> or \<vote-abort T> within timeout period
- if C receive \<ack T> from all, complete/mark end of transaction
- else resend global decision until receving \<ack T> from all

There also be **_logging_** between each exchanges between C and Ps. see below:
**logging without abort**
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/2PC-logging.png?raw=true)
**logging with abort**
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/2PC-logging-abort.png?raw=true)
Each node will have state and state-transistions base on message received.
State diagrams of C and Ps:
C
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/state-diag-coord.png?raw=true)
P
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/state-diag-part.png?raw=true)

## Dealing with Failures/Recoveries

C and Ps can fail during a commit.
- **_Termination protocol_** is invoked when other nodes time out while waiting for the next message from the failed node
- **_Recovery protocol_** is invoked when failed node restarts, it tries to resolve the current state of the commit
- The protocols' behaviours depends on the state the node were when they failed.

### Termination Protocol (Coordinator)
2 timeout scenarios:
1. Timeout in **WAIT** state: **_May abort the Global Transaction_**
	- (C waits for Ps to vote on whether they will _commit_ or _abort_)
	- (Missing vote means C cannot commit a _Global Transaction_)
2. Timeout in **COMMIT**/**ABORT** states: **_Resends global decision to Ps who did not acknowledge_**
	- (C waits for Ps to acknowledge commit or abort)

### Termination Protocol (Participant)
2 timeout scenarios:
1. Timeout in **INITIAL** state: **_Unilaterally abort the transaction_**
	- P waits for a \<prepare T>
	- P may unilaterally abort the transaction after a timeout
	- if \<prepare T> arrives after unilateral abort, either:
		- resend the \<vote-abort T> 
		- ignore (C will then time out on **WAIT**)
2. Timeout in **READY** state: **_Contact other Ps to find out if they know the decision (coorperative protocol)_**
	- P waits for C to receive \<commit T> or \<abort T>
	- P can contact other Ps to find out if they know the decision (coorperative protocol)

### Recovery Protocol (Coordinator)
1. Recover after failure in **INITIAL**: **_Restart commit procedure_**
2. Recover after failure in **WAIT**: **_Restarting commit procedure by sending \<prepare T>_**
	- C has send \<prepare T> to all Ps, but has not received \<vote-commit T> or \<vote-abort T> from all Ps
3. Recover after failure in **COMMIT**/**ABORT**: **_Terminate commit procedure_**
	- if C doesn't receive \<ack> from all Ps

### Recovery Protocol (Participant)
1. Recover after failure in **INITIAL**: **_unilaterally send \<vote-abort T>_**
	- P has not yet voted, hence C could not have reached a decision
2. Recovery after failure in **READY**: **_Cooperative termination protocol_**
	- P has sent \<vote-commit T> to reach **READY** state, but didn't receive \<commit T> or \<abort T> from C (when C has reached a global decision)
3. Recover after failure in **COMMIT** / **ABORT**: **_Resend \<ack> to C_**
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTk2NDU5NjAyLDUyOTMzMjA2Niw2Nzk1OD
U4ODMsLTE3Mjg5ODgwOSwtMjkzOTkzODY2LDM3NzY2NDU0MCwt
NDY0NjkyODA3LDU4OTkxNDczNiwxNDUwNDY0MDkyLDc1NjI1MD
MyNSwxNTQ2NzAxOTMzLDE4MzcwNDIzNDMsLTU3ODAwMjg0LDE0
Nzk5MjQxMjUsMzEwNjkxNTY1LDU3MDY3Njg4NiwtNjgyMjUwMD
UzLC0xNjYyMDUzODIzLDE2MDM2MjAwNjksNzU5NTA2MjAxXX0=

-->