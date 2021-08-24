
## input data
AWS provides SES customers with separate directories for each class of event.

####file system 
```text
 ├── bounce
 │   └── *.json
 ├── click
 │   └── *.json
 ├── complaint
 │   └── *.json
 ├── delivery
 │   └── *.json
 ├── open
 │   └── *.json
 ├── reject
 │   └── *.json
 └── send
│    └── send.json
```

####schema
```java
StructType([
    StructField('eventType', StringType(), True),
    StructField('delivery', StringType(), True),  
    StructField('send', StringType(), True),
    StructField('reject', StringType(), True),
    StructField('open', StringType(), True),
    StructField('bounce', StringType(), True),
    StructField('click', StringType(), True),
    StructField('complaint', StringType(), True),
    StructField('mail', StructType([
        StructField('timestamp', StringType(), True),
        StructField('source', StringType(), True),
        StructField('sendingAccountId', StringType(), True),
        StructField('messageId', StringType(), True),
        StructField('destination', ArrayType(StringType()), True),
        StructField('headersTruncated', BooleanType(), True),
        StructField('tags', MapType(StringType(), ArrayType(StringType())), True),
        StructField('headers', ArrayType(
            StructType([
                StructField('name', StringType(), True),
                StructField('value', StringType(), True)])), True),

        StructField('commonHeaders', StructType([
            StructField('from', ArrayType(StringType()), True),
            StructField('to', ArrayType(StringType()), True),
            StructField('messageId', StringType(), True),
            StructField('subject', StringType(), True)]), True)

    ])
```
####example
```json
{
  "eventType": "Bounce",
  "mail": {
    "timestamp": "2021-08-23 06:56:30.087",
    "source": "Contoso <test@contoso.mail.com>",
    "sourceArn": "arn:aws:ses:us-east-1:123:identity/mail.contoso.com",
    "sendingAccountId": "742617195046",
    "messageId": "1a70218a-de80-4cf9-9802-2ca63f175f87",
    "destination": ["adella.gerlach@yahoo.com"],
    "headersTruncated": false,
    "headers": [...],
    "commonHeaders": {
      "from": ["Contoso <test@contoso.mail.com>"],
      "to": ["adella.gerlach@yahoo.com"],
      "messageId": "1a70218a-de80-4cf9-9802-2ca63f175f87",
      "subject": "Hello from Contoso.com!"
    },
    "tags": {
      "ses:operation": ["SendRawEmail"],
      "ses:configuration-set": ["config-set-value"],
      "ses:source-ip": ["10.22.22.33"],
      "ses:from-domain": ["mail@contoso.com"],
      "ses:caller-identity": ["caller-id-value"]
    }
  },
  "bounce": {
    "feedbackId": "0100017755bd1a4a-653e5aa7-6579-4300-b6db-20d34f971c80-000000",
    "bounceType": "Transient",
    "bounceSubType": "mailboxfull",
    "bouncedRecipients": [
      {
        "emailAddress": "adella.gerlach@yahoo.com",
        "action": "delayed",
        "status": "4.0.0",
        "diagnosticCode": "smtp; 452 4.2.2 Mailbox full"
      }
    ],
    "timestamp": "2021-08-23 06:56:32.087",
    "reportingMTA": "dns; s01.spamshield.eu"
  }
}
```
