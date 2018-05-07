
# Logging and Recovery
Consider a **disk**, a **buffer**, and a **in-memory transaction**

and operations:
- write(x) - write from memory to buffer
- read(x) - read from buffer to memory, or from disk if not present in buffer
- output(x) - write to disk from buffer 
- input(x) - write to buffer from disk

|   Action  |  X |  Y | Xbuf | Ybuf | Xdisk | Ydisk | Log |
|:---------:|:--:|:--:|:----:|:----:|:-----:|:-----:|:---:|
|           |    |    |      |      |   20  |   50  |     |
|  read(x)  | 20 |    |  20  |      |   20  |   50  |     |
|   x=x-10  | 10 |    |  20  |      |   20  |   50  |     |
|  write(x) | 10 |    |  10  |      |   20  |   50  |     |
|  read(y)  | 10 | 50 |  10  |  50  |   20  |   50  |     |
|   y=y+10  | 10 | 60 |  10  |  50  |   20  |   50  |     |
|  write(y) | 10 | 60 |  10  |  60  |   20  |   50  |     |
| output(x) | 10 | 60 |  10  |  60  |   10  |   50  |     |
| output(y) | 10 | 60 |  10  |  60  |   10  |   60  |     |

### Logging
main approach of recovering from a system crash relies on a persistent record of changes made during a transaction

3 approaches:
- undo logging
- redo logging
- undo/redo logging

Log record types:
- **\<start-T>**: transaction T has started execution
- **\<commit-T>**: transaction T has completed successfully and will make not further changes to database items
- **\<abort-T>**: transaction T could not complete successfully, no changes made by T will be copied to disk

# Undo Logging

1. If transaction T modifies database item X, then a log record of the form <T, X, old-value> must be written to disk **before** the new value of X is written to disk

2. If a transaction T commits, then its \<commit-T\> log record must be written to disk only after all database items changed by T have been written to disk

|   Action  |  X |  Y | $X_b$ | $Y_b$ | $X_d$ | $Y_d$ |      Log     |
|:---------:|:--:|:--:|:-----:|:-----:|:-----:|:-----:|:------------:|
|           |    |    |       |       |   20  |   50  |  \<start-T\> |
|  read(x)  | 20 |    |   20  |       |   20  |   50  |              |
|   x=x-10  | 10 |    |   20  |       |   20  |   50  |              |
|  write(x) | 10 |    |   10  |       |   20  |   50  | \<T, X, 20\> |
|  read(y)  | 10 | 50 |   10  |   50  |   20  |   50  |              |
|   y=y+10  | 10 | 60 |   10  |   50  |   20  |   50  |              |
|  write(y) | 10 | 60 |   10  |   60  |   20  |   50  | \<T, Y, 50\> |
| flush log |    |    |       |       |       |       |              |
| output(x) | 10 | 60 |   10  |   60  |   10  |   50  |              |
| output(y) | 10 | 60 |   10  |   60  |   10  |   60  |              |
|           |    |    |       |       |       |       | \<commit-T\> |
| flush log |    |    |       |       |       |       |              |

### Recovery with Undo Logging

		for each log entry <T, X, old>, scanning backwards {
			if <commit T> has been seen {
				do nothing
			} else {
				change the value of X in the database back to old
			}
		}
		for each incomplete transaction T (that was not borted) {
			write <abort T> to log
		}
		flush log

### Checkpointing with Undo Logging

- Disadvantage of above approach: we must scan the entire log.
- Introduce a periodic checkpoint in the log
	- Before checkpoint, all transactions have committed or aborted
	- Only need search backwards through the log to the most recent checkpoint
- New log record type:
	\<ckpt>: The database has been checkpointed

Checkpointing:
1. Stop accepting new transactions
2. Wait until all active transactions commit/abort and write <commit T>/<abort T> to the log
3. flush log
4. write <ckpt> to log
5. flush log
6. Resume accepting transactions

### Nonquiescent Checkpointing
- Need to stop transaction processing while checkpointing (method above)
- Slow, reduce pull through, system may appear to stall
- Allow new transactions to enter the system during the checkpoint.

New log record types:
- \<start ckpt (T1...Tn)> (Checkpoint starts. T1...Tn are active transactions that have not yet committed)
- \<end ckpt>

1. Write \<start ckpt (T1...Tn)> to log and flush log
2. Wait until T1..Tn have all committed or aborted
3. Write <end ckpt> to log and flush log
(Note that new transactions may be started during step 2)

**Recovery with checkpointed undo logging**
Two cases, depending on latest checkpoint log record:

1. \<end ckpt>
	- All incomplete transactions began after the previous \<start ckpt ()>
	- Disregard the log before the previous \<start ckpt ()> 

2. \<start ckpt (T1...Tn)>
	- System crash occurred during checkpoint
	- Incomplete transactions are those encountered before the \<start ckpt ()> and those of T1...Tn that were not committed before the crash
	- Disregard the log before the start of the earliest incomplete transaction

# Redo Logging
- Opposite of undo logging, \<commite T> is written before changes are written to disk
- The ideal is about ignoreing incomplete transactions
- a new record type **\<T, X, new>**

**Rule:**
1. Before modifying an item X on disk, all log records related to the modification i.e. \<T, X, new>, \<commit T> must be written to disk. For example:

|   Action  |  X |  Y | $X_b$ | $Y_b$ | $X_d$ | $Y_d$ |      Log     |
|:---------:|:--:|:--:|:-----:|:-----:|:-----:|:-----:|:------------:|
|           |    |    |       |       |   20  |   50  |  \<start-T\> |
|  read(x)  | 20 |    |   20  |       |   20  |   50  |              |
|   x=x-10  | 10 |    |   20  |       |   20  |   50  |              |
|  write(x) | 10 |    |   10  |       |   20  |   50  | \<T, X, 10\> |
|  read(y)  | 10 | 50 |   10  |   50  |   20  |   50  |              |
|   y=y+10  | 10 | 60 |   10  |   50  |   20  |   50  |              |
|  write(y) | 10 | 60 |   10  |   60  |   20  |   50  | \<T, Y, 60\> |
|           |    |    |       |       |       |       | \<commit-T\> |
| flush log |    |    |       |       |       |       |              |
| output(x) | 10 | 60 |   10  |   60  |   10  |   50  |              |
| output(y) | 10 | 60 |   10  |   60  |   10  |   60  |              |

**Recovery**
```
identify the committed transactions
for each log entry <T, X, new>, scanning forwards {
if T is not committed {
do nothing
} else {
write value new for X to the database
}
}
for each incomplete transaction T {
write <abort T> to log
}
flush log
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE2MzI2NjM5NDIsLTU0MDk2MTQ3NSwtMT
M3MTI4MzI3NywtMjA2NzYyODM2OCwtMTcxOTIwMTIzOCwxNDM0
MjQ3Mzk2LDE2Mjc4MzA4ODcsLTEyMDg1ODQ2NTUsNzcxNDk4OD
Q0LC03NDQ3NjUyODQsNDIzMTkwOTJdfQ==
-->