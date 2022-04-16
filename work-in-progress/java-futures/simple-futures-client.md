

###calling blocking worker 

```java 
public static void callServerBlocking() {
    long start = System.nanoTime();

    // call blocking service
    PhatClient.callService();

    long retrievalTime = ((System.nanoTime() - start) / 1_000_000);
    System.out.println("Syncronous client blocked for " + retrievalTime + " msecs");
}
```


###calling blocking worker - asynchronously

```java 
public static void callServerAsync() {
    long start = System.nanoTime();

    // call the service async
    CompletableFuture<Integer> cf = CompletableFuture.supplyAsync(PhatClient::callService);

    // return a reference to a future immediately
    long invocationTime = ((System.nanoTime() - start) / 1_000_000);
    System.out.println("Async client returned a future after " + invocationTime+" msecs");

    // while the id is being retreived, do other work
    while (!cf.isDone() && !cf.isCancelled() ) {
        System.out.println("\tWorking on other important tasks .... ");
        Util.delay(500L);
    }

    // get the returned value off of the future
    try {
        var id = cf.get();
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
    long retrievalTime = ((System.nanoTime() - start) / 1_000_000);
    
    System.out.printf("Async client completed in %d milliseconds, then did other work before returning after %s milliseconds\n" , invocationTime,  retrievalTime);
}

```

```java 
    public static void main(String[] args) {
        System.out.println("------------------------\nSynchronous Client ....");
        callServerBlocking();

        System.out.println("------------------------\nAsync Client ....\n");
        callServerAsync();
    }
```

* [x] The call takes 2000 milliseconds - and a CPU is blocked for that time
* [x] The call still took 2000 milliseconds, but we were free to do other work while waiting

```text
------------------------
Synchronous Client ....
Synchronous client blocked for 2000 msecs


------------------------
Async Client ....
Async client returned a future after 6 msecs
	Working on other important tasks .... 
	Working on other important tasks .... 
	Working on other important tasks .... 
	Working on other important tasks .... 
Async client completed in 6 milliseconds, then did other work before returning after 2018 milliseconds

```