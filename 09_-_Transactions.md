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

![incorrect-summary-problem](images/incorrect-summary-problem.jpg)
![lost-update-problem](images/lost-update-problem.jpg)

### Transaction Lifecycle

- consists of multiple stages
    - BEGIN_TRANSACTION
    - READ, WRITE
    - END_TRANSACTION
    - COMMIT_TRANSACTION
    - ROLLBACK (or ABORT)
    
   
### ACID

### Schedules and Serialisability

### Locking (2PL)

### Timestamps
