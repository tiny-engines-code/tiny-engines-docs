# client

every service that calls another service - use feign instead of resttemplate()

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.cloud:spring-cloud-starter-config'
    implementation 'org.springframework.cloud:spring-cloud-starter-openfeign'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

application class

```java
@SpringBootApplication
@EnableFeignClients
public class CurrencyConversionServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(CurrencyConversionServiceApplication.class, args);
    }

}
```

create a proxy interface
```java
@FeignClient(name="currency-exchange-service",  url="localhost:8000")
public interface CurrencyExchangeProxy {

    @GetMapping("/currency-exchange/from/{from}/to/{to}")
    public CurrencyConversion getExchangeRate(@PathVariable("from") String from, @PathVariable("to") String to) ;
}

```

MAKE SURE that the name (e.g. name="currency-exchange-service") is the same as the configured name of the service you are trying to each -- we find it by  name:

```
spring.application.name=currency-exchange-service
```



use the proxy to call the service

```java
private final CurrencyExchangeProxy proxy;

public CurrencyConversionController(CurrencyExchangeProxy feinClient) {
    this.proxy = feinClient;
}


@GetMapping("/currency-conversion/from/{from}/to/{to}/quantity/{quantity}")
public CurrencyConversion getExchangeRatePoxy(@PathVariable String from, @PathVariable String to, @PathVariable String quantity) {

     var exchangeRates = proxy.getExchangeRate(from, to);

     if (exchangeRates == null)
         throw new RuntimeException("can't find exchange rate");

    var currencyConversion = CurrencyConversion.builder()
            .id(1000L)
            .conversionMultiple(exchangeRates.getConversionMultiple())
            .fromCurrency(from)
            .toCurrency(to)
            .port(exchangeRates.getPort())
            .fromQuantity(BigDecimal.ZERO)
            .toQuantity(BigDecimal.ZERO)
            .build();

    currencyConversion.calculateExchangeRate(new BigDecimal(quantity));
    return currencyConversion;
}

```
