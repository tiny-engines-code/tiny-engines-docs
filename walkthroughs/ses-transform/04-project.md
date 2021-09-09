
#### The project
The whole solution includes the following work-streams :
* [x] The `dev-ops` work to set up and maintain the AWS platform components
  * Firehose delivery streams for each event class (e.g. Bounce, Send, etc...)
  * S3 buckets and folders for each delivery stream
  * Scheduling and scripts to update the meta-data for the s3 folders
---
* [x] The `ses-transformer` that will transform into the database format we'll need
---
* [x] The `sample queries` for our customer to explore and validate the data

