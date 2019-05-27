# Data Pipeline
* ETL service that moves data from one place to another. Consists of EC2 instances or an EMR cluster which are provisioned and terminated automatically. 
*   Useful for backups like making copies of RDS or dynamo across regions.
*   Consists of:
    *   Datanodes
        *   Destination for your data
    *   Activities
        *   Action initiated as part of the pipeline, could be a script, hive job, pig job, sql query, etc
    *   Preconditions
        *   Readiness check
    *   Schedules
        *   When and how often the activities run
*   Much of this has been replaced by lambda.


### Blogs 

https://aws.amazon.com/blogs/big-data/using-aws-lambda-for-event-driven-data-processing-pipelines/


### Docs

https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-how-tasks-scheduled.html 

https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-concepts-datanodes.html 

https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-concepts-databases.html 

https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-importexport-ddb-part1.html 

https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/datapipeline-related-services.html

### Code

