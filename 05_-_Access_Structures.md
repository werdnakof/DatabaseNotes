**Goal: Reduce the number of blocks accessed**

## Index basics
* Data file / file: a collection of blocks
* Relations: stored in files
* blocks: contain multiple tuples and their relationships
* Tuple: a single row in a relational database.

## Sequential Files
* Tuples are sorted by their primary key, and distributed to blocks following this order
* Free space is usually left in each block for further insertions

## Indexing Methods

### Dense Indexes
* Sequence of Blocks: containing only indexes 
  (i.e. keys and pointers to a specific tuple in a Data Block)
* These Index blocks are smaller in size
* Follow the same order as the tuples spread across blocks
* Each block will contain a range e.g. range 50, and keys are 10, 21, 29, 35, 45, 50 i.e. tuple's primary key
* When querying 45 or 56, we don't have to scan all tuples, saving time
* Binary search can be used

### Sparse indexes
* Also a sequence of blocks! Just like dense indexes
* However, each Index block points to a starting block of a specific range
* E.g. Sparse Index Block has key values 20, 40, 60, 80 of range 20
* key 20 will point to Data Block containing tuples (21, 29, 35)
* Longer to find Data Block than dense index, but **save more space**

### Tradeoff between Sparse Vs. Dense
Sparse:
- less index space per record can keep more of index in memory
- better for insertions, requiring less frequent index updates

Dense:
- Can tell if a record exists without accessing file
- needed for secondary indexes

### Multi-level indexes
* Sparse Index on top of dense index
* Each Sparse Index entry points to a Dense Index Block
* Sparse Index pointer is called **Block Pointers** - points to starting Dense Index Block
* Dense Index pointer is called **Record Pointers** - points to Data Block and the offset from that block
* Sparse Index cannot tel whether an actual record exists, Dense index can
* Care required to maintain both dense and sparse indicies during insertion and deletion.

**Duplicate Primary Keys Allowed (with care)**
* Dense Index still points to the starting tuple in the Data Block, follow by tuples of same primary key
* Sparse Index needs to points to block containing the **_first appearance of a primary key_**, meaning some block will not be pointed.
  if **_same primary key spans across multiple blocks_**, care must be taken when deleting tuple in Data Block for Sparse Index! 
* If a record is inserted to a full block, then an overflow Data Block will be created, and will be referenced by the full Data Block

## Secondary Indexes

If **unsorted keys in data blocks**: a sorted Dense Index must first be built, then Sparse Index
If **duplicate keys in data blocks**:
1. allow duplicate keys, but excess disk space, excess search time
2. drop repeated keys in secondary indexes, but still keeping pointers to repeated records
3. Each data record has pointer to another repeated data record (linked list)
    Dense Index can simply point to the first occurrence of a record primary key
    Problem: need to add extra reference/field to other records of same primary key, less space
4. Indirection via adding a Bucket Index layer, a continuous number of blocks containing primary key in a sorted order, then use a dense index to point to the first occurence of a primary key within the Block Index

# B-tree
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/b+tree.png?raw=true)
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
eyJoaXN0b3J5IjpbLTcwMTQ2MDgyMywtNDg4OTM5MDYsMTIwNj
UwOTI5MF19
-->