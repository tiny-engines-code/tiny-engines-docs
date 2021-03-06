![](../../.gitbook/assets/flow2-API.png)

---

####Integration testing continued...
We want to be able to test the whole flow from the client's initial request, to the call to the SendGrid API.  But we don't want to actually send mail, and we want to control what we get back from the API, so we can create all the scenarios we want to test.

We'll accomplish that by using Wiremock to mock the SendGrid API.  As seen above, Wiremock lets us get all the way out to the actual call to SendGrid API - with Wiremock we are no longer mocking the SendGrid "client", we are mocking the SendGrid "server" itself.

---
#### Wiremock and Junit5 setup
Let's get started...
Currently, the Junit5 and Wiremock integration is tricky, so we'll step through it

Create the gradle dependency for wiremock
```java
// build.gradle
testImplementation group: 'com.github.tomakehurst', name: 'wiremock', version: '2.27.2'
```

Create a wiremock initializer class to bridge some of the application context gaps.

```java
public class WireMockInitializer
        implements ApplicationContextInitializer<ConfigurableApplicationContext> {

    @Override
    public void initialize(ConfigurableApplicationContext configurableApplicationContext) {
        WireMockServer wireMockServer = new WireMockServer(new WireMockConfiguration().dynamicPort());
        wireMockServer.start();

        configurableApplicationContext
                .getBeanFactory()
                .registerSingleton("wireMockServer", wireMockServer);

        configurableApplicationContext.addApplicationListener(applicationEvent -> {
            if (applicationEvent instanceof ContextClosedEvent) {
                wireMockServer.stop();
            }
        });

        TestPropertyValues
                .of(Map.of("todo_base_url", "http://localhost:" + wireMockServer.port()))
                .applyTo(configurableApplicationContext);
    }
}
```
---
#### Example - integration test
Create your tests.   We could use a rest client to create POST requests to our controller, but you'll often see developers  calling the controller's methods directly, and that's what we'll do here.

We'll use an argument generator to create all the status codes that we could get from the SendGrid API.   Given our present logic that seems like overkill, but in a real service we'd want to react differently based on the return code.  For 500 errors we might want to retry.  For most 400 errors we will not want to retry, but for 429 return codes we have a chance to throttle our request down and retry.  So being able to create those diffreent returns from the mocked SendGrid API will be needed.

```java 
    static Stream<Integer> statusCodeProvider() {
        return Stream.of(
                Stream.of(200,201,202, 428,429,431),
                IntStream.range(400, 426).boxed(),
                IntStream.range(500, 511).boxed()
        ).flatMap(x -> x);
    }
```

Finally, we'll create one example to run the scenarios we are creating with our statusCodeProvider generator.  The function 

```java 
    @ParameterizedTest
    @MethodSource("statusCodeProvider")
    void handle_valid_email_format_scenario(int expectedStatusValue) throws JsonProcessingException {
    
        /* --- a valid email format is submitted so that it can get all the way to the Sendgrid client---*/
        var thisEmail = validJsonEmail;

        /* --- We'll have the Wiremock return the specific http code that was passed in for this iteration ---*/
        var expectedStatus = HttpStatus.valueOf(expectedStatusValue);

        /* --- AND We call the service using Wiremock to mock the real SendGrid API---*/
        var matchUrl = "/" + this.apiVersion + "/" + "mail/send";
        this.wireMockServer.stubFor(
                post(urlMatching(matchUrl))
                        .willReturn(aResponse()
                                .withStatus(expectedStatus.value())
                                .withHeader("Content-Type", MediaType.APPLICATION_JSON_VALUE)
                                .withBody(responseBody(expectedStatus.value()))));

        SendgridController sendgridController = new SendgridController(new SendgridHandler(mailer));
        var response = sendgridController.handleRequest(thisEmail);

        /* --- THEN we will recieve expected status and apss it back up the stack to the user ---*/
        assertThat(response.getStatusCode()).isEqualTo(expectedStatus);
    }
```