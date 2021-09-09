
```python
def transform_ses_records(raw: DataFrame, event_class: str) -> DataFrame:
    """Transforms a raw SES Dataframe into a common schema Dataframe

    Args:
        :param raw: the spark session
        :param event_class one of "open", "delivery", "complaint", "send", "reject", "bounce", "click"

    Returns:
        * A transformed dataframe

    """
    subtype_udf = udf(get_event_data, event_schema)
    filename_udf = udf(get_file_info, file_schema)

    merge_df = raw.withColumnRenamed(event_class, "event")

    # flatten it out a bit and conform any class specific names e.g. bounce --> event
    df = merge_df \
        .withColumn("Headers", col("mail.commonHeaders")) \
        .withColumn("Tags", col("mail.tags")) \
        .withColumn("FileInfo", filename_udf(input_file_name())) \
        .withColumn("EventInfo", subtype_udf(merge_df['event']))

    # do the main transform, add some tracking info and return
    return df.select(lower(col("eventType")).alias("event_class"),
                     lit(current_timestamp()).alias("etl_time"),
                     col("mail.timestamp").alias("timestamp"),
                     col("mail.source").alias("source"),
                     col("mail.sendingAccountId").alias("account"),
                     col("mail.messageId").alias("message_id"),
                     "Headers.*",
                     # "Tags.*",
                     "EventInfo.*",
                     col("event").alias("event_data"),
                     "FileInfo.*").withColumn("completion_time", to_timestamp(col("event_time")))



```


```json
{
  "event_class": "open",
  "etl_time": "2021-09-07T20:24:15.093-07:00",
  "timestamp": "2021-08-23 06:56:30.37",
  "source": "Contoso <test@contoso.mail.com>",
  "account": "742617195046",
  "message_id": "25491b38-bbef-4af0-9c0b-a92262b3bd3c",
  "from": [
    "Contoso <test@contoso.mail.com>"
  ],
  "to": [
    "oliva.mante@gmail.com"
  ],
  "messageId": "25491b38-bbef-4af0-9c0b-a92262b3bd3c",
  "subject": "Hello from Contoso.com!",
  "event_time": "2021-08-23 06:56:32.37",
  "event_type": "",
  "event_subclass": "",
  "event_recipients": "[]",
  "event_data": "{\"timestamp\":\"2021-08-23 06:56:32.37\",\"userAgent\":\"Mozilla/5.0 (iPhone; CPU iPhone OS 14_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148\",\"ipAddress\":\"68.134.49.14, 130.176.17.135\"}",
  "bucket": "not-supported",
  "filename": "file:///Users/clome1/Source/projects/tiny-engines/s3-spark-transform/input/open/open.json",
  "completion_time": "2021-08-23T06:56:32.370-07:00"
}
```
