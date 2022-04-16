

>* If you’re doing computation-heavy operations with no I/O, the Stream interface provides the simplest implementation and the one that’s likely to be most efficient. (If all threads are compute-bound, there’s no point in having more threads than processor cores.) 
>* If your parallel units of work involve waiting for I/O (including network connections), the CompletableFutures solution provides more flexibility and allows you to match the number of threads to the wait/computer (W/C) ratio, as discussed previously. Another reason to avoid using parallel streams when I/O waits are involved in the stream-processing pipeline is that the laziness of streams can make it harder to reason about when the waits happen.
