# Parallel Databases

### I/O bottleneck

| Type      | Capacity            | Latency      |
|-----------|---------------------|--------------|
| Registers | 10^1 bytes          | 1 cycle      |
| L1        | 10^4 bytes          | <5 cycles    |
| L2        | 10^5 bytes          | 5-10 cycles  |
| RAM       | 10^9 - 10^10 bytes  | 20-30 cycles |
| Disk      | 10^11 - 10^12 bytes | 10^6 cycles  |

- Time to file access (latency) is high on disks, parallelism can speed up file access on multiple disks
 
### Parallel Architectures

**Shared Memory**
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/shared-memory-arch.png?raw=true)
Properties:
- less complex software
- limited scalability
- single buffer, more consistent
- single database storage

**Shared Disc**
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/shared-disc-arch.png?raw=true)
(S - switch)
- avoid memory bottleck
- incoherence issues: same page can be in more than one buffer
- 

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTk0MDM0NjU3MywyODA0NDE0NDgsMTU1ND
E1Mjk2LC0xODU2Nzg5MTM0LC0zNzM2MTE5MjksLTE4NTY1Njc0
NywxNDk4NDk5ODA2XX0=
-->