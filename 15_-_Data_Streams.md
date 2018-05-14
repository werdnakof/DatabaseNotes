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
Certain operators can be deal with tuple at a time, such as:
 _Count_, _Sum_, _Average_, _Max_, _Min_ and _Group By_. But not _Order By_
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/full-relation-op.png?raw=true)
Others can't e.g. _Intersection_, _difference_, _Product_ and _Join_. These may **_block_** because no output until entire input is seen and streams are unbounded _Joins_ may need to join tuples that are far apart (dislocality).







<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc1MTgyNTExNSwtOTgzMzUwMjM5LC0xMz
AzMTQ5NzU4LC0xMjE3ODE4NTBdfQ==
-->