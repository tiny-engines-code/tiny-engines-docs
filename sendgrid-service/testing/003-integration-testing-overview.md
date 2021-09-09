####Unit testing recap
While we were unit testing we tested all the components ***individually***.  Piecing all those individual tests together we get all the way from the start to the final call the SendGrid client.  We were looking for the way our code would respond to different inputs - we were not trying to test SendGrid's code.  

When we got to the mailer function we did not want to actually call SendGrid and send a real email, so we mocked the entire SendGrid client with Mockito (below)   

![](../../.gitbook/assets/unittest-mock.png)

---
####Integration view
Now we want to test all our components together as a whole, and cover as much of the end to end functionality as possible, but we still don't want to actually call SendGrid and send a real email, and we want to create scenarios that would be difficult to re-create with a real production API.

So we use Wiremock to "mock" the SendGrid API.  Wiremock provides the capability to return anything th SendGrid API might return without having to set up each specific scenario.  And we won't ever accidentally call the real API and send an email.

![](../../.gitbook/assets/flow2-wiremock.png)

