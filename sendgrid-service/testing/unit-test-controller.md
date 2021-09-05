---
description: Unit testing the SendgridController
---

The SendgridController is a REST API that receives a simple json request fr an email to be sent, and responds back with the status of our attempt to format and send that email to Sendgrid.

The SendgridController:

* [ ] Receives a POST /send request with a JSON body
* [ ] Calls the requestHandler() function of the SendgridHandler class
* [ ] Returns a status code as its response to the caller


![](../../.gitbook/assets/sendgrid-personal-controller.png)


<p/><strong>The function "function" under test is a POST /send request</strong>

* A call to the function has the following components:
  * The path  \("/email/v2/send"\)
  * The method \(POST\)
  * A json string having a simplified email request


<p/><strong>The function accepts a JSON request body</strong>

* The **path** can be correct or incorrect
* The method can be **POST** or an unsupported method such as GET, PUT, etc.
* The **JSON** string can be valid or invalid



<p/><strong>What kind of response could we possibly get back</strong>

* a Sendgrid Response object
* a Runtime exception
* a null Response object



### Testing the controller inputs

First, we want to test how the controller responds to good and bad inputs.  In this is case we can establish a pattern in one unt test and then simply vary the data to extend it to other cases.  That's the case with the client input. 

For each test we can 
1. create a MockMvc call to the controller - this mimics a real call to the /send endpoint
2. set the **content** that we want to test for
3. expect the correct response for that content

We'll use this pattern to test:
1. **Bad json** format (Something that will fail json conversion)
2. **Bad path** (the client is calling a path that we do not support)
3. **Bad method** (something other than POST)

{% code title="// example - bad json format" %}
```java
@Test
void handles_bad_json_format() throws Exception {
   // WHEN the client calls with  a bad json format
   var expectedStatus = HttpStatus.INTERNAL_SERVER_ERROR;
   
   // THEN expect an internal service error
   mockMvc.perform(MockMvcRequestBuilders.post("/email/v2/send")
      .contentType(MediaType.APPLICATION_JSON)
      .content("{ \"bad }")
      .accept(MediaType.APPLICATION_JSON))
      .andExpect(result -> assertEquals("Status",
           expectedStatus.value(), 
           result.getResponse().getStatus()));
}


```
{% endcode %}

### Testing SendgridHandler::requestHandler responses 

The goal of the controller is to get the user input and hand off the processing to a requestHandler function.  With that design, the only thing we need to test  is how it handles the return from the requestHandler.  We'll use the same pattern as above, but we need to mock the requestHandler so that we can get the return values we want to test.

1. set up the MockMvc call
2. Mock the requestHandler\(\) to return the value we want to test
3. expect the correct http status response for that content

For each test we can
1. Use the same MockMvc patter for inputs
2. set the **Response** conditions that the SendgridHandler could return
3. expect the correct response for that content

We'll use this pattern to test:
1. **Any return code** Handle any return code we get from the SendgridHandler
2. **Null return** Handle a null returning from the SendgridHandler
3. **Exceptions** Handle a runtime exception being thrown from the SendgridHandler



{% code title="// example - handler throws an exception" %}
```java
@Test  
void handles_sendgrid_handle_exceptions() throws Exception {
  var expectedStatus = HttpStatus.INTERNAL_SERVER_ERROR;
  
  Mockito.when(sendgridHandler.requestHandler(anyString())).thenThrow(new RuntimeException("Bad juju"));
  
  mockMvc.perform(MockMvcRequestBuilders.post("/email/v2/send")
      .contentType(MediaType.APPLICATION_JSON)
      .content(content)
      .accept(MediaType.APPLICATION_JSON))
      .andExpect(result -> assertEquals("Status", expectedStatus.value(), result.getResponse().getStatus()));
}
```
{% endcode %}
