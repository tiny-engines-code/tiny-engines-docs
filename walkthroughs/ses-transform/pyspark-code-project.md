

##Project
The code to perform a simple transform is just a small part of a software deliverable.  We'll walk through an implementation today to help cement the idea of what the solution needs to do.  But this is "textbook" logic and the non-functional requirements require more effort than the code we and our stakholders tend to focus on:

* [x] tuning
* [x] scaling
* [x] deploying
* [x] quality control
* [x] monitoring
* [x] security
* [x] error handling
* [x] logging, metrics and observability

But we will walk through a textbook implementation today.   That code is available  on GitHub.

{% hint style="info" %}
Code is stored [HERE](https://github.com/tiny-engines-code/s3-spark-transform)
{% endhint %}


### Directory structure
The files for this project are organized as follows:
* An `input` directory for "input" data that we can test with
* A `sql` directory for SQL statements we'll use to query the final data
* A `src` directory for the pyspark code that will transform the raw SES events into our records

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


