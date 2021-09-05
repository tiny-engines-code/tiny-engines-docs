###Creating a Cucumber scenario

For this example, we'll first add the Cucumber modules to our build.  I'm adding the spring module as well because we are working wih Spring Boot.  At the current date there are a few additional configurations and files we need to add to make Cucumber work with Junit5 - we'll list those in this section

---
#####Setup

Add the Cucumber modules to the build 

{% code title="build.gradle.java" %}
```javascript
configurations {
    cucumberRuntime {
        extendsFrom testImplementation
    }
}

dependencies {

    ...
    
// Cucumber
testImplementation group: 'io.cucumber', name: 'cucumber-java', version:  "${cukesVersion}"
testImplementation group: 'io.cucumber', name: 'cucumber-junit-platform-engine', version: "${cukesVersion}"
testImplementation group: 'io.cucumber', name: 'cucumber-spring', version: "${cukesVersion}"
testImplementation group: 'io.cucumber', name: 'cucumber-core', version: "${cukesVersion}"
}
```

{% endcode %}

Add a junit-platform.properties file for cucumber junit properties 

---
##### Code

To support Junit5 and Cucumber as of the date of this walk-through we separate out the typical annotations into separate classes

* [x] create a runner class (RunCucumber) annotated with the @Cucumber annotation
* [x] create a spring configuration class (CucumberSpringConfiguration)
* [x] create the feature file (mailer.feature)
* [x] create the supporting test cases

![](../../../.gitbook/assets/run-cucumber.png)

* [x] create a runner class (RunCucumber) annotated with the @Cucumber annotation

```java 
@Cucumber
public class RunCucumberTest {
}
```

* [x] create a spring configuration class (CucumberSpringConfiguration)

```java 
@SpringBootTest(
        webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT,
        classes = com.chrislomeli.sendgridmailer.SendgridMailerApplication.class)
@ContextConfiguration(initializers = {WireMockInitializer.class})
@AutoConfigureWebTestClient
@CucumberContextConfiguration
public class CucumberSpringConfiguration {

}
```

* [x] create the feature file (mailer.feature)

For this example scenario we want to ensure that invalid values are caught before they are sent to the SendGrid API.  We start off with one valid payload which will mak it throough our validation but will be 'caught' before actually being sent.  The rest of the cases have on missing or incorrect field per test. 

{% code title="mailer.feature" %}
```gherkin
Feature: Sendgrid acceptance test example
  A few example bdd tests using cucumber as the driver

  Scenario: Invalid values should be caught before sending to the API
    Given the API is available
    When I am sending valid and invalid emails
      | sender | from           | to              | subject     | args             | body                     | expected |
      | "me"   | me@gmail.com   | you@gmail.com   | Hello world | {"head": "bang"} | I was a teenage werewolf | 200      |
      | "john" | me@gmail.com   | ERROR.gmail.com | Hello world | {"head": "bang"} | I was a teenage werewolf | 400      |
      | "fred" | ERROR@gmailcom | you@gmail.com   | Hello world | {"head": "bang"} | I was a teenage werewolf | 400      |
      | "me"   | me@gmail.com   | you@gmail.com   |             | {"head": "bang"} | I was a teenage werewolf | 400      |
      | "me"   | me@gmail.com   | you@gmail.com   | Hello world |                  | I was a teenage werewolf | 200      |
      | "me"   | me@gmail.com   | you@gmail.com   | Hello world | {"head": "bang"} |                          | 200      |
      |        | me@gmail.com   | you@gmail.com   | Hello world | {"head": "bang"} | I was a teenage werewolf | 200      |
    Then Validation errors should be caught before being sent to the API
```

{% endcode %}

* [x] create the supporting test cases

{% code title="HandleInvalidFormats.java" %}
```java 
    ... 
 
    @Given("the API is available")
    public void the_api_is_available() {
        mailer = new SendgridMailer(new Client(true));  // set to true to get http
        sendgridProperties.setHost("mocked");
        sendgridProperties.setApiKeyValue(this.apiKeyValue);
        sendgridProperties.setApiVersion(this.apiVersion);
        sendgridProperties.setSdkVersion(this.sdkVersion);
    }

    @When("I am sending valid and invalid emails")
    public void i_am_sending_the_following_emails(io.cucumber.datatable.DataTable dataTable) {
        this.testSuite = new ArrayList<>();
        List<Map<String, String>> rows = dataTable.asMaps(String.class, String.class);
        for (Map<String, String> columns : rows) {

            var sg = SendgridRequest.builder()
                    .senderName(columns.get("sender"))
                    .toAddress(columns.get("to"))
                    .fromAddress(columns.get("from"))
                    .subject(columns.get("subject"))
                    .content(columns.get("body"))
                    .build();

            var status = Integer.parseInt(columns.get("expected"));
            this.testSuite.add(Pair.of(status, sg));
        }
    }

    @Then("Validation errors should be caught before being sent to the API")
    public void then_i_should_receive_the_expected_reponses() {
        this.testSuite.forEach(x -> handle_invalid_formats(x.getLeft(), x.getRight()));
    }

```
