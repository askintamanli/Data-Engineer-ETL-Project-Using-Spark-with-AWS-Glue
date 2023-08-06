## Hello everyone. Welcome my Data Engineer (ETL) project. We will talk about ETL job using Apache Spark with AWS Glue. You can also view the project in my medium account ([https://medium.com/@askintamanli](https://awstip.com/etl-project-using-spark-with-aws-glue-extract-step-1-3-8710ce27264b)). CAUTION!!! If you practice with me, it may cost much. Don’t save the job an delete the job after you done with it.

![export](https://github.com/askintamanli/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/63555029/b866df61-9b20-47db-a648-83fbb24e1974)

## What we gonna do step by step
1. [Create IAM Role for whole project](#create-ıam-role-for-whole-project)
2. [Create an S3 bucket and load data to the bucket from our local](#create-an-s3)
3. [Create AWS Glue database and table](#create-aws-glue-database-and-table)
4. [Create Glue Studio Notebook](#create-glue-studio-notebook)
5. [Transform data using Spark](#transform-data-using-spark)
6. [Create AWS Redshift Cluster](#create-aws-redshift-cluster)
7. [Load the transformed data from S3 to Redshift](#load-the-transformed-data-from-s3-to-redshift)

# PART-1-EXTRACT

![Slide1 1396](https://github.com/askintamanli/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/63555029/d8aea115-9c32-42e7-bf9a-ffebf47e0230)

## 1.1  Firstly we should create an IAM Role for whole project.

Go to AWS IAM → Roles → Create Role

Use cases for other AWS services : Select Glue

Add permissions → Search and Select ‘AdministratorAccess’

Role name : ‘IAM-Role-etl-project’

![extra](https://user-images.githubusercontent.com/63555029/228253564-6e65992a-1c0c-4f53-be04-aabec063a6f1.png)

## 2.1- We create a bucket in AWS S3.
Go to AWS S3 → Buckets → Create bucket

Bucket name: ‘etl-project-for-medium’

![1](https://user-images.githubusercontent.com/63555029/228254010-97443b14-b3d3-460c-b71f-e989b9c0d8d0.png)

## 2.2- We create database folder.

AWS S3 → Buckets → ‘etl-project-for-medium’ → Create folder

Folder name: ‘etl-project-for-medium-database’

![2](https://user-images.githubusercontent.com/63555029/228255263-cfd3e59b-70dc-402e-9e00-19900116e586.png)

## 2.3- We create 2 folder for raw data and transformed data.

AWS S3 → Buckets → ‘etl-project-for-medium’ → ‘etl-project-for-medium-database’ → 2 x Create folder

Folder name : ‘raw_data’

Folder name : ‘transformed_data’

![3](https://user-images.githubusercontent.com/63555029/228255709-5f5314ac-807b-4273-8158-67033dbcbe46.png)

## 2.4- We upload our data from local to ‘raw_data’ bucket. You can get the data from this repo.

AWS S3 → Buckets → ‘etl-project-for-medium’ → ‘etl-project-for-medium-database’ → ‘raw_data’ → Upload → Add Files → ‘marketing_campaign.csv’

![4](https://user-images.githubusercontent.com/63555029/228255897-2cffbb16-4c32-4cf8-a0c8-65a907f563b9.png)

Okay, everything looks good in our bucket. Now, we should create Glue database and table. And load data to table from AWS S3.

## 3.1- Firstly, we create a database.

Go to AWS Glue → Data Catalog → Databases → Add database

Database name: ‘etl-project-for-medium-database’

Location: Copy S3 URI of ‘etl-project-for-medium-database’ folder and paste it to location space.

![5](https://user-images.githubusercontent.com/63555029/228256298-65829739-c071-4207-814d-dfd569e0a74e.png)

## 3.2- We create a table in database we just created.

AWS Glue → Data Catalog → Databases → ‘etl-project-for-medium-database’ → Add tables using crawler

Crawler name: ‘etl-project-for-medium-crawler’

Data source S3 path: Choose the ‘raw_data’ bucket

IAM role → Choose IAM role → ’IAM-Role-etl-project’

Target database → Choose the ‘etl-project-for-medium-database’

Schedule : On demand

![extra2](https://user-images.githubusercontent.com/63555029/228259725-eaa8a949-6345-4f20-bdc5-058e4676de8f.png)


## 3.3- Run the crawler.

![7](https://user-images.githubusercontent.com/63555029/228256629-e504361a-a655-4072-a918-8442a7d3d11f.png)

## 3.4- Our crawler is successfully completed. Let’s check the table and schema of our table.

AWS Glue → Data Catalog → Databases → ‘etl-project-for-medium-database’ → raw_data

![8](https://user-images.githubusercontent.com/63555029/228256846-6b620a1a-33ac-4edf-8276-d0a6b6faf950.png)

We just created our table. Check the data types of columns of data. Everything looks good in our table.

# PART-2-TRANSFORM

![Slide1](https://github.com/askintamanli/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/63555029/8132c39b-994d-4b05-be82-0af06b6b23ae)

## 4- Firstly we create ETL Job in AWS Glue.

o to AWS Glue → Data Integration and ETL → Interactive Sessions → Notebooks

Job name: ‘etl-project-for-medium-job’

IAM Role → Choose the role that we created first episode of series → ‘’IAM-Role-etl-project’

Kernel : Spark

![1](https://user-images.githubusercontent.com/63555029/228258375-5680b1be-1b76-4eb6-b00e-bce0ed3b711f.png)


## 5.1 Okay, let’s write some PySpark code for transform the data. You can get the source "etl-project-transform-data.ipynb" file in this repo.

1. For set up and start your interactive session.
   ```
    %idle_timeout 2880
    %glue_version 3.0
    %worker_type G.1X
    %number_of_workers 5

    import sys
    from awsglue.transforms import *
    from awsglue.utils import getResolvedOptions
    from pyspark.context import SparkContext
    from awsglue.context import GlueContext
    from awsglue.job import Job

    sc = SparkContext.getOrCreate()
    glueContext = GlueContext(sc)
    spark = glueContext.spark_session
    job = Job(glueContext)
    ```
2. Create a DynamicFrame from a table in the AWS Glue Data Catalog and display its schema.
    ```
    dyf = glueContext.create_dynamic_frame.from_catalog(database='etl-project-for-medium-database',            table_name='raw_data')
    dyf.printSchema()
    ```
3. Convert the DynamicFrame to a Spark DataFrame and display a sample of the data.
    ```
    df = dyf.toDF()
    df.show()
    ```
4. Drop columns that we don't need it.
    ```
    df = df["id","year_birth","education","marital_status","income","dt_customer"]
    df.show()
    ```
5. Check NaN values for each column.
    ```
    from pyspark.sql.functions import *
    df.select([count(when(col(c).isNull(),c)).alias(c) for c in df.columns]).show()
    ```
6. There are 24 NaN values in "income" column. Let's fill NaN values with mean.
    ```
    # Calculate the mean value of the column
    mean_value = df.select(mean(col('income'))).collect()[0][0]

    # Fill missing values with the mean value
    df = df.fillna(mean_value, subset=['income'])

    # Check
    df.select([count(when(col(c).isNull(),c)).alias(c) for c in df.columns]).show()
    ```
7. Write the data to our S3 Bucket named "transformed_data" as csv.
    ```
    df.write \
      .format("csv") \
      .mode("append") \
      .option("header", "true") \
      .save("s3://etl-project-for-medium/etl-project-for-medium-database/transformed_data/")
    ```
8. Write the data to our S3 Bucket named "transformed_data" as json.
     ```
     df.write \
      .format("json") \
      .mode("append") \
      .save("s3://etl-project-for-medium/etl-project-for-medium-database/transformed_data/")
     ```



## 5.2 Let’s check our ‘transformed_bucket’.

![3](https://user-images.githubusercontent.com/63555029/228259450-80d283e1-a6b2-406a-b150-b15dbec04de2.png)

Everything looks good. Select the one of them and download. You will see the results.

# PART-3-LOAD

![Slide1](https://user-images.githubusercontent.com/63555029/228977183-c3091fb1-6e57-4608-bf88-d24807af46bd.jpg)

## 6.1 We should create another IAM Role for Redshift.

Go to AWS IAM → Roles → Create Role

Use cases for other AWS services : Select Redshift - Customizable

Add permissions → Search and Select 'AdministratorAccess'

Role name : 'IAM-Role-etl-project-redshift'

![extra](https://user-images.githubusercontent.com/63555029/228977738-f61c5f3b-bc19-4c4d-9a50-869e305646f3.png)


## 6.2- Let's create AWS Redshift Cluster.

Go to AWS Redshift → Clusters → Create cluster

Cluster identifier : 'etl-project-cluster'

Node type : dc2.large (lowest price for per node)

Number of nodes : 1

Mark the box that load sample data

Configure the admin user name and password

Associated IAM roles → Select 'IAM-Role-etl-project-redshift'

![1](https://user-images.githubusercontent.com/63555029/228977775-6261a957-da08-4041-9317-e84476210d5d.png)


## 6.3 We created a cluster. Let's view our database with editor.

AWS Redshift → Cluster → 'etl-project-cluster' → Query Data v2

![2](https://user-images.githubusercontent.com/63555029/228977819-75df9364-b1da-47ad-a744-ead14f27b940.png)

'dev' and 'sample_data_dev' are database names. You can create notebook or editor page and you can run SQL codes.


## 7.1 Everything looks good. Let's load transformed data from s3 to 'dev' database. Firstly, we should create a table. Let's write some SQL for create table.

   ```
   CREATE TABLE etl_project_transformed_data_table(
   "id" INTEGER NULL,
   "year_birth" INTEGER NULL,
   "education" VARCHAR NULL,
   "marital_status" VARCHAR NULL,
   "income" INTEGER NULL,
   "dt_customer" DATE NULL
   ) ENCODE AUTO;
   ```
![3](https://user-images.githubusercontent.com/63555029/228978528-c2c266b4-1183-453d-a213-1a2fa31dddd5.png)

## 7.2 Now, load the data.

Copy the S3 URI of transformed data which csv, paste it to 'from' field

Copy the ARN of IAM Role 'IAM-Role-etl-project-redshift', paste it IAM_ROLE field.

   ```
   COPY etl_project_transformed_data_table
   FROM 's3://etl-project-for-medium/etl-project-for-medium-database/transformed_data/part-00000-6429f588-c5f4-4f6e-88df-b8bd3506113e-c000.csv'
   IAM_ROLE 'arn:aws:iam::835769464848:role/IAM-Role-etl-project-redshift'
   IGNOREHEADER 1
   DELIMITER ',';
   ```
## 7.3 Let's check our table.

   ```
   SELECT * FROM etl_project_transformed_data_table
   ```
   
![4](https://user-images.githubusercontent.com/63555029/228979014-87d30860-754e-4e6e-937a-029d326324e2.png)

## 7.4 Cool. Let's query the data.

   ```
   SELECT education, COUNT(id), AVG(income)
   FROM etl_project_transformed_data_table
   GROUP BY education
   ```
   
![5](https://user-images.githubusercontent.com/63555029/228979117-7e85568a-9ff8-443c-8e2f-930d5de922fd.png)

## That's it. This is the end of this project series. I really appreciate you for reading this series.
