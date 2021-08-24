
### Motivation
We'll have a data record detailing what we sent to SES. Our goal is to join that local record with the SES events data set so that we can trace the "journey" of each email for each recipient.  Lets tae an example to nmake that more clear

#### Review the ses_mail table
One of the writers in the repository can be used to write to Postgres instead of a file.   It's just easier to quickly model and 'tweak" data this way rather than dumping it the parquet and implementing Redshift, or Athena. Eventually we will want to be in one of those "big" data stores, so we can't use any specialized pivot functions.  Using the jdbc writer from th sample code I've just dumped some scrubbed data into a Postgres table called **ses_mail**.

<details> 
    SES_MAIL record

```json
  {
    "event_class": "open",
    "etl_time": "2021-08-23 11:32:30.209687",
    "timestamp": "2021-08-23 06:56:30.37",
    "source": "Contoso <test@contoso.mail.com>",
    "account": "742617195046",
    "message_id": "25491b38-bbef-4af0-9c0b-a92262b3bd3c",
    "from": ["Contoso <test@contoso.mail.com>"],
    "to": ["oliva.mante@gmail.com"],
    "messageId": "25491b38-bbef-4af0-9c0b-a92262b3bd3c",
    "subject": "Hello from Contoso.com!",
    "event_time": "2021-08-23 06:56:32.37",
    "event_type": "",
    "event_subclass": "",
    "event_recipients": "[]",
    "event_data": "{\"timestamp\":\"2021-08-2...130.176.17.135\"}",
    "bucket": "not-supported",
    "filename": "file:///Users/c../s3-spark-transform/input/open/open.json",
    "completion_time": "2021-08-23 06:56:32.370000",
    "ctid": "(0,1)"
  }
```
</details>

---

#### Create some fake request data
Let's create a "fake" data-set representing our local data.  

```sql
create table IF NOT EXISTS ses_requests as (
select message_id,    -- join key
   "to" as recipient,
   (completion_time::timestamp) - (random() * 4)::int * interval '1 seconds' request_time, -- random request time
   coalesce((select ARRAY ['LATAM', 'APAC', 'EU'])[floor((random() * 20))::int],'NA') as region -- random region
  from ses_mail
);

```

```json
  {
    "message_id": "25491b38-bbef-4af0-9c0b-a92262b3bd3c",
    "recipient": ["oliva.mante@gmail.com"],
    "request_time": "2021-08-23 06:56:30.370000",
    "region": "NA",
    "ctid": "(0,1)"
  }

```
---
#### Pivot the ses data
Because all of the SES events are now in one simple data set users will be able to easily pivot across any recipients journey - for every email we send. 
![](../../.gitbook/assets/pyspark/pivot.png)

---
#### Summarize by region
We can now use this to join to our internal (fake) data and the ses records to summarize over any column and across any cohort.  In this simple example we are grouping by region for the total cohort (all classes, all times)  

![](../../.gitbook/assets/pyspark/by-region.png)