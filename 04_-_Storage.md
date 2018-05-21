## Memory Hierarchy

> Cache > Main Memory > Secondary Storage > Tertiary Storage

* **Cache**: fast, expensive, limited capacity
* **Main Memory (RAM)**: fast, affordable, medium capcity
* **Secondary**: slow, cheap, large capacity
* **Tertiary**: v.slow, v.cheap, v.large capacity

## Disk Structure: 
Platter, surface, head, actuator, cylinder track, geometric sector, Track-Sector, cluster

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/disk-structure.png?raw=true)

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/zone-bit-recording.png?raw=true)

* **Track-sector** are numbered from 0 to n − 1 on a disk. 
  
* The disk can be viewed as an array of sectors - 0 to n − 1 is thus the **_Address Space_** of the drive.

* Track-sector's size changes along with radius

* **_Zone Bit recording_**: vary the number of Track-sector per track, improved overall storage density

* Each Track-Sector is called a **Block**

## Disk Access Costs

* **Cost of Reading Disk** = seek-time + rotational-delay + transfer-time + negligible-costs
    * Seek Time: time for head to move to the track, delayed after disk spinned
    * Transfer time: Block size/transfer rate
    * negligible costs: reading the next block -> skipping inter-block gap, switch track (sequential i/o access is less expensive than random i/o)

* **Cost of Writing Disk** = seek-time + rotational-delay (0.5 rotation) + transfer-time (writing) + rotational-delay (full rotation) + transfer-time (for verification)

* **Cost of Modifying Disk** = seek-time + rotational-delay (0.5 rotation) + transfer-time (reading) + rotational-delay (full rotation) + transfer-time (writing) + rotational-delay (full rotation) + transfer-time (for verification)

## Logical Block Addressing (LBA)
* **Blocks** are located by integer index
* HDD firmware maps LBA addresses to physical location on disk
* Allows remapping of bad blocks (damaged track sector)

**Block size vs access costs trade-off**
- Big blocks reduce access costs i.e. fewer seeks, but increase irrelevant data reads 
- i.e. when trying to read a single record in a block, larger blocks force you to read more data

**Buffer Management**
1. Copy data into **Frames** inside a buffer pool
2. Some of the data in that frame will be 

**Buffer Metadata**

Each **Frame** in the buffer pool contains:
* **Pin Count**: number of users of the Block in that frame
* **Dirty Flag**: 1 if copy in the buffer has been changed, 0 otherwise
* **Access time**: used for LRU replacement
* **Loading time**: used for FIFO replacement
* **Clock flag**: used for Clock replacement

**Requesting a Block**

* If buffer pool already has a frame containing the Block then increment pin count (“pin the Block”)
* Elseif there is an empty frame, then read Block into frame and set pin count to 1
* Else
 
    Choose a frame to be replaced

    If dirty bit on the replacement frame is set
    
        then write Block in replacement frame to disk
    
    endif
    
    read requested Block into replacement frame
  endif
endif

**Buffer Replacement Strategies**

  A frame will not be selected for replacement until its pin count is 0
  If there’s more than one frame with a pin count of 0,
  use a replacement strategy to choose the frame to be replaced
  
  Least Recently Used (LRU): Select the frame with the oldest access time

  First In First Out (FIFO): Select the frame with the oldest loading time

  Clock: Approximation of LRU – cycle through each buffer in turn, if
  a buffer hasn’t been accessed in a full cycle then mark it for
  replacement

Single Buffering Cost

Single buffer time = n(P + R)
P: time to process a Block
R: time to read a Block
n: number of Blocks

Use a pair of buffers:
– While reading a Block and writing into buffer A
– Process Block previously read into buffer B
– After Block read into A, process A and read next Block into B
Double buffer time = R + nP

The Five Minute Rule: Data referenced every five minutes should be memory resident

Assume a Block is accessed every X seconds:

CD = cost if we keep that Block on disk
– $D = cost of disk unit
– I = number of IOs that unit can perform per second
– In X seconds, unit can do XI IOs
– So, CD = $D / XI

CM = cost if we keep that Block in RAM
– $M = cost of 1MB of RAM
– P = number of pages in 1MB RAM
– So CM = $M / P

If CD is smaller than CM,
– keep Block on disk
– else keep in memory
Break even point when CD = CM, or X = ($D P) / (I $M)


## Disk Organisation

Consists of:
* Data Items
* Records
* Blocks
* Files

**Data Item**
* bytes: 8bits i.e. 1 byte
* Integer: 2 bytes
* Real number: 1 bit sign, n bits mantissa, m bits exponent
* Characters: 1 byte, based on encoding scheme e.g. ASCII, utf8
* Dates: 
    * 'days since a given date' i.e. an integer 
    * ISO8601 dates, using characters YYYYMMDD
*  Times:
    * seconds (integers) since midnight
    * ISO8601 times, using characters HHMMSS
*  Strings:
    * sequential of charcters, can be null terminated, length given, fixed length
    * bits arrays: length | bits

**Records**
* Collection of related fields (data items)
* Fixed format records: specified by schema e.g. no. fields, types of fields, ordering, meaning/label of field
* Variable format records: record itself self-described its format, may waste space compared to fixed format records

**Blocks**
* Each Track-Sector on disk contains a Block
* Records are packed into each Block
* Block header: contains info such as
    * file id, 
    * Block id, 
    * record directory (a structure of pointers to each record), 
    * pointer to free space, 
    * type of Block, 
    * pointer to other similar Blocks (relation to other records), 
    * timestamp (write time)

**File**
* A collection of Blocks

## Placement of Records in Blocks:

Separating records in Block
- fixed length, no need to separate
- markers to indicate record
- note down record lengths/offsets within each record or Block header

Spanned record vs unspanned record in Block
- unspanned: each record fit within a single Block, waste space, but simpler
- spanned: records can split between Blocks, needs a pointer to the partial record in the next Block

Sequencing
- ordering records in file and Block by some key value
- records ordering matter when merge-join
- more efficient
- each record may have a pointer to idicate the next record
- Once records are stored in sequence, in order to add a record in the middle of sequence, an overflow area is created in the Block header, the area stores pointers to data (which are added to the middle to sequence) and where they fit in the sequence

## Indirection: how to reference our records?
- physical, indirect addressings.
- trade off: flexibility (difficulty in moving records on insertion/deletion) vs cost of maintaining indirection

Physical addressing
- Block id: device, cylinder, head, section
- record id: (has everything Block id has) + offset in Block

Indirect addressing
- A table mapping between record ids and physical addresses
- However, it needs maintanence when records are moved to different position in Blocks

Indirection within a single Block
- records can be shifted within a Block without changing record id
- access time of a record is fast, no need to change Block

Address Management
- Every Block and record has two addresses: database address (secondary storage), memory address (buffer)
- need to translate database address to memory address in buffer (Pointer Swizzling)

Pointer Swizzling
- Consists of: 1 bit to indicate whether the pointer is a database address or a memory address, Database or memory pointer, as appropriate
- A record could have been loaded into memory(buffer) already, so when loading a record which has a pointer to another in-memory record, need to swizzle the pointer to in-memory address instead of database address
- Strategies:
automatic: replace pointers in Block (in-memory) with new entries via translation table
on-demand: only swizzle pointers when dereferenced
no-swizzle: translation table to map pointers to each dereference
- Unswizzle: rewrite sizzled pointers using translation table when a Block is written back from memory to disc (pin count can Block as other process still using the memory)

Record Insertion
- Insert new record at end of file or in deleted slot (Easy)
- Records in sequence, insert in close by free space, or use overflow idea
- Considerations: how much free space to leave in each Block, track and cylinder, frequency of re-organisation

Record Deletion, 2 options
- 1. reclaim and utilise space immediately after deletion 
- 2. mark space as deleted
- deletion marking: add a new field for indicating whether it is deleted
- there is overhead in book keeping of free space chains and detele fields
- dealing with dangling pointers: mark as deleted and never use that pointer
<!--stackedit_data:
eyJoaXN0b3J5IjpbNjI4OTE3NjQ2XX0=
-->