# AWS-Automation-Assignments
AWS Lambda and Automation Assignments

# AWS Automation Assignments using AWS Lambda, S3, EC2, EBS and CloudWatch

## Project Overview

This repository contains AWS automation assignments implemented using AWS Lambda and Python (Boto3). The objective of these assignments is to automate common AWS administrative tasks such as EC2 instance management, S3 bucket cleanup, S3 encryption auditing, and EBS snapshot creation.

The assignments demonstrate the usage of:

- AWS Lambda
- Amazon EC2
- Amazon S3
- Amazon EBS
- AWS IAM
- Amazon CloudWatch
- Python Boto3 SDK


# Assignment 1: Automatic EC2 Start and Stop using AWS Lambda

## Objective

Automatically start and stop EC2 instances based on tags using AWS Lambda.

## AWS Services Used

- Amazon EC2
- AWS Lambda
- AWS IAM
- Amazon CloudWatch
- Python Boto3


## Implementation Steps

### Step 1: Create EC2 Instances

Created two EC2 instances:

| Instance Name | Tag |
|---------------|------|
| Server-AutoStop | Action=Auto-Stop |
| Server-AutoStart | Action=Auto-Start |



### Step 2: Create IAM Role

Created IAM Role:
LambdaEC2Role
Attached policies:
AmazonEC2FullAccess


### Step 3: Create Lambda Function

Function Name:
EC2AutoManager
Runtime:
Python 3.x


### Step 4: Lambda Code

```python
import boto3

ec2 = boto3.resource('ec2')

def lambda_handler(event, context):

    action = event['action']

    filters = [{
        'Name':'tag:Action',
        'Values':[action]
    }]

    instances = ec2.instances.filter(Filters=filters)

    instance_ids = [i.id for i in instances]

    if action == 'Auto-Stop':
        ec2.instances.filter(
            InstanceIds=instance_ids
        ).stop()

    elif action == 'Auto-Start':
        ec2.instances.filter(
            InstanceIds=instance_ids
        ).start()

    return instance_ids

### Result

Successfully started and stopped EC2 instances using Lambda.


# Assignment 2: Automated S3 Bucket Cleanup

## Objective

Automatically delete all objects inside an S3 bucket using AWS Lambda.


## AWS Services Used

- Amazon S3
- AWS Lambda
- IAM
- CloudWatch
- Python Boto3


## Implementation Steps

### Step 1: Create S3 Bucket

Created bucket:
cleanup-bucket-demo

Uploaded:
- test1.txt
- test2.txt
- test3.txt


### Step 2: Create IAM Role

Created:

LambdaS3CleanupRole
Attached policies:
AmazonS3FullAccess
AWSLambdaBasicExecutionRole

### Step 3: Create Lambda Function

Function:

S3CleanupFunction

Runtime:

Python 3.x

### Step 4: Lambda Code
`python
import boto3

s3 = boto3.client('s3')

def lambda_handler(event, context):

    bucket='cleanup-bucket-demo'

    objects=s3.list_objects_v2(
        Bucket=bucket
    )

    if 'Contents' in objects:
        for obj in objects['Contents']:
            s3.delete_object(
                Bucket=bucket,
                Key=obj['Key']
            )

    return {
        'statusCode':200,
        'body':'Cleanup Completed'
}

### Result

Successfully deleted all objects from the S3 bucket.

# Assignment 3: Monitor Unencrypted S3 Buckets

## Objective

Identify S3 buckets that are not encrypted and log the results to CloudWatch.


## AWS Services Used

- Amazon S3
- AWS Lambda
- IAM
- CloudWatch
- AWS KMS
- Python Boto3


## Implementation Steps

### Step 1: Create Buckets

Created:

bucket-encryptednew
bucket-unencryptednew


Configuration:

| Bucket | Encryption |
|---------|------------|
| bucket-encryptednew | SSE-KMS |
| bucket-unencryptednew | SSE-S3 |


### Step 2: Create IAM Role

Created:

LambdaS3AuditRole

Attached policies:

AmazonS3ReadOnlyAccess
AWSLambdaBasicExecutionRole

### Step 3: Create Lambda Function

Function:

S3EncryptionChecker


### Step 4: Lambda Code
import boto3
s3 = boto3.client('s3')
def lambda_handler(event, context):
    unencrypted = []
    for bucket in s3.list_buckets()['Buckets']:
        name = bucket['Name']
        try:
            response = s3.get_bucket_encryption(Bucket=name)
            algo = response['ServerSideEncryptionConfiguration']['Rules'][0]\
                ['ApplyServerSideEncryptionByDefault']['SSEAlgorithm']
            if algo == 'aws:kms':
                print(f"{name} : ENCRYPTED")
            else:
                print(f"{name} : NOT ENCRYPTED")
                unencrypted.append(name)
        except:
            print(f"{name} : NOT ENCRYPTED")
            unencrypted.append(name)
    return {
        "statusCode": 200,
        "unencrypted_buckets": unencrypted
    } 


### Result

CloudWatch logs successfully identified:

bucket-encryptednew : ENCRYPTED
bucket-unencryptednew : NOT ENCRYPTED


# Assignment 4: Automatic EBS Snapshot and Cleanup

## Objective

Create EBS snapshots automatically using AWS Lambda.

## AWS Services Used

- Amazon EC2
- Amazon EBS
- AWS Lambda
- IAM
- CloudWatch
- Python Boto3

## Implementation Steps

### Step 1: Create EBS Volume

Created:
10 GB gp3 volume


### Step 2: Create IAM Role

Created:
LambdaEBSRole

Attached policies:
AmazonEC2FullAccess
AWSLambdaBasicExecutionRole

### Step 3: Create Lambda Function

Function:

EBSSnapshotManager

### Step 4: Lambda Code

python
import boto3

ec2=boto3.client('ec2')

def lambda_handler(event,context):

    volume_id='YOUR_VOLUME_ID'

    response=ec2.create_snapshot(
        VolumeId=volume_id,
        Description='Automated Snapshot'
    )

    return {
        'statusCode':200,
        'snapshot_id':
        response['SnapshotId']
    }

### Result

Successfully created EBS snapshots automatically using Lambda.

# Architecture Diagram

AWS Resources
       |
       v
AWS Lambda
       |
       v
Python Boto3 SDK
       |
       +----------------+
       |                |
       v                v
      EC2              S3
       |
       v
      EBS
       |
       v
CloudWatch Logs

# Technologies Used

- AWS Lambda
- Amazon EC2
- Amazon S3
- Amazon EBS
- AWS IAM
- Amazon CloudWatch
- AWS KMS
- Python 3
- Boto3 SDK

# Author

**Saranya S**

AWS Automation and Cloud Learning Project
