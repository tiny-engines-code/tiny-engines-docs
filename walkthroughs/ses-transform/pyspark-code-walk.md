
{% hint style="info" %}
Read the code [HERE](https://github.com/tiny-engines-code/s3-spark-transform)
{% endhint %}

##Code
This is a quick walk-through of the basic logic for the Simple Email Service (SES) event processor. It's a textbook implementation with the basic structure of the solution and some code to prove out the concepts.
A production implementation will include error handling, logging, security deployments, etc. but this is a good place to start to understand what we are building.

### main.py
Out goal is to read SES events as batch files from a fileserver (NFS or s3) and transform them into a flat queryable data schema

```python 
# --------------------------------------------------
# main
#    - get a spark session and call the real etl processor
#
if __name__ == "__main__":

    spark = get_spark()
    local_path = "/Users/clome1/Source/projects/tiny-engines/s3-spark-transform"

    etl(spark, input_path=f"{local_path}/input/", output_path=f"{local_path}/output_local/")

```

SES reports diffrent event classes (e.g. send, ounce, etc) into different directories and while there's some performance benefit to reading them all at once, there are also benefits to reading each different event class as a separate job.  We are going to take that approach in this example.

```python
def etl(pspark: SparkSession, input_path, output_path, checkpoint_path=None):
    for i in ["open", "delivery", "complaint", "send", "reject", "bounce", "click"]:
        process_s3_directory(pspark, i, input_path, output_path, checkpoint_path)

```

### readers.py
We will want to read the files as a structured stream so we can take advantage of Apache Spark's checkpoint functionality.   Spark Structured Streaming will keep track of where it is in the "stream" -- and when you add more files to a directory structure it will consider them part of the "stream" and read them automatically.

But it's nice to be able to check your code using a batch readr and write - so I'll include both the batch and the streaming implementations in this example.

```python
# --------------------------------------------------
#  read as a batch
#
def read_json_file(spark: SparkSession, input_folder: str):
    raw = spark.read.schema(input_schema).json(input_folder)
    return transform_ses_records(raw)


# --------------------------------------------------
#  read as a stream
#
def read_json_streaming(spark: SparkSession, input_folder: str):
    raw = spark.readStream.schema(input_schema).json(input_folder)
    return transform_ses_records(raw)



```

### transformer.py

The heart of the requirement is transforming the different SES record schemas into a single flat schema so they can be more easily pivoted as a single data set.

```python
# --------------------------------------------------
#  transform_ses_records
#       - Main transform method
#
#  input:  any ses events df
#  output: flat conformed records df
#
def transform_ses_records(raw: DataFrame) -> DataFrame:
    subtype_udf = udf(get_event_data, event_schema)
    filename_udf = udf(get_file_info, file_schema)

    conformed_df = conform_events(raw)

    df = conformed_df \
        .withColumn("Headers", col("mail.commonHeaders")) \
        .withColumn("Tags", col("mail.tags")) \
        .withColumn("FileInfo", filename_udf(input_file_name())) \
        .withColumn("EventInfo", subtype_udf(conformed_df['eventType'], conformed_df['event']))

    return df.select(lower(col("eventType")).alias("event_class"),
                     lit(current_timestamp()).alias("etl_time"),
                     col("mail.timestamp").alias("timestamp"),
                     col("mail.source").alias("source"),
                     col("mail.sendingAccountId").alias("account"),
                     col("mail.messageId").alias("message_id"),
                     "Headers.*",
                     "EventInfo.*",
                     col("event").alias("event_data"),
                     "FileInfo.*").withColumn("completion_time", to_timestamp(col("event_time")))

```

###writers.py
As with the readers -- I've included both batch and streaming writers. I've thrown in a jdbc writer as well so that we can dumpl quickly to Postgres demonstrate the big pivot which is an important outcome of this work.  

```python
def write_stream_to_filestream(mail_df: DataFrame, mail_folder: str, checkpoint_path: str):
    x = datetime.now()
    sub_folder = f"year={x.year}/month={x.month:02}/day={x.day:02}/hour={x.hour:02}"

    mail_query = mail_df \
        .writeStream \
        .option("path", mail_folder + sub_folder) \
        .option("checkpointLocation", f"{checkpoint_path}/mail") \
        .format('json') \
        .start() \
        .awaitTermination()
```