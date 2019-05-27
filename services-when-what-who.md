
[Data was gathered from this White paper - Big Data Analytics Options on AWS](https://d0.awsstatic.com/whitepapers/Big_Data_Analytics_Options_on_AWS.pdf)




| Service Name       | Domain        |  When to Use                                           | When not to use                                     | Integrations      |
|:-------------------|:--------------|:-------------------------------------------------------|:--------------------------------------------------- |:------------------|
| Lambda             | Collection    | real time file/stream processing, ETL, Cron, AWS Events| long running tasks, dynamic sites, stateful apps    |S3, DynamoDB, Kinesis Data Streams, SNS , and CloudWatch|
| DataPipeline       | Collection    ||||
| SQS                | Collection    ||||
| IOT                | Collection    ||||
| Kinesis Streams    | Collection    ||||
| Kinesis Firehose   | Collection    ||||
| Kinesis Analytics  | Collection    ||||
| Dynamodb           | Storage       | NoSQL, Gaming, apps, logs, web session, metadata| SQL or complex joins, Binary data, large data with lo IO||
| S3                 | Storage       ||||
| RDS                | Storage       ||||
| EMR                | Processing    | highly distributed computing framework, Large ETL, Predictive analytics | small data sets, ACID,  | s3, DDB, Kinesis, Redshift, EMR |
| Spark              | Processing    | In memory processing engine                           |||
| Hive               | Processing    ||||
| Pig                | Processing    ||||
| Presto             | Processing    | distributed SQL query engine for interactive queries  | No database, OLTP or batch process    ||  
| HBase              | Processing    ||||
| Hue                | Processing    ||||
| HCatalog           | Processing    ||||
| Glue               | Processing    | serverless ETL jobs | Streaming Data, Many ETL, NoSQL | Athena, EMR and Redshift|
| Redshift           | Analysis      | Petabyte scale dataware |||
| Machine Learning   | Analysis      | Create and Train ML models, prediction, personlization, forcasting| very large datasets, unsppuorted learning tasks| s3, Redshift, RDS |
| Athena             | Analysis      | query data stored on s3||s3|
| Quicksight         | Visualization ||||
| ElasticSearch      | Visualization ||||



