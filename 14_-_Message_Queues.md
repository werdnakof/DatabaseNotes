# Message Queues
### Synchronous vs Asynchronous protocols
**HTTP** is a synchronous protocol: request from client to server is followed by server-to-client in the same instance of TCP connection

**SMTP** is an asynchronous protocol: email messages are sent on a _store-and-forward_ basis, clients need not to be active when receiving messages.

**_Messaging and Queueing (MQ)_**:
- Enables programs to communicate across a network _asynchronously_
- No private, dedicated link required
- Systems can be heterogeneous
- Message delivery is guaranteed

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/message-queue.png?raw=true)

**bidirectional queue**
![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/bidirectional-queue.png?raw=true)

MQ esstenially is a datastructure which abstracts away the underlying networking between processes. It can be used on the same system, or across many systems.

**_Direct Transaction Processing (DCP)_** is **synchronous**, and has weaknesses:
- Difficulties with _long lived_ transactions and communication errors
- Difficult to balance loads between servers carrying out the same task
- Difficult to prioritise one request over another
- Server failure - cannot distinguish between:
	- request not delivered to server
	- server failed
	- reply not delivered to client
- Client failure - cannot tell:
	- if response has been received by client
	- client failed
	- response not delivered to server

**_MQ_** resolves DCP's weaknesses by:
-  perserving requests and responses on the queue if servers or clients are down
- simple API hide communications complexity
- fewer constraints on inter-program operation
- fewer network sessions needed, and programs are less vulnerable to network failures
- business change easier to handle
- assured message delivery

**_Queued Transaction Processing_** uses the queue to handle transaction requests and responses.

![](https://github.com/werdnakof/DatabaseNotes/blob/master/images/queued-transaction-processing-2.png?raw=true) 

If _transaction_ is aborted:
- _request_ returns to input queue
- changes made by transaction are rolled back
- _response_ removed from output queue if necessary
- repeated aborts due to bad transaction request can be prevented by setting a limit on retries

Messages (request/responses) can be **ordered**, either _FIFO_ or _highest priority first_. **Aborted transaction** may lead to _out-of-order_ processing:  T1 dequeues M1 > T2 dequeues M2 > T2 commits, T1 aborts > M1 is re-send to queue.







<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1NzczODAwNzYsLTEwNTg5MjcxOTksLT
ExNTA0OTYwMzJdfQ==
-->