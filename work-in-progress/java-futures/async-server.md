

#### Longhand version of a completable future - server side

The server:
* [x] creates the completable future
* [x] calls the worker on a separate thread
* [x] hands the completable future back immediately so is pointed to by both client and the server
* [x] updates the completable future

```java 
public Future<Integer> createResourceSDKExample(String name) {

    // create the completeable future
    CompletableFuture<Integer> futurePrice = new CompletableFuture<>();

    // call the blocking worker on it's own thread and return immediately on this thread
    new Thread (() -> {  
        try {
            var future = createResourceService(name);
            // once we are done - update the future -- since the client already has it 
            futurePrice.complete(future);   
        } catch (Exception e) {
            futurePrice.completeExceptionally(e);
        }
    }).start();

    return futurePrice;  // return the completable immediately
}
```

#### Shorthand - sytanctical sugar
The `supplyAsync()` function handles everything above for us.

* [x] creates the completable future
* [x] calls the worker on a separate thread in the ForkJoin pool
* [x] hands the completable future back immediately so is pointed to by both client and the server
* [x] updates the completable future

```java
public Future<Integer> createResourceSDK(String name) {
    return CompletableFuture.supplyAsync(() -> createResourceService(name));
}
```

