# ECS deployment

Deployment for Tower onto an AWS ECS cluster. 

## Requirements

* An AWS Account
* The AWS CLI tool 
* Pull permissions for Nextflow Tower container registry
* Software License for Nextflow Tower

## 1. Configure an AWS ECS cluster

Navigate to the ECS console in AWS and select **Create cluster**.

Amazon ECS -> Clusters -> EC2 Linux + Networking

#### ECS Cluster Configuration

* Name: nf-tower

#### Instance Configuration

* Provisioning Model: On-demand
* EC2 instance type: c4.2xlarge
* Number of instances: 1
* EC2 AMI id: Amazon Linux 2
* Root EBS Volume Size (GiB): 30 GB
* Key pair: none


#### Networking Configuration

* Create new VPC


#### Container instance IAM role

* Create new role (if ecsInstanceRole doesn't exist)


#### Obtain Instance `ServerURL`

* Select 'View Cluster' -> 'ECS Instances' - > 'EC2 Instance' -> 'Instance Id'
* Record the Public IPv4 of the instance e.g. `3.122.246.202`


## 2. Configure an SMTP server

#### Go to Simple Email Service (SES) console in AWS

Make note of SMTP Server Settings

* SMTP Server Name e.g. `email-smtp.eu-central-1.amazonaws.com`
* Port: 25, 465 or 587
* Use Transport Layer Security (TLS): Yes
* Authentication: Your SMTP credentials. See below for more information.

#### Create SMTP credentials
Create an SMTP IAM User and recored credentials, e.g.
* IAM name:`ses-smtp-user.20210122-161426`
* SECRET ACCESS: `AKIAS3ISGBZV5Z5BZHFR`
* SECRET KEY: `BOcG7nJMStdoy4kjxRvL/068DL2Shx1mW20Bg7Whtt5Q`


## 4. Create an database MySQL database with RDS

* Select 'Standard create' and configure with:
* Engine type: Amazon Aurora
* Edition: Amazon Aurora with MySQL compatibility
* Capacity type: Serverless
* Version: Aurora (MySQL 5.7)
* DB instance identifier: `tower`
* Master username: `admin`
* Master password: <password>
* Select "Create database"

Once the database has been setup, record the endpoint for the `TowerDbUrl` in the format:
`jdbc:mysql://tower.c5r6mzfoj9im.us-east-2.rds.amazonaws.com:3306/tower`

## 5. Set up the MySQL database.

* Select 'Query Editor' from the RDS console.
* Enter the credentials from above to access the database
* Modify the database to create the tables and user access:

```
    CREATE DATABASE tower;
    ALTER DATABASE tower CHARACTER SET utf8 COLLATE utf8_bin;
    CREATE USER 'tower' IDENTIFIED BY <password>;    
    GRANT ALL PRIVILEGES ON tower.* TO tower@'%' ;
```
Record the user and password for the next steps.

## 6. Update the CloudFormation parameter JSON

Copy `params.json.template` to `params.json` and update values

* ClusterName: Name of the ECS cluster (e.g. nf-tower)
* SMTPUser: Your mail server user name.
* SMTPPassword: Your mail server user password.
* TowerEmail: Mail notification sender address e.g. tower@your-company.com
* ServerURL: The Nextflow Tower HTTP endpoint e.g. https://tower.your-company.com
* JWTSECRET: Secret used to generate the login JWT token. Use a long random string (35 chars or more).
* CRYPTOSECRETKEY: Random secret key used to encrypt user credentials.
* TowerDbUrl: `jdbc:mysql://<db-instance>.c5r6mzfoj9im.us-east-2.rds.amazonaws.com:3306/tower`
* DBUSER: MySQL connection user name.
* DBPASSWORD: MySQL connection user password.
* SMTPHOST: Your mail server host name.
* SMTPPORT: Your mail server port.
* DBVOLUME: Directory used for Tower database


## 7. Create the Cloudformation stack

Running the following command will launch the containers on the specified ECS cluster.

```
aws cloudformation create-stack \
    --stack-name TowerCloudformation \
    --template-body file://aws-ecs-cloudformation.json \
    --parameters file://params.json
```

## 8. Delete the Cloudformation stack (optional)

If you obtain any errors, you can delete stack at any moment and change the parameters.

```
aws cloudformation delete-stack \
    --stack-name TowerCloudformation
```
