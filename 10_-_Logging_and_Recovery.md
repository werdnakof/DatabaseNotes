# Logging and Recovery
Consider a **disk**, a **buffer**, and a **in-memory transaction**

and operations:
- write(x) - write from memory to buffer
- read(x) - read from buffer to memory, or from disk if not presen
- output(x) - write to disk from buffer
- input(x) - write to buffer from disk

| Action |  X  |  Y  |$X_m$|$Y_m$|$X_d$|$Y_d$| Log |
|:------:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|        |     |     |     |     |20   |50   |     |
| read(x)|  20 |     |20   |     |20   |50   |     |
| x=x-10 |  10 |     |20   |     |20   |50   |     |
|write(x)|  10 |     |10   |     |20   |50   |     |
| read(y)|  10 | 50  |10   | 50  |20   |50   |     |
| read(x)|  20 |     |20   |     |20   |50   |     |
| read(x)|  20 |     |20   |     |20   |50   |     |

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTYxNzExMDA2MSw4MjAxNzc3NTcsNjI2Nj
Y3MDQ3LC0xNzgxMTAxODU3XX0=
-->