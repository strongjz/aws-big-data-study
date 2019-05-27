# DynamoDB

Nosql, fully managed. No practical limit on size or number of records. Uses ssd. Highly available and resilient, 
replicated across 3 AZs. Specify i/o r/w and integrated with IAM. Tables set r/w/sec. Write Capacity Units is number 
of 1KB blocks / sec. Read Capacity Units is number of 4KB blocks / sec. Eventually consistent or strongly consistent. 
Partition or Hash key. Sort or Range key.

* Attribute types:
    * scalar / string
    * Integer
    * Binary
    * Boolean
    * Null
    * json / document
    * set / array

Can copy from 

* dynamo to Redshift, EMR and hive. 
* Export / import to s3 via data pipeline which used an EMR cluster. 
* Dynamo can use lambda and triggers as well as be populated with kinesis.

##### Partitions

Initially one table = one partition and all data for that table is stored in that partition. 
You cannot directly control the number of partitions. A partition can store 10GB. Can handle 3k RCU and 1k WCU. 
When you go over 10GB additional partition is created and data distributed over time. Data distributed based on 
partition key. Does not automatically scale down. Each partition key will only exist on one partition. 

Indexes

Can SCAN or QUERY.

Local secondary indexes

Can only be created at table creation. Contain partition key, sort key, secondary sort key, and optional projected 
values. Data copied asynch from the table to the LSI and shares the RCU/WCU with the table. LSI only contains items 
with the secondary index value.


You are charged for the index entries rounded to the nearest 4k. Query against non-projected values runs twice, 
once against the LSI and the base table, doubling the 

Deletes and Updates count as multiple reads, on the table and on the LSI. Update = delete/add on the LSI.

ItemCollections only apply to LSI. Max size is 10GB.

Global Secondary Indexes
Shares the same concepts of LSI but can be created at any time and can have an alternative partition and sort key. 
Attribute projection can be:

* KEYS_ONLY - new partition and sort keys, old partition key, and old sort key (if present)
* INCLUDE - custom projection values
* ALL - project all attributes

RCU/WCU are defined on the GSI and not shared. Changes are written asynchronously. Only supports eventually consistent.

If your GSI is provisioned too low, or have a hot key issue, then the performance throttling will cause issues 
on the table as well.


##### Streams and Replication

Stream is an ordered record of updates. Enabled on a table via console or api. Records changes up to 24 hours. 
Only be read or processed via streams endpoint and api requests. Guarantees each record occurs once and only once, 
and order is preserved. 

Can be created with 4 views:
* KEYS_ONLY
* NEW_IMAGE
* OLD_IMAGE
* NEW_AND_OLD_IMAGES


Streams are good for replication and triggers. DR or analytics via lambda, etc. Can also bust caches with this.

Replication can be achieved with elastic beanstalk and kinesis. Could also used streams to lambda and trigger replication.

##### Performance

Basically take the higher of those two calculations.

Shard partition keys via random suffix example

You can burst for 300 seconds

You can buffer with sqs / lambda

Throttle writes with your application

Can bust cache using dynamo streams


### Blogs 

### Docs

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-design.html#bp-partition-key-partitions-adaptive 

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/globaltables_monitoring.html 

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-data-upload.html 

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/globaltables_reqs_bestpractices.html 

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-gsi-aggregation.html 

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-gsi-overloading.html 

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-indexes-gsi-sharding.html

### Code

https://aws.amazon.com/blogs/big-data/scaling-writes-on-amazon-dynamodb-tables-with-global-secondary-indexes/

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html

[Cross region replication](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.CrossRegionRepl.html)
