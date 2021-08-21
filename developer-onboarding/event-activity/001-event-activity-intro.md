## Work in progress

{% hint style="info" %}
Code for this section is at: [Github](https://github.com/tiny-engines-code/webhook-service-walkthrough)
{% endhint %}

###Basic Flow

When we submit a request to SendGrid we get an immediate response that the request was received.  We can configure sendgrid with our webhook service so that we can get later information about how the recipient interacted with the email.
* [x] SendGrid completes processing of the request
* [x] The email makes it to the recipient (or not)
* [x] The recipient opens the email (or not)
* [x] The recipient clicks on any link that we've embedded in the email (or not)

###The service

If we want to know what happened after we requested the email, we need to set up a REST gateway so that SendGrid can deliver those events asynchronously.

The `Event Activity` service can listen for these events and store the raw data in Kafka for further processing.  Unlike the Sendgrid mailer service, we replace the controller with a reactive router so that we can explore testing that sort of service.  In the next few sections we'll

* [x] Do a brief walk-through of the code
* [x] Explore testng the reactive router 
* [x] Explore testing a message service client

![](../../.gitbook/assets/event-activity.png)

