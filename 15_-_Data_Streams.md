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

### Relation-to-Stream operators
**Insert Stream (Istream)**: whenever a tuple is inserted into the relation, emit it on the stream
**Delete Stream (Dstream)**: whenever a tuple is deleted from relation, emit it on the stream
**Relation Stream (Rstream**: every time instant, emit every tuple in relation on the stream

**_Example CQL Query:_**
_Example 1_
SELECT Istream(*) FROM S [rows unbounded] WHERE S.A > 10
- _S is converted into a relation of unbounded size_
- _resulting relation is converted back to a stream via Istream_

_Example 2_
SELECT * 
FROM S1[rows 1000], 
             S2[range 2 minutes]
WHERE S1.A = S2.A AND S1.A > 10

_Example 3_
SELECT Rstream(S.A, R.B)
FROM S [now], R
WHERE S.A = R.A
- _query probes a stored table R based on each tuple in stream S and streams the result._
- [now] time-based sliding window containing tuples received in the last time step

Continuous Query optimisation is difficult due to streams being unbounded/infinite size, contrary to traditionaly fixed size relational query. Cotinuous adaptive based optimisation is required. Some might look at rate of stream arriving, tuples/second, some look at resources require to execute the stream operations, etc


<!--stackedit_data:
eyJoaXN0b3J5IjpbNTY4MDY3MzM2LC0xODUyMzQ3NjA1LC0xND
g3NTQxNTYxLC05ODMzNTAyMzksLTEzMDMxNDk3NTgsLTEyMTc4
MTg1MF19
-->