---
description: >-
  We'll use a simple textbook solution to illustrate some of the components of a complete production deliverable.
---
![](../.gitbook/assets/sendgrid-personal-sendgrid-pipeline.png)

---

### What are we building?

We're going to cover some skills I alluded to in the last section using a simple solution consisting of a single microservice and a feedback loop.  We'll start off with basic textbook code and gradually add in examples of a particularly interesting subject. 

We are going to build a simple pipeline consisting of:
* [x] A blocking REST service that sends emails to a vendor (SendGrid) who then sends them to customers
* [x] A webhook listener that received information back from the vendor and stored it in Kafka
* [x] A connector that streams records out of Kafka and into an `AWS Athena` Database
* [x] A pyspark "sweeper" utility that picks up Dead Letter data in AWS S3 and stores it for use in AWS Athena

#### Components

|**Component** | **Description** |  
| :--- | :--- |
| SendGridMailer | Our rest-service that listens for request to send email, then formats and sends to SendGrid |  |  
| SendGrid | An external service owned by Twilio that sends emails on behalf of its customers and then provides asynchronous information about how the customer interacted with the email|  |  
| EventActivity | Our second rest-service that accepts callbacks from SendGrid, and sends them to Kafka.    |  |  
| Confluent Kafka | A high-volume message broker |  |  
| Connector | There are many ways to get information from Kafka to a database.  In this example we'll store the data to parquet data on AWS S3 |  |  


