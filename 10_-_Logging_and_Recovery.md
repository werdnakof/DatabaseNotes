# Logging and Recovery
Consider a **disk**, a **buffer**, and a **in-memory transaction**

and operations:
- write(x) - write from memory to buffer
- read(x) - read from buffer to memory
- output(x) - write to disk from buffer
- input(x) - write to buffer from disk

| Action |  X  |  Y  |$X_m$|$Y_m$|$X_d$|$Y_d$| Log |
|:------:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|        |     |     |     |     |20   |50   |     |
| read(x)|  20 |     |20   |     |20   |50   |     |

<!--stackedit_data:
eyJoaXN0b3J5IjpbODIwMTc3NzU3LDYyNjY2NzA0NywtMTc4MT
EwMTg1N119
-->