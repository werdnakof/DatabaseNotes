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

**Inter-query parallelism**: different queries excute on different processors
**intra-query parallelism**: 
	- **intra-operator (horizontal, JOINs)**: 
		- operators decomposed into independent operators instances, which perform the same operation on different subsets of data
		- e.g. JOIN, the two sub relations can be carried out seperately before join
	- **inter-operator (vertical)**: operations overlapped
		- pipeline data from one stage to the next without materialisation
		- e.g. SELECT -> PROJECT
	- **bushy (independent)**: subtrees in query plan excuted concurrently

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA3NDcxMzAxNSwxNjAzNjIwMDY5LDc1OT
UwNjIwMSwyODA0NDE0NDgsMTU1NDE1Mjk2LC0xODU2Nzg5MTM0
LC0zNzM2MTE5MjksLTE4NTY1Njc0NywxNDk4NDk5ODA2XX0=
-->