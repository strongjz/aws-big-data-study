# Redshift
  Fast, managed, petabyte scale data warehouse. Designed for OLAP (online analytic processing) and BI (business intelligence). Not suitable for OLTP (online transaction processing). ASNI sql compatible. Columnar based. Single AZ. HA and durable. Runs on 10GB network. Shared nothing, massively parallel.
  
  * Leader node
    * provides sql endpoint
    * coordinates parallel execution on compute nodes
    * stores metadata
  
  * Compute node
    * Executes queries in parallel
    * Dedicated cpu/mem/storage
    * Scale out/in, up/down
    * Backups done in parallel
  
  * Node Slices
    * Portion of memory and disk
    * Data loaded in parallel
    * Number of slices depends on the node
  
  Columnar Databases store data tables as columns instead of rows. Efficiently r/w data to/from disk to speed up query results by reducing the amount of data and avoid scanning and discarding unwanted rows. 
  
  
  #####  Table Design
  
  Distribution Key Styles
  
  * Even
    * Rows are distributed across slices regardless of values in a particular column
    * Default style
    * No joins, reduced parallelism, and where KEY and ALL are not a good choice
    * Distributed round-robin style
  * Key
    * Distribute data evenly among slices
    * Collocate matching rows across tables on same slice
    * Use when you are joining across tables on a particular row
    * When you have large fact tables in a star schema
    * When you want to distribute data evenly among slices
  * ALL
    * The entire table is copied to every node
    * Good for when data doesn’t change often
    * Reasonable size (a few million rows)
    * No common key
  
  ##### Sort Keys
  Uses block size of 1MB and zone maps (min / max values). Data must be loaded in sort order or you have to vacuum.
  
  * Compound
    * Columns are listed in the sort key definition
    * Is the Default type
    * Used for JOIN, ORDER BY, GROUP BY
    * PARTITION BY and ORDER BY window functions
    * Use vacuum and analyze operations to optimize large unsorted data
  
  * Interleaved
    * Equal weight to each column in the sort key
    * Used for multiple queries with multiple filters
    * Data load and vacuum operations are slower
    * Only use on > 100 million row tables
    * Not good for data being loaded in sort order as they don’t preserve the order
  
  ##### Data Types
  
  Supported data types
  
  * CHAR - 4096 bytes
  * VARCHAR - 65535 bytes
  * BPCHAR - 256
  * TEXT - 260 (converted to VARCHAR
  
  ##### Compression
  
  Lowers cost and reduces disk i/o. Compression can be applied to each column using ENCODE on create table. Use automatic compression.
  
  ##### Constraints
  Rules on what types of data can be entered into a table. Helps maintain referential data integrity. Table level constraints are: PRIMARY KEY, FOREIGN KEY, UNIQUE, REFERENCES - but not enforced, they are just used for query optimization and information. NOT NULL / NULL on rows *is* enforced.
  
  Table Inspector script can be used to analyze table design.
  
  ##### Workload Management
  Manages long / short running queries. Manages rules to route queries to queues. Configure memory to queues. QOS manager. Priority is queue based, not query based.
  
  Limits:
    * 500 concurrent connections
    * 50 concurrent queries across all queues
    * 8 user defined queues
    * 1 superuser queue
    * Default 5 concurrent queries per queue
  
  Requires new redshift parameter group. Defines user and query groups. Queues selected by SET command.
  
  * Static WLM Properties (requires reboot)
    * User Groups
    * User Groups Wildcard
    * Query Groups
    * Query Groups Wildcard
  
  * Dynamic WLM Properties
    * Concurrency
    * Memory allocation
    * Timeout
  
  Timeout queries can be hopped to the next queue.
  
  ##### Loading Data
  * COPY (direct):
    * S3 (s3:// fastest way) 
    * EC2 (s3:// via ssh manifest on s3)
    * EMR (emr://)
    * Dynamo (dynamodb://)
  
  * Via S3:
    * Kinesis Firehose
    * Kinesis enabled application
    * DB migration service
  
  Should have same number of slices as files being loaded. Split files into same size of lines. Try to keep them between 1MB and 1GB. Copy supports bzip/gzip
  
  Can find slices qty via stv_slices table.
  
  
  
  ##### Manifest
   
  * Load required files only
  * Load files from different buckets
  * Load files with different prefixes
  * Json format
  
  Data format supported by COPY:
  * csv
  * delimited (pipe/tbl)
  * fixed width
  * json
  * avro
  
  Check STL_LOAD_ERRORS and STL_LOADERROR_DETAIL
  
  UPSERT not supported by redshift, two options:
  
 1. Use staging table to replace all data in target
  
   * Merge all rows in staging table into target
   * Target and staging columns must match
 
 2. Specify column list
  
  * Update specific columns in a target table
  * Most rows in staging table will not be used to update target table
  
  
  Loading Encrypted Data from s3:
  
  * Supported:
    * Server side with s3 managed (SSE-S3)
    * Server-side with KMS (SSE-KMS)
    * COPY automatically recognizes SSE-S3 and SSE-KMS
    * Can be done with client-side symmetric master key
  
  * Not Supported
    * Server-side with customer provided keys (SSE-C)
    * Client-side using KMS
    * Client-side using customer asymmetric master key
  
  UNLOAD loads from redshift into s3 automatically using SSE-S3, can also use KMS by providing the KMS key ID or client-side encryption using customer managed key (CSE-CMK). UNLOAD does not support client-side with customer-supplied key SSE-C.
  
##### Provisioning

  Clusters should have free space 2 - 2.5 times the size of the largest table. On resize, cluster is made read-only and all connections terminated. Data is copied to new cluster, and once completed dns endpoint is routed to the new cluster.
  
  Study:
  
  Vacuum, Deep Copy and Snapshots
  
  
  AnalyzeVacuumUtility script can be used. Do not use on tables over 700GB, use a deep copy instead.
  
  Deep copy can be done by:
  * CREATE from the original ddl, INSERT INTO, DROP then ALTER / RENAME (preferred)
  * CREATE with LIKE, INSERT INTO, DROP then ALTER / RENAME
  * CREATE TEMP, TRUNCATE, INSERT INTO, then DROP
  
  
  Snapshots can be kept up to 31 days, default 1 day. Taken every 8 hours. You can exclude tables from snapshots in the create table. You can snapshot into another region. KMS can be used to encrypt. You can restore a single table.
  
  
##### Security

Require ssl. Enable encryption in transit and at rest. Use views to limit access to data. Obvious things.


### Blogs 

https://aws.amazon.com/blogs/big-data/a-zero-administration-amazon-redshift-database-loader/

### Docs

https://docs.aws.amazon.com/redshift/latest/dg/tutorial-tuning-tables-distribution.html 

https://docs.aws.amazon.com/redshift/latest/dg/c_best-practices-best-dist-key.html 

https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-about-clusters-and-nodes 

https://docs.aws.amazon.com/redshift/latest/mgmt/enhanced-vpc-working-with-endpoints.html 

https://docs.aws.amazon.com/redshift/latest/dg/c_designing-queries-best-practices.html 

https://docs.aws.amazon.com/redshift/latest/dg/c_best-practices-use-copy.html 

https://docs.aws.amazon.com/redshift/latest/dg/c_intro_STL_tables.html 

https://docs.aws.amazon.com/redshift/latest/dg/c_intro_STV_tables.html 

https://docs.aws.amazon.com/redshift/latest/dg/cm-c-implementing-workload-management.html 

https://docs.aws.amazon.com/redshift/latest/dg/wlm-short-query-acceleration.html


### Code
