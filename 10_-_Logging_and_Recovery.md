# Logging and Recovery
Consider a **disk**, a **buffer**, and a **in-memory transaction**

and operations:
- write(x) - write from memory to buffer
- read(x) - read from buffer to memory
- output(x) - write to disk from buffer
- input(x) - write to buffer from disk

Action | X | Y | $X_m$ | $Y_m$ | $X_d$ | $Y_d$ | Log
:--------:
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTUwMDMxODA3Myw2MjY2NjcwNDcsLTE3OD
ExMDE4NTddfQ==
-->