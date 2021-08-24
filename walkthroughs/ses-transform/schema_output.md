###output data
```java
StructType([
    StructField('event_class', StringType(), True),
    StructField('event_type', StringType(), True),  
    StructField('event_subclass', StringType(), True),
    StructField('etl_time', StringType(), True),
    StructField('timestamp', StringType(), True),
    StructField('source', StringType(), True),
    StructField('account', StringType(), True),
    StructField('message_id', StringType(), True),
    StructField('from', ArrayType(StringType()), True),
    StructField('to', ArrayType(StringType()), True),
    StructField('messageId', StringType(), True),
    StructField('subject', StringType(), True),
    StructField('event_time', StringType(), True),
    StructField('event_recipients', ArrayType(StringType()), True),
    StructField('event_data', StringType(), True),
    StructField('bucket', StringType(), True),
    StructField('filename', StringType(), True),
    StructField('completion_time', StringType(), True)
    ])
```


###example
```json
{
  "event_class": "bounce",
  "event_type": "transient",
  "event_subclass": "mailboxfull",
  "etl_time": "2020-08-23T11:36:03.116-07:00",
  "timestamp": "2021-08-23 06:55:29.174",
  "source": "Contoso <test@contoso.mail.com>",
  "account": "742617195046",
  "message_id": "7ec4ddd6-cb9c-4c8a-b6bf-3dd11d727871",
  "from": ["Contoso <test@contoso.mail.com>"],
  "to": ["ernesto.jakubowski@hotmail.com" ],
  "messageId": "7ec4ddd6-cb9c-4c8a-b6bf-3dd11d727871",
  "subject": "Hello from Contoso.com!",
  "event_time": "2021-08-23 06:55:31.174",
  "event_recipients": "[{action=delayed, emailAddress=ernesto.jakubowski@hotmail.com, diagnosticCode=smtp; 452 4.2.2 Mailbox full, status=4.0.0}]",
  "event_data": "{....}",
  "bucket": "not-supported",
  "filename": "file:///s3-spark-transform/input/bounce/bounce.json",
  "completion_time": "2020-08-23T06:55:31.174-07:00"
}
```