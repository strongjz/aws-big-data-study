# Kinesis
  * Streams - Collect and process large streams of data
  * Analytics - Collect and process streaming data with sql
  * Firehose - For loading data into services like red shift or emr
  
### Uses
  * Logs and data feeds
  * Metrics and reporting
  * Real time analytics
  * Aggregations of data
  * Durable and Elastic
  
  * FIFO:
    * KPL - kinesis producer library
        * Adds a delay of RecordMaxBufferedTime. HIgher increases throughput but increases latency.
        * Batching
            * Aggregation: combines user records into a single streams record to increase efficiency of shards
            * Collection: batches groups of stream records to reduce http calls
    
    
    * KCL - kinesis client library
        * Consumes and processes kinesis data
        * Handles load balancing, etc
        * Java, node, .net, python or ruby
        * Can de-aggregate records aggregated by the KPL
        * Uses dynamo to track state
            * 10 read / 10 write capacity by default
    * Kinesis Agent
        * Java app
        * Can monitor and write to multiple streams and directories
        * Can convert logs prior to sending to kinesis
    * Kinesis REST API
        * OutRecords: will return 200 even if some records fail in the request, you can use PutRecordsResult to check for individual failures.
  
  * Producers <> Consumers
    * Shard capacity:
        * 1MB/sec input
        * 2MB/sec output
        * 5 read transactions / sec
        * 1000 write records / sec
  
  * Resharding: 
    split
    merge 
  
  * Records:
    * partition key
        * groups data by shard
        * Identifies shard record is in
        Is specified by the producer
    * sequence #
        * Unique id for data blob / record
        * Assigned on PutRecord or PutRecords
        * Cannot use to separate records by shard
    * data blob
        * Max size is 1MB
  
  * Retention period: default 24 hours, max 7 days. Can change through cli
  * Can send data to S2, DynamoDB, Elastic Search, Redshift and EMR. For S3, Redshift, Dynamo and ElasticSearch you have to use the kinesis connector library. EMR must pull down from spark streaming or S3. You can also poll the stream with lambda and send to S3, Dynamo or RedShift
  
  
  * Kinesis connector:
    * iTransformer
    * iFilter
    * iBuffer
    * iEmitter
  
  * Firehose
    * Collects and loads streaming data near real time
    * Loads data into S3, Redshift and Elasticsearch
    * Can use existing BI tools and dashboards
    * Highly available and durable
    * Console or API
    * Fully managed
        * Scalability, sharding and monitoring
        * Minimizes storage
        * Encryption supported
    * Delivery buffer size 1MB - 128 MB or 60 - 900 sec, whichever is triggered first is sent to elasticsearch or S3. For RedShift, throughput is determined by the speed of redshift COPY.
    * Can send using kinesis agent or aws sdk
    * Can invoke custom lambda functions or blueprints for transformation
        * Function must have record id
        * Status must be ok, dropped, processingfailed 
        * And data payload
    * For S3, firehose will retry delivery for up to 24 hours. Redshift has retry values of 0-7200 seconds from S3
  


### Blogs 

https://aws.amazon.com/blogs/big-data/implementing-efficient-and-reliable-producers-with-the-amazon-kinesis-producer-library/

https://aws.amazon.com/blogs/compute/amazon-kinesis-firehose-data-transformation-with-aws-lambda/

### Docs

### Kinesis

https://docs.aws.amazon.com/streams/latest/dev/key-concepts.html

https://docs.aws.amazon.com/streams/latest/dev/introduction-to-enhanced-consumers.html

https://docs.aws.amazon.com/streams/latest/dev/kinesis-record-processor-ddb.html

https://docs.aws.amazon.com/streams/latest/dev/kinesis-using-sdk-java-resharding-split.html 

https://docs.aws.amazon.com/streams/latest/dev/developing-producers-with-kpl.html 

https://docs.aws.amazon.com/streams/latest/dev/building-consumers.html 

https://docs.aws.amazon.com/streams/latest/dev/creating-using-sse-master-keys.html 

https://docs.aws.amazon.com/streams/latest/dev/kinesis-kpl-concepts.html 

https://docs.aws.amazon.com/streams/latest/dev/kinesis-producer-adv-retries-rate-limiting.html 

https://docs.aws.amazon.com/streams/latest/dev/service-sizes-and-limits.html 

https://docs.aws.amazon.com/streams/latest/dev/monitoring-with-kcl.html 

https://docs.aws.amazon.com/streams/latest/dev/agent-health.html 

https://docs.aws.amazon.com/streams/latest/dev/kinesis-using-sdk-java-resharding-merge.html

### Kinesis Firehose

https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html#data-flow-diagrams 

https://docs.aws.amazon.com/firehose/latest/dev/data-transformation.html 

https://docs.aws.amazon.com/firehose/latest/dev/create-configure.html 

https://docs.aws.amazon.com/firehose/latest/dev/record-format-conversion.html 

https://docs.aws.amazon.com/firehose/latest/dev/data-transformation.html#lambda-blueprints 

https://docs.aws.amazon.com/firehose/latest/dev/encryption.html

### Kinesis Data Analytics 

https://docs.aws.amazon.com/kinesisanalytics/latest/dev/what-is.html 

https://docs.aws.amazon.com/kinesisanalytics/latest/dev/streams-pumps.html 

https://docs.aws.amazon.com/kinesisanalytics/latest/dev/authentication-and-access-control.html 

https://docs.aws.amazon.com/kinesisanalytics/latest/dev/stagger-window-concepts.html 

https://docs.aws.amazon.com/kinesisanalytics/latest/dev/tumbling-window-concepts.html 

https://docs.aws.amazon.com/kinesisanalytics/latest/dev/sliding-window-concepts.html 

https://docs.aws.amazon.com/kinesisanalytics/latest/dev/continuous-queries-concepts.html

### Code

https://github.com/awslabs/amazon-kinesis-producer

https://github.com/awslabs/amazon-kinesis-agent

https://github.com/awslabs/amazon-kinesis-client

https://github.com/awslabs/amazon-kinesis-connectors



