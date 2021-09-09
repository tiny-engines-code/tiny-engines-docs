
AWS Simple Email Service sends emails to recipients on our behalf.  For many organizations there's a lot more to sending emails to customers than just calling a sendmail program, and having a service lke SES manage that for us is a huge benefit.  When SES sends emails for us, it also keeps track of whether those emails were delivered to the customer's inbox, and whether they were opened by the customer and even if the customer clicked on a link in the email.

![](../../.gitbook/assets/pyspark/ses-events.png)

Amazon SES can be configured to send event files back to us describing each milestone for a specific email.  We'll get many events for each email -- SES sends an event when the email is:
* [x] `rejected` or `sent`
* [x] `delivered` or `bounced`
* [x] `opened` by the recipient
* [x] whenever a recipient `clicked` on one of our embedded links
* [x] if the customer filed a `complaint` against our email

Each event "class" comes in a separate stream and has a slightly different schema.  We'll use Apache Spark to merge all the different event schemas into a single dataset that we can query to find the entire journey of any recipients email.




---


