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

**intra-query parallelism**:  involves having more than one CPU handle a single request simultaneously, so that portions of the query are computed in parallel on multi-processor hardware. Processing of these portions is handled by the Exchange algorithm. See  [Exchange algorithm (Exchange)](http://dcx.sybase.com/1200/en/dbusage/queryopt-exchange.html).

Intra-query parallelism can benefit a workload where the number of simultaneously-executing queries is usually less than the number of available processors. The maximum degree of parallelism is controlled by the setting of the max_query_tasks option. See  [max_query_tasks option](http://dcx.sybase.com/1200/en/dbadmin/dboptions-s-5481804.html).

- **intra-operator (horizontal, JOINs)**: 
	- operator split into independent operators instances, 
	  which perform the operation on different subsets of data
	- e.g. JOIN, the two sub relations can be carried out seperately before join
- **inter-operator (vertical)**: operations overlapped
	- pipeline data from one stage to the next without materialisation
	- e.g. SELECT -> PROJECT -> ... (each stage can be excuted in different process)
- **bushy (independent)**: 
	- subtrees in query plan excuted concurrently

### Intra-query operator parallelism
 ![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/intra-query.png?raw=true)
 - To achieve this sort of parallelism, the data needs to be partitioned across servers.
 - e.g. a RELATION containing 1000 tuples is spread across 10 nodes, the time to search for a tuple has been reduced by a factor of 10, given we know which node to access beforehand (see next bullet point)
 - Choice of partition types affords different parallel query processing approaches:
	 1. **Range partition** (e.g. each partition contains a specific range e.g. a-h, i-p or 1-25, 26-50)
	 2. **Hash partition** (e.g. hash a key to access by index, but we don't know where tuple will be in which partition)
	 3. **schema partition** (by tables)

- Require **rebalancing data** in partitions to maintain efficiency

### Data Shipping
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/data-shipping.png?raw=true)
$t_{1-4}$ are the partitions of the database

Assume we have 10000 tuples for a relation
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/data-shipping-2.png?raw=true)
### Query Shipping

We try to push as much of the 

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTQ1MDQ2NDA5Miw3NTYyNTAzMjUsMTU0Nj
cwMTkzMywxODM3MDQyMzQzLC01NzgwMDI4NCwxNDc5OTI0MTI1
LDMxMDY5MTU2NSw1NzA2NzY4ODYsLTY4MjI1MDA1MywtMTY2Mj
A1MzgyMywxNjAzNjIwMDY5LDc1OTUwNjIwMSwyODA0NDE0NDgs
MTU1NDE1Mjk2LC0xODU2Nzg5MTM0LC0zNzM2MTE5MjksLTE4NT
Y1Njc0NywxNDk4NDk5ODA2XX0=
-->