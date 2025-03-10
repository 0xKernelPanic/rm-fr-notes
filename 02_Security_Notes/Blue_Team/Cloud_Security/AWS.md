---
title: AWS
tags:
  - cloud
  - aws
created: 2025-03-07
---

# AWS

## Description
`AWS (Amazon Web Service)` is a cloud service platform made by **Amazon** that offers variety of services like:
1. Compute
2. Storage
3. Networking
4. Databases
5. and a lot of more.
## Use Cases
- Scalability and flexibility
- High availability
- Disaster recovery and backup
- Don\`t need to bother about infrastructure
- Ease of use
- Shared responsibility model

## Services
### Compute
#### EC2 (Elastic Compute Cloud)
Allow  you to launch VM\`s. Its called an **instance**.

#### ECS (Elastic Container Service)
ECS is a container orchestration service that support Docker containers. Launches a cluster of servers on EC2 instances with Docker installed. When you need a Docker as a Service or you need to run containers.

#### AWS Lambda (a serverless function)
It is designed to enable developers to run code without provisioning or managing servers. It executes code in response to events and automatically manages the computing resources required by that code. Pay only for the compute time you consume.

and more...
### Storage
#### EBS (Elastic Block Store) - Block
Data is split into evenly split blocks direclty accessed by the OS. Supports only a single write volume. It is used when you need a virtual hard drive attached to a VM.

#### EFS (AWS Elastic File Storage) - File
File is stored with data and metadata. Multiple connections via a network share. Supports multiple reads and writings. Eg. NFS or SMB. It is used when you need a file share where multiple users or VMs needs to access the same drive 

#### S3 (Amazon Simple Storage Service) - Object
Object is stored with data, metadata and Unique ID. Scales with a storage limit (basically unlimited storage if you pay) and supports multiple reads and writes. Eg. HTTP/S and API. No intended for high IOPs

and more...

##### S3 Objects
Objects contains data. They are like files. Objects may consist of key, value, version ID, metadata. 
**Fun fact:** you can store an individual object from 0 Bytes to 5 Terabytes in size.

##### S3 Buckets
Buckets hold objects. S3 is a universal namespace so buckets names must be unique.
### Databases
#### DynamoDB
DynamoDB is a serverless NoSQL key/value and document database. It is designed to scale to **bilions of records** with guaranteed consistent data return in at least a second. its a flagship database service. Its cost effective and very fast.
#### DocumentDB
DocumentDB is a NoSQL document database that is compatible with MongoDB.

#### Amazon Keyspaces
is a fully managed Apache Cassandra database.

and more...
### Networking

#### Region
Geographical location of your network

#### Availability Zones (AZ)
The datacentre of your AWS resources.

#### Virtual Private Cloud (VPC)
VPC is a logically isolated section of the AWS Cloud where you can launch AWS Resources. You choose a range of the IPs using CIDR Range (10.0.0.0/16). There is a concept of private and public subnets.
##### Direct Connect
Dedicated gigabit connection from on-premise datacentre to AWS (very fast connection). Its not directly encrypting data in transit thats why is used together with **VPN**.

##### PrivateLinks (VPC Interface Endpoints)
Keeps traffic within the AWS network and not traverse the internet to keep traffic secure.

##### Autoscaling vs loadbalancers
Auto Scaling is used for automatic scaling up and scaling down. Load balancer is used to distribute the incoming traffic across multiple targets. 

#### Internet Gateway (IGW)
IGW enable access to the internet.

#### Route Tables
determine where network traffic from subnets are directed.

#### Network Access Control List (NACLs)
act as a firewall at a subnet level

#### Security Group
act as a firewall at a instance level

##### NACLs vs Security Groups

| Name       | NACL                                                                                                | Security Group                                                                                                                        |
| ---------- | --------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Descrption | - Acts as a firewall at the subnet level. <br>- You create **Allow and Deny rules**.<br>- Stateless | - Acts as a firewall at the instance level.<br> - Implicitly denies all traffic.<br> - You create **only Allow rules**. <br>-Stateful |
| Example    | - Block a specific IP address known for abuse                                                       | - Allow EC2 instance access on port 22 for SSH. <br>- You cannot block a single IP address.                                           |

##### Cloudfront
AWS CloudFront is a Content Delivery Network (CDN) service that speeds up the delivery of web content (like websites, videos, APIs, or software downloads) to users by caching content at **edge locations** around the world.
## Security
### Best Practices
- Never commit access keys/credentials to a codebase.
- Restrict public access to S3 buckets and enable encryption.
- Encrypt block storage (EBS volumes).
- Implement regular snapshots and backups.
- Store critical data across multiple Availability Zones (AZs) for redundancy.
- Enable MFA on AWS users (root included)
- Don\`t use aws root user for daily basis

### Single Sign-on (SSO)
is an authentication scheme that allows user to log in with a single ID and password to different systems and software. Its based on **SAML** protocol for example login.gov.pl (**profil zaufany**).

### AWS IAM
AWS identity and access management is used to create and manage aws users, groups and permissions by allowing or denying access to aws resources.  It controls authentication (who) and authorization (what they can do).
#### Policies and permissions

1. IAM Policies - JSON documents which grants permissions for specific user, group or role to access services. Policies are attached to **IAM Identities**.
2. IAM Permissions - The API actions that can or cannot be perfomerd. They are represented in the IMAP Policy documents.

#### IAM Identities
1. Users - end users who log into the console or interact with AWS.
2. Groups - Group of users that share the same permission.
3. Roles  - Roles grant AWS resources permissions to specific AWS API actions. Associate policies to a role and then assign it to an aws resource.

##### ConsoleME
ConsoleMe is an open source Netflix project to self-serve short-lived IAM policies so an end user can access AWS resources while enforcing JEA and JIT (Just-Enough-Access Just-In-Time)

##  References 
- https://en.wikipedia.org/wiki/Amazon_Web_Services
- https://aws.amazon.com/architecture/security-identity-compliance/?cards-all.sort-by=item.additionalFields.sortDate&cards-all.sort-order=desc&awsf.content-type=*all&awsf.methodology=*all
- https://www.youtube.com/watch?v=NhDYbskXRgc
- https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html
- https://synergytop.com/blog/aws-auto-scaling-and-load-balancing-what-do-they-mean/