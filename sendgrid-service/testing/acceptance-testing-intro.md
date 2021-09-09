

The idea with what I'm calling acceptance testing is that we are providing validation not to ourselves, but to business stakeholders.  Many feel that a more conversational language for creating test cases can help us communicate the business scenarios.  

#### BDD
BDD (Behavioral Driven Development) is an oft used approach to achieving that business communication.  It's a more conversational way of writing tests that some feel is more readable.  The 'language' that is often used for this is called "Gherkin". There are several frameworks that support BDD.  `Cucumber` is one such framework.  

#### When it's useful
The promise of BDD really requires that business users are closely and effectively involved in the creation and validation of tests.  If BBD is not a set of behaviors supported in the organization then it's just another testing framework, and honestly, all Java test frameworks (testNG, Juint, Mockito, Spock) accomplish the same things just fine without having to support yet another syntax.

But, if a team simply loves the way BDD tests are developed and can be efficient in testing, then they should use BDD.

####Cucumber
Like many BDD tools, Cucumber is not so much a new testing framework as a way of expressing the tests in a different way.  We'll still use most of our existing test frameworks 'under the covers'.  In this section we'll use Cucumber to express test that we actually write in Junit5 and Wiremock.


---

####Gherkin

The most widely-used language of BDD is Gherkin. Cucumber is a popular implementation of the Gherkin language and BDD.  The basic idea is that there's a very opinionated way to declare a test.

* [x] **Feature** - is a suite of tests
* [x] **Scenario** - is the title of a given test
* [x] **GIVEN** - is used for defining the starting state before the test starts
* [x] **WHEN** is used for defining an action
* [x] **THEN** is used for defining the expected or desired state after the action takes place
* [x] **AND** is for adding conditions or states to any of the above


As an example,

```gherkin
Scenario: Happy path email delivery
    Given that I want to send email to a valid recipient
    When I fill out a simple email form correctly
    And I send it to our new application
    Then I expect that an email request will be sent to SendGrid
    And I expect that the email will be delivered to the recipient
```

---
#### Cucumber

Cucumber "wires" these statements to the actual code that implements the test -- the actual test is often still in Junit, Spock or any and all the tools we've used so far.

![](../../.gitbook/assets/feature-method.png)


#### Cucumber feature files
Cucumber scenarios are stored in files with the .feature extension.

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

---
Once Cucumber has been configured properly then it can be run from gradle or directly from the Cucumber feature file.


![](../../.gitbook/assets/run-cucumber.png)