Interface
# DDL - Data Definition Language
- I.e. Creating tables, indices, manipulating database schema
# DML - Data Manipulation Language
- I.e. Queries, Updating table contents

# System Catalogue
- Contains metadata about stored data and schemas
- E.g. names and sizes of files, storage details of files, names and data types of data items, mappings between schemas, constraints, statistical information

DDL Compiler
- process schema definitions
- store schema descriptions in system catalogue

Query compiler
- Parses and validates queries
- Compiles queries to internal form (query plan)
- Passes compiled queries to query optimiser

Query Optimiser
- Rearranges and reorders operations within query plan
- Eliminates redundancies
- Identifies appropriate algorithms and indexes used to implement operations
- Consults system catalogue for statistical and other information
- Generates executable code

Precompiler
- Extracts DML commands from application programs and sends them to the DML compiler

DML Compiler
- Compiles DML into executable code that can be sent to the runtime
processor

Runtime Database Processor
• Executes privileged commands
• Executes query plans from the query optimiser
• Accesses database through stored data manager

Stored Data Manager
- Controls access to information on disc, using basic operating system services
- Manages shared buffer pool (available main memory used for transferring data to and from disc)


