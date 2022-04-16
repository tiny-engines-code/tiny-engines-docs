
#Config Server

###create the configuration repo

```
mkdir configuration_repo
cd configuration_repo

vi limits-service-dev.properties   # dev profile
  (add some properties)

git add .
git commit -m"local git repo - not pushed to remote"

```

limits-service-dev.properties
```
limits-service.minimum=1    ## backup / default
limits-service.maximum=10000   ## backup / default
```


###Create the config server

new spring boot gradle project

```groovy
dependencies {
    implementation 'org.springframework.cloud:spring-cloud-config-server'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

application.properties example

```
spring.application.name=spring-cloud-config-server
spring.cloud.config.server.git.uri=file:///Users/clome1/Source/java/in28minutes/git-localconfig-repo
server.port=8888
```


finally - annotate the main program

```java
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }

}

```


# Spring Cloud Config Client

pointing to the server on 8888 (above)

gradle build

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.cloud:spring-cloud-starter-config'
    annotationProcessor group: 'org.springframework.boot', name: 'spring-boot-configuration-processor'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```


application.properties

```
spring.application.name=limits-service
spring.cloud.config.enabled=true
spring.config.import=optional:configserver:http://localhost:8888
limits-service.minimum=0    ## backup value
limits-service.maximum=1   ## backup value
```


configuration class

```java
@Configuration
@ConfigurationProperties("limits-service")
@Data
public class LimitsConfiguration {
   private int minimum;
   private int maximum;
}

```
