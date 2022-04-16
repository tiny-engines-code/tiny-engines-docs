# Create a naming service

There's a lot of great documentation out there - but it sometimes helps to have a working sample to start with

create the naming service

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-server'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```
annotate the main

```java
@SpringBootApplication
@EnableEurekaServer
public class NamingServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(NamingServiceApplication.class, args);
    }

```

application.properties

```
spring.application.name=netflix-eureka-naming-server
server.port=8761
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
```

that gets the server running with no registered services

bring up the services at port 8761

```http
http://localhost:8761/
```


![eureka]('eureka.png')

---

# Register a CLIENT

go to the client application -- eg currency-conversion-services
add the client dependency

```
implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-client'
```

make sure any proxy is not pointing to a specific url
```java
@FeignClient(name="currency-exchange-service")  // removed the url argument
public interface CurrencyExchangeProxy {

    @GetMapping("/currency-exchange/from/{from}/to/{to}")
    public CurrencyConversion getExchangeRate(@PathVariable("from") String from, @PathVariable("to") String to) ;
}

```

finally - a nice to have for feign to load balance (client-side load balancing) is to update the application.properties of the clients to pint to the eureka service
this replaces the older Ribbon module
 

```
eureka.client.service-url.default-zone=http://localhost:8761/eureka
```



with the naming server up - start up this clients
you should see the client 'register' with Eureka Server

![eureka client]('eureka-client.png')
