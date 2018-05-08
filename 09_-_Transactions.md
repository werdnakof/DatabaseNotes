# Transactions and Concurrency

### ACID

Transaction is a set of operations/changes used to achieve ACID behaviour.

**Atomicity** guarantees that a **whole** transaction occurs, or none of it does; you can do complex operations as one single unit i.e. a transaction, all or nothing, and a crash, power failure, error, or anything else won't allow you to be in a state in which allow partial changes.

**Consistency** means that you guarantee that your data will be consistent; none of the constraints you have on related data will ever be violated.

**Isolation** means that one transaction cannot read data from another transaction that is not yet completed. If 2 transactions are executing concurrently, each one will see the _world_ as if they were executing sequentially. If one needs to read data that is written by another, it will have to wait until the other is finished.

This also means that transactions require **concurrency locking mechanisms**. They guarantee correctness even when being _interleaved_. Isolation brings us the benefit of hiding uncommitted state changes from the outside world, as failing transactions shouldn’t ever corrupt the state of the system.

**Durability** means that:
1. a transaction must guarantee that **all of the changes** have been recorded permanently to disk, before marking it as complete.
2. The changes are recorded in a persisted transaction log. If a system crash or a power outage occurs, then all unfinished committed transactions may be replayed.

### Transaction Processing Terminologies

**Concurrency**
- Databases nowadays are designed to be used by multiple users concurrently
- Stored data items can be accessed concurrently

**Transaction**
- a logical unit of work that apply changes to the contents of a database
- it groups a bunch of database operations to be executed together

**Serial** vs **Serialisable**
- (ideal) run transactions serially -> no overlap
- (in practise) parallelism is required, too many transactions need to be executed
- Serialisable: behave like serial, but executed concurrently

**Atomicity**
- Transaction need to be atomic
- either all operations are executed, or none

Hence, a valid Ttransaction requires both **Serialisbility** and **Atomicity**

### Transaction Problems
**_Incorrect Summary Problem_**
| T1        | T2         | X_T1 | Y_T1 | S  | X_T2 | Y_T2 | Xdisk | Ydisk |
|-----------|------------|------|------|----|------|------|-------|-------|
|           |            |      |      |    |      |      | 20    | 50    |
| S = 0     |            |      |      | 0  |      |      | 20    | 50    |
|           | read(X)    |      |      | 0  | 20   |      | 20    | 50    |
|           | X = X - 10 |      |      | 0  | 10   |      | 20    | 50    |
|           | write(X)   |      |      | 0  | 10   |      | 10    | 50    |
| read(X)   |            | 10   |      | 0  | 10   |      | 10    | 50    |
| S = S + X |            | 10   |      | 10 | 10   |      | 10    | 50    |
| read(Y)   |            | 10   | 50   | 10 | 10   |      | 10    | 50    |
| S = S + Y |            | 10   | 50   | 60 | 10   |      | 10    | 50    |
|           | read(Y)    | 10   | 50   | 60 | 10   | 50   | 10    | 50    |
|           | Y = Y + 10 | 10   | 50   | 60 | 10   | 60   | 10    | 50    |
|           | write(Y)   | 10   | 50   | 60 | 10   | 60   | 10    | 60    |

**_Lost Update Problem_**
| T1         | T2        | X_T1 | Y_T1 | X_T2 | Y_T2 | Xdisk | Ydisk |
|------------|-----------|------|------|------|------|-------|-------|
|            |           |      |      |      |      | 20    | 50    |
| read(X)    |           | 20   |      |      |      | 20    | 50    |
| X = X - 10 |           | 10   |      |      |      | 20    | 50    |
|            | read(X)   | 10   |      | 20   |      | 20    | 50    |
|            | X = X + 5 | 10   |      | 25   |      | 20    | 50    |
| write(X)   |           | 10   |      | 25   |      | 10    | 50    |
| read(Y)    |           | 10   | 50   | 25   |      | 10    | 50    |
|            | write(X)  | 10   | 50   | 25   |      | 25    | 50    |
| Y = Y + 10 |           | 10   | 60   | 25   |      | 25    | 50    |
| write(Y)   |           | 10   | 60   | 25   |      | 25    | 60    |

**_Temporary Update Problem_**
| T1         | T2        | X_T1 | Y_T1 | X_T2 | Y_T2 | Xdisk | Ydisk |
|------------|-----------|------|------|------|------|-------|-------|
|            |           |      |      |      |      | 20    | 50    |
| read(X)    |           | 20   |      |      |      | 20    | 50    |
| X = X - 10 |           | 10   |      |      |      | 20    | 50    |
| write(X)   |           | 10   |      |      |      | 10    | 50    |
|            | read(X)   | 10   |      | 10   |      | 10    | 50    |
|            | X = X + 5 | 10   |      | 15   |      | 10    | 50    |
|            | write(X)  | 10   |      | 15   |      | 15    | 50    |
| read(Y)    |           | 10   | 50   | 15   |      | 15    | 50    |
| **crash!** |           |      |      |      |      |       |       |
| rollback   |           |      |      |      |      | 20    | 50    |

**_Uprepeatable Read Problem_**
| T1      | T2         |
|---------|------------|
| read(X) |            |
|         | read(X)    |
|         | X = X - 10 |
|         | write(X)   |
| read(X) |            |


### Transaction Lifecycle
Consists of multiple stages
   - BEGIN_TRANSACTION
   - READ, WRITE
   - END_TRANSACTION
   - COMMIT_TRANSACTION
   - ROLLBACK (or ABORT)
    
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/transaction-life-cycle.png?raw=true)

### Schedules and Serialisability

A **Schedule** S of N transactions is:
- an **ordering** of operations of transactions
- subject to constraint that for each transaction T within S
- operations in T must appear in the same order in S as they do in T

Operations in schedule are **conflicting** if
- belong to different transactions
- accessing the same data item (read or write)
- at least one of the operations is a write()

A Schedule is **Serial** if
- for each transaction T, all operations are executed consecutively
- otherwise it is **non-serial**

A Schedule is **Serialisable** if:
- it is **equivalent** to some serial schedule of the same N transactions in S

Multiple schedules are **result equivalent** if they achieve the same final state

Multiple schedules are **conflict equivalent** if order of any 
two conflicting operations is the same in both schedules

Serial-Schedule

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/Serial-Schedule-1.png?raw=true)

Serial-Schedule

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/Serial-Schedule-2.png?raw=true)

Non-Serial-but-Serialisable

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/Non-Serial-but-Serialisable.png?raw=true)

Non-Serial-and-Non-Serialisable

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/Non-Serial-and-Non-Serialisable.png?raw=true)

### Locking

**Shared Lock** - read only
**Exclusive Lock** - read and write

Lock Operations:
**lock-shared(X)** - attempt to acquire to a shared lock on X
**lock-exclusive(X)** - attempt to acquire an exclusive lock on X
**unlock(X)** - relinquish all locks on X

Outcome of lock operations on a data item:

|            |           | (Lock    | Requested)|
|------------|-----------|----------|-----------|
|            |           | Shared   | Exclusive |
| (lock mode | Shared    | Granted  | Wait      |
| in X)      | Exclusive | Wait     | Wait      |


Locking Rules:

1. Must issue lock-shared(X) or lock-exclusive(X) before a read(X) operation
2. Must issue lock-exclusive(X) before a write(X) operation
3. Must issue unlock(X) after all read(X) and write(X) operations are completed
4. Cannot issue lock-shared(X) if already holding a lock on X
5. Cannot issue lock-exclusive(X) if already holding a lock on X
6. Cannot issue unlock(X) unless holding a lock on X

Lock Conversion:

Rules 4 and 5 may be relaxed in order to allow lock conversion
• A lock-shared(X) may be upgraded to a lock- exclusive(X)
• A lock-exclusive(X) may be downgraded to a lock-shared(X)

**S1**

T1		           T2				XT1	YT1	XT2	YT2	Xd	Yd
                                                    20	50
lock-shared(Y)			      						20	50
read(Y)					        		50			20	50
unlock(Y)				       			50			20	50
lock-exclusive(X)	       				50			20	50
read(X)					        	20	50			20	50
X := X + Y				       		70	50			20	50
write(X)				       		70	50			70	50
unlock(X)				       		70	50			70	50
                  lock-shared(X)	70	50	        70	50
                  read(X)			70	50	70		70	50
                  unlock(X)		    70	50	70		70	50
                  lock-exclusive(Y)	70	50	70		70	50
                  read(Y)			70	50	70	50	70	50
                  Y := Y + X		70	50	70	120	70	50
                  write(Y)			70	50	70	120	70	120
                  unlock(Y)			70	50	20	120	70	120

**S2**

T1		          T2				XT1	YT1	XT2	YT2	Xd	Yd
                                                    20	50
                  lock-shared(X)	    			20	50
                  read(X)			    	20		20	50
                  unlock(X)		    		20		20	50
                  lock-exclusive(Y)	    	20		20	50
                  read(Y)					20	50	20	50
                  Y := Y + X				20	70	20	50
                  write(Y)		    		20	70	20	70
                  unlock(Y)			    	20	70	20	70
lock-shared(Y)						    	20	70	20	70
read(Y)					        		70	20	70	20	70
unlock(Y)						    	70	20	70	20	70
lock-exclusive(X)						70	20	70	20	70
read(X)				        		20	70	20	70	20	70
X := X + Y					    	90	70	20	70	20	70
write(X)			    			90	70	20	70	90	70
unlock(X)				    		90	70	20	70	90	70


Serial Schedules
After T1;T2, we have: X=70, Y=120
After T2;T1, we have: X=90, Y=70

non-serial Schedules

T1		             T2				XT1	YT1	XT2	YT2	Xd	Yd
								             		20	50
lock-shared(Y)					     				20	50
read(Y)							        50			20	50
unlock(Y)					    	    50			20	50
		             lock-shared(X)	    50			20	50
		             read(X)		    50	20		20	50
		             unlock(X)		    50	20		20	50
		             lock-exclusive(Y)  50	20		20	50
		             read(Y)		    50	20	50	20	50
		             Y := Y + X 	    50	20	70	20	50
		             write(Y)		    50	20	70	20	70
		             unlock(Y)	        50	20	70	20	70
lock-exclusive(X)					    50	20	70	20	70
read(X)						        20	50	20	70	20	70
X := X + Y			    			70	50	20	70	20	70
write(X)				    		70	50	20	70	70	70
unlock(X)					    	70	50	20	70	70	70

For a non-serial schedule to be result equivalent, 
- its final state must equal to a serial schedule
- same operations

Locking by itself its not enough to achieve serialisability

We need extra rules:
- all locks operations precede the first unlock operation in a transaction
- locks are only release after a transaction commits or aborts

### Two-phase locking

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/two-phrase-locking.png?raw=true)

| T1:               | T2:               |
|-------------------|-------------------|
| lock-shared(Y)    | lock-shared(X)    |
| read(Y)           | read(X)           |
| lock-exclusive(X) | lock-exclusive(Y) |
| unlock(Y)         | unlock(X)         |
| read(X)           | read(Y)           |
| X := X + Y        | Y := X + Y        |
| write(X)          | write(Y)          |
| unlock(X)         | unlock(Y)         |


**Dead Lock**

Deadlock exists when 2+ transactions are 
waiting for each other to release a lock on an item

Conditions must be satisfied for deadlock to occur:
- Concurrency: two processes claim exclusive control of one resource
- Hold: one process continues to hold exclusively controlled resources 
  until its need is satisfied
- Wait: processes wait in queues for additional resources while holding resource 
  already allocated
- Mutual dependency

Deadlock prevention
- Every transaction locks all items it needs in advance; if an item cannot be obtained, no items are locked
- Transactions updating the same resources are not allowed to execute concurrently

**Deadlock detection**

- **Wait-for graph (WFG)**
    A Directed graph contains:
    - A vertex for each transaction that is currently executing
    - An edge from T1 to T2 if T1 is waiting to lock an item that is currently locked by T2
    
    deadlock exists if WFG contains a cycle
    
- **Timeouts**
    
    If a transaction waits for a resource for longer than a
    given period (the timeout), the system assumes that
    the transaction is deadlocked and aborts it

### Timestamps

An alternative to locks – deadlock cannot occur

Timestamps are unique identifiers for transactions
- the transaction start time: TS(T)
- For each resource X, there is:
    - A read timestamp, read-TS(X)
    - A write timestamp, write-TS(X)
- read-TS(X) and write-TS(X) are set to the timestamp
  of the most recent corresponding transaction that
  accessed resource X
  
Transactions are ordered based on their timestamps
- Schedule is serialisable
- Equivalent serial schedule has the transactions in order of
  their timestamps

For each resource accessing by conflicting
operations, the order in which the resource is
accessed must not violate the serialisability order

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/timestamp-ordering-1.png?raw=true)

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/timestamp-ordering-2.png?raw=true)

**Timestamp Ordering: write(X)**

if read-TS(X) > TS(T) or write-TS(X) > TS(T)
then
    abort and rollback T and reject operation
else
    execute write(X)
    set write-TS(X) to TS(T)

**Timestamp Ordering: read(X)**

if write-TS(X) > TS(T)
then
    abort and rollback T and reject operation
else
    execute read(X)
    set read-TS(X) to max(TS(T), read-TS(X))

### Granularity of data items

What should be locked?

– Record
– Field value of record
– Disc block
– File
– Database

**Coarser** granularity gives lower degree of concurrency

**Finer** granularity gives higher overhead
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA2NTI3MDEwMiwtOTcyMTAxNzQxLDE5ND
A2NzA2NTJdfQ==
-->