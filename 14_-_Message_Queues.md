# Message Queues
### Synchronous vs Asynchronous protocols
**HTTP** is a synchronous protocol: request from client to server is followed by server-to-client in the same instance of TCP connection

**SMTP** is an asynchronous protocol: email messages are sent on a _store-and-forward_ basis, clients need not to be active when receiving messages.

**_Messaging and Queueing (MQ)_**:
- Enables programs to communicate across a network _asynchronously_
- No private, dedicated link required
- Systems can be heterogeneous
- Message delivery is guaranteed

MQ esstenially is a datastructure which abstracts away the underlying networking between processes. It can be used on the same system, or across many systems.

**_Direct Transaction Processing_**
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4MzEyNzgxNzYsLTExNTA0OTYwMzJdfQ
==
-->