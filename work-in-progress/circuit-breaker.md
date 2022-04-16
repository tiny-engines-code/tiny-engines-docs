
#Retry

in currency-exchage-service

####ADD A new controller

```java

@Slf4j
@RestController
public class TryCircuitBreakController {

    @GetMapping("/sampleapi")
//    @Retry(name="default")  // no properties needed - defaults to three retries
    @Retry(name="my-sample")  // application properties to configure this
    public String sampleAPI() {
        log.info("Enter Sample API call");       // run the service - call /sampleapi - and see the retries

        // This is always going to fail
        var response = new RestTemplate()
                .getForEntity("http://localhost:9090/fail", String.class);

        return response.getBody();
    }
}

```

####in application.properties
- add configurations for any custom retry tokens (

```
resilience4j.retry.instances.my-sample.max-attempts=5
```  

####Retry fallback

- add a string fallback method -- pointing to a real fallback method handleMaxRetries()

```java
@Slf4j
@RestController
public class TryCircuitBreakController {

    @GetMapping("/sampleapi")
//    @Retry(name="default")
//    @Retry(name="my-sample")  // application properties to configure this
    @Retry(name="my-sample", fallbackMethod = "handleMaxRetries")  // application properties to configure this with fallback
    public String sampleAPI() {
        log.info("Enter Sample API call");
        var response = new RestTemplate()
                .getForEntity("http://localhost:9090/fail", String.class);

        return response.getBody();
    }

    public String handleMaxRetries(Exception ex) {
        return String.format("HANDLE ERROR (%s)\n " ,  ex.getMessage());
    }
}
```


---
setting wait duration

```
resilience4j.retry.instances.my-sample.max-attempts=5
resilience4j.retry.instances.my-sample.wait-duration=1s
```

setting exponential backoff
each subsequent retry taking a little EnableConfigServer
```
resilience4j.retry.instances.my-sample.enable-exponential-backoff=true
```
#CircuitBeaker

https://resilience4j.readme.io/docs/circuitbreaker

```java
@Slf4j
@RestController
public class TryCircuitBreakController {

    @GetMapping("/sampleapi")
//    @Retry(name="default")
//    @Retry(name="my-sample")  // application properties to configure this
//    @Retry(name="my-sample", fallbackMethod = "handleMaxRetries")  // application properties to configure this with fallback
    @CircuitBreaker(name="default", fallbackMethod = "handleMaxRetries")  // application properties to configure this with fallback
    public String sampleAPI() {
        log.info("Enter Sample API call");
        var response = new RestTemplate()
                .getForEntity("http://localhost:9090/fail", String.class);

        return response.getBody();
    }

    public String handleMaxRetries(Exception ex) {
        return String.format("HANDLE ERROR (%s)\n " ,  ex.getMessage());
    }
}

```

all kinds of settings (yaml examples on the docs site: https://resilience4j.readme.io/docs/getting-started-3)

```
# circuit breaker - inject the name my-sample into this command:  https://resilience4j.readme.io/docs/getting-started-3
resilience4j.circuitbreaker.instances.my-sample.failure-rate-threshold=50
resilience4j.circuitbreaker.instances.my-sample.wait-duration-in-open-state=50s
resilience4j.circuitbreaker.instances.my-sample.minimum-number-of-calls=20
resilience4j.circuitbreaker.instances.my-sample.sliding-window-type=TIME_BASED
resilience4j.circuitbreaker.instances.my-sample.permitted-number-of-calls-in-half-open-state=3
resilience4j.circuitbreaker.instances.my-sample.register-health-indicator=true
```


Send a whole bunch of requests in a separate window  

```
watch -n 0.1 curl http://localhost:8000/sampleapi
```

- and watch the behavior in the log window and this window.
- sends until threshold, then OPENS the circuit breaker for a while - then tries again


# ------------------ REWRITING the whole class to include different paths for each ------------------------------

```java
@Slf4j
@RestController
public class TryCircuitBreakController {

    @GetMapping("/retry")
//    @Retry(name="default")
    @Retry(name="my-retry")  // application properties to configure this
    public String testRetry() {
        log.info("Enter Sample API call");
        var response = new RestTemplate()
                .getForEntity("http://localhost:9090/fail", String.class);

        return response.getBody();
    }

    @GetMapping("/retryfallback")
    @Retry(name="my-retry", fallbackMethod = "handleMaxRetries")  // application properties to configure this with fallback
    public String testRetryWithFallback() {
        log.info("Enter Sample API call");
        var response = new RestTemplate()
                .getForEntity("http://localhost:9090/fail", String.class);

        return response.getBody();
    }

    @GetMapping("/circuitbreaker")
    @CircuitBreaker(name="my-circuitbreaker", fallbackMethod = "handleMaxRetries")  // application properties to configure this with fallback
    public String testCircuitBreaker() {
        log.info("Enter Sample API call");
        var response = new RestTemplate()
                .getForEntity("http://localhost:9090/fail", String.class);

        return response.getBody();
    }


    @GetMapping("/ratelimiter")
    @RateLimiter(name="my-ratelimiter")  // application properties to configure this with fallback
    public String sampleAPI() {
        log.info("Enter Sample API call");
        return "RATE_LIMITER_OUTPUT";
    }

    public String handleMaxRetries(Exception ex) {
        return String.format("HANDLE ERROR\t:: (%s)\n " ,  ex.getMessage());
    }
}

```

with application.properties

```python
spring.application.name=currency-exchange-service
server.port=8000
# cloud config
spring.cloud.config.enabled=true
spring.config.import=optional:configserver:http://localhost:8888
# jpa/h2
spring.jpa.show-sql=true
spring.h2.console.enabled=true
spring.datasource.url=jdbc:h2:mem:testdb
spring.jpa.defer-datasource-initialization=true
eureka.client.service-url.default-zone=http://localhost:8761/eureka
# retry - inject the name my-sample into this command
resilience4j.retry.instances.my-retry.max-attempts=5
resilience4j.retry.instances.my-retry.wait-duration=1s
resilience4j.retry.instances.my-retry.enable-exponential-backoff=true
# circuit breaker - inject the name my-sample into this command:  https://resilience4j.readme.io/docs/getting-started-3
resilience4j.circuitbreaker.instances.my-circuitbreaker.failure-rate-threshold=50
resilience4j.circuitbreaker.instances.my-circuitbreaker.wait-duration-in-open-state=50s
resilience4j.circuitbreaker.instances.my-circuitbreaker.minimum-number-of-calls=20
resilience4j.circuitbreaker.instances.my-circuitbreaker.sliding-window-type=TIME_BASED
resilience4j.circuitbreaker.instances.my-circuitbreaker.permitted-number-of-calls-in-half-open-state=3
resilience4j.circuitbreaker.instances.my-circuitbreaker.register-health-indicator=true
# rate-limiters 2 requests every 10sec
resilience4j.ratelimiter.instances.my-ratelimiter.limit-for-period=2
resilience4j.ratelimiter.instances.my-ratelimiter.limit-refresh-period=10s
```
