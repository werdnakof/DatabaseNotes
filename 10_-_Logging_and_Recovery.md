# Logging and Recovery

Consider a  **disk**, a  **buffer**, and a  **in-memory transaction** and operations:
-   write(x) - write from memory to buffer
-   read(x) - read from buffer to memory, or from disk if not present in buffer
-   output(x) - write to disk from buffer
-   input(x) - write to buffer from disk

|   Action  |  X |  Y | Xbuf | Ybuf | Xdsik | Ydisk | Log |
|:---------:|:--:|:--:|:----:|:----:|:-----:|:-----:|:---:|
|           |    |    |      |      |   20  |   50  |     |
|  read(x)  | 20 |    |  20  |      |   20  |   50  |     |
|   x=x-10  | 10 |    |  20  |      |   20  |   50  |     |
|  write(x) | 10 |    |  10  |      |   20  |   50  |     |
|  ready(y) | 10 | 50 |  10  |  50  |   20  |   50  |     |
|   y=y+10  | 10 | 60 |  10  |  50  |   20  |   50  |     |
|  write(y) | 10 | 60 |  10  |  60  |   20  |   50  |     |
| output(x) | 10 | 60 |  10  |  60  |   10  |   50  |     |
| output(y) | 10 | 60 |  10  |  60  |   10  |   60  |     |

### Logging

Logging is the main approach to recovering from a system crash relies on a persistent record of changes made during a transaction

3 ways:

-   undo logging
-   redo logging
-   undo/redo logging  
    log records:
-   **start-T**: transaction T has started execution
-   **commit-T**: transaction T has completed successfully and will make not further changes to database items
-   **abort-T**: transaction T could not complete successfully, no changes made by T will be copied to disk

### [](https://github.com/werdnakof/DatabaseNotes/blob/master/10_-_Logging_and_Recovery.md#undo-logging-rules)Undo Logging Rules

1.  If transaction T modifies database item X, then a log record of the form <T, X, old-value> must be written to disk  **before**the new value of X is written to disk
2.  If a transaction T commits, then its <commit-T> log record must be written to disk only after all database items changed by T have been written to disk
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA5MDg5MDAyMSwtNzQ0NzY1Mjg0LDQyMz
E5MDkyXX0=
-->