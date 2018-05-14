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

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI0OTc5NDQ3OSwtOTgzMzUwMjM5LC0xMz
AzMTQ5NzU4LC0xMjE3ODE4NTBdfQ==
-->