# Data Streams
Traditional DBMS
- persistet data storage
- relatively static records
- (typically) no predefined notion of time
- complex one-off queries

Some applications might require:
- data arrives at real time
- ordering of data
- too much data to store and persist
- data never stops coming
- ongoing analysis of rapidly changeing data

There are also two types of data streams:
- **_Transactional_** data streams: logs of entities interactions.
- **_Measurement_** data streams: monitoring evolution of entity states
 
 **One time query vs Continuous queries**
 - One time: run once to completion
 - Continuous: issues once and then continue to evaluate over currnet and future data e.g.
	 - notify me when the temperature drops below X
	 - notify me when stock Y > 300

### DSMS mechanism
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/dsms.png?raw=true)

| DBMS                                             | DSMS                         |
|--------------------------------------------------|------------------------------|
| Persistent relations (relatively static, stored) | Transient streams            |
| One-time queries                                 | Continuous queries (CQs)     |
| Random access                                    | Sequential access            |
| Unbounded disk store                             | Bounded disk store           |
| Only current state matters                       | Historical data is important |

### Query Processing is DSMS
**_Continuous Query Language_** - queries produce/refer to relations and streams.

Properties:
- Based on SQL
- streams as new data type
- continuous instead of one-time semantics
- windows on streams
- sampling on streams
- constructe query plan based on relational operators like DBMS

During query processing, continuous streams are combined to form the requested query plan. E.g.

**Tuple-at-a-time** operators: _Selection_ and _Projection_

**Full Relation**
Certain operators can be dealt by considering a tuple at a time / directly on incoming stream, such as:  _Count_, _Sum_, _Average_, _Max_, _Min_, _Union_,  _Select_ and _Group By_.
 
 Others can't e.g. _Intersection_, _Difference_, _Product_, _Order By_ and _Join_. These will require accumulation.
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/full-relation-op.png?raw=true)
 In addition, they may **_block_** because no output until entire input is seen and streams are unbounded _Joins_ may need to join tuples that are far apart (dislocality).

### Windows
Windows collect tuples from streams over a time period or certain amount of tuples.

**Sliding window** updates itself by shifting out oldest tuple and shifting in newest tuple.
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/window-sliding.png?raw=true)

**Tumbling window** updates itself by replacing the entire tuples entry
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/window-tumbling.png?raw=true)

**Join** operation can then be carried out over two windows collected from two relations, and returns an output stream.
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/window-join.png?raw=true)

### Scalability and Completeness in DBMS vs DSMS

DBMS deals with **finite relations**, query evaluation should produce all results for a given query

DSMS deals with **unbounded data streams**
- may not return all results for a given guery
- trade-off between resource use and completeness of result set
- size of buffers used for windows is an parameter that affects resource use and completeness
- can further reduce resource by randomly sampling from streams








<!--stackedit_data:
eyJoaXN0b3J5IjpbLTc3OTg0ODA4NCwtMTg1MjM0NzYwNSwtMT
Q4NzU0MTU2MSwtOTgzMzUwMjM5LC0xMzAzMTQ5NzU4LC0xMjE3
ODE4NTBdfQ==
-->