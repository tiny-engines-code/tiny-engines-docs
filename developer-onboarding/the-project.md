---
description: >-
  We'll use a simple textbook solution to illustrate some of the components of a complete production deliverable.
---
I'm going to cover the additional skills I alluded to in the last section using a simple solution consisting of a single microservice and a feedback loop.  We'll start off with basic textbook code and gradually add in examples of a particularly interesting subject. 

Again, this is not a tutorial.  Although the code is available in Github I'm not trying to teach how to build the solution.   I'm just using it as a simple solution that we can use to explore and provide a few real examples of the areas we are covering.  I'll cover some skills in more depth -- others I'll just cover a basic outline and leave them for further research   

### What are we building?
We are going to build a simple pipeline consisting of:
* [x] A `SendgridMailer` REST service
* [x] An `Event Activity` webhook listener
* [x] A `Kafka` producer and consumer to ingest callback data from SendGrid
* [x] A Connector that streams records out of Kafka and into an `AWS Athena` Database

![](../.gitbook/assets/sendgrid-personal-sendgrid-pipeline.png)
* [x] A Pyspark "sweeper" utility that picks up Dead Letter data in AWS S3 and stores it into our AWS Athena Database

---
* The `SendgridMailer` REST service 
  * accepts a simple json request from the user
  * validates, format and send the request to the Twilio **SendGrid** email API
  * The SendGrid API will send the email we constructed to the recipient
<p/>

* The `Event Activity` webhook listener that can
  * receives information about the email we sent from the **SendGrid** webhook service,
  * That information includes whether the customer received and opened the email, and
  * whether they 'clicked' on any link in the email body
  * it then forwards the raw payload to a **Confluent Kafka** message broker
<p/>
<p/>


---

### Components

|**Component** | **Description** |  
| :--- | :--- |
| SendGridMailer | Our rest-service that listens for request to send email, then formats and sends to SendGrid |  |  
| SendGrid | An external service owned by Twilio that sends emails on behalf of its customers and then provides asynchronous information about how the customer interacted with the email|  |  
| EventActivity | Our second rest-service that accepts callbacks from SendGrid, and sends them to Kafka.    |  |  
| Confluent Kafka | A high-volume message broker |  |  
| Connector | There are many ways to get information from Kafka to a database.  In this example we'll store the data to parquet data on AWS S3 |  |  


