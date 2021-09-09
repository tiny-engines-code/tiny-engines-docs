
---
#### ses-transformer proof of concept (POC) 

We'll build the solution on a laptop for development - with the goal of porting it to the Amazon s3 Parquet filesystem, and using Amazon Athena as our database

For the development environment today, well need:
* [x] Python 3+
* [x] Pycharm IDE (but this can be any IDE or test editor)
* [x] Pyspark (Apache Spark)
* [x] Postgres (just to quickly model our sql while developing)


### POC code

> The code is available here: [Github](https://github.com/tiny-engines-code/s3-spark-transform)

The files for this project are organized as follows:
* An `input` directory for some "input" data that we can test with
* A `sql` directory for SQL statements we'll use to query the final data
* A `src` directory for the pyspark code that will transform the raw SES events into our records

The program will create:
* An output_local directory - when we run the non-streaming version
* An output_streaming directory for the streaming examples

```text
// Directory structure

├── input                           --- dev test files
│   ├── bounce
│   │   └── bounce.json
│   ├── click
│   │   └── click.json
│   ├── complaint
│   │   └── complaint.json
│   ├── delivery
│   │   └── delivery.json
│   ├── open
│   │   └── open.json
│   ├── reject
│   │   └── reject.json
│   └── send
│       └── send.json
│
├── sql                            --- sql to create pivot
│   ├── 01-mock_request_data.sql
│   ├── 02_pivot_base.sql
│   ├── 03_ses_pivot.sql
│   └── 04_join_pivot.sql
│
├── src                            --- pyspark code
   ├── main.py                             -- main
   ├── readers.py                          -- read and process files
   ├── transformer.py                      -- perform transformations
   └── writers.py                          -- write to jdbc, batch and stream writers

```
