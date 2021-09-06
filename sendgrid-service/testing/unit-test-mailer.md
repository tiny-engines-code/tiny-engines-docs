


![](../../.gitbook/assets/sendgrid-personal-mailer.png)


<p/><strong>The function under test</strong>

* [ ] Receives a SendgridRequest object from the SendgridHandler
* [ ] Validates the data
* [ ] Calls the SendGrid API
* [ ] Passes the Response object back to the SendgridHandler


<p/><strong>Possible inputs</strong>

* The **data** in the SendgridRequest can be correct or incorrect
* if the contents are not valid - returns a **Response** object with the appropriate Http status code
* If the contents are valid
  * creates a SendGrid API **Request** object from our **SendgridRequest** request, and
  * calls the SendGrid API which returns a **Response** object

<p/><strong>Possible outputs</strong>

* The SendGrid API normally returns a SendGrid Response object
* The Response will contain an http status code
* We need to handle any http status so that later we can handle failure, retry, and circuit breaker cases
* We need to handle any Runtime exception


---

For testing we need to cover three basic boundaries

1. Verification of the input SendgridRequest object
2. Creation of a correct SendGrid API request based on the SendgridRequest
3. Calling the API and handling any response

---
#### Verification of the input SendgridRequest object

Since we want to test many scenarios, we'll use a parameterized test and pass several different scenarios into the same test.  

```java
private static Stream<Arguments> payloadProvider() {
    return Stream.of(
       Arguments.of("happy_path", HttpStatus.OK, "sender", "from@gmail.com", "to@gmail.com", "subjectline", "body", Map.of("cheese", "grater")),
       Arguments.of("bad_from_address", HttpStatus.BAD_REQUEST, "sender", "from.BAD.com", "to@gmail.com", "subjectline", "body", Map.of("cheese", "grater")),
       Arguments.of("bad_to_address", HttpStatus.BAD_REQUEST, "sender", "from@gmail.com", "to.BAD.com", "subjectline", "body", Map.of("cheese", "grater")),
       Arguments.of("empty_subject", HttpStatus.BAD_REQUEST, "sender", "from@gmail.com", "to@gmail.com", "", "body", Map.of("cheese", "grater")),
       Arguments.of("empty_body", HttpStatus.OK, "sender", "from@gmail.com", "to@gmail.com", "subject", "", Map.of("cheese", "grater")),
       Arguments.of("null_from_address", HttpStatus.BAD_REQUEST, "sender", null, "to@gmail.com", "subjectline", "body", Map.of("cheese", "grater")),
       Arguments.of("null_to_address", HttpStatus.BAD_REQUEST, "sender", "from@gmail.com", null, "subjectline", "body", Map.of("cheese", "grater")),
       Arguments.of("null_subject", HttpStatus.BAD_REQUEST, "sender", "from@gmail.com", "to@gmail.com", null, "body", Map.of("cheese", "grater")),
       Arguments.of("null_body", HttpStatus.OK, "sender", "from@gmail.com", "to@gmail.com", "subject", null, Map.of("cheese", "grater")),
       Arguments.of("null_args", HttpStatus.OK, "sender", "from@gmail.com", "to@gmail.com", "subject", "body", null)
   );
}
```

We don't want to call the real API so we'll mock the api() method that calls it.  But we do want to verify what we sent to the API, so we'll use a Captor to see the Request that was passed into our mocked  api\(\)

```text
@Captor
ArgumentCaptor<Request> apiRequestCaptor;/**
```

#####The method below takes any number of payload inputs and:
* [ ] returns any Validation errors before calling the SendGrid API
* [ ] If the request is valid re-formats the SendgridRequest into a SendGrid Request object
* [ ] calls the SendGrid API and handles any response


{% code title="// example - test the sendgridMailer" %}
```java
 * For each argument passed in call the send() method 
 *   and check the that actual status code equals the expected
 * Capture the Request that we send to the SendGrid API
 *   and validate the API was called correctly only when the contents were valid      
 */
@ParameterizedTest(name = "{index} => ''{0}'' - ''{1}''")
@MethodSource("payloadProvider")
void validates_requests_and_return_with_code_or_call_api(String message, HttpStatus expectedStatus, String sender, String from, String to, String subject, String body, Map<String, String> custom) throws IOException {
    // mock the API
    lenient().when(sendGridClient.api(any(Request.class))).thenReturn(new Response(HttpStatus.OK.value(), "{}", null));

    // create an input sendgridRequest based on the parameterized values passed in and set properties
    var mailer = new SendgridMailer(sendGridClient);
    mailer.apiKeyValue = this.apiKeyValue;
    mailer.host = this.host;
    mailer.apiVersion = this.apiVersion;
    mailer.sdkVersion = this.sdkVersion;

    // call SendgridMailer::send
    var response = mailer.send(
            SendgridRequest.builder()
                    .senderName(sender)
                    .toAddress(to)
                    .fromAddress(from)
                    .subject(subject)
                    .content(body)
                    .customArgs(custom)
                    .build());

    // verify the expected stats code
    assertThat(response.getStatusCode()).isEqualTo(expectedStatus.value());

    // if the status code is OK, then we should have caled the API
    //   - otherwise we should have returned early and NOT called the API
    if (expectedStatus == HttpStatus.OK) {
        verify(sendGridClient).api(apiRequestCaptor.capture());
        Request request = apiRequestCaptor.getValue();
        assertThat(request.getMethod()).isEqualTo(Method.POST);
        assertThat(request.getBaseUri()).isNotNull();
        assertThat(request.getEndpoint()).isEqualTo(String.format("/%s/mail/send", this.apiVersion));
        assertThat(request.getHeaders().getOrDefault("Authorization", "")).isEqualTo("Bearer " + apiKeyValue);
    } else {
        verifyNoInteractions(sendGridClient);

    }
}


```
{% endcode %}






