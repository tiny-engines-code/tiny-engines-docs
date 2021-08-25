

##Running the code on a laptop
In order to run the solution without mocks we have two dependencies:
* [x] Provide Sendgrid with a public IP address that it can send event activity to -- we'll use `ngrok` for this
* [x] Provide a Kafka broker to send our data to -- we'll use docker for this

We can always deploy a public facing gateway and a Confluent Kafka broker, but when developing it's sometimes helpful to be able to run from our laptops.  I'll focus on running from a laptop in this example.

---

####Steps

* [ ] Download and install the wonderful **ngrok** proxy
* [ ] In the project root directory type docker-compose up - to start Kafka and Kowl (Kowl is a Kafka UI)
* [ ] Start up our event activity service listening on port 5000 (in this example)
* [ ] Start up the ngrok proxy and get the proxy IP address
* [ ] Log into SendGrid and configure the Sendgrid `webhook` to send event data to our ngrok proxy
* [ ] Send some emails to ourselves -- use our SendgridMailer service or manually create an email in SendGrid
* [ ] Your email body should have a clickable link, so you can "click' on it when you get the email
* [ ] once you have received the email, log into Kafka to see the event activity
  * Open the Kafka UI (Kowl) at localhost:8080
  * review the `sendgrid_events` topic
  * You should see the following records
    * processed
    * opened
    * click (if you clicked on the link in the email)
---

####Sample email
```json
{
  "senderName": "me",
  "fromAddress": "sender@gmail.com",
  "toAddress": "receiver@gmail.com",
  "subject": "This is my email",
  "content": "Click here to generate a click event : http://www.google.com"

}
```
---
####Using ngrok

`prompt>` npx ngrok http 5000

```shell
ngrok by @inconshreveable                                                                                                                                                                                                                           (Ctrl+C to quit)

Session Status                online
Session Expires               1 hour, 59 minutes
Version                       2.3.40
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://78e3-73-96-191-223.ngrok.io -> http://localhost:5000
Forwarding                    https://78e3-73-96-191-223.ngrok.io -> http://localhost:5000

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```



in the example above ngrok is proxying public ip http://78e3-73-96-191-223.ngrok.io to our local event activit listener.
