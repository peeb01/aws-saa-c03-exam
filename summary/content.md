# AWS Certified Solutions Architect - Associate (SAA-C03) Deep-Dive Feature Checklist

## 1. Storage & Data Transfer
### Amazon S3 (Simple Storage Service)
- Standard Storage Class
- Intelligent-Tiering Storage Class
- Standard-IA Storage Class
- One Zone-IA Storage Class
- Glacier Instant Retrieval Storage Class
- Glacier Flexible Retrieval Storage Class
- Glacier Deep Archive Storage Class
- S3 Express One Zone Storage Class
- Outposts Storage Class
- Lifecycle Policies
- Event Notifications
- Storage Lens
- Object Lock
- Object Lock: Compliance Mode
- Object Lock: Governance Mode
- Object Lock: Legal Holds
- Glacier Vault Lock
- Versioning
- Versioning: Delete Marker
- Cross-Region Replication (CRR)
- Same-Region Replication (SRR)
- Presigned URLs
- Transfer Acceleration
- S3 Select
- Object Lambda
- S3 Mountpoint
- S3 Access Grants
- S3 CORS (Cross-Origin Resource Sharing)
- S3 Requester Pays
- S3 MFA Delete
- S3 Access Points
- S3 Access Logs
- Multipart Upload

### Amazon EBS (Elastic Block Store)
- gp2 SSD Volume
- gp3 SSD Volume
- io1 Provisioned IOPS SSD Volume
- io2 Provisioned IOPS SSD Volume
- io2 Block Express SSD Volume
- st1 Throughput Optimized HDD Volume
- sc1 Cold HDD Volume
- EBS Multi-Attach
- Fast Snapshot Restore (FSR)
- EBS Encryption via KMS
- Data Lifecycle Manager (DLM)

### Amazon EFS (Elastic File System)
- EFS Standard Storage Class
- EFS Infrequent Access (IA) Storage Class
- EFS Archive Storage Class
- EFS Elastic Throughput Mode
- EFS Provisioned Throughput Mode
- EFS General Purpose Performance Mode
- EFS Max I/O Performance Mode
- EFS Replication

### Amazon FSx
- FSx for Windows File Server (SMB/NTFS/Active Directory)
- FSx for Lustre (HPC/S3 Integration)
- FSx for NetApp ONTAP
- FSx for OpenZFS

### AWS Backup
- Centralized Backup Plans
- Cross-Region Backup
- Cross-Account Backup
- Backup Vault Lock
- Backup Legal Holds

### Hybrid Storage (AWS Storage Gateway)
- S3 File Gateway
- FSx File Gateway
- Volume Gateway: Cached Mode
- Volume Gateway: Stored Mode
- Tape Gateway (VTL)

### Data Migration & Transfer
- AWS Snowcone
- Snowball Edge: Storage Optimized
- Snowball Edge: Compute Optimized
- Snowmobile
- AWS Transfer Family (SFTP/FTPS/FTP/AS2)
- AWS DataSync

## 2. Compute & Scaling
### Amazon EC2 & AMIs
- Instance Families: General Purpose (T/M)
- Instance Families: Compute Optimized (C)
- Instance Families: Memory Optimized (R/X/Z)
- Instance Families: Accelerated Compute (P/G)
- Instance Families: Storage Optimized (I/D/H)
- Public AMIs
- Private AMIs
- AWS Marketplace AMIs
- Community AMIs
- EC2 User Data Bootstrap Script
- IMDSv1 (Instance Metadata Service v1)
- IMDSv2 (Session Token via HTTP PUT)
- EC2 Instance Store (Ephemeral Storage)
- Cluster Placement Group (Low-Latency/High-Throughput)
- Spread Placement Group (Max 7 Instances per AZ on separate racks)
- Partition Placement Group (Distributed Racks/Big Data)
- EC2 Hibernate (RAM to Root EBS)

### EC2 Pricing Models
- On-Demand Instances
- Spot Instances
- Spot Fleet
- Standard Reserved Instances (RI)
- Convertible Reserved Instances (RI)
- Savings Plans
- Dedicated Instances
- Dedicated Hosts (BYOL/Compliance)

### ASG (Auto Scaling Group) & AWS Auto Scaling
- Target Tracking Scaling Policy
- Step Scaling Policy
- Simple Scaling Policy
- Predictive Scaling Policy (Machine Learning Forecast)
- Scheduled Scaling Policy
- Launch Templates
- Launch Configurations
- Instance Refresh (Rolling Update)
- Warm Pools (Pre-booted Stopped Instances)
- Lifecycle Hooks (Pending:Wait / Terminating:Wait)

### AWS Lambda & Serverless Compute
- Lambda Layers
- Provisioned Concurrency (Cold Start Mitigation)
- Function URLs (Direct HTTPS Endpoints)
- SnapStart (Java Initialization Snapshot)
- Destination Policies
- Lambda Limits: 15-Minute Timeout
- Lambda Limits: 10GB Temporary Storage (/tmp)
- Lambda inside VPC

### AWS Batch & Elastic Beanstalk
- AWS Batch Jobs
- AWS Batch Job Definitions
- AWS Batch Job Queues
- AWS Batch Compute Environments
- AWS Elastic Beanstalk
- Beanstalk Deployments: All at Once
- Beanstalk Deployments: Rolling
- Beanstalk Deployments: Rolling with Additional Batch
- Beanstalk Deployments: Immutable
- Beanstalk Deployments: Blue/Green

## 3. Networking & Content Delivery
### VPC (Virtual Private Cloud)
- Public Subnet (Route to IGW)
- Private Subnet (Route to NAT Gateway)
- Isolated Subnet (No Internet Route)
- Internet Gateway (IGW)
- NAT Gateway (Managed/Public Subnet/Scale Automatically)
- NAT Gateway: Regional High Availability Configuration
- NAT Instance (Disable Source/Destination Check)
- Bastion Hosts
- Egress-Only Internet Gateway (IPv6 Private Subnet Outbound Only)
- Customer Gateway (CGW)
- Virtual Private Gateway (VGW)
- Security Groups (Stateful/Allow Rules Only/Instance Level)
- NACLs (Stateless/Allow & Deny Rules/Numbered Rules/Subnet Level)
- VPC Peering (No Transitive Routing)
- AWS Transit Gateway (Hub-and-Spoke Mesh)
- AWS Direct Connect (DX)
- Direct Connect Gateway (Cross-Region/Cross-Account VPC Link)
- Site-to-Site VPN (IPsec)
- Gateway Endpoints (S3 & DynamoDB Only/Free)
- Interface Endpoints (AWS PrivateLink/ENI/Traffic Cost)
- VPC Flow Logs
- Traffic Mirroring (Deep Packet Inspection)
- VPC Reachability Analyzer
- AWS Network Firewall (Layer 3-7 VPC Protection)
- Amazon VPC Lattice

### ELB (Elastic Load Balancing)
- Application Load Balancer (ALB - Layer 7/HTTP/HTTPS/Path/Host/Query)
- Network Load Balancer (NLB - Layer 4/TCP/UDP/Static IP/Ultra-Low Latency)
- Gateway Load Balancer (GWLB - Layer 3/Virtual Appliances Check)
- Target Groups
- Health Checks
- Cross-Zone Load Balancing
- Sticky Sessions (Session Affinity via Cookies)
- SSL Termination
- Connection Draining / Deregistration Delay

### Route 53
- Route 53 Record Types: CNAME
- Route 53 Record Types: Alias (Root Domain Mapping/Free)
- Simple Routing Policy
- Weighted Routing Policy (Canary Testing)
- Latency-based Routing Policy
- Failover Routing Policy (Active-Passive Disaster Recovery)
- Geolocation Routing Policy (By Country/Continent)
- Geoproximity Routing Policy (By Physical Distance + Bias)
- IP-based Routing Policy
- Multi-value Answer Routing Policy (Up to 8 Healthy Records)
- Route 53 Resolver (Inbound/Outbound Endpoints for Hybrid DNS)
- Route 53 Application Recovery Controller (ARC)

### CloudFront & AWS Global Accelerator
- Cache Behaviors
- Cache Invalidation (Forced Cache Clear)
- Origin Groups (Failover)
- Origin Shield (Centralized Cache Layer)
- CloudFront Functions (High-Performance/Header Manipulation)
- Lambda@Edge (Complex Computing/External Data Fetching)
- AWS Global Accelerator (Anycast Static IPs/AWS Backbone Network)

## 4. Databases
### Amazon RDS & Aurora
- RDS Multi-AZ Deployment (Synchronous/HA/Failover)
- RDS Multi-AZ DB Cluster (1 Primary + 2 Readable Standbys)
- RDS Read Replicas (Asynchronous/Scalability/Cross-Region)
- RDS Custom (Oracle & Microsoft SQL Server OS-level access)
- RDS Proxy (Serverless Connection Pooling)
- Amazon Aurora MySQL-Compatible
- Amazon Aurora PostgreSQL-Compatible
- Aurora Serverless v2 (Instant Scalability via ACUs)
- Aurora Global Database (Cross-Region Asynchronous/DR)
- Aurora Write Forwarding

### Amazon DynamoDB & Purpose-Built Databases
- Serverless NoSQL Database
- DynamoDB Accelerator (DAX) (In-memory Read Cache)
- DynamoDB Global Tables (Multi-Region Active-Active)
- DynamoDB Streams
- DynamoDB Time to Live (TTL)
- DynamoDB Point-in-Time Recovery (PITR)
- DynamoDB Import/Export to S3
- LSI (Local Secondary Index - Same Partition Key / Different Sort Key)
- GSI (Global Secondary Index - Different Partition Key / Different Sort Key)
- ElastiCache for Redis (HA/Data Structures)
- ElastiCache for Memcached (Simple Key-Value/Horizontal Scale)
- Amazon MemoryDB (In-memory + Multi-AZ Transaction Log)
- Amazon DocumentDB (MongoDB Compatible)
- Amazon Keyspaces (Apache Cassandra Compatible)
- Amazon Neptune (Graph DB/Fraud Detection)
- Amazon QLDB (Quantum Ledger Database/Immutable History)
- Amazon Timestream (Time-Series Data/IoT/Clickstream)

## 5. Security, Identity & Compliance
### IAM (Identity and Access Management)
- IAM Users
- IAM Groups
- IAM Roles
- IAM Policies (JSON Document)
- IAM Least Privilege Principle
- IAM Policy Evaluation Logic: Explicit Deny
- IAM Policy Evaluation Logic: Explicit Allow
- IAM Policy Evaluation Logic: Default Deny (Implicit Deny)
- IAM Permissions Boundaries
- IAM Identity Center (AWS Single Sign-On)
- AWS Control Tower (Landing Zone Multi-Account Deployment)

### AWS KMS & Encryption Keys
- AWS Managed Keys
- Customer Managed Keys (CMK)
- KMS Symmetric Keys
- KMS Asymmetric Keys
- KMS Multi-Region Keys
- Custom Key Store (KMS backed by CloudHSM)

### Data Protection & Firewalls
- CloudHSM (Dedicated Hardware/FIPS 140-2 Level 3/Single-Tenant)
- AWS Secrets Manager (Secret Rotation via Lambda)
- AWS WAF (Web Application Firewall - Layer 7 Rules)
- AWS Shield Standard (Default DDoS Protection/Free)
- AWS Shield Advanced (24/7 DRT Team/DDoS Cost Protection)
- AWS Shield: Anti-DDoS Architecture Best Practices
- AWS Firewall Manager (Centralized Policy Enforcement)
- AWS GuardDuty (Intelligent Threat Detection via Machine Learning)
- AWS Inspector (Vulnerability Scanning for EC2, ECR, Lambda)
- Amazon Macie (PII Detection in S3 using AI)
- AWS Security Hub (Centralized Security Compliance Dashboard)
- Amazon Detective (Graph-based Security Investigation)
- AWS Directory Service (Managed Microsoft AD / AD Connector / Simple AD)
- AWS Audit Manager
- AWS Artifact (Compliance Reports Download Portal)
- Cognito User Pools (Authentication & Login)
- Cognito Identity Pools (Authorization to AWS Resources)

## 6. Analytics & Data Integration
### Data Analytics & Warehousing
- Amazon Redshift (OLAP Columnar Storage Data Warehouse)
- Redshift Serverless
- Redshift Spectrum (Query S3 directly)
- Redshift Concurrency Scaling
- Redshift RA3 Nodes (Separate Compute & Storage)
- Redshift Data Sharing
- Amazon Athena (Serverless SQL Query on S3)
- Athena Federated Query
- Athena Workgroups
- Athena Notebooks
- AWS Glue
- Glue Data Catalog
- Glue Crawlers
- Glue Jobs (ETL Spark)
- Glue DataBrew
- Glue Studio
- AWS Lake Formation (Row & Column Level Security)
- Amazon EMR (Elastic MapReduce - Spark/Hadoop Clusters)
- Amazon QuickSight (BI Dashboards)

### Streaming & Pipelines
- Kinesis Data Streams (Shard Management/Real-Time Milliseconds)
- Kinesis Data Firehose (Serverless Streaming Data Delivery to S3/Redshift)
- Kinesis Data Analytics (SQL or Apache Flink on Streams)
- Kinesis Video Streams
- Amazon MSK (Managed Streaming for Apache Kafka)
- OpenSearch Service (Log Analytics & Search Engine)
- AWS Data Exchange

## 7. Messaging & Application Integration
### Amazon SQS (Simple Queue Service)
- SQS Standard Queue (Unlimited Throughput/At-least-once Delivery)
- SQS FIFO Queue (First-In-First-Out/Exactly-once Delivery)
- SQS Dead Letter Queue (DLQ)
- SQS Delay Seconds
- SQS Visibility Timeout (Hide message during processing)
- SQS Long Polling (Reduce empty responses)
- SQS Short Polling

### Messaging Infrastructure
- Amazon SNS (Simple Notification Service - Pub/Sub 1-to-Many)
- SNS FIFO Topics
- Message Filtering
- SNS and SQS Fan-Out Pattern
- Amazon MQ (Managed ActiveMQ / RabbitMQ)
- AWS Step Functions: Standard Workflows (Long-running)
- AWS Step Functions: Express Workflows (High-volume up to 5 mins)
- Amazon AppFlow
- Amazon AppSync (GraphQL Serverless Service)
- API Gateway: HTTP API (Fast/Cost-Effective)
- API Gateway: REST API (Full Features)
- API Gateway: WebSocket API (Bi-directional Connection)
- API Gateway Features: Caching
- API Gateway Features: Throttling
- API Gateway Features: Usage Plans & API Keys
- API Gateway Features: Canary Deployments

## 8. Management, Monitoring & Governance
- CloudWatch Metrics
- CloudWatch Logs
- CloudWatch Alarms
- CloudWatch Dashboards
- CloudWatch Container Insights
- CloudWatch Contributor Insights
- Amazon EventBridge (Serverless Event Bus)
- EventBridge Pipes
- EventBridge Schema Registry
- AWS CloudTrail (API Audit Tool - Who, What, When, Where)
- AWS Config (Configuration History & Auto-Remediation)
- AWS Compute Optimizer
- AWS Health Dashboards (Service Health / Personal Health)
- AWS Service Catalog
- AWS Organizations
- Service Control Policies (SCPs) (Guardrails for Multi-Account/Root User restriction)
- Tag Policies
- Backup Policies
- AWS Systems Manager (SSM) Session Manager (Secure Shell via Browser/No Port 22)
- AWS Systems Manager (SSM) Parameter Store (Key-Value Config Storage)
- AWS Systems Manager (SSM) Patch Manager
- AWS Systems Manager (SSM) State Manager
- AWS Budgets (Cost & Forecast Alarms)
- AWS Cost Explorer (Historical & Future Cost Graph)
- AWS Cost Anomaly Detection (ML Cost Spike Warning)

## 9. Containers, Dev Tools & Media
- Amazon ECR (Container Image Registry with Vulnerability Scanning)
- Amazon ECS (Elastic Container Service)
- ECS EC2 Launch Type (Manage Infrastructure)
- AWS Fargate (Serverless Container/No Infrastructure Management)
- ECS Task Definitions
- ECS Services & Auto Scaling
- Amazon EKS Cloud (Managed Kubernetes Service)
- AWS Amplify (Full-Stack App Development Framework)
- Amazon Q Developer (Generative AI Coding Assistant)
- AWS CodeWhisperer
- Device Farm (Real Smartphone Testing on Cloud)
- AWS MediaConvert (Broadcast-Grade Video Transcoding)
- Elastic Transcoder (Legacy Transcoding Service)

## 10. Generative AI & Machine Learning
- Amazon Bedrock (Serverless Access to Foundation Models via API)
- Amazon SageMaker (End-to-End ML Platform)
- Amazon SageMaker Canvas (No-Code ML Interface)
- Amazon Rekognition (Image & Video Analysis)
- Amazon Polly (Text-to-Speech)
- Amazon Transcribe (Speech-to-Text)
- Amazon Translate (Language Translation)
- Amazon Comprehend (Natural Language Processing/Sentiment Analysis)
- Amazon Comprehend Medical (Medical Document NLP)
- Amazon Lex (Chatbot & Conversational Interface)
- Amazon Connect (Cloud Contact Center Service)
- Amazon Textract (Intelligent Document & Data Extraction/Advanced OCR)
- Amazon Kendra (ML-Powered Enterprise Search Engine)
- Amazon Personalize (Real-Time Recommendation Engine)
- Amazon Forecast (Time-Series Forecasting)

## 11. Migration & Real-World Architecture Patterns
### Migration Tools
- AWS Migration Hub
- AWS Application Migration Service (MGN) (Lift-and-Shift Server Rehost)
- AWS Database Migration Service (DMS)
- DMS Schema Conversion Tool (SCT) (Cross-Engine Database Schema Translation)
- VMware Cloud on AWS

### Real-World Architecture Patterns (กรณีศึกษาโจทย์ออกสอบบ่อย)
- MyClothes.com Pattern (Stateless Architecture / External Session Store on ElastiCache or DynamoDB)
- WhatsTheTime.com Pattern (Edge Caching / CloudFront Frontend API Cache via TTL Optimization)
- MyWordPress.com Pattern (VPC Multi-AZ Setup / ALB Public / ASG Private / Amazon EFS Shared File System / RDS Multi-AZ DB)
- Ingestion Pipeline Pattern (Big Data Pipeline: App -> API Gateway -> Kinesis Data Streams -> Kinesis Data Firehose -> S3 Data Lake)

## 12. Cloud Architecture Frameworks & Governance
### AWS Well-Architected Framework (6 Pillars)
- Well-Architected Framework: Operational Excellence Pillar
- Well-Architected Framework: Security Pillar
- Well-Architected Framework: Reliability Pillar
- Well-Architected Framework: Performance Efficiency Pillar
- Well-Architected Framework: Cost Optimization Pillar
- Well-Architected Framework: Sustainability Pillar
- AWS Well-Architected Tool

### Architecture Advisory & Evaluation
- AWS Trusted Advisor
- AWS Trusted Advisor: Cost Optimization Evaluation
- AWS Trusted Advisor: Security & Vulnerabilities Checks
- AWS Trusted Advisor: Fault Tolerance & High Availability
- AWS Trusted Advisor: Performance Insights
- AWS Trusted Advisor: Service Limits / Quotas Monitoring

## 13. Global & Advanced Corporate Network Patterns
### Hybrid Cloud Identity Integration
- AWS Directory Service: Managed Microsoft AD
- AWS Directory Service: AD Connector (No data sync / Direct on-prem Active Directory proxy)
- AWS Directory Service: Simple AD
- Identity Federation via SAML 2.0 (Corporate Single Sign-On to AWS Console)

### Advanced Cross-Account Sharing
- Encrypted AMI Sharing Process (Requires both AMI share and custom KMS key share permissions)
- S3 Cross-Account Access (IAM Policies + S3 Bucket Policies Integration)
- AWS Resource Access Manager (RAM) (Share Subnets / Transit Gateways across AWS Accounts)

### Special Purpose Infrastructure
- Instance Scheduler on AWS (Automated EC2/RDS Start-Stop cron schedules)
- Amazon SES (Simple Email Service - High-volume transactional emails)
- Amazon Pinpoint (Targeted multi-channel customer engagement messaging)