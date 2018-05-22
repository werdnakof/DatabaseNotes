**Goal: Reduce the number of blocks accessed**

## Index basics
* Data file: a file i.e. a collection of Blocks
* Relations: stored in files
* Blocks: contain multiple tuples and their relationships
* Tuple/Record: a single row in a relational database.

## Sequential Files
* Tuple are sorted by their primary key
* Tuples are distributed to blocks following this order
* Free space is usually left in each block to allow further insertions
* Let's call these blocks - **Data Blocks**

## Sparse Indexes
**Trade-off of Indexing**
> Time of retrieval vs Time to update indexes after modification

## Dense Indexes
* Sequence of Blocks: containing only indexes 
  (i.e. keys and pointers to a specific tuple in a Data Block)
* These Index Blocks are smaller in size
* Follow the same order as the tuples spread across Data Blocks
* Each Index Block will contain a range e.g. range 50, and keys are 10, 21, 29, 35, 45, 50 i.e. tuple's primary key
* When querying 45 or 56, we don't have to scan all tuples, saving time
* A binary search can be used

## Sparse indexes
* Also a sequence of blocks! Just like dense indexes
* However, each Index block points to a starting Data block of a specific range
* E.g. Sparse Index Block has key values 20, 40, 60, 80 of range 20
* key 20 will point to Data Block containing tuples (21, 29, 35)
* Longer to find Data Block than dense index, but **save more space**

## Multi-level indexes
* Sparse Index on top of dense index
* Each Sparse Index entry points to a Dense Index Block
* Sparse Index pointer is called **Block Pointers** - points to starting Dense Index Block
* Dense Index pointer is called **Record Pointers** - points to Data Block and the offset from that block
* Sparse Index pointer uses less space than Dense Index pointer
* Sparse Index requires less update in record insertions
* Sparse Index cannot tel whether an actual record exists, Dense index can

**Duplicate Primary Keys Allowed**

* Dense Index still points to the starting tuple in the Data Block, 
  follow by tuples of same primary key

* Sparse Index needs to points the Data Block containing the first appearance of a primary key.
  This also means some Data Block will not be pointed,
  if they contain the same primary keys as previous Data Block

* Care must be taken when deleting tuple in Data Block for Sparse Index! 

* If a record is inserted to a full Data Block, 
  then an overflow Data Block will be created,
  and will be referenced by the full Data Block,

## Secondary Indexes

If keys are not sorted:

A sorted Dense Index must first be built, then Sparse Index

If repeated keys and not sorted, solutions:

1. Use Dense Index, but drop repeated keys, but keeping pointers to repeated records
   
   Problem: each Dense Index block size are variable

2. Each data record has pointer to another repeated data record (think linked list)

    Dense Index can simply point to the first occurrence of a record primary key

    Problem: need to add extra reference/field to other records of same primary key

3. Make another Bucket Index layer, a continuous number of blocks containing primary key in a sorted order,
   then use a dense index to point to the first occurence of a primary key within the Block Index

# B-tree

Contains:
* each node will occupy a **single block**
* Non-leaf nodes including root node
* root node is kept in memory
* Leaf nodes with pointers to the actual data record
* Each non-leaf node has n keys, each separated by a specific range
* For n keys, every node will have **maximum** n + 1 pointers to the next layer
* E.g. non-leaf node, 120, 150, 180, n = 3, range 20
    * 1st pointer goes to non leaf nodes keys < 120
    * 2nd pointer goes to non leaf nodes 120 <= keys < 150
    * 3rd ... 150 <= keys < 180
    * 4th ... keys >= 180
* leaf node with n keys and n + 1 pointers to data record **and** a pointer to next neighbour leaf node
* e.g. leaf node (150, 156 and 179) will have three pointers + 1 point to leaf node (180, 122, 133)
* **minimum** pointers: 
    * non-leaf: rounded down (n+1)/2 pointers
    * leaf: rounded up (n+1)/2 pointers

Example:

* block size b = 8kb, 8000 bytes
* key length k = 10 bytes
* record length r = 100 bytes
* record pointer p = 6 bytes
* leaf node 
    > (as primary index) **lp**: round up b/r = 80 records
    > (as secondary index) **ls**: round up b-p / k + p = 499 records
* non-leaf
    * **i** entries: b - p / k + p = 499
* expansion factor u = 0.6 
* non-leaf node initially points to:
> i * u blocks
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTExOTM4OTAxMzYsMTIwNjUwOTI5MF19
-->