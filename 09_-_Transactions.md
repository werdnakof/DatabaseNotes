# Transactions and Concurrency

### Transaction Processing

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

Hence, a valid Transaction requires both **Serialisbility** and **Atomicity**

### Transaction Problems

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/incorrect-summary-problem.png?raw=true)

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/lost-update-problem.png?raw=true)

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/temporary-update.png?raw=true)

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/unrepeatable-problem.png?raw=true)


### Transaction Lifecycle

- consists of multiple stages
    - BEGIN_TRANSACTION
    - READ, WRITE
    - END_TRANSACTION
    - COMMIT_TRANSACTION
    - ROLLBACK (or ABORT)
    
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/transaction-life-cycle.png?raw=true)
   
### ACID

Atomic, Consistent, Isolation, durability

### Schedules and Serialisability

A **Schedule** S of N transactions is:
- an **ordering** of operations of transactions
- subject to constraint that for each transaction T within S
- operations in T must appear in the same order in S as they do in T

Operations in schedule are **conflicting** if
- belong to different transactions
- accessing the same data item
- at least one of the operations is a write()

A Schedule is **Serial** if
- for each transaction T, all operations are executed consecutively
- otherwise it is **non-serial**

A Schedule is **Serialisable** if:
- it is **equivalent** to some serial schedule of the same N transactions in S

Multiple schedules are **result equivalent** if they achieve the same final state

Multiple schedules are **conflict equivalent** if order of any 
two conflicting operations is the same in both schedules

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/Serial-Schedule-1.png?raw=true)

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/Serial-Schedule-2.png?raw=true)

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/Non-Serial-but-Serialisable.png?raw=true)

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/Non-Serial-and-Non-Serialisable.png?raw=true)

### Locking (2PL)

### Timestamps