# DBMS Architecture
## Interfaces to access DBMS
1. **_DDL - Data Definition Language_**: i.e. Creating tables, indices, manipulating database schema
2. **_DML - Data Manipulation Language_**: i.e. Queries, Updating table contents

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/DBMS-components.png?raw=true)

**_System Catalogue_** - **_Contains metadata about stored data and schemas_** e.g. names and sizes of files, storage details of files, names and data types of data items, mappings between schemas, constraints, statistical information

**_DDL Compiler_**
- Process schema definitions
- Store schema descriptions in system catalogue

**_Query Compiler_**
- Parses and validates queries
- Compiles queries to internal form (query plan)
- Passes compiled queries to query optimiser

**_Query Optimiser_**
- Rearranges and reorders operations within query plan to eliminates redundancies
- Identifies appropriate algorithms and indexes used to implement operations
- Consults system catalogue for statistical and other information
- Generates executable code

**_Precompiler_**: Extracts DML commands from application programs and sends them to the DML compiler

**_DML Compiler_**: Compiles DML into executable code that can be sent to the runtime processor

**_Runtime Database Processor_**
- Executes privileged commands
- Executes query plans from the query optimiser
- Accesses database through stored data manager

**_Stored Data Manager_**
- Controls access to information on disc, using basic operating system services
- Manages shared buffer pool (available main memory used for transferring data to and from disc)


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU5NDMyOTY2NV19
-->