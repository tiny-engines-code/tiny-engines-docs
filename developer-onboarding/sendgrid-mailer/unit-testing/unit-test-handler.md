---
description: Unit testing with Junit5
---

# Unit testing the SendgridHandler

The SendgridHandler.responseHandler\(\) takes a json string as its input and returns a Response object as its output.   It performs two testable functions:

1. Json conversion - converts the json string to a SendgridRequest bean
   1. handle good conversions
   2. handle exceptions
2. Handles the SendgridMailer.send\(\) return 
   1. handle all status codes \(this is probably overkill\)
   2. handles any exceptions

#### Testing the json conversion

We don't have many scenarios to cover.  The conversion will either fail or succeed for any number of reasons.   If it fails we want to return a BAD\_REQUEST.

```java
@Test
void test_handle_null_input_returns_400() throws Exception {
    var response = new SendgridHandler(new SendGridMailer())
                            .requestHandler(null);
                            
    assertThat(
        response.getStatusCode())
          .isEqualTo(HttpStatus.BAD_REQUEST.value());
}
```

#### 

#### Testing the SendgridMailer response

The repeatable pattern for all SendgridMailer returns is to mock the return we are trying to test, then test for the expected results.

{% code title="example - handle a mailer exception" %}
```java
@Test 
void handles_requestHandler_null() throws Exception {
    // mock the mailer
    var sendGridMailer = mock(SendGridMailer.class);
    when(sendGridMailer.send(any(SendgridRequest.class)))
            .thenThrow(new RuntimeException("Badd JuJu"));
    
    // execute
    var response = new SendgridHandler(sendGridMailer)
                            .requestHandler(createRequest());
    // test output
    assertAll(
         () -> assertEquals(response.getStatusCode(), HttpStatus.INTERNAL_SERVER_ERROR.value()),
         () -> assertEquals("Badd JuJu", response.getBody())
    );
}
```
{% endcode %}

