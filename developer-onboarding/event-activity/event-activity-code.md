
For this endpoint we replace the standard blocking controller with a 'reactive' router.The router  hands off the processing to a handler

---
####Controller

```java
@Configuration
public class EventRouter {
    @Bean
    public RouterFunction<ServerResponse> route(EventHandler handler) {
        return RouterFunctions.route()
                .POST("/events", handler::eventHandler)
                .build();
    }
}

```

---
#### Handler

This kind of endpoint is usually `store and forward` -- we want to capture and handle any errors - because there's no one on the other side to react to our error.

So we want to get the raw data to the message broker as fast and risk-free as possible.  We can always clean it up later in SQL or with Apache Spark, Flink or some other tool.  

But at high volumes we also want to make sure that the batch size never gets bigger than a certain configured amount (and we want something to test for this example).  So we are splitting large batches into smaller chunks and sending those chunks to Kafka.

```java 
    public void eventService(String json) {
        try {
        
            // a list of different schemas can come in, so handle as a list of maps
            TypeReference<List<Map<String, Object>>> typeRef = new TypeReference<>() {};
            List<Map<String, Object>> list = objectMapper.readValue(json, typeRef);

            // It's nice to leave the records in batches for performance, but make sure the batches are not huge
            if (list.size() <= chunkSize) {
                // send it!
                writer.write(list);
            } else {
                // split into arrays of chunkSize
                AtomicInteger counter = new AtomicInteger();
                list.stream()
                        .collect(Collectors.groupingBy(i -> counter.getAndIncrement() / chunkSize))
                        .values()
                        .forEach(writer::write);
            }

        } catch (Exception ignore) {
            // todo - forward failures to re-queue
        }
    }

```

---
#### Kafka Producer

Finally we are calling the Kafka producer that:
* implements a local queue - batching our requests 
* then forwards to the message broker in batches
* calls our callback function (if we set one) 

```java 
  /*
   * Write to the Spring kafka producer
   *  the real producer is wrapped by our KafkaTemplate bean
   *  Our callback from the Kafka Producer 
   */
  public void write(Object Object) {
    try {
      var future = this.kafkaTemplate.send(this.topic, Object);
      future.addCallback(new KafkaCallback());

    } catch (Exception e) {
    }
  }

```

In this case we have implemented a callback function that's not doing too much right now.  It will:
* report successes on a debug log
* report errors on the error log
* In a real implementation we would forward these errors to some sort of dead letter queue (DLQ) 

```java 
/**
 * KafkaCallback
 *  Handle callbacks from the Kafka Producer 
 */
  static class KafkaCallback implements ListenableFutureCallback<SendResult<String, Object>> {
    @Override
    public void onFailure(Throwable ex) {
      log.error("FOOBAR-DLQ: {}", ex.getLocalizedMessage());
    }

    @Override
    public void onSuccess(SendResult<String, Object> result) {
      if (result == null ) return;
      RecordMetadata meta = result.getRecordMetadata();
      log.debug("Delivered {}<--[{}] at partition={}, offset={}", meta.topic(), result.toString(), meta.partition(), meta.offset());
    }
  }

```
