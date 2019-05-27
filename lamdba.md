# Lambda

Receives two json encoded objects on invoke:
 * Context
 * Methods and Attributes
 * Event
 * One or more records
 
Limitations
 * 512MB /tmp ephemeral disk
 * 1024 threads max
 * 300 second execution time
 
Redshift Database Loader (pre-loaded function) example will push data from s3 on trigger, ETL and load to redshift

### Blogs 

https://aws.amazon.com/blogs/compute/amazon-kinesis-firehose-data-transformation-with-aws-lambda/

https://aws.amazon.com/blogs/compute/implementing-a-serverless-aws-iot-backend-with-aws-lambda-and-amazon-dynamodb/

https://aws.amazon.com/blogs/database/indexing-metadata-in-amazon-elasticsearch-service-using-aws-lambda-and-python/


### Docs

https://docs.aws.amazon.com/lambda/latest/dg/with-ddb.html

### Code
