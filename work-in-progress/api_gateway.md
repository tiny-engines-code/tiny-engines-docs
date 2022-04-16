##API Gateway

-- replaces Zuul

-- Spring Cloud Gateway instead

- actuator
- eureka client
- api-Gateway
- dev tools

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.cloud:spring-cloud-starter-gateway'
    implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-client'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```


application.properties

```
spring.application.name=api-gateway
server.port=8765
eureka.client.service-url.default-zone=http://localhost:8761/eureka
spring.cloud.gateway.discovery.locator.enabled=true

```

That's it - fire up the api-gateway and see it running in Eureka


![eureka]('eureka-api-gateway.png')

NOW YOU CAN CALL he gateway - giving it the service name and the path instead of calling the service directly

http://localhost:8765/CURRENCY-CONVERSION-SERVICE/currency-conversion/from/USD/to/INR/quantity/10

-- common features can be handled in the GATEWAY before and after invoking the destination service


don't like the Uppper case?

```
spring.application.name=api-gateway
server.port=8765
eureka.client.service-url.default-zone=http://localhost:8761/eureka
spring.cloud.gateway.discovery.locator.enabled=true
spring.cloud.gateway.discovery.locator.lower-case-service-id=true
```

now I can use:

http://localhost:8765/currency-conversion-service/currency-conversion/from/USD/to/INR/quantity/10



# ROUTES

Add a configuration class to the api-gateway  -- the idea is that you can catch and change the http headers, params, and destinations, etc.

```java
@Configuration
public class ApiGatewayConfiguration {

    @Bean
    public RouteLocator routeLocator(RouteLocatorBuilder routeLocatorBuilder) {

        // route function with a dummy output and filter
        Function<PredicateSpec, Buildable<Route>> routeFunction =
                p -> p.path("/get")
                        .filters( f -> f
                                .addRequestHeader("Myheader", "Myheader value")
                                .addRequestParameter("param", "MyValue"))
                        .uri("http://httpbin.org:80");

        // build the route locator
        return routeLocatorBuilder.routes()
                .route(routeFunction)
                .build();
    }
}
```

and when you calls
http://localhost:8765/get

your get:


```json
{
  "args": {
  "param": "MyValue"
  },
  "headers": {
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
    "Accept-Encoding": "gzip, deflate, br",
    "Accept-Language": "en-US,en;q=0.9",
    "Cache-Control": "max-age=0",
    "Content-Length": "0",
    "Cookie": "Idea-208f5851=7cf5b662-6a5d-4e9a-b8ca-7d0dc8ef3c6e; Webstorm-950d3d19=c320a16f-0966-47f9-af49-2945558daee9; Idea-208f5852=7947d717-5d1a-4798-bf07-21d642634d2f",
    "Forwarded": "proto=http;host=\"localhost:8765\";for=\"[0:0:0:0:0:0:0:1]:61330\"",
    "Host": "httpbin.org",
    "Myheader": "Myheader value",
    "Sec-Ch-Ua": "\"Google Chrome\";v=\"93\", \" Not;A Brand\";v=\"99\", \"Chromium\";v=\"93\"",
    "Sec-Ch-Ua-Mobile": "?0",
    "Sec-Ch-Ua-Platform": "\"macOS\"",
    "Sec-Fetch-Dest": "document",
    "Sec-Fetch-Mode": "navigate",
    "Sec-Fetch-Site": "none",
    "Sec-Fetch-User": "?1",
    "Upgrade-Insecure-Requests": "1",
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/93.0.4577.82 Safari/537.36",
    "X-Amzn-Trace-Id": "Root=1-614d6145-26d4d7681c09c6ae0d819d26",
    "X-Forwarded-Host": "localhost:8765"
  },
  "origin": "0:0:0:0:0:0:0:1, 73.96.191.223",
  "url": "http://localhost:8765/get?param=MyValue"
}
```


this allows us to do the routing manually instead of via discovery --- how to do it?


First - disable discovert in the api-agetway application.properties

```
spring.application.name=api-gateway
server.port=8765
eureka.client.service-url.default-zone=http://localhost:8761/eureka
#spring.cloud.gateway.discovery.locator.enabled=true
#spring.cloud.gateway.discovery.locator.lower-case-service-id=true
```


Ok -- lets back up

####Directly to the services

- http://localhost:8100/currency-conversion/from/USD/to/INR/quantity/100
- http://localhost:8000/currency-exchange/from/USD/to/INR

####Via gateway with discovery properties on
- http://localhost:8765/currency-conversion-service/currency-conversion/from/USD/to/INR/quantity/10
- http://localhost:8765/currency-exchange-service/currency-exchange/from/USD/to/INR

####Turn discovery off for exchange - and route directly to the lb

- http://localhost:8765/currency-conversion/from/USD/to/INR/quantity/100
- http://localhost:8765/currency-exchange/from/USD/to/INR

AFTER doing the following:

- add a new config class

```java

@Configuration
public class ApiGatewayConfiguration {

    @Bean
    public RouteLocator routeLocator(RouteLocatorBuilder routeLocatorBuilder) {

      return routeLocatorBuilder.routes()
              .route( p -> p.path("/currency-exchange/**").uri("lb://currency-exchange-service"))
              .route( p -> p.path("/currency-conversion/**").uri("lb://currency-conversion-service"))
              .build();
    }
}

```

turn discovery off in application.properties

```
spring.application.name=api-gateway
server.port=8765
eureka.client.service-url.default-zone=http://localhost:8761/eureka
#spring.cloud.gateway.discovery.locator.enabled=true
#spring.cloud.gateway.discovery.locator.lower-case-service-id=true

```


####Rewritting

```java
@Configuration
public class ApiGatewayConfiguration {

    @Bean
    public RouteLocator routeLocator(RouteLocatorBuilder routeLocatorBuilder) {

        return routeLocatorBuilder.routes()
                // exchange route
                .route( p -> p.path("/currency-exchange/**").uri("lb://currency-exchange-service"))

                // rewrite conversion shortcut so http://localhost:8765/fx/from/USD/to/INR/quantity/100 works
                .route( p -> p.path("/fx/**")
                        .filters( f -> f
                                .rewritePath("/fx/(?<segment>.*)", "/currency-conversion/${segment}"))
                        .uri("lb://currency-conversion-service"))
                // normal conversion route
                .route( p -> p.path("/currency-conversion/**").uri("lb://currency-conversion-service"))
                .build();
    }
}
```
