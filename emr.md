
# EMR

Master, core and task nodes, each in instance groups. Up to 50 instance groups. 

* Core node = slave node, runs tasks, store data on HDFS or EMRFS.

* Task node = ephemeral, optional, no hdfs

* Master node keeps the registry of blocks on hdfs, including replication scale

* 64MB block size is default for hdfs

* Block sizes and replication factor can be set per file

* Replication factor default 3, can be set in the hdfs-site.xml file, can also be done real time

* EMRFS implements HDFS on s3

* EMRFS consistent view


##### Storage and Compression

HDFS is automatically split into chunks by Hadoop. If on s3, hadoop will split the data by reading files in multiple http range requests. Ebs volumes do not persist when used with EMR clusters.


File Sizes
* Gzip is not splittable, keep them less than 2GB
* Avoid lots of small files (< 100MB), fewer larger is better
* S3DistCp can be used to combine small files into larger files.
    * An extension of DistCp
    * S3DistCp can be used to copy between s3 buckets or between s3 hdfs and s3
    * Can be added as a step on the cluster


Supported File Formats
* Text - csv, etc
* Parquet - columnar-oriented file format
* ORC - Optimized row columnar file format
* Sequence - flat files containing key/value pairs
* Avro - json based data serialization framework


##### Hadoop

Modules: Hadoop common, HDFS, Yarn (yet another resource negotiator), MapReduce

* Map Reduce tasks:
    * Input
    * Split
    * Map
    * Shuffle
    * Reduce
    * Result

Releases - Amazon and MapR

Basic options are: Core Hadoop, HBase, Presto and Spark. Deploys to default vpc and security groups.

Advanced options: Older releases are available and can cherry pick applications. Can edit config. Can add steps now or later. Can select vpc / az. If running into private subnet, you’ll need to create vpc or endpoint. Can request spot and setup autoscaling. Always tag with the EMR tools or they won’t roll back to the EMR cluster. Can also set encryption and IAM / key options. Security groups get set for master and core/task.

##### MapReduce

* Batch oriented
* M3 or M4 instance types
* Scale horizontally

##### Provisioning

* Master node
    * clusters < 50 nodes use m3.xlarge or m4.xlarge
    * clusters > 50 nodes use m3.2xlarge or m4.2xlarge

* Core nodes
    * Default replication factors:
        * Cluster of > 10 nodes: 3
        * Cluster of 4-9 nodes: 2
        * Cluster of =<3 nodes: 1
        
    * Can be overridden in hdfs-site.xml
    * Machine Learning P2, C3 or C4 instance types
    
HDFS capacity calculation: total storage / replication factor = hdfs capacity



##### Monitoring
* Use cloudwatch for events
* Metrics are updated every 5 mins, which is not configurable
* Metrics are archived for 2 weeks, no charge for them
* Can use native application web interfaces
* Use ganglia, open source monitoring tool, runs on master node

##### Resizing clusters
* Manually:
    * done with api, cli or console
    * Size down can be done on instance hour or task complete
    * When resizing core nodes, you cannot size down below the requirements for the replication factor. 
    You can change in hdfs-site.xml and restart the NameNode daemon.

* Autoscaling:
    * Can be based on metrics
    * Must add autoscaling role on cluster creation or you cannot use autoscaling
   
##### Security

* IAM policies - allow or deny permissions for IAM users and groups to perform actions. Policies can be combined with 
tagging to control access on a cluster-by-cluster basis.

* IAM roles for EMRFS requests to Amazon S3 - You can control whether cluster users can access files from within 
Amazon EMR based on user, group, or the location of EMRFS data in Amazon S3.

* IAM roles - The Amazon EMR service role, instance profile, and service-linked role control how Amazon EMR is able to access other 
AWS services. For more information, see Configure IAM Roles for Amazon EMR Permissions to AWS Services.

* Kerberos - You can set up Kerberos to provide strong authentication through secret-key cryptography. 

* SSH - provides a secure way for users to connect to the command line on cluster instances. 
It also provides tunneling to view web interfaces that applications host on the master node. Clients can authenticate 
using Kerberos or an Amazon EC2 key pair.

* Data encryption - You can implement data encryption to help protect data at rest in Amazon S3 and in cluster 
instance storage, and data in transit. For more information, see Encrypt Data in Transit and At Rest. You can also 
use a custom Amazon Linux AMI to encrypt the EBS root device volume of cluster instances.

* Security groups - you can use your own to isolate EMR clusters. Can also add additional groups to allow ssh, etc. 
IAM Roles - can use custom roles


##### Transient vs Long-running Clusters
	
* Long Running
    * keeps data on core nodes
    * Auto termination disabeld (this is default)
    * Termination protection is enabled
* Transient Clustered
    * input/output and code stored on s3
    * Hive metastore can be stored in rds

##### EMR Arch

**Single AZ**

* 3 instances groups, Master, Core and Instance 

* Can paste config override or load json from s3

* Can run core and master nodes on same instance

* Single Master node 
    * Manages resources of cluster
    * Cooridnates of the running of tasks
    * manages HDFS
    * Runs resrouce manager
    * monitors health of core and tasks nodes
    
* Core Node
    * slave node 
    * run tasks
    * HDFS or EMFS
    * Datanode daemon
    * NodeManager
    * ApplicationMaster    

* Task Node
    * Same as Slave node
    * Optional
    * No HDFS
    * Added and removed from Running Clusters
    * Extra Capacity


##### HDFS EMFS

* A Distributed file systems
* Allows simultaneous access to data 
* Stores data in blocks
* Single Global namespace maintained by the Master node

* Block size
    * 64 mb is default
    * 64 - 256 MB
    * large files use bigger block
    * smaller files use smaller block
    * can be set per file

##### EMR Operations

* Resizing is possible 
* Cloudwatch is used to monitor the cluster

##### Hive
Data warehouse built on hadoop, uses a sql-like interface (HiveQL) to summarize, query and analyze very large data sets. 
Provides a high level language interface to mapreduce. Differences between Apache Hive and Hive on EMR, Hive on EMR has 
native s3 and Dynamo integration, kinesis streams can be used by pushing to s3 first. Supports partitioning on s3. 

You can use the EMR DynamoDB connector to:

* Join hive and Dynamo tables
* Query data in Dynamo
* Copy data to/from Dynamo and HDFS
* Copy data to/from Dynamo and s3

##### Hue

* Hadoop user experience
* Open source web interface for hadoop and non hadoop applications
* browser base
* write queries spark sql 
* Makes using the EMR cluster easy
* s3 broswer for data 
* HDFS browser
* Hive editor
* PIG editor
* Access the metastore data
* Hbase access
* job browser
* Logs are accessible and searcher
* User management 
    * LDAP
    * PAM
    * SPNEGO
    * OpenID
    * Oauth
    * SAML2

[Connecting to HUE]()

##### Hbase

Open source NoSQL database

Massively scalable distributed big data store. Non-relational, versioned database which runs on top of s3 using EMRFS or HDFS. Built for random, strictly consistent realtime access for tables with billions of rows and millions of columns. Integrates with Hadoops, Hive and Pheonix.

Use cases:
* adtech, content, FINRA
* Large amounts of data: 100s of GBs to PBs.
* high write and update i/o.
* Can use NoSQL and need flexible schema.
* Fast access to random and real-time.
* Fault-tolerance in non-relational environment

Limitations
* No transactions
* Not relational 
* Small amount of data

Can connect hive to hbase via zookeeper.


##### Presto

* Open source in memory distributed fast SQL query engine
* Run interactive analytic queries against a variety of data sources with dize ranging from GB to PB

* Query different types of data sources from relational databases, NoSQL databases, frameworks like Hive to stream
processing platforms like Kakfa

* Connectors
    * cassandra
    * hive 
    * kafka
    * mongodb
    * mysql
    * postgresl
    * redis

* High concurrency, run 1000s of queries per day 
* In memory processing helps avoid unnecessary IO leading to low latency
* Does not need separate processing layer like hive does


##### Spark

Fast in memory query engine

Use Cases

* Interactive analytics
* Faster than hive
* flex in terms of language, scala, python etc
* run queries against live streams
* stream processing  
    * disparate dat sources
    * data in small sizes
* Machine learning
    * repeated queries at scale against data set    
    * time machine learning alg
    * MLib 
* Data integration
    * ETL
    * Reduc time and cost 
  
When not to use Spark
  
* Not a database
* not designed for OLTP
* Not for batch processing
* Multiuser requests

Run ETL in Spark and move the data to a typical reporting database

 Components

* Spark Core - General execution engine 
    * SQL
    * Streaming
    * MLib
    * GraphX
* Standalone Scheduler
* YARN
* Mesos


Data APIs:
* Resilient Distributed Datasets (RDDs): logical collection of data partitioned across machines
* DataSet: distributed collection of data
* DataFrame: DataSet organized into named columns

Streaming

* DStreams (Discretized Streams)
* Abstraction of continuous streaming data
* Created from input data sources like kinesis streams
* Are a collection of RDDs
* Transformations are applied to RDDs
* Published to HDFS, databases or dashboards


##### HCatalog

Tools to access Hive metastore tables.

##### Glue

Managed ETL (spark) service to categorize, clean and enrich data. Can move between data stores, can be used 
as the metadata catalog. Serverless. Can discover and correlate data across multiple datastores. 

### Blogs

[Tagged Articles for EMR](https://aws.amazon.com/articles/?tag=articles%23keywords%23elastic-mapreduce)

https://aws.amazon.com/blogs/big-data/combine-nosql-and-massively-parallel-analytics-using-apache-hbase-and-apache-hive-on-amazon-emr/

https://aws.amazon.com/blogs/big-data/analyze-your-data-on-amazon-dynamodb-with-apache-spark/

https://aws.amazon.com/blogs/big-data/analyze-realtime-data-from-amazon-kinesis-streams-using-zeppelin-and-spark-streaming/

https://aws.amazon.com/blogs/big-data/optimize-spark-streaming-to-efficiently-process-amazon-kinesis-streams/

https://aws.amazon.com/blogs/big-data/powering-amazon-redshift-analytics-with-apache-spark-and-amazon-machine-learning/

https://aws.amazon.com/blogs/big-data/using-spark-sql-for-etl/

https://aws.amazon.com/blogs/compute/ad-hoc-big-data-processing-made-simple-with-serverless-mapreduce/


### Docs

* [EMR Releases and Components](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-release-components.html)

* [Access Hbase tables with Hive](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hbase-access-hive.html)

* [Configuring Hue for LDAP](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/hue-ldap.html)


https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-overview.html#emr-overview-clusters 

https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-file-systems.html 

https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-consistent-view.html 

https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-encryption-enable.html#emr-awskms-keys 

https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-data-encryption-options.html 

https://docs.aws.amazon.com/emr/latest/ManagementGuide/emrfs-configure-sqs-cw.html 

### Code

[Web interfaces hosted on EMR](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-web-interfaces.html)

|Name of interface	    | URI                                       |
|:----------------------|:------------------------------------------|
| YARN ResourceManager	| http://master-public-dns-name:8088/       |
| YARN NodeManager	    | http://coretask-public-dns-name:8042/     |
| Hadoop HDFS NameNode	| http://master-public-dns-name:50070/      |
| Hadoop HDFS DataNode	| http://coretask-public-dns-name:50075/    |
| Spark HistoryServer	| http://master-public-dns-name:18080/      |
| Zeppelin		        | http://master-public-dns-name:8890/       |
| Hue	                | http://master-public-dns-name:8888/       |
| Ganglia		        | http://master-public-dns-name/ganglia/    |
| HBase	                | http://master-public-dns-name:16010/      |
| JupyterHub		    | https://master-public-dns-name:9443/      |


AWS Labs

[EMR DynamoDB connector](https://github.com/awslabs/emr-dynamodb-connector)

[Redshift Data Source for Apache Spark](https://github.com/databricks/spark-redshift)