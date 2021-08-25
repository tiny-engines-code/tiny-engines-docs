Let's take a bird's eye view of what we are building today.  Our solution will:
* [x] Ingest event records from AWS and store them in S3
* [x] Transform different event records into a single flat schema
* [x] Provide the ability to join that data to our own internal data

### Data ingestion
Getting the data from AWS into our own s3 buckets is just a matter of configuration and I won't cover that part of the project.

---

####Inputs and outputs

The SES data format is a little different for each event class, so we are reading it into one "uber" `SES Input Schema` that brings the differences into our transformer as strings.  Then, we'll transform the data into a simple flat `Required Schema` output.

* [x] [SES Input Schema](schema_input.md)
* [x] [Required Schema](schema_output.md)

---

#### Data flow

* [x] The data is generated at AWS and pushed into Firehose delivery streams that we provide.  There's one delivery stream for each event class (e.g. Sent, Rejected, Bounced, Delivered, ...) and they are store in separate s3 folders.
* [x] We'll create an Apache Spark Structured Streaming solution to read the files, transform them and move them into a common bucket in parquet format.  
* [x] Once in parquet they can be processed and queried in AWS Athena, Redshift or Snowflake.


![](../../.gitbook/assets/pyspark/ses-flow.png)

---

####Tasks

* [x] Configure SES to push all activity to Kinesis Firehose delivery streams
  * each event class (e.g. delivery, bounce) requires a different stream
* [x] Create an `etl solution` using Spark Structured Streaming to
  * read all files
  * stream SES records into a common flat record schema
  * write the flat records to s3, parquet, snappy format
  * keep track of our position in the stream
* [x] If in s3/parquet, schedule near-real-time (10 minutes latency) meta-data updates


