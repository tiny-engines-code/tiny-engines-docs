# Overview

In this walk-through we'll cover converting event records from Amazon's Simple Email Service, and transforming and loading to a local data "lake".

---
## Background
AWS Simple Email Service sends emails to recipients on our behalf.  For many organizations there's a lot more to sending emails to customers than just calling a sendmail program, and having a service lke SES manage that for us is a huge benefit.  When SES sends emails for us it also keeps track of whether those emails were delivered to the customer's inbox, and whether they were opened by the customer and even if the customer clicked on a link in the email.  

![](../../.gitbook/assets/pyspark/ses-events.png)

Aws can send these events back to us as event records.  They can tell us:
* [x] was our email was `rejected` or `sent` to the recipient?
* [x] was it `delivered` to the recipient's inbox or `bounced`?
* [x] was it was `opened` by the recipient?
* [x] what links did the recipient `click` on?
* [x] did the recipient lodge a `complaint` about our email as junk?

Today we are focused ingesting this data back into our data.  That data joined with our internal data should be able to help us answer the additional question of:

* [x] How do users' success-rates differ by internal criteria such as region?
* [x] how can I trace each recipient's experience across the entire flow?



---


