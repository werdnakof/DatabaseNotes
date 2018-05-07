# Logging and Recovery

Consider a  **disk**, a  **buffer**, and a  **in-memory transaction** and operations:
-   write(x) - write from memory to buffer
-   read(x) - read from buffer to memory, or from disk if not present in buffer
-   output(x) - write to disk from buffer
-   input(x) - write to buffer from disk

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTc0NDc2NTI4NCw0MjMxOTA5Ml19
-->