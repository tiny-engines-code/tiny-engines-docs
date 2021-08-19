---
description: Unit testing with Junit5
---

# Unit testing the controller

Testing starts with modular code design.  Of course that's much easier in a textbook example like this, but the thought process for each unit under test \(method\) should be the same.  In this case the controller is just a simple gateway that calls a single function to handle the input, then returns the result back to the user.

For any unit under test we first determine what and why we are testing.  That includes understanding the external dependencies that provide input into our unit.  These are the variations that we want our unit to be able to handle.   

![](../../../.gitbook/assets/sendgrid-personal-page-6-1-.png)

In the case of the controller we have client inputs from the client that is calling the rest endpoint.  And we have process inputs because the controller is calling a handler function and needs to be able to handle whatever it gets back from that function.

#### 

#### client inputs

The SendgridController has a single endpoint with three inputs

1. The path  \("/email/v2/send"\)
2. The method \(POST\)
3. A json string

* The path can be correct or incorrect
  * correct url
  * incorrect url
* The method can be POST or something incorrect
  * has to be POST
* The json string can
  * be valid SendgridRequest json 
  * have bad format 
  * be null or empty

#### process inputs

SendgridController calls a single process, which can return

* a valid Response object with different http status codes
* an exceptions 
* a null

### 

### Testing the client input

![](../../../.gitbook/assets/sendgrid-personal-controller.png)

In this case we just want to test how the controller responds to good and bad inputs.  Once we establish a pattern we can often cover several scenarios reusing it with varying inputs and expected responses 

1. create a MockMvc call
2. set the content that we want to test
3. expect the correct response for that content

{% code title="// example - bad json format" %}
```java
@Test
void handles_bad_json_format() throws Exception {

   var expectedStatus = HttpStatus.INTERNAL_SERVER_ERROR;
   
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

### Testing requestHandler\(\) returns 

The goal of the controller is to get the user input and hand off the processing to a requestHandler function.  With that design, the only thing we need to test  is how it handles the return from the requestHandler.  We'll use the same pattern as above, but we need to mock the requestHandler so that we can get the return values we want to test.

1. create a MockMvc call
2. Mock the requestHandler\(\) to return the value we want to test
3. expect the correct http status response for that content

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
