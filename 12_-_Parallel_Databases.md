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

**Exchange Operator**: a type of operator which is inserted in-between steps of a query to 1) pipeline results between inter/intra-operations 2) coordinate streams. 
Exchange operator assists inter operations by splitting up the 
This provides supports to both vertical and horizontal. For example:
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














> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTkwOTA4MzQwOSwzNzc2NjQ1NDAsLTQ2ND
Y5MjgwNyw1ODk5MTQ3MzYsMTQ1MDQ2NDA5Miw3NTYyNTAzMjUs
MTU0NjcwMTkzMywxODM3MDQyMzQzLC01NzgwMDI4NCwxNDc5OT
I0MTI1LDMxMDY5MTU2NSw1NzA2NzY4ODYsLTY4MjI1MDA1Mywt
MTY2MjA1MzgyMywxNjAzNjIwMDY5LDc1OTUwNjIwMSwyODA0ND
E0NDgsMTU1NDE1Mjk2LC0xODU2Nzg5MTM0LC0zNzM2MTE5Mjld
fQ==
-->