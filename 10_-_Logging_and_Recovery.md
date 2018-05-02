# Logging and Recovery
Consider a **disk**, a **buffer**, and a **in-memory transaction**

and operations:
- write(x) - write from memory to buffer
- read(x) - read from buffer to memory, or from disk if not present in buffer
- output(x) - write to disk from buffer 
- input(x) - write to buffer from disk

| Action  |  X  |  Y  |$X_b$|$Y_b$|$X_d$|$Y_d$| Log |
|:-------:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|         |     |     |     |     |20   |50   |     |
| read(x) |  20 |     |20   |     |20   |50   |     |
| x=x-10  |  10 |     |20   |     |20   |50   |     |
|write(x) |  10 |     |10   |     |20   |50   |     |
| read(y) |  10 | 50  |10   | 50  |20   |50   |     |
| y=y+10  |  10 | 60  |10   | 50  |20   |50   |     |
|write(y) |  10 | 60  |10   | 60  |20   |50   |     |
|output(x)|  10 | 60  |10   | 60  |10   |50   |     |
|output(y)|  10 | 60  |10   | 60  |10   |60   |     |

### Logging
main approach to recovering from a system crash relies on a persistent record of changes made during a transaction

3 ways:
- undo logging
- redo logging
- undo/redo logging
log records:
- **start-T**: transaction T has started execution
- **commit-T**: transaction T has completed successfully and will make not further changes to database items
- **abort-T**: transaction T could not complete successfully, no changes made by T will be copied to disk


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQ0NDU5NzI3MiwtMjI2MzAzNzgsODIwMT
c3NzU3LDYyNjY2NzA0NywtMTc4MTEwMTg1N119
-->