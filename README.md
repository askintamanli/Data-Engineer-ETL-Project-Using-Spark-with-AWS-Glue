# Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue

## Hello everyone. Welcome my Data Engineer (ETL) project. We will talk about ETL job using Apache Spark with AWS Glue. You can also view the project in my medium account (https://medium.com/@askintamanli). CAUTION!!! If you practice with me, it may cost much. Don’t save the job an delete the job after you done with it.

## What we gonna do step by step
1. [Create IAM Role for whole project](#create-ıam-role-for-whole-project)
2. [Create an S3 bucket and load data to the bucket from our local](#create-an-s3)
3. [Create AWS Glue database and table](#create-aws-glue-database-and-table)
4. [Create Glue Studio Notebook](#create-glue-studio-notebook)
5. [Transform data using Spark](#transform-data-using-spark)


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

## 4- Firstly we create ETL Job in AWS Glue.

o to AWS Glue → Data Integration and ETL → Interactive Sessions → Notebooks

Job name: ‘etl-project-for-medium-job’

IAM Role → Choose the role that we created first episode of series → ‘’IAM-Role-etl-project’

Kernel : Spark

![1](https://user-images.githubusercontent.com/63555029/228258375-5680b1be-1b76-4eb6-b00e-bce0ed3b711f.png)


## 5.1 Okay, let’s write some PySpark code for transform the data. Please check the "etl-project-transform-data.ipynb" file in this repo.


## Let’s check our ‘transformed_bucket’.

![3](https://user-images.githubusercontent.com/63555029/228259450-80d283e1-a6b2-406a-b150-b15dbec04de2.png)

Everything looks good. Select the one of them and download. You will see the results.

