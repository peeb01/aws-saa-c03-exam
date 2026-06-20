# AWS Certified Solutions Architect - Associate (SAA-C03) Exam Cheat-Sheet

สารบัญสรุปย่อบริการและฟีเจอร์ของ AWS ทั้งหมดสำหรับเตรียมสอบ SAA-C03 เน้นเนื้อหากระชับ จบใน 1 บรรทัด พร้อมคีย์เวิร์ดสำคัญ (Exam Keywords) เพื่อใช้ตัดช้อยส์และทำคะแนนได้รวดเร็วที่สุด

---

## Deep-Dive Cheat Sheets
เนื้อหาเจาะลึกของแต่ละหมวดหมู่สามารถอ่านเพิ่มเติมได้ใน Folder [content/](content/):
- **[01 Storage & Data Transfer](content/01_storage_data_transfer.md)** - S3, EBS, EFS, FSx, Hybrid Storage, Migration, AWS Backup
- **[02 Compute & Scaling](content/02_compute_scaling.md)** - EC2, AMIs, Pricing Models, ASG, Lambda, Batch, Beanstalk
- **[03 Networking & Content Delivery](content/03_networking_content_delivery.md)** - VPC, ELB, Route 53, CloudFront, Global Accelerator
- **[04 Databases](content/04_databases.md)** - RDS, Aurora, DynamoDB, ElastiCache, Purpose-Built Databases
- **[05 Security, Identity & Compliance](content/05_security_identity_compliance.md)** - IAM, KMS, WAF, Shield, GuardDuty, Macie, Cognito, Directory Service
- **[06 Analytics & Data Integration](content/06_analytics_data_integration.md)** - Redshift, Athena, Glue, EMR, QuickSight, Kinesis, MSK
- **[07 Messaging & Application Integration](content/07_messaging_application_integration.md)** - SQS, SNS, MQ, Step Functions, API Gateway, AppSync
- **[08 Management, Monitoring & Governance](content/08_management_monitoring_governance.md)** - CloudWatch, EventBridge, CloudTrail, Config, Organizations, SCPs, SSM
- **[09 Containers, Dev Tools & Media](content/09_containers_dev_tools_media.md)** - ECS, EKS, Fargate, ECR, Amplify, Q Developer, MediaConvert
- **[10 Generative AI & Machine Learning](content/10_generative_ai_machine_learning.md)** - Bedrock, SageMaker, Rekognition, Polly, Transcribe, Textract, Kendra
- **[11 Migration, Architecture Frameworks & Advanced Patterns](content/11_migration_architecture_frameworks.md)** - DMS, MGN, Well-Architected Tool, Trusted Advisor, Corporate Hybrid AD, RAM

---

## 🚀 สรุปย่อรายบริการแบบบรรทัดเดียว (Cheat-Sheet Index)

## สารบัญเนื้อหา (Table of Contents)
* [01. Storage & Data Transfer](#01-storage)
* [02. Compute & Scaling](#02-compute)
* [03. Networking & Content Delivery](#03-networking)
* [04. Databases](#04-databases)
* [05. Security, Identity & Compliance](#05-security)
* [06. Analytics & Data Integration](#06-analytics)
* [07. Messaging & Application Integration](#07-messaging)
* [08. Management, Monitoring & Governance](#08-management)
* [09. Containers, Dev Tools & Media](#09-containers)
* [10. Generative AI & Machine Learning](#10-genai)
* [11. Migration, Architecture Frameworks & Advanced Patterns](#11-migration)

### <a id="01-storage"></a>01. Storage & Data Transfer
#### Amazon S3 (Simple Storage Service)
- **Standard Storage Class:** เก็บข้อมูลแบบ Object ทั่วไป (Active Data), ปลายทางต้องการ High Availability (99.99%) และ Low Latency ระดับมิลลิวินาที
- **Intelligent-Tiering Storage Class:** ปรับย้าย Storage Class อัตโนมัติ (ไม่มีค่าธรรมเนียมการย้ายและไม่มีขั้นต่ำเวลาเก็บ) เมื่อไม่ทราบ Pattern การเข้าถึงข้อมูลที่แน่นอน
- **Standard-IA Storage Class:** ข้อมูลเข้าถึงไม่บ่อย (อย่างน้อยเดือนละครั้ง) แต่ต้องการเข้าถึงทันทีเมื่อมีคำขอ (มีค่าธรรมเนียม Retrieval Fee, เก็บขั้นต่ำ 30 วัน)
- **One Zone-IA Storage Class:** ข้อมูลเข้าถึงไม่บ่อย เก็บไว้ใน AZ เดียว ประหยัดกว่า Standard-IA 20% เหมาะกับข้อมูลสำรองหรือ Non-critical ที่สร้างใหม่ได้ง่าย
- **Glacier Instant Retrieval:** ข้อมูล Archive ที่เข้าถึงไม่บ่อยมาก (ไตรมาสละครั้ง) แต่ยังต้องการเข้าถึงทันทีในระดับมิลลิวินาที (เก็บขั้นต่ำ 90 วัน)
- **Glacier Flexible Retrieval:** ข้อมูล Archive ที่ยอมรับเวลาการดึงข้อมูลได้ตั้งแต่ 1-5 นาที (Expedited), 3-5 ชั่วโมง (Standard), หรือ 5-12 ชั่วโมง (Bulk) ฟรี
- **Glacier Deep Archive:** Class เก็บข้อมูลที่ประหยัดที่สุดสำหรับข้อมูลเก็บไว้นานเกิน 180 วัน เพื่อ Compliance ยอมรับการดึงกลับระดับ 12 หรือ 48 ชั่วโมง
- **S3 Express One Zone:** Class ที่ความเร็วสูงสุดและ Latency ต่ำสุดระดับ Single-digit Millisecond เหมาะกับ Real-time ML, Finance, และ Session Store
- **Outposts Storage Class:** เก็บข้อมูล S3 บน On-premises Hardware ผ่าน AWS Outposts เพื่อตอบโจทย์ Data Residency และ Local Processing
- **Lifecycle Policies:** ตั้งกฎเปลี่ยน Storage Class อัตโนมัติตามระยะเวลา (Transition) หรือลบ Object ที่หมดอายุเพื่อประหยัดค่าใช้จ่าย (Expiration)
- **Event Notifications:** ส่งแจ้งเตือนไปยัง SNS, SQS หรือ Lambda ทันทีเมื่อเกิดการเปลี่ยนแปลง เช่น PUT, POST, COPY, DELETE Object ใน S3
- **Storage Lens:** บริการแดช Board สรุปวิเคราะห์การใช้งาน S3 ทั้งองค์กร เพื่อระบุจุดลดค่าใช้จ่ายและตรวจสอบแนวปฏิบัติด้านความปลอดภัย
- **Object Lock (Compliance Mode):** Lock ไม่ให้ลบ/เขียนทับ Object โดยเด็ดขาดรวมถึง Root User ด้วย จนกว่าจะพ้นระยะเวลา Retention (ป้องกัน Ransomware)
- **Object Lock (Governance Mode):** Lock ไม่ให้ลบ/เขียนทับ Object ยกเว้นผู้ใช้ที่ได้รับสิทธิ์พิเศษ เช่น IAM user ที่มีสิทธิ์ `s3:BypassGovernanceRetention`
- **Object Lock (Legal Holds):** Lock ไม่ให้ลบ/เขียนทับ Object แบบไม่มีกำหนดเวลา โดยสามารถ Unlock ได้โดยผู้ใช้ที่มีสิทธิ์เท่านั้นเพื่อประเด็นทางกฎหมาย
- **Glacier Vault Lock:** บังคับใช้นโยบายความปลอดภัยของ Glacier Vault แบบถาวร (WORM) โดยแก้ไขหรือลบไม่ได้หลังจาก Lock นโยบายสำเร็จแล้ว
- **Versioning:** เก็บประวัติของ Object ทุกเวอร์ชัน ป้องกันการลบหรือเขียนทับโดยไม่ได้ตั้งใจ (มีค่าพื้นที่เก็บสำหรับทุกเวอร์ชันที่เก็บไว้)
- **Versioning (Delete Marker):** ตัวระบุแทนการลบ Object ในระบบ Versioning โดยการลบจริงต้องระบุ Version ID หรือลบผ่าน MFA เท่านั้น
- **Cross-Region Replication (CRR):** คัดลอก Object ข้าม Region อัตโนมัติแบบ Asynchronous เพื่อทำ Disaster Recovery (DR) หรือลด Latency
- **Same-Region Replication (SRR):** คัดลอก Object ภายใน Region เดียวกันข้าม AZ อัตโนมัติ เหมาะกับข้อกำหนดด้านการเก็บ Lock หรือ Data Lake เดียวกัน
- **Presigned URLs:** สิทธิ์เข้าถึงชั่วคราวแก่ผู้ใช้ที่ไม่มีสิทธิ์ IAM เพื่อดาวน์โหลดหรืออัปโหลด Object ใน S3 ภายในเวลาที่กำหนดอย่างปลอดภัย
- **Transfer Acceleration:** อัปโหลดข้อมูลขนาดใหญ่ข้ามทวีปได้เร็วขึ้นผ่านการส่งข้อมูลเข้า Edge Location ของ CloudFront แล้ววิ่งบน AWS Private Network
- **S3 Select:** กรองดึงเฉพาะข้อมูลแถวหรือคอลัมน์ที่ต้องการจาก File CSV, JSON, Parquet ด้วย SQL ก่อนส่งกลับ ช่วยประหยัด Bandwidth และลด Latency
- **Object Lambda:** รันโค้ด Lambda เพื่อดัดแปลงหรือแปลงข้อมูลดิบใน S3 ทันทีในระหว่างที่มีการเรียกขอ (GET) ก่อนส่งมอบให้ Application ปลายทาง
- **S3 Mountpoint:** Client สแต็คประสิทธิภาพสูงที่ช่วยให้สามารถติดตั้ง (Mount) ถังเก็บ S3 เสมือนเป็น Local File System ใน Linux container/instance
- **S3 Access Grants:** จัดการสิทธิ์การเข้าถึงข้อมูลใน S3 ให้กับ Application หรือผู้ใช้ผ่านสิทธิ์ใน Active Directory (AD) หรือ Identity Provider
- **S3 CORS:** อนุญาตให้เว็บ Application จากโดเมนอื่นดาวน์โหลดทรัพยากร (เช่น รูปภาพ, Script) จาก S3 bucket ได้อย่างปลอดภัยผ่าน Browser
- **S3 Requester Pays:** กำหนดให้ผู้ดาวน์โหลดข้อมูลเป็นผู้รับผิดชอบค่าใช้จ่ายในการโอนย้ายข้อมูล (Data Transfer Out) แทนเจ้าของ bucket
- **S3 MFA Delete:** บังคับให้ผู้ใช้งานต้องยืนยันตัวตนแบบหลายปัจจัย (MFA) เมื่อต้องการเปลี่ยนสถานะ Versioning หรือลบเวอร์ชันของ Object
- **S3 Access Points:** จุดเข้าถึงสำหรับ Bucket ขนาดใหญ่ที่มีการใช้งานจากหลายทีม ช่วยให้สามารถสร้างนโยบายความปลอดภัย (Policy) แยกเฉพาะกลุ่มได้
- **S3 Access Logs:** เก็บประวัติรายละเอียดการร้องขอ (Request) ทุกครั้งที่มีการเรียกใช้หรือเข้าถึงทรัพยากรภายใน S3 Bucket เพื่อการตรวจสอบย้อนหลัง
- **Multipart Upload:** อัปโหลด File ขนาดใหญ่แยกเป็นส่วนๆ แบบขนาน ช่วยเพิ่มประสิทธิภาพการทำงานและสามารถอัปโหลดใหม่เฉพาะส่วนที่พังได้ (ควรใช้หาก File > 100MB)

#### Amazon EBS (Elastic Block Store)
- **gp2 SSD Volume:** Block Storage SSD ทั่วไปสำหรับ EC2 ประสิทธิภาพ IOPS อิงตามขนาดพื้นที่เก็บ (ความจุมาก IOPS ยิ่งสูง สูงสุด 16,000 IOPS)
- **gp3 SSD Volume:** Block Storage SSD ยุคใหม่ แยกการกำหนดประสิทธิภาพ IOPS และ Throughput ออกจากขนาดพื้นที่เก็บได้อย่างเป็นอิสระ ประหยัดกว่า gp2
- **io1 Provisioned IOPS:** Block Storage SSD ประสิทธิภาพสูงพิเศษ กำหนด IOPS สูงได้ตามต้องการ เหมาะสำหรับ Database ขนาดใหญ่ เช่น Oracle, SQL Server
- **io2 Provisioned IOPS:** Block Storage SSD ประสิทธิภาพสูง เสถียรและทนทานกว่า io1 ถึง 100 เท่า (ความน่าเชื่อถือ 99.999%) ป้องกันข้อมูลสูญหายได้ดีขึ้น
- **io2 Block Express:** ให้ประสิทธิภาพสูงสุดระดับ SAN สำหรับงาน I/O หนักที่สุด เช่น SAP HANA, Database ระดับเวิลด์ Class (สูงสุด 256,000 IOPS)
- **st1 Throughput Optimized:** ฮาร์ดดิสก์ HDD มุ่งเน้นการโอนถ่ายข้อมูลสูง (Throughput) เหมาะสำหรับงาน Big Data, Data Warehousing, Log Processing
- **sc1 Cold HDD:** ฮาร์ดดิสก์ HDD ราคาประหยัดที่สุด ออกแบบมาสำหรับข้อมูลที่นานๆ เข้าถึงที แต่ยังต้องการทำงานแบบ Sequential Read
- **EBS Multi-Attach:** ติดตั้ง (Attach) วอลุ่ม EBS ประเภท io1/io2 เดียวกันเข้ากับ EC2 ใน AZ เดียวกันได้สูงสุด 16 ตัวพร้อมกัน เหมาะสำหรับ Clustered App
- **Fast Snapshot Restore (FSR):** เรียกคืนข้อมูลจาก Snapshot ลงวอลุ่มใหม่ได้เต็มประสิทธิภาพทันที โดยไม่ต้องรอกระบวนการอุ่นวอลุ่ม (Pre-warming)
- **EBS Encryption via KMS:** เข้ารหัสข้อมูลที่จัดเก็บ (Data-at-rest), ข้อมูลโอนย้าย, และ Snapshot โดยไม่มีผลกระทบต่อประสิทธิภาพการทำงานของ EC2
- **Data Lifecycle Manager (DLM):** บริการจัดการการสร้าง สำรองข้อมูล และลบ Snapshot ของ EBS และ AMI อัตโนมัติตามกฎเวลาที่ตั้งไว้

#### Amazon EFS (Elastic File System)
- **EFS Standard:** File ระบบเครือข่าย Linux (NFSv4) ขยายขนาดเก็บข้อมูลอัตโนมัติ เข้าถึงได้พร้อมกันจาก EC2 หลายพันตัวข้ามหลาย AZ
- **EFS Infrequent Access (IA):** ย้าย File ที่ไม่ได้ใช้งานเกินกำหนดไป Class ราคาประหยัดอัตโนมัติ โดยคิดค่าบริการดึงข้อมูลเมื่อมีการเรียกอ่าน File
- **EFS Archive:** เก็บรักษา File ที่ใช้งานน้อยมากเป็นเวลานานในราคาประหยัดที่สุดของ EFS โดยยังคงเข้าถึงข้อมูลได้ผ่านทางเดิน File เดิม
- **EFS Elastic Throughput Mode:** ปรับเปลี่ยนประสิทธิภาพการส่งผ่านข้อมูล (Throughput) ตามกิจกรรมการใช้งานจริงโดยอัตโนมัติ เหมาะกับ Workload ที่พยากรณ์ไม่ได้
- **EFS Provisioned Throughput Mode:** กำหนดความเร็วในการส่งผ่านข้อมูลคงที่ไว้ล่วงหน้าตามต้องการ ไม่ขึ้นกับขนาดพื้นที่จัดเก็บจริง
- **EFS General Purpose Mode:** โหมดประสิทธิภาพเริ่มต้นที่มี Latency ต่อการเรียกค้นต่ำสุด เหมาะสำหรับ Web Server, CMS, และ Shared Home Directory
- **EFS Max I/O Mode:** โหมดที่รองรับปริมาณงาน I/O รวมขนานกันในระดับสูงมาก ยอมแลกกับ Latency ที่เพิ่มขึ้นเล็กน้อย เหมาะกับงานประมวลผล Big Data
- **EFS Replication:** คัดลอกและซิงค์ข้อมูล File ระบบ EFS ไปยัง Region อื่นหรือตำแหน่งอื่นภายใน Region เดียวกันแบบ Asynchronous เพื่อทำ DR

#### Amazon FSx
- **FSx for Windows File Server:** File ระบบเครือข่าย Windows (SMB/NTFS) รองรับ Microsoft Active Directory ในตัว เหมาะสำหรับงานย้ายแอพ Windows
- **FSx for Lustre:** ระบบ Shared File ประสิทธิภาพสูงพิเศษ (HPC, ML) ทำงานร่วมกับ S3 ได้แนบแน่น สามารถโหลดและเขียนข้อมูลกลับเข้า S3 ได้รวดเร็ว
- **FSx for NetApp ONTAP:** Storage แชร์ที่ยกฟีเจอร์ระดับองค์กรของ NetApp ONTAP มารันบน AWS ช่วยให้โยกย้ายระบบ NFS/SMB เดิมขึ้นคลาวด์ได้โดยไม่ต้องเขียนโค้ดใหม่
- **FSx for OpenZFS:** Shared File ประสิทธิภาพสูงที่พัฒนาบนฐาน OpenZFS เพื่อตอบโจทย์ Application ที่ต้องการ Latency ต่ำระดับ Sub-millisecond

#### AWS Backup
- **Centralized Backup Plans:** นโยบายกลางสำหรับกำหนดเวลาทำสำรองข้อมูล ความถี่ และระยะเวลาจัดเก็บ (Retention) ทรัพยากร AWS ทั้งหมดในที่เดียว
- **Cross-Region Backup:** สำรองข้อมูลและส่งไปเก็บข้าม Region ปลายทางอัตโนมัติ เพื่อเตรียมรับมือกับภัยพิบัติระดับภูมิภาค
- **Cross-Account Backup:** คัดลอกข้อมูลสำรองไปยัง AWS Account อื่นใน AWS Organization ป้องกันการถูกโจมตีหรือลบข้อมูลจากบัญชีหลัก
- **Backup Vault Lock:** ป้องกันการลบข้อมูลสำรองหรือการย่อระยะเวลาจัดเก็บข้อมูลใน Backup Vault เพื่อให้สอดคล้องกับกฎระเบียบขององค์กร (WORM)
- **Backup Legal Holds:** Lock fileBackup ไว้ชั่วคราวเพื่อ Object ประสงค์ในการสืบสวนคดีหรือกฎหมาย โดยจะไม่ถูกลบแม้จะพ้นกำหนดเวลาเก็บแล้วก็ตาม

#### Hybrid Storage (AWS Storage Gateway)
- **S3 File Gateway:** แปลง ProtocolShared File (NFS/SMB) จากเครื่องเครื่องลูกข่าย On-premises ไปเก็บเป็น Object ใน Amazon S3 โดยมี Cache เก็บในเครื่อง
- **FSx File Gateway:** ขยายพื้นที่แชร์ File Windows (SMB) จาก On-premises ไปยัง FSx for Windows File Server บน AWS แบบมี Local Cache บน On-premises
- **Volume Gateway (Cached Mode):** เก็บข้อมูลหลักบน S3 และเก็บเฉพาะข้อมูลที่ใช้งานบ่อยไว้ใน Cache บนเครื่อง On-premises เพื่อประหยัดพื้นที่จัดเก็บในองค์กร
- **Volume Gateway (Stored Mode):** เก็บข้อมูลทั้งหมดบนเครื่อง On-premises และทำการสำรองข้อมูล (Asynchronous Snapshot) ไปเก็บไว้บน S3 เพื่อทำ DR
- **Tape Gateway (VTL):** เปลี่ยนระบบสำรองข้อมูลเทปฟิสิกส์เดิม (Physical Tape) ให้กลายเป็นเทปเสมือน (Virtual Tape) ไปเก็บไว้ใน S3 Glacier

#### Data Migration & Transfer
- **AWS Snowcone:** อุปกรณ์เคลื่อนย้ายข้อมูลและประมวลผลขนาดพกพา (8TB) ทนทานพิเศษ เหมาะสำหรับพื้นที่ห่างไกลหรือส่งผ่านเครือข่ายไม่ได้
- **Snowball Edge Storage Optimized:** อุปกรณ์เคลื่อนย้ายข้อมูลความจุสูง (80TB/100TB) สำหรับขนย้ายข้อมูลขนาดใหญ่ขึ้นคลาวด์แบบออฟไลน์อย่างปลอดภัย
- **Snowball Edge Compute Optimized:** อุปกรณ์ที่มีพลังประมวลผลสูง (VCPU/GPU) สำหรับรันงาน Edge Computing และเก็บข้อมูลในพื้นที่ไม่มี Internet
- **Snowmobile:** รถบรรทุกตู้คอนเทนเน็ตจัดเก็บข้อมูลขนาดใหญ่พิเศษ (สูงสุด 100PB) สำหรับย้ายข้อมูลระดับ Exabyte ขึ้น AWS อย่างรวดเร็วและปลอดภัย
- **AWS Transfer Family:** บริการโอนย้าย File ผ่าน Protocol ยอดนิยม (SFTP, FTPS, FTP, AS2) เข้า-ออก S3 หรือ EFS โดยไม่ต้องดูแล Server เอง
- **AWS DataSync:** บริการซิงค์โอนย้ายข้อมูลความเร็วสูงอัตโนมัติระหว่าง On-premises กับ AWS (S3, EFS, FSx) ผ่านระบบ Network ออนไลน์

---

### <a id="02-compute"></a>02. Compute & Scaling
#### Amazon EC2 & AMIs
- **Instance Families (T/M):** ตระกูล General Purpose เน้นความสมดุลของ CPU, Memory และ Network เหมาะกับ Web Server และระบบงานทั่วไป
- **Instance Families (C):** ตระกูล Compute Optimized เน้นพลังประมวลผล CPU สูงสุด เหมาะกับงานคำนวณซับซ้อน, Batch Processing, High Performance Web
- **Instance Families (R/X/Z):** ตระกูล Memory Optimized เน้นแรมขนาดใหญ่เป็นพิเศษ เหมาะสำหรับ In-memory Database (Redis) และระบบประมวลผลข้อมูลขนาดใหญ่
- **Instance Families (P/G):** ตระกูล Accelerated Compute มีการประมวลผลด้วย GPU เหมาะกับงานกราฟิก 3D, วิดีโอเรนเดอร์ และการฝึกสอน Generative AI / ML
- **Instance Families (I/D/H):** ตระกูล Storage Optimized มี IOPS สูงและ Throughput มหาศาล เหมาะกับงาน Database NoSQL และระบบ File แบบกระจายตัว
- **Public AMIs:** ImageOS สำเร็จรูปที่ AWS หรือคอมมูนิตี้เปิดให้คนทั่วไปใช้งานได้ฟรี
- **Private AMIs:** Image ระบบที่ผู้ใช้สร้างขึ้นเองภายในองค์กร เพื่อใช้ติดตั้ง Program และ Configure เฉพาะตัวสำหรับ Deploy เป็น EC2 ภายในบัญชี
- **AWS Marketplace AMIs:** แหล่งรวม Image ระบบจากพาร์ทเนอร์รายใหญ่ที่ผ่านการตรวจสอบความปลอดภัยและ Software ลิขสิทธิ์เชิงพาณิชย์
- **Community AMIs:** Template และระบบที่แชร์โดยนักพัฒนาทั่วไปในชุมชน ซึ่งผู้ใช้ควรตรวจสอบความปลอดภัยก่อนนำมาใช้งาน
- **EC2 User Data:** ScriptBoot ระบบ (Shell Script) ที่รันอัตโนมัติครั้งแรกเพียงครั้งเดียวเมื่อเปิดใช้งาน Instance เพื่อทำ Program เบื้องต้น
- **IMDSv1:** บริการเรียกข้อมูล Metadata ของเครื่อง EC2 ผ่าน Protocol Request-Response (HTTP GET) ทั่วไปโดยไม่ต้องมี Token ยืนยันตัวตน
- **IMDSv2:** บริการดึงข้อมูล Metadata ที่ปลอดภัยยิ่งขึ้น โดยต้องใช้ Token (HTTP PUT) ก่อนทำ GET ป้องกันช่องโหว่ SSRF (Server-Side Request Forgery)
- **EC2 Instance Store:** Storage ความเร็วสูงมากที่ติดอยู่กับเมน Board จริงของ EC2 ข้อมูลจะสูญหายทันทีเมื่อมีการ Stop/Terminate เครื่อง (Ephemeral Storage)
- **Cluster Placement Group:** วาง Instance ไว้ใน Rack เดียวกันใน AZ เดียวกัน เพื่อให้ได้ Network Latency ต่ำสุดและ Throughput สูงสุด (แนะนำสำหรับ HPC)
- **Spread Placement Group:** วาง Instance บน Rack ฟิสิกส์แยกขาดจากกัน (สูงสุด 7 เครื่องต่อ AZ) ป้องกันระบบล่มพร้อมกันจากฮาร์ดแวร์จุดเดียว
- **Partition Placement Group:** แบ่ง Instance ออกเป็นกลุ่มๆ (Partition) บน Rack แยกกัน เหมาะกับงานขนาดใหญ่ เช่น Hadoop, Cassandra, Kafka
- **EC2 Hibernate:** บันทึกสถานะหน่วยความจำ RAM ลงใน Root วอลุ่ม EBS แล้วปิดเครื่อง เมื่อเปิดใหม่จะกลับมาทำงานต่อจากจุดเดิมได้อย่างรวดเร็ว

#### EC2 Pricing Models
- **On-Demand Instances:** จ่ายค่าบริการตามชิ้นงานที่ใช้งานจริงรายวินาทีหรือนาที ไม่มีข้อผูกมัด เหมาะกับ Application ช่วงเริ่มต้นที่พยากรณ์ไม่ได้
- **Spot Instances:** ประหยัดสูงสุดถึง 90% โดยใช้ทรัพยากรว่างของ AWS แต่พร้อมโดนทวงคืนใน 2 นาที เหมาะกับงานที่ยอมรับการขาดตอนได้ (เช่น Batch Job)
- **Spot Fleet:** ชุดกลุ่ม Instance ที่รวม Spot และ On-demand เข้าด้วยกันอัตโนมัติ เพื่อคุมงบประมาณและคงความเสถียรของปริมาณงานคำนวณ
- **Standard Reserved Instances:** จองสิทธิ์ใช้งาน 1 หรือ 3 ปี แลกส่วนลดสูงสุด 72% ปรับเปลี่ยนคุณสมบัติ Instance ได้ในตระกูลเดียวกันเท่านั้น
- **Convertible Reserved Instances:** จองสิทธิ์ที่ให้ความยืดหยุ่น สามารถเปลี่ยนตระกูล ขนาด OS และประเภท Instance ได้ตลอดสัญญา
- **Savings Plans:** ข้อตกลงจ่ายเงินรายชั่วโมงคงที่ (1 หรือ 3 ปี) เพื่อรับส่วนลดสูงในการใช้ Compute (EC2, Fargate, Lambda) แบบยืดหยุ่นที่สุด
- **Dedicated Instances:** รันเครื่อง Instance บนฮาร์ดแวร์ระดับ Host เฉพาะของผู้ใช้รายนั้น โดยยังคงแชร์ฮาร์ดแวร์กับเครื่องอื่นๆ ภายในบัญชีเดียวกันได้
- **Dedicated Hosts:** เช่าฮาร์ดแวร์ Server กายภาพแบบผูกขาดทั้งเครื่อง ตอบโจทย์การใช้งานที่มีเงื่อนไขเรื่องลิขสิทธิ์ Software เดิม (BYOL) และกฎระเบียบบริษัท

#### ASG (Auto Scaling Group)
- **Target Tracking Scaling:** ปรับขนาดอัตโนมัติเพื่อรักษาค่าเฉลี่ยของ Metric ให้อยู่ในระดับที่กำหนด เช่น พยายามคุม CPU Utilization ไว้ที่ 50%
- **Step Scaling:** ปรับเพิ่ม/ลด Instance เป็นขั้นๆ ตามระดับความรุนแรงของเงื่อนไขการแจ้งเตือน เช่น เกิน 70% บวก 2, เกิน 85% บวกอีก 4
- **Simple Scaling:** ปรับจำนวน Instance ทันทีเพียงค่าเดียวตามเงื่อนไข CloudWatch Alarm แล้วต้องรอให้พ้นช่วงพักตัว (Cooldown Period)
- **Predictive Scaling:** ใช้ Machine Learning พยากรณ์ Traffic ในอนาคตล่วงหน้า เพื่อเตรียมขยายขีดความสามารถรองรับก่อน Traffic จริงจะมาถึง
- **Scheduled Scaling:** ปรับขนาด Instance ตามวันและเวลาที่กำหนดไว้ล่วงหน้า เหมาะกับ Traffic ที่มีรอบเวลาการใช้งานชัดเจน เช่น สิ้นเดือน
- **Launch Templates:** Template กำหนดคุณสมบัติ Instance สำหรับ ASG รันสิทธิ์ได้หลากหลายเวอร์ชัน และรองรับการทำ Instance Mixed Options
- **Launch Configurations:** รูปแบบเดิมสำหรับกำหนดคุณสมบัติการเปิด EC2 ให้กับ ASG (ไม่แนะนำให้ใช้ในระบบใหม่ เพราะจำกัดการแก้ไขและอัปเดตเวอร์ชัน)
- **Instance Refresh:** กระบวนการอัปเดต Instance ใน ASG แบบอัตโนมัติทีละส่วน (Rolling Update) เพื่อใช้คุณสมบัติหรือ AMI ชุดใหม่โดยไม่ทำให้ระบบล่ม
- **Warm Pools:** กลุ่ม Instance ที่ Boot ระบบและเตรียม App ไว้รอในสถานะ Stopped ช่วยย่นเวลาในการขยายเครื่องรับโหลดกะทันหัน
- **Lifecycle Hooks:** หยุดยั้งขั้นตอนการเปิดหรือปิด Instance ไว้ชั่วคราว เพื่อรัน Script เตรียมความพร้อมหรือเก็บ Lock การทำงานก่อนการทำงานเสร็จสิ้น

#### AWS Lambda & Serverless Compute
- **Lambda Layers:** จัดเก็บโค้ด Library สารบัญข้อมูล หรือการพึ่งพิงต่างๆ แยกออกจากตัว Program หลัก ช่วยลดขนาด ZIP File และแชร์สิทธิประโยชน์ข้าม Function
- **Provisioned Concurrency:** Boot สภาพแวดล้อมรอไว้ล่วงหน้าเพื่อลดปัญหา Cold Start สำหรับงานที่ต้องการการตอบสนองที่รวดเร็วระดับมิลลิวินาที
- **Function URLs:** สร้าง Link เชื่อมต่อ HTTP(S) ที่ใช้งานได้โดยตรงสู่ Function Lambda โดยไม่ต้องติดตั้ง API Gateway ข้างหน้าให้วุ่นวาย
- **SnapStart:** ตัวช่วยลดเวลารอบเริ่มต้นการทำงานสำหรับ Java Function โดยเก็บ Cache สแน็ปช็อตของหน่วยความจำของสภาพแวดล้อมรันไทม์ที่สร้างเสร็จแล้ว
- **Destination Policies:** ตั้งค่าปลายทางปลายการทำงานอัตโนมัติ ส่งต่อผลลัพธ์ (Success/Failure) ของ Asynchronous Event ไปยัง SQS, SNS, EventBridge, Lambda
- **Lambda Limits (15-min):** ขีดจำกัดเวลาทำงานสูงสุดของ Lambda ที่ 15 นาที (เกินกว่านี้ต้องย้ายไปใช้งาน ECS หรือ AWS Batch)
- **Lambda Limits (10GB /tmp):** พื้นที่เก็บ File ชั่วคราว (/tmp) สูงสุด 10GB สำหรับดาวน์โหลดและประมวลผลข้อมูลแบบ Local ในระหว่างรัน Function
- **Lambda inside VPC:** รัน Lambda ภายในเครือข่ายส่วนตัว (VPC) เพื่อเชื่อมต่อ Database ภายในได้อย่างปลอดภัยผ่านกลไก Hyperplane ENI

#### AWS Batch & Elastic Beanstalk
- **AWS Batch Jobs:** หน่วยประมวลผลงานในรูปแบบของ Container หรือ Script ที่ถูกส่งไปประมวลผลผ่าน Batch Queue
- **AWS Batch Job Definitions:** Template ที่กำหนดคุณลักษณะของงาน เช่น Docker Image, การจัดสรร CPU/Memory, ตัวแปรสภาพแวดล้อม
- **AWS Batch Job Queues:** Queue จัดการเรียงลำดับความสำคัญ of งานที่รอก่อนจะถูกส่งไปรันในสภาพแวดล้อมการคำนวณที่เหมาะสม
- **AWS Batch Compute Environments:** โครงสร้างระบบคำนวณที่ AWS บริหารจัดการให้ ซึ่งจะยืด/หดทรัพยากร (EC2/Fargate/Spot) ตามปริมาณ Queue งานที่เหลืออยู่
- **AWS Elastic Beanstalk:** Platform สำเร็จรูป (PaaS) สำหรับผู้พัฒนาอัปโหลดโค้ดขึ้นไปแล้วระบบจะจัดเตรียมทรัพยากร (EC2, ASG, ELB, DB) ให้พร้อมรันทันที
- **Beanstalk Deployments (All at Once):** ติดตั้งเวอร์ชันใหม่พร้อมกันทุก Instance ส่งผลให้เกิด Downtime ชั่วคราวระหว่างการรีสตาร์ทระบบ
- **Beanstalk Deployments (Rolling):** ทยอยอัปเดต Application ทีละกลุ่มเพื่อป้องกันการล่มของระบบ โดยกำลังการให้บริการรวมจะลดลงเล็กน้อยระหว่างทำงาน
- **Beanstalk Deployments (Rolling with Additional Batch):** อัปเดตทีละกลุ่มโดยเปิด Instance ใหม่มารองรับชั่วคราว เพื่อคงกำลังการให้บริการรวมไว้ 100%
- **Beanstalk Deployments (Immutable):** DeployApp เวอร์ชันใหม่ลงในเครื่องกลุ่มใหม่ทั้งหมด และสลับ Traffic ทีเดียวเพื่อป้องกันปัญหาและย้อนกลับง่ายสุด
- **Beanstalk Deployments (Blue/Green):** สร้างสภาพแวดล้อมใหม่ขึ้นมาขนานกันแล้วเปลี่ยน TrafficTraffic ผ่าน DNS Route 53 เหมาะสำหรับงานอัปเดต Database ใหญ่

---

### <a id="03-networking"></a>03. Networking & Content Delivery
#### VPC (Virtual Private Cloud)
- **Public Subnet:** Subnet ที่มีตารางเส้นทาง (Route Table) เชื่อมโยงตรงไปที่ Internet Gateway (IGW) เพื่อให้เครื่องภายในติดต่อและเข้าถึง Internet ได้
- **Private Subnet:** Subnet ที่ไม่มีเส้นทางตรงไป Internet แต่มักเชื่อมผ่าน NAT Gateway ใน Public Subnet เพื่อให้ออกเน็ตได้แต่ภายนอกห้ามวิ่งเข้ามา
- **Isolated Subnet:** Subnet เครือข่ายปิดสนิท ไม่มีทั้ง IGW และ NAT Gateway เหมาะสำหรับ Database ที่เข้าถึงได้เฉพาะจาก Network ภายในเท่านั้น
- **Internet Gateway (IGW):** ประตูเชื่อมระหว่างเครือข่าย VPC กับ Internet สาธารณะ รองรับการแปลงที่อยู่เครือข่ายแบบหนึ่งต่อหนึ่ง (1-to-1 NAT)
- **NAT Gateway (Managed):** บริการบริหารจัดการอัตโนมัติที่ช่วยให้ Instance ใน Private Subnet เชื่อมออกสู่ Internet ภายนอกได้โดยยังคงซ่อนตัวตนไว้
- **NAT Gateway HA:** การออกแบบเพื่อป้องกันความล้มเหลวโดยการเปิด NAT Gateway ไว้ในทุก AZ แยกจากกัน เพื่อไม่ให้ AZ ใดพังแล้วดึงบริการทั้งหมดล่มตาม
- **NAT Instance:** เครื่อง EC2 ที่ลง Configure ทำหน้าที่เป็นตัวผ่านเน็ต (ต้องปิด Source/Destination Check) ปัจจุบันไม่แนะนำเพราะต้องจัดการและปรับปรุงเอง
- **Bastion Hosts:** เครื่อง EC2 ใน Public Subnet ที่ทำหน้าที่เป็นสะพานเชื่อมต่อผ่าน SSH/RDP เข้าไปจัดการระบบใน Private Subnet อย่างปลอดภัย
- **Egress-Only Internet Gateway:** บริการช่วยระบาย Traffic สำหรับ IPv6 เท่านั้น ช่วยให้เครือข่ายภายในติดต่อกับภายนอกได้โดยไม่มี Traffic สุ่มข้ามกลับเข้ามา
- **Customer Gateway (CGW):** อุปกรณ์ฝั่ง On-premises ของลูกค้าหรือ SoftwareApplication ที่ใช้ในการสร้างการเชื่อมต่อ Site-to-Site VPN ร่วมกับ AWS
- **Virtual Private Gateway (VGW):** ประตูเชื่อมต่อฝั่ง AWS สำหรับการเชื่อมต่อ VPN หรือ Direct Connect เข้ากับเครือข่าย VPC
- **Security Groups:** ไฟร์วอลล์ระดับ Instance ที่ทำงานแบบ Stateful (ยอมให้ออกไปก็ยอมให้ตอบกลับอัตโนมัติ) และมีเฉพาะกฎ "อนุญาต" เท่านั้น
- **NACLs:** ไฟร์วอลล์ระดับ Subnet ที่ทำงานแบบ Stateless (ต้องกำหนดทั้ง Traffic ขาเข้าและขาออกแยกกัน) รองรับทั้งการอนุญาตและปฏิเสธ (Allow/Deny)
- **VPC Peering:** การเชื่อมเครือข่ายระหว่างสอง VPC เข้าหากันโดยตรง วิ่งผ่าน Network ของ AWS ปลอดภัย ไม่รองรับการเชื่อมต่อผ่านตัวกลาง (Non-Transitive)
- **AWS Transit Gateway:** อุปกรณ์ Router ส่วนกลางที่เชื่อมต่อ VPC หลายร้อยแห่งและ NetworkOn-premises เข้าด้วยกันแบบ Hub-and-Spoke Mesh เพื่อคุมศูนย์กลาง
- **AWS Direct Connect (DX):** การเชื่อมต่อสายสัญญาณกายภาพเฉพาะจาก On-premises ตรงเข้า AWS เพื่อให้ได้ Network ที่เสถียร Bandwidth สูง และไม่ใช้ Internet
- **Direct Connect Gateway:** ประตูเชื่อมต่อของ Direct Connect ช่วยให้สายแลนเส้นเดียวสามารถเชื่อมต่อไปยัง VPC ต่างๆ ข้ามหลายภูมิภาคและบัญชีได้
- **Site-to-Site VPN:** การเชื่อมต่อ VPN ความปลอดภัยสูงผ่าน Protocol IPsec บน Internet สาธารณะ สร้างระบบการเชื่อมต่อแบบด่วนราคาประหยัด
- **Gateway Endpoints:** จุดเชื่อมต่อ VPC ภายในเพื่อเข้าใช้บริการ Amazon S3 และ DynamoDB โดยตรงโดยไม่มีค่า Traffic และไม่ต้องใช้ NAT Gateway/IGW
- **Interface Endpoints:** จุดเชื่อมต่อผ่าน ENI (Private IP) ภายใน VPC โดยใช้เทคโนโลยี AWS PrivateLink มีค่าบริการตามจำนวน Traffic ข้อมูลที่ไหลผ่าน
- **VPC Flow Logs:** บริการจับภาพและบันทึกข้อมูล Traffic IP ขาเข้าและขาออกของ Network ภายใน VPC ส่งไปเก็บบน CloudWatch Logs หรือ S3 เพื่อความปลอดภัย
- **Traffic Mirroring:** ทำการสำเนา Traffic เครือข่ายระดับอินเทอร์เฟซ (ENI) ส่งต่อไปยังเครื่องมือตรวจสอบภายนอกเพื่อทำ Deep Packet Inspection (DPI)
- **VPC Reachability Analyzer:** เครื่องมือช่วยวิเคราะห์ปัญหาเส้นทาง Network ว่าติดขัดจากไฟร์วอลล์ ตัวกรอง หรือการตั้งค่าจุดใดโดยจำลองการส่งข้อมูล
- **AWS Network Firewall:** ไฟร์วอลล์ระดับรัฐวิสาหกิจที่สามารถตรวจสอบและป้องกันภัยคุกคามในระดับ Layer 3 ถึง Layer 7 สำหรับทุก Subnet ภายใน VPC
- **Amazon VPC Lattice:** บริการเครือข่ายระดับ Application ที่ช่วยในการเชื่อมต่อ รักษาความปลอดภัย และติดตามบริการย่อย (Microservices) ได้อย่างง่ายดาย

#### ELB (Elastic Load Balancing)
- **Application Load Balancer (ALB):** Load Balancer ระดับ Layer 7 (HTTP/HTTPS) ทำงานบนข้อมูลเชิงโครงสร้าง เช่น คัดกรองผ่าน Path, Hostname, Query-string
- **Network Load Balancer (NLB):** Load Balancer ระดับ Layer 4 (TCP/UDP) รองรับ Traffic ขนาดใหญ่ระดับล้านคำขอต่อวินาที มีจุดเด่นเรื่อง Static IP และความเร็วสูงสุด
- **Gateway Load Balancer (GWLB):** Load Balancer ระดับ Layer 3 ออกแบบมาเพื่อรับและกระจาย Traffic ไปยังระบบตรวจสอบภัยคุกคามหรือ Third-party Firewalls
- **Target Groups:** กลุ่มปลายทางที่เป็นผู้รับ Traffic จาก ELB เช่น EC2 Instances, ECS Tasks, Lambda หรือ IP addresses ต่างๆ
- **Health Checks:** การส่งสัญญาณตรวจสอบสถานะของ Application ปลายทางอย่างเป็นระยะ หากเครื่องใดพัง ELB จะหยุดส่ง Traffic ไปหาทันที
- **Cross-Zone Load Balancing:** ตั้งค่าให้ Load Balancer กระจาย Traffic ไปยังปลายทางในทุก AZ เท่าๆ กัน โดยไม่คำนึงถึงจำนวนเครื่องในแต่ละโซน
- **Sticky Sessions:** บังคับให้ Client เดิมถูกส่งไปประมวลผลที่ Server หลังบ้านตัวเดิมตลอดระยะเวลา Session โดยใช้งาน Cookie
- **SSL Termination:** ให้เครื่อง Load Balancer ถอดรหัส SSL/TLS แทน Server หลังบ้าน เพื่อช่วยแบ่งเบาภาระการประมวลผลและลดภาระจัดการใบรับรอง
- **Connection Draining (Deregistration Delay):** การหน่วงเวลาเพื่อรอให้ Traffic เดิมทำงานเสร็จก่อนที่จะถอนตัวเครื่องออกจากระบบ Load Balancer อย่างปลอดภัย

#### Route 53
- **CNAME:** เรค Core ดชี้ชื่อโดเมนเดิมไปยังอีกชื่อโดเมนอื่น (Alias) แต่ไม่สามารถนำมาชี้ที่ระดับ Root Domain (เช่น example.com) ได้
- **Alias Record:** เรค Core ดพิเศษเฉพาะ Route 53 สำหรับชี้ Root Domain ไปยังทรัพยากรของ AWS (เช่น ALB, CloudFront) ได้ฟรีและอัปเดต IP อัตโนมัติ
- **Simple Routing Policy:** นโยบายชี้โดเมนไปยังปลายทางเดียวหรือสุ่มที่อยู่ IP กลับไปกลุ่มหนึ่ง โดยไม่มีการตรวจสอบสุขภาพหลังบ้าน (Health Check)
- **Weighted Routing:** นโยบายแบ่งสัดส่วนการส่ง Traffic เป็นเปอร์เซ็นต์ตามค่าน้ำหนักที่กำหนด เหมาะกับการทดสอบระบบแบบ Canary Deployments
- **Latency-based Routing:** นโยบายที่เลือกส่งผู้ใช้ไปยัง AWS Region ที่ให้ค่าการเชื่อมต่อเครือข่ายและความหน่วง Network ต่ำที่สุดสำหรับผู้ใช้คนนั้น
- **Failover Routing:** ตั้งค่านโยบายแบบ Active-Passive เพื่อสลับเส้นทางโดเมนไปยังระบบสำรอง (Secondary) ทันทีหากระบบหลัก (Primary) ล่มจากการตรวจสอบ
- **Geolocation Routing:** นโยบายจัดเส้นทางการเข้าถึงข้อมูลของโดเมนโดยอิงจากตำแหน่งทางภูมิศาสตร์หรือประเทศต้นทางของผู้ใช้งาน
- **Geoproximity Routing:** กำหนด Traffic ตามระยะทางฟิสิกส์ระหว่างผู้ใช้กับทรัพยากร โดยสามารถปรับเปลี่ยนขอบเขตพื้นที่บริการได้ผ่านค่า Bias
- **IP-based Routing:** กำหนดเส้นทาง Network อิงตามช่วงที่อยู่ IP (CIDR block) ของผู้ร้องขอ เช่น ส่ง Traffic องค์กรส่วนตัวไปหา Server เฉพาะ
- **Multi-value Answer:** ส่งคืนที่อยู่ IP ของทรัพยากรที่มีสุขภาพดีสูงสุด 8 เรค Core ดกลับไปให้ App ฝั่งผู้ใช้สุ่มสลับใช้ทำระบบกระจายโหลดราคาถูก
- **Route 53 Resolver:** บริการแปลและเชื่อมโยงระบบ DNS ข้ามกันระหว่างเครื่องภายใน VPC ของ AWS และระบบ DNS Server ในเครือข่าย On-premises
- **Route 53 Application Recovery Controller (ARC):** บริการช่วยตรวจสอบความพร้อมของระบบและการทำสำรองข้อมูลข้ามภูมิภาคเพื่อยกระดับความน่าเชื่อถือ

#### CloudFront & AWS Global Accelerator
- **Cache Behaviors:** การตั้งค่ากฎการจัดการข้อมูลของ CloudFront เช่น การระบุระยะเวลาเก็บ Cache (TTL), การกรองฟิลด์, หรือการสลับใช้ Protocol
- **Cache Invalidation:** สั่งเคลียร์และทำลายข้อมูลที่ Cache ไว้ใน Edge Location ทันที เพื่อบังคับให้ CloudFront ดึง File เวอร์ชันล่าสุดจาก Server ต้นทาง
- **Origin Groups:** รวม Server ต้นทาง (Origin) 2 ตัวเข้าเป็นกลุ่มเพื่อทำ Failover อัตโนมัติเมื่อตัวหลักไม่พร้อมให้บริการเพื่อป้องกันระบบล่ม
- **Origin Shield:** เลเยอร์ Cache พิเศษส่วนกลางคั่นระหว่าง Edge กับ Origin ช่วยลดโหลดการเรียกค้นข้อมูลซ้ำๆ จากเครื่องต้นทางหลักได้สูงสุด
- **CloudFront Functions:** Script JavaScript ขนาดเล็กพิเศษสำหรับจัดการส่วนหัว (Headers) หรือเปลี่ยนเส้นทาง ทำงานที่ Edge ด้วยความเร็วสูงและราคาประหยัด
- **Lambda@Edge:** Function Node.js/Python ที่สามารถประมวลผลงานซับซ้อน เช่น ต่อ Database ภายนอก ดันข้อมูล โดยรันที่เครือข่ายขอบทางภูมิศาสตร์
- **AWS Global Accelerator:** บริการลดปัญหา Latency บนเครือข่ายโดยใช้ที่อยู่ IP แบบคงที่ (Anycast IP) และนำ Traffic วิ่งบนสายเคเบิลความเร็วสูงของ AWS

---

### <a id="04-databases"></a>04. Databases
#### Amazon RDS & Aurora
- **RDS Multi-AZ Deployment:** การสำรองข้อมูลแบบซิงโครนัส (Synchronous) ข้าม AZ เพื่อความทนทานและการเปลี่ยนเครื่องสำรองขึ้นมาทำงานแทนทันทีเมื่อเครื่องหลักล่ม
- **RDS Multi-AZ DB Cluster:** โครงสร้าง RDS ยุคใหม่ มี 1 Primary และ 2 Readable Standby Instances ช่วยเพิ่มความเร็วในการ Failover และรองรับการอ่าน
- **RDS Read Replicas:** การทำสำเนาข้อมูลแบบ Asynchronous เพื่อกระจายโหลดการอ่าน (Read Scaling) ข้ามหลาย AZ หรือข้ามภูมิภาค (Cross-Region)
- **RDS Custom:** บริการช่วยติดตั้ง Database RDS ที่เปิดให้ผู้เข้าใช้มีสิทธิ์ระดับ Root/OS เพื่อปรับแต่ง Software เช่น Oracle หรือ SQL Server
- **RDS Proxy:** บริการ Server คนกลางสำหรับทำ Connection Pooling ช่วยจัดการและรักษาขีดจำกัดการเชื่อมต่อ Database สำหรับ App แบบ Serverless
- **Aurora MySQL-Compatible:** Database แบบ Relational ประสิทธิภาพสูงเทียบชั้นระดับรัฐวิสาหกิจ เร็วกว่า MySQL ทั่วไปสูงสุดถึง 5 เท่า
- **Aurora PostgreSQL-Compatible:** Database แบบ Relational เร็วกว่า PostgreSQL มาตรฐานสูงสุด 3 เท่า ทำงานร่วมกับ Application เดิมได้ทันที
- **Aurora Serverless v2:** บริการปรับเพิ่ม/ลดปริมาณทรัพยากรคำนวณ (ACUs) อัตโนมัติตามความต้องการของ Application ภายในเวลาเสี้ยววินาที
- **Aurora Global Database:** Database แบบกระจายตัวข้ามโลก ซิงค์ข้อมูลข้าม Region Asynchronous ในเวลาไม่เกิน 1 วินาที เหมาะสำหรับทำแผนภัยพิบัติ DR
- **Aurora Write Forwarding:** ความสามารถของ Aurora ในการส่งคำสั่งเขียนข้อมูลจาก Read Replica ในภูมิภาคอื่นกลับมายังเครื่อง Primary หลักโดยอัตโนมัติ

#### Amazon DynamoDB & Purpose-Built Databases
- **Serverless NoSQL Database:** Database NoSQL แบบไม่ต้องจัดการ Server ขยายตัวได้ไม่จำกัดและตอบสนองการทำงานเร็วคงที่ระดับมิลลิวินาที
- **DynamoDB Accelerator (DAX):** ระบบหน่วยความจำ Cache อินเมมโมรีส่วนหน้าสำหรับ DynamoDB ช่วยเร่งความเร็วในการอ่านข้อมูลระดับไมโครวินาที (10x)
- **DynamoDB Global Tables:** Database แบบ Multi-Region, Active-Active ซิงค์ข้อมูลสองทางอัตโนมัติ เขียนและอ่านข้อมูลได้รวดเร็วใกล้ตัวผู้ใช้ทั่วโลก
- **DynamoDB Streams:** บันทึกประวัติการเปลี่ยนแปลงข้อมูลในตารางแถวต่อแถวแบบเรียลไทม์ ใช้เพื่อกระตุ้น Function Lambda ทำงานต่อเนื่อง (เช่น ส่งอีเมล)
- **DynamoDB Time to Live (TTL):** ตั้งเวลาทำลายข้อมูลโดยลบแถวที่หมดอายุการใช้งานออกจากตารางแบบอัตโนมัติโดยไม่มีค่าธรรมเนียมประมวลผล
- **DynamoDB PITR:** บริการช่วยสำรองข้อมูลต่อเนื่องช่วยให้สามารถกู้คืนย้อนเวลากลับไปจุดใดก็ได้ในรอบ 35 วันล่าสุดเพื่อป้องกันระบบพัง
- **DynamoDB Import/Export to S3:** นำเข้าหรือส่งออกข้อมูลตาราง DynamoDB ไปเก็บบน S3 ในรูปแบบ CSV, JSON หรือ Parquet โดยไม่แย่งปริมาณงานคำนวณหลัก
- **LSI (Local Secondary Index):** ดัชนีช่วยค้นหาข้อมูลเพิ่มเติม ใช้ Partition Key เดิมเหมือนตารางหลัก แต่เปลี่ยน Sort Key (สร้างได้เฉพาะตอนสร้างตาราง)
- **GSI (Global Secondary Index):** ดัชนีช่วยค้นหาแบบยืดหยุ่น กำหนดทั้ง Partition Key และ Sort Key ใหม่ได้อิสระ (สร้างหรือลบเมื่อไหร่ก็ได้หลังสร้างตาราง)
- **ElastiCache for Redis:** ระบบ Cache อินเมมโมรีความเร็วสูง รองรับโครงสร้างข้อมูลซับซ้อน ทำ Multi-AZ และทำข้อมูลคงอยู่ได้ (Data Persistence)
- **ElastiCache for Memcached:** ระบบ Cache อินเมมโมรีแบบคีย์-แวลูธรรมดา เน้นการขยายขนาดในแนวนอนเพื่อรองรับ TrafficApp ทั่วไปที่ไม่ซับซ้อน
- **Amazon MemoryDB:** Database อินเมมโมรีที่ทำงานร่วมกับ Protocol Redis ได้อย่างสมบูรณ์แบบ แต่ออกแบบมาให้บันทึกข้อมูลถาวรเสถียรสูงระดับ Database หลัก
- **Amazon DocumentDB:** บริการจัดการ Database ประเภทเอกสาร (Document DB) ที่รองรับคำสั่งและเครื่องมือของ MongoDB ได้อย่างสมบูรณ์
- **Amazon Keyspaces:** บริการจัดการ Database NoSQL แบบคอลัมน์กว้างที่ทำงานร่วมกับชุดคำสั่ง Apache Cassandra ได้โดยตรงไม่ต้องดูแล Infra
- **Amazon Neptune:** Database กราฟ (Graph Database) ประสิทธิภาพสูงสำหรับการจัดเก็บและค้นหาความสัมพันธ์ของข้อมูลที่เชื่อมโยงกัน เช่น เครือข่ายสังคมออนไลน์
- **Amazon QLDB:** Database บัญชีแยกประเภทแบบรวมศูนย์ (Ledger) ที่เก็บประวัติการเปลี่ยนแปลงข้อมูลถาวร แก้ไขไม่ได้ และตรวจสอบย้อนหลังได้ด้วยวิทยาการเข้ารหัส
- **Amazon Timestream:** Database ประสิทธิภาพสูงสำหรับการจัดเก็บและวิเคราะห์ข้อมูลที่เปลี่ยนไปตามเวลา (Time-series) เช่น ข้อมูล IoT หรือ Log จาก App

---

### <a id="05-security"></a>05. Security, Identity & Compliance
#### IAM (Identity and Access Management)
- **IAM Users:** บัญชีระบุตัวตนบุคคลหรือผู้เข้าใช้ระบบ มีข้อมูลประจำตัวยืนยันและสิทธิ์เฉพาะบุคคลในการจัดการทรัพยากร
- **IAM Groups:** กลุ่มที่รวบรวมสิทธิ์ผู้เข้าใช้งาน เพื่อให้ง่ายต่อการบริหารจัดการนโยบายความปลอดภัยพร้อมกันหลายคน
- **IAM Roles:** ตัวตนเสมือนที่ไม่มีรหัสผ่านผ่านการสวมสิทธิ์ชั่วคราว มอบให้เครื่องบริการ (EC2) หรือผู้ใช้บัญชีอื่นเข้ามาทำงานตามขอบเขต
- **IAM Policies (JSON):** เอกสารนิยามสิทธิ์ความปลอดภัยในรูปแบบ File JSON ระบุว่าใคร (Principal) ทำอะไร (Action) บนทรัพยากรไหน (Resource) ได้บ้าง
- **IAM Least Privilege:** หลักการบริหารจัดการสิทธิ์ขั้นต่ำสุดที่จำเป็นเพื่อให้บุคคลหรือระบบทำงานนั้นๆ ได้ เพื่อจำกัดช่องโหว่ความปลอดภัย
- **IAM Policy Evaluation (Explicit Deny):** หากมีกฎ Deny ใดๆ ระบุไว้ในนโยบาย สิทธิ์นั้นจะถูกบ Lock ทันทีโดยไม่มีข้อยกเว้นและข้ามทุกกฎ Allow
- **IAM Policy Evaluation (Explicit Allow):** สิทธิ์การทำงานจะได้รับอนุญาตก็ต่อเมื่อมีกฎเขียนยอมรับไว้อย่างชัดเจนเท่านั้น
- **IAM Policy Evaluation (Implicit Deny):** หากไม่มีกฎเขียนอนุญาตเอาไว้ ระบบจะทำการบ Lock สิทธิ์การทำงานนั้นโดยอัตโนมัติเป็นค่าเริ่มต้น
- **IAM Permissions Boundaries:** นโยบายขั้นสูงสุดที่ใช้จำกัดขอบเขตของสิทธิ์ที่ผู้ใช้หรือบทบาท (Role) นั้นจะสามารถมีได้ ป้องกันการยกระดับสิทธิ์ตัวเอง
- **IAM Identity Center:** บริการส่วนกลางสำหรับจัดการสิทธิ์การลงชื่อเข้าใช้งานระบบ (SSO) ไปยังบัญชีและ Application ต่างๆ ในเครือข่ายองค์กร
- **AWS Control Tower:** บริการช่วยจัดตั้งและบริหารจัดการระบบ Multi-account (Landing Zone) ให้เป็นไปตามมาตรฐานความปลอดภัยที่สม่ำเสมอ

#### AWS KMS & Encryption Keys
- **AWS Managed Keys:** กุญแจเข้ารหัสลับที่สร้างและดูแลโดยตัวบริการของ AWS เองโดยอัตโนมัติ (ไม่คิดค่าบริการรายเดือนสำหรับกุญแจเหล่านี้)
- **Customer Managed Keys (CMK):** กุญแจเข้ารหัสลับที่ผู้ใช้งานสร้างและควบคุมเอง ปรับเปลี่ยนนโยบายการเข้าใช้ หมุนรอบกุญแจ และตั้งเวลาลบได้
- **KMS Symmetric Keys:** กุญแจเข้ารหัสที่มีรหัสชุดเดียวกันทั้งฝั่งเข้ารหัสและถอดรหัส ข้อมูลมีขนาดใหญ่ใช้ดี และทำงานได้รวดเร็ว
- **KMS Asymmetric Keys:** กุญแจเข้ารหัสที่มีพับบลิกคีย์สำหรับเข้ารหัส และไพรเวทคีย์สำหรับถอดรหัสหรือใช้เซ็นลายเซ็นดิจิทัล
- **KMS Multi-Region Keys:** กุญแจเข้ารหัสแบบแบ่งปันที่ใช้งานและแชร์คีย์เดียวกันในหลายภูมิภาคโดยไม่ต้องเข้ารหัสข้อมูลซ้ำเมื่อย้ายภูมิภาค
- **Custom Key Store:** ตัวเลือกการต่อเชื่อม KMS เข้ากับตู้เก็บกุญแจฮาร์ดแวร์พิเศษ CloudHSM ของตัวเองเพื่อความปลอดภัยขั้นสูงสุดระดับองค์กร

#### Data Protection & Firewalls
- **CloudHSM:** บริการฮาร์ดแวร์เก็บกุญแจเข้ารหัสลับแบบเช่าเดี่ยว (Single-tenant) ได้รับการรับรอง FIPS 140-2 Level 3 สำหรับงานเก็บรักษาสิทธิ์ขั้นสูง
- **AWS Secrets Manager:** บริการเก็บและหมุนเวียน (Rotation) รหัสผ่านและ API key อัตโนมัติร่วมกับ Lambda ปลอดภัยกว่า Parameter Store ทั่วไป
- **AWS WAF:** ไฟร์วอลล์ระดับ Application (Layer 7) ป้องกันการโจมตีเว็บยอดนิยม เช่น SQL Injection, Cross-Site Scripting (XSS) หรือบ Lock ประเทศ IP
- **AWS Shield Standard:** ระบบป้องกันภัย DDoS พื้นฐานระดับ Layer 3/4 ที่เปิดใช้งานให้ฟรีโดยอัตโนมัติกับทุกทรัพยากรบน AWS
- **AWS Shield Advanced:** บริการป้องกันภัย DDoS ระดับพรีเมียม มีทีม DRT ช่วยเหลือ 24 ชั่วโมง และชดเชยค่าบริการส่วนเกินที่เกิดจากการโจมตี
- **AWS Shield Best Practices:** สถาปัตยกรรมป้องกัน DDoS แนะนำ (Route 53 + CloudFront + ALB + Auto Scaling + Shield Advanced)
- **AWS Firewall Manager:** บริการกลางที่ช่วยในการบังคับใช้นโยบายความปลอดภัยและกฎไฟร์วอลล์ (WAF, Shield, SG) ข้ามทุกบัญชีใน AWS Organization
- **AWS GuardDuty:** บริการตรวจจับความผิดปกติและภัยคุกคามในระบบโดยใช้ Machine Learning วิเคราะห์ข้อมูล Log (VPC Flow, CloudTrail, DNS)
- **AWS Inspector:** บริการตรวจสอบหาช่องโหว่ความปลอดภัย (Vulnerability) บน OS EC2, Image ใน ECR และ Function Lambda อัตโนมัติ
- **Amazon Macie:** บริการวิเคราะห์หาข้อมูลส่วนบุคคลที่สำคัญ (PII) หรือข้อมูลทางการเงินที่หลุดไปเก็บใน S3 โดยใช้ AI สแกนตรวจหา
- **AWS Security Hub:** Board สรุปผลความปลอดภัยกลางที่ช่วยรวบรวม สรุปข้อมูล และประเมินมาตรฐานความปลอดภัยของทรัพยากรทั้งหมดใน AWS
- **Amazon Detective:** บริการวิเคราะห์และทำกราฟความเชื่อมโยงของ Log data เพื่อค้นหาต้นตอและรูปแบบการโจมตีระบบรักษาความปลอดภัยอย่างง่ายดาย
- **AWS Directory Service:** บริการจัดตั้งและเชื่อมต่อระบบจัดการตัวตนองค์กรแบบ Microsoft Active Directory (AD) บนโครงสร้างพื้นฐาน AWS
- **AWS Audit Manager:** ระบบช่วยรวบรวมหลักฐานและข้อมูลการดำเนินงาน เพื่อช่วยให้การทำรายงานประเมินความสอดคล้องกับมาตรฐานกฎระเบียบต่างๆ ทำได้ง่าย
- **AWS Artifact:** คลัง Port ัลดาวน์โหลดเอกสารรับรองมาตรฐานความปลอดภัยของ AWS (เช่น SOC, ISO, PCI DSS) เพื่อนำไปส่งยื่นให้ผู้ตรวจสอบภายนอก
- **Cognito User Pools:** บริการระบบจัดเก็บ จัดการโปร File ยืนยันตัวตน และลงชื่อเข้าใช้งานของ Application (Authentication) รองรับ Social Login
- **Cognito Identity Pools:** บริการออกสิทธิ์เครดิตชั่วคราวของ AWS (Authorization) ให้กับผู้ใช้งานที่ผ่านด่านตรวจสอบเข้ามาใช้บริการของ AWS โดยตรง

---

### <a id="06-analytics"></a>06. Analytics & Data Integration
#### Data Analytics & Warehousing
- **Amazon Redshift:** Data Warehouse วิเคราะห์เชิงลึก (OLAP) เก็บข้อมูลแบบ Columnar Storage รองรับข้อมูลขนาดระดับ Petabyte เหมาะสำหรับงาน Business Intelligence
- **Redshift Serverless:** ทำงานวิเคราะห์และ Queue รี Database โดยไม่ต้องบริหารจัดการ ClusterServer จ่ายเงินตามปริมาณทรัพยากรคำนวณที่ใช้รันงานจริง
- **Redshift Spectrum:** ความสามารถในการยิง Queue รี SQL ไปยังข้อมูลดิบที่เก็บในระบบ S3 data lake ได้โดยตรงโดยไม่ต้องนำข้อมูลเข้าระบบจัดเก็บของ Redshift
- **Redshift Concurrency Scaling:** ระบบปรับขยายกำลังการประมวลผลเพิ่มเครื่องอัตโนมัติชั่วคราว เพื่อรองรับผู้ใช้และ QueueQueue รีวิเคราะห์ที่อัดแน่นพร้อมกัน
- **Redshift RA3 Nodes:** สถาปัตยกรรมแยกการคำนวณและ Storage ออกจากกัน ช่วยลดค่าใช้จ่ายโดยเก็บข้อมูลเย็นไว้ใน S3 อัตโนมัติ
- **Redshift Data Sharing:** ความสามารถในการแชร์ตารางข้อมูลภายใน Redshift ข้ามกลุ่ม Cluster หรือบัญชีโดยไม่มีการคัดลอก File ข้อมูลซ้ำซ้อน
- **Amazon Athena:** บริการ Queue รีประมวลผลข้อมูลใน S3 โดยใช้ภาษา SQL แบบ Serverless ปลายทางจ่ายเงินตามปริมาณข้อมูลที่สแกนอ่าน File
- **Athena Federated Query:** ความสามารถในการเขียน SQL บน Athena เพื่อไป Queue รีข้อมูลจาก Database อื่นนอก S3 เช่น RDS, DynamoDB, DocumentDB
- **Athena Workgroups:** ฟีเจอร์แบ่งกลุ่มการใช้งานสำหรับจัดงบประมาณ จำกัดปริมาณการสแกน และควบคุมความปลอดภัยแยกส่วนกันในทีม
- **Athena Notebooks:** ใช้สมุดบันทึกแบบ Interactive (Jupyter notebook) ในการรันชุดประมวลผลและเขียน Program คู่กับระบบ Queue รีของ Athena
- **AWS Glue:** บริการเตรียมและย้ายข้อมูลประเภท ETL แบบ Serverless ที่สามารถแปลง ค้นหา และรวมข้อมูลจากแหล่งต่างๆ เข้าด้วยกัน
- **Glue Data Catalog:** คลังเก็บ Metadata ประวัติโครงสร้างตารางข้อมูลกลาง เพื่อให้เครื่องมืออย่าง Athena, EMR หรือ Redshift ทราบชนิดข้อมูล
- **Glue Crawlers:** ตัวนำสแกนค้นหาแหล่งจัดเก็บ File ข้อมูลเพื่อจำแนกประเภท หาโครงสร้าง (Schema) แล้วนำกลับมาเขียนลงใน Data Catalog
- **Glue Jobs (ETL Spark):** งานประมวลผลสำหรับแปลงรูปแบบ โครงสร้าง และโยกย้าย File ข้อมูลโดยอัตโนมัติบนระบบ Apache Spark
- **Glue DataBrew:** เครื่องมือสำหรับการทำความสะอาดและจัดเตรียมข้อมูล (Data Preparation) ผ่านหน้าต่าง UI แบบไม่ต้องเขียนโค้ด
- **Glue Studio:** เครื่องมือสร้างแบบจราจรเส้นทางข้อมูล ETL (Visual Workflow Editor) ด้วยวิธีการลากวางโดยไม่ต้องเขียนชุดคำสั่งเอง
- **AWS Lake Formation:** บริการกลางที่ช่วยในการจัดตั้ง ดูแล และควบคุมสิทธิ์ความปลอดภัยระดับคอลัมน์/แถวให้กับระบบ Data Lake (Data Lake)
- **Amazon EMR (Elastic MapReduce):** Cluster ระบบประมวลผลขนาดใหญ่ที่รัน Application Big Data เช่น Apache Spark, Hadoop, Hive ได้ง่ายและคุ้มค่า
- **Amazon QuickSight:** บริการแสดงผลข้อมูลในรูปแบบแดช Board (Business Intelligence) ที่สวยงาม รวดเร็ว และรองรับระบบถามตอบภาษาธรรมชาติ Q

#### Streaming & Pipelines
- **Kinesis Data Streams:** ระบบรับส่งข้อมูลสตรีมมิ่งความเร็วสูงระดับเรียลไทม์ (Milliseconds) จัดการ Queue ผ่านการแบ่งช่องสัญญาณ (Shards)
- **Kinesis Data Firehose:** บริการส่งมอบข้อมูลสตรีมมิ่ง (Ingestion) แบบ Serverless โหลดข้อมูลลง S3, Redshift, OpenSearch ได้โดยไม่ต้องคุมกำลังบริการ
- **Kinesis Data Analytics:** บริการเขียนภาษา SQL หรือโค้ด Apache Flink เพื่อสืบค้นข้อมูลและวิเคราะห์สตรีมมิ่งสดขณะกำลังไหลผ่านระบบ
- **Kinesis Video Streams:** บริการดูดสตรีม บันทึก และประมวลผล File วิดีโอจากกล้องหรืออุปกรณ์เพื่อส่งต่อไปยังงานวิเคราะห์ระบบภาพ
- **Amazon MSK:** บริการจัดการ Platform รับส่งสตรีมมิ่งยักษ์ใหญ่อย่าง Apache Kafka แบบพร้อมใช้งานโดยลดภาระควบคุมดูแลระบบพื้นฐานเอง
- **OpenSearch Service:** ระบบค้นหาและวิเคราะห์ Log data ที่สืบทอดมาจาก Elasticsearch รองรับการตรวจสอบข้อมูลและสืบค้นความปลอดภัย
- **AWS Data Exchange:** ตลาดข้อมูลกลางสำหรับนำเข้า สมัครรับ และเข้าใช้งานชุดข้อมูลเชิงพาณิชย์จากพาร์ทเนอร์รายใหญ่อย่างปลอดภัย

---

### <a id="07-messaging"></a>07. Messaging & Application Integration
#### Amazon SQS (Simple Queue Service)
- **SQS Standard Queue:** Queue รับส่งข้อมูลรองรับ Throughput ไม่จำกัด รับประกันข้อความถูกส่งอย่างน้อยหนึ่งครั้ง (At-least-once) แต่อาจสลับ Queue กันได้
- **SQS FIFO Queue:** Queue รับประกันการทำงานเรียงลำดับก่อนหลังอย่างแม่นยำ (First-In-First-Out) และข้อความไม่ซ้ำซ้อนแน่นอน (Exactly-once)
- **SQS Dead Letter Queue (DLQ):** Queue พิเศษสำหรับแยกข้อความที่ประมวลผลไม่สำเร็จตามจำนวนครั้งที่กำหนด เพื่อเก็บไว้วิเคราะห์หาสาเหตุความผิดพลาด
- **SQS Delay Seconds:** ตั้งค่าถ่วงเวลาการส่งข้อความเข้าระบบ Queue ครั้งแรก (สูงสุด 15 นาที) เพื่อให้ปลายทางเตรียม Application ก่อนประมวลผล
- **SQS Visibility Timeout:** ระยะเวลาซ่อนข้อความชั่วคราวจากการเห็นของตัวอ่านอื่นระหว่างโดนดึงไปประมวลผล เพื่อเลี่ยงไม่ให้ประมวลผลซ้ำซ้อน
- **SQS Long Polling:** Client จะรอเปิดรับข้อความค้างไว้ใน Queue (สูงสุด 20 วินาที) เพื่อลดปริมาณการร้องขอ (Request) และประหยัดค่าใช้จ่าย
- **SQS Short Polling:** Client จะดึงและตอบกลับข้อความจากส่วนหนึ่งของ Queue ทันที แม้ว่ากลุ่มข้อมูลนั้นยังว่างอยู่ก็ตาม (มีค่าการร้องขอสูง)

#### Messaging Infrastructure
- **Amazon SNS (Pub/Sub):** บริการส่งข้อความกระจายข้อมูลแบบหนึ่งต่อหลายคน (Publisher/Subscriber) ไปยังปลายทาง เช่น อีเมล, SMS, SQS, Lambda
- **SNS FIFO Topics:** การันตีการส่งข้อความไปยังปลายทาง Queue ในเครือข่ายเรียงตามลำดับอย่างแม่นยำและไม่มีข้อความซ้ำซ้อนร่วมกับ SQS FIFO
- **Message Filtering:** ตั้งกฎให้ปลายทาง (Subscriber) เลือกกรองรับเฉพาะข้อความที่มีคุณลักษณะที่ตนเองต้องการจากหัวข้อ SNS หลัก
- **SNS and SQS Fan-Out Pattern:** สถาปัตยกรรมกระจายส่งข้อความหนึ่งชุดเข้า SNS แล้วส่งต่อเข้าไปยัง Queue SQS หลายอันขนานกันเพื่อประมวลผลคนละเรื่อง
- **Amazon MQ:** บริการจัดทำระบบ Queue ส่งข้อความแบบดั้งเดิม (ActiveMQ/RabbitMQ) เพื่อความสะดวกในการย้ายระบบเดิมขึ้นคลาวด์โดยไม่ต้องเขียน Program ใหม่
- **Step Functions (Standard):** บริการเขียน Workflow จัดการขั้นตอนการทำงานสำหรับกระบวนการใช้เวลาทำงานนาน (สูงสุด 1 ปี) และเน้นความเสถียร
- **Step Functions (Express):** บริการจัดทำ Workflow ประสิทธิภาพสูงสำหรับการประมวลผลปริมาณคำขอขนาดใหญ่ ระยะเวลาสั้นไม่เกิน 5 นาที
- **Amazon AppFlow:** บริการโอนย้ายข้อมูลแบบ Serverless ระหว่าง Application ภายนอก (เช่น Salesforce, Slack) กับบริการของ AWS อย่างปลอดภัย
- **Amazon AppSync:** บริการจัดการ API แบบ GraphQL แบบ Serverless ช่วยให้ระบบหน้าบ้านสามารถดึงข้อมูลหลากหลายแหล่งได้ในการเชื่อมต่อครั้งเดียว
- **API Gateway (HTTP API):** บริการสร้าง REST API ยุคใหม่ที่ออกแบบมาให้มีความเร็วสูงสุด Latency ต่ำ และประหยัดค่าใช้จ่ายกว่า REST API แบบดั้งเดิม
- **API Gateway (REST API):** บริการสร้าง API ที่มีฟีเจอร์ครบครัน รองรับการควบคุมสิทธิ์ ปรับแต่งขั้นสูง และเชื่อมต่อระบบหลังบ้านหลากหลายรูปแบบ
- **API Gateway (WebSocket):** บริการสร้างเส้นทางติดต่อสื่อสารแบบสองทางเชื่อมต่อค้างไว้ (Real-time Bi-directional) เหมาะสำหรับเว็บ Chat
- **API Gateway Caching:** ตั้งค่าบันทึกผลลัพธ์การร้องขอไว้ชั่วคราว เพื่อลดโหลดการประมวลผลและลดเวลาตอบสนองสำหรับ Server หลังบ้าน
- **API Gateway Throttling:** กำหนดขีดจำกัดจำนวนปริมาณคำขอต่อวินาที (TPS) เพื่อป้องกันระบบหลังบ้านล่มจากการโดนรุมยิงเรียกใช้งาน
- **API Gateway Usage Plans:** การสร้างข้อตกลงและแผนการใช้งานสำหรับลูกค้า โดยระบุ Quota จำกัดปริมาณ และควบคุมผ่านกุญแจ API Keys
- **API Gateway Canary Deployments:** การทยอยแบ่งส่งคำขอใช้งาน API ส่วนหนึ่งไปยังโค้ดเวอร์ชันทดสอบใหม่เพื่อประเมินผลก่อนปล่อยใช้งานจริง

---

### <a id="08-management"></a>08. Management, Monitoring & Governance
- **CloudWatch Metrics:** บริการรวบรวมข้อมูลสถานะและการใช้งานทรัพยากรของระบบเป็นตัวเลขตามคาบเวลา เพื่อใช้วิเคราะห์และตัดสินใจขยายเครื่อง
- **CloudWatch Logs:** แหล่งรวม เก็บรักษา และตรวจสอบค้นหา Log data ต่างๆ จาก Application และทรัพยากรบนคลาวด์ทั้งหมด
- **CloudWatch Alarms:** ระบบเฝ้าระวังที่จะส่งแจ้งเตือนหรือกระตุ้นการทำงานของระบบขยายตัว (ASG) เมื่อค่าสถานะเกินระดับที่ตั้งไว้
- **CloudWatch Dashboards:** แดช Board ปรับแต่งภาพสรุปข้อมูลแสดงผลแบบเรียลไทม์เพื่อตรวจสอบสุขภาพของ Application จากศูนย์กลางเดียว
- **CloudWatch Container Insights:** บริการเจาะลึกวิเคราะห์และสร้าง Board สุขภาพสำหรับการรันงานของระบบ Container (ECS, EKS, Fargate)
- **CloudWatch Contributor Insights:** วิเคราะห์ข้อมูลบันทึกเพื่อตรวจสอบและสรุปว่าผู้ใดหรือผู้ใช้ IP ใดสร้างปริมาณ Traffic ข้อมูลสูงสุดในระบบ
- **Amazon EventBridge (Event Bus):** บริการรับส่งเหตุการณ์เพื่อช่วยในการเชื่อมต่อ Application ย่อยเข้าด้วยกันแบบลดความยึดโยง (Decoupled Architectures)
- **EventBridge Pipes:** เครื่องมือช่วยสร้างทางเชื่อมต่อ (Point-to-Point) เพื่อกรองและแปลงส่งต่อข้อมูลจากแหล่ง Queue ไปหาปลายทางได้สะดวกขึ้น
- **EventBridge Schema Registry:** ศูนย์กลางเก็บรวบรวม โครงสร้าง และข้อมูลลักษณะรูปแบบของเหตุการณ์ เพื่อให้นักพัฒนาเขียน Program ตอบสนองได้ง่าย
- **AWS CloudTrail:** คลังเก็บประวัติการเรียกใช้ API ทั้งหมดบนระบบบันทึกว่า ใคร (Who) ทำอะไร (What) เมื่อไหร่ (When) และจากที่ไหน (Where)
- **AWS Config:** บริการบันทึกประวัติการตั้งค่าทรัพยากรคลาวด์ พร้อมประเมินตามกฎเกณฑ์ (Rules) และมีระบบแก้ไขจุดผิดพลาดอัตโนมัติ
- **AWS Compute Optimizer:** แนะนำการปรับขนาดเครื่องและทรัพยากร (EC2, EBS, Lambda) ให้เหมาะสมที่สุดด้วยการวิเคราะห์ผ่าน AI เพื่อเซฟงบ
- **AWS Health Dashboards:** รายงานแจ้งสถานะการให้บริการระบบคลาวด์ทั่วโลก และความเคลื่อนไหวแจ้งเตือนที่มีผลกระทบกับบัญชีตัวเองโดยเฉพาะ
- **AWS Service Catalog:** ระบบช่วยให้ผู้ดูแลกลางสร้าง จัดทำ และแจกจ่ายรายการคลัง Application ที่ได้รับการอนุมัติใช้งานให้กับทีมภายในเลือกเปิดใช้
- **AWS Organizations:** บริการรวมศูนย์จัดการบัญชี AWS หลายร้อยรายการเข้าด้วยกันเพื่อควบคุมความปลอดภัย และรวมค่าใช้จ่ายส่วนกลางเข้าด้วยกัน
- **Service Control Policies (SCPs):** นโยบายควบคุมสิทธิ์ความปลอดภัยสูงสุดสำหรับใช้จำกัดขอบเขตสิทธิ์ของทุกบัญชีในองค์กร รวมทั้งควบคุมบัญชี Root User ด้วย
- **Tag Policies:** กฎเกณฑ์ที่บังคับใช้นโยบายการติด Tag (Tags) ให้เป็นไปในรูปแบบและรูปแบบข้อความที่เป็นเอกภาพเหมือนกันทั่วทั้งบัญชีองค์กร
- **Backup Policies:** บริการนโยบายขององค์กรที่ช่วยบังคับสั่งเปิดใช้ จัดการ และคุมการทำสำรองข้อมูลสำหรับบัญชีผู้ใช้ย่อยทั้งหมด
- **SSM Session Manager:** บริการ Login เข้าควบคุม OS EC2 (Shell) ผ่านหน้าเว็บ Browser อย่างปลอดภัยโดยไม่จำเป็นต้องเปิด Port 22 (SSH)
- **SSM Parameter Store:** บริการจัดเก็บข้อมูลการตั้งค่าและรหัสผ่านแบบคีย์-แวลูฟรี ปลอดภัย และสามารถอ่านค่าเชื่อมต่อผ่าน Script ได้สะดวก
- **SSM Patch Manager:** ระบบช่วยรันและจัดการติดตั้ง PatchSoftware อัปเดต OS ให้กับเครื่อง Server แบบอัตโนมัติตามช่วงเวลา
- **SSM State Manager:** ระบบช่วยควบคุม ดูแล และบังคับให้เครื่อง Server มีสถานะการติดตั้งและการตั้งค่าระบบที่สม่ำเสมอตามนโยบาย
- **AWS Budgets:** บริการแจ้งเตือนเมื่อการใช้จ่ายเงินจริงหรือแนวโน้มการใช้งานพยากรณ์เกินกว่างบประมาณที่กำหนดไว้ล่วงหน้า
- **AWS Cost Explorer:** เครื่องมือแสดงและวิเคราะห์กราฟค่าใช้จ่ายคลาวด์ ช่วยชี้จุดเด่นและแนวโน้มค่าบริการย้อนหลังและไปข้างหน้า
- **AWS Cost Anomaly Detection:** บริการแจ้งเตือนล่วงหน้าเมื่อค่าบริการคลาวด์เกิดสไปก์กระโดดสูงผิดปกติโดยใช้ปัญญาประดิษฐ์ตรวจจับ

---

### <a id="09-containers"></a>09. Containers, Dev Tools & Media
- **Amazon ECR:** คลังจัดเก็บ Image ระบบ Container (Docker images) ปลอดภัยสูง มีระบบตรวจสอบวิเคราะห์หาช่องโหว่ความปลอดภัยในตัว
- **Amazon ECS:** บริการจัดการและรันระบบตู้ Container (Container Orchestration) ความปลอดภัยระดับสูงสุด ออกแบบมาให้ทำงานร่วมกับ AWS ได้ลึกซึ้ง
- **ECS EC2 Launch Type:** รัน Container โดยผู้ใช้งานต้องรับผิดชอบการดูแลระบบ ปรับขนาด และตั้ง Server เครื่อง EC2 ขึ้นมารองรับการประมวลผลเอง
- **AWS Fargate (Serverless):** รันงาน Container โดยไม่ต้องติดตั้ง Configure หรือดูแล Server ด้านล่าง จ่ายค่าบริการตามทรัพยากรที่ Container เรียกใช้จริง
- **ECS Task Definitions:** File ข้อกำหนดการตั้งค่า Container เช่น การใช้ CPU, แรม, ปริมาณ Port เชื่อมต่อ, และไดรฟ์จัดเก็บข้อมูล
- **ECS Services:** ตัวควบคุมดูแลรันปริมาณชุด Container ให้อยู่ในสถานะที่กำหนด พร้อมจัดการเชื่อมต่อ Load Balancer และทำระบบขยายตัว
- **Amazon EKS:** บริการจัดตั้งระบบการทำงานของ Kubernetes (Managed Kubernetes) บนโครงสร้าง AWS เพื่อความยืดหยุ่นและการทำงานร่วมกันกับระบบภายนอก
- **AWS Amplify:** Platform สำเร็จรูปช่วยพัฒนาหน้าบ้านเชื่อมหลังบ้านของ ApplicationMobile และเว็บได้อย่างสะดวกและจัดส่งขึ้นคลาวด์ทันใจ
- **Amazon Q Developer:** ผู้ช่วยพัฒนา Program และสร้างโครงสร้างคลาวด์ด้วยปัญญาประดิษฐ์อัจฉริยะ (GenAI) ช่วยแก้ไขปัญหาและตอบคำถาม
- **AWS CodeWhisperer:** ตัวช่วยเขียนและแนะนำโค้ดด้วย AI อิงตามบริบทและคอมเมนต์ข้อความใน Program (ปัจจุบันรวมอยู่ใน Amazon Q Developer)
- **Device Farm:** บริการเข้าถึงและทดสอบ Application กับเครื่องมือถือสมาร์ทโฟนของจริงหลายร้อยรุ่นบนคลาวด์เพื่อตรวจหาบั๊กก่อนปล่อยใช้จริง
- **AWS MediaConvert:** บริการแปลงสกุล File วิดีโอคุณภาพสูงระดับมืออาชีพให้ออกมาในขนาดและฟอร์แมตต่างๆ สำหรับเล่นบนอุปกรณ์ปลายทางหลากหลายชนิด
- **Elastic Transcoder:** บริการจัดการแปลงรูป File วิดีโอแบบเดิม (ปัจจุบันแนะนำให้หันมาใช้งาน MediaConvert ที่ทันสมัยและประหยัดกว่า)

---

### <a id="10-genai"></a>10. Generative AI & Machine Learning
- **Amazon Bedrock:** เข้าถึงและต่อยอด Model ปัญญาประดิษฐ์ขนาดใหญ่ (Foundation Models) ของบริษัทดังระดับโลกผ่าน Serverless API
- **Amazon SageMaker:** Platform เตรียมข้อมูล สร้าง ฝึกสอน และนำ Model Machine Learning ไปให้บริการแบบครบวงจร
- **Amazon SageMaker Canvas:** หน้าต่างพัฒนา ML แบบลากวาง (No-code UI) ช่วยให้ทีมธุรกิจทั่วไปสร้างพยากรณ์ข้อมูลได้โดยไม่ต้องเขียนโค้ด
- **Amazon Rekognition:** วิเคราะห์ ค้นหา คัดกรอง คัดลอกข้อความ และระบุข้อมูล Object รวมถึงใบหน้าบุคคลในรูปภาพหรือวิดีโออัจฉริยะ
- **Amazon Polly:** แปลงตัวอักษรข้อความให้ออกมาเป็น File เสียงพูดเสมือนจริง สนับสนุนรูปแบบภาษาและเสียงธรรมชาติที่หลากหลาย
- **Amazon Transcribe:** แปลงข้อมูลเสียงจาก File เสียงหรือการบันทึกสตรีมเสียงพูดให้ออกมาเป็นข้อความตัวอักษรได้อย่างแม่นยำ
- **Amazon Translate:** บริการแปลภาษาต่างๆ แปลงข้อความต่างประเทศข้ามกันด้วยเทคนิคปัญญาประดิษฐ์ที่รวดเร็วและเป็นธรรมชาติ
- **Amazon Comprehend:** ค้นหาประเด็น วิเคราะห์อารมณ์ความรู้สึก และดึงข้อมูลสาระสำคัญออกจาก File เอกสารตัวอักษรแบบ NLP
- **Amazon Comprehend Medical:** ระบบสกัดวิเคราะห์คำและทำประวัติข้อมูลยาและโรคภัยไข้เจ็บจากเอกสารบันทึกการแพทย์อย่างปลอดภัยสอดคล้องกับ HIPAA
- **Amazon Lex:** บริการสร้างส่วนระบบเสียงตอบโต้หรือแชต Bot สนทนาอัจฉริยะด้วยเทคโนโลยีการประมวลผลเสียงพูดเดียวกับ Alexa
- **Amazon Connect:** บริการศูนย์บริการทางโทรศัพท์คลาวด์ (Cloud Contact Center) ครบวงจร ตั้งค่าง่าย รองรับ Scale แชตและแชต Bot ในตัว
- **Amazon Textract:** ระบบตรวจจับ อ่านและคัดแยกตัวหนังสือ ข้อมูลตารางจากภาพเอกสารใบเสร็จหรือ PDF ได้อย่างถูกต้องเหนือกว่า OCR ทั่วไป
- **Amazon Kendra:** ระบบค้นหาข้อมูลอัจฉริยะภายในองค์กรที่สามารถเข้าใจคำถามและพากลุ่มผู้ใช้ไปหาข้อมูลคำตอบที่ซ่อนอยู่ภายในได้ทันที
- **Amazon Personalize:** ระบบจัดทำคำแนะนำเสนอแนะสินค้าหรือรายการความสนใจเฉพาะบุคคลแบบเรียลไทม์ เลียนแบบเทคโนโลยีเว็บบริการยอดนิยม
- **Amazon Forecast:** ระบบพยากรณ์ความต้องการ แนวโน้ม หรือตัวเลขอิงข้อมูลอนุกรมเวลาในอนาคตด้วยความสามารถของ Machine Learning

---

### <a id="11-migration"></a>11. Migration, Architecture Frameworks & Advanced Patterns
#### Migration Tools
- **AWS Migration Hub:** ศูนย์กลางสรุปรายงานและบันทึกติดตามความคืบหน้าของโครงการย้ายระบบไอทีทั้งหมดขององค์กรขึ้น AWS
- **Application Migration Service (MGN):** ระบบย้ายเครื่อง Server แบบ Lift-and-Shift คัดลอกทั้ง OS และ File ขึ้น EC2 แบบอัตโนมัติ
- **AWS DMS:** บริการย้ายระบบ Database อย่างปลอดภัย โดยสามารถตั้งค่าซิงค์ข้ามคลาวด์และทำงานควบคู่กันได้ขณะกำลังโอนย้าย (No Downtime)
- **DMS SCT:** เครื่องมือตรวจสอบ แปลงโครงสร้าง และแปลง Queue รีกฎการจัดเก็บข้ามค่ายเพื่อเตรียมย้าย Database ไปค่ายอื่น (เช่น Oracle ไป Aurora)
- **VMware Cloud on AWS:** สภาพแวดล้อมรันระบบสำหรับย้ายงาน VMware vSphere เดิมจาก On-premises ขึ้นมาทำงานบน AWS ได้อย่างราบรื่นโดยไม่ต้องทำใหม่

#### Real-World Architecture Patterns
- **MyClothes.com Pattern:** สถาปัตยกรรมไร้สถานะ (Stateless) โดยทำการแยกประวัติ Session ไปฝากเก็บข้างนอกบน ElastiCache หรือ DynamoDB
- **WhatsTheTime.com Pattern:** สถาปัตยกรรมลดโหลดการ Queue รี Database โดยการทำ Cache ข้อมูลหน้าบ้านผ่าน CloudFront และตั้งค่า TTL ให้เสถียร
- **MyWordPress.com Pattern:** การวางระบบเว็บแชร์ File แบบทนทานสูง (Public ALB, Auto Scaling ใน Private Subnet, ข้อมูลเก็บส่วนกลางบน EFS, RDS Multi-AZ)
- **Ingestion Pipeline Pattern:** ท่อรับส่งข้อมูลขนาดใหญ่ความเร็วสูง (App -> API Gateway -> Kinesis Data Streams -> Firehose -> S3 Data Lake)

#### AWS Well-Architected Framework
- **Well-Architected Tool:** บริการช่วยประเมินสถานะความปลอดภัย ประสิทธิภาพ และความคุ้มค่าของสถาปัตยกรรมเทียบตามมาตรฐาน Well-Architected
- **Trusted Advisor:** เครื่องมือสแกนระบบและช่วยแจ้งคำแนะนำใน 5 มิติ (ลดต้นทุน, ความปลอดภัย, ความทนทาน, ประสิทธิภาพ, การคุมลิมิตทรัพยากร)
- **Corporate Hybrid AD Setup:** การเชื่อมบัญชีตัวตนข้ามข่ายด้วย SAML 2.0 ร่วมกับ AD Connector เพื่อจำกัดและเปิดรับ Console Login ขององค์กร
- **Encrypted AMI Sharing:** กระบวนการส่งแชร์ AMI ข้ามบัญชีโดยเปิดให้อีกบัญชีเข้าถึงคีย์เข้ารหัส KMS หลักที่ใช้สร้างภาพก่อนกดนำรูปภาพไป Boot
- **S3 Cross-Account Access:** การตั้งค่ายอมรับสิทธิ์ข้ามบัญชีผ่านการผสานกฎควบคุมตัวบุคคล (IAM) และกฎถังจัดเก็บปลายทาง (S3 Bucket Policy)
- **AWS RAM:** แชร์ทรัพยากรระบบ Network (เช่น Subnet, Transit Gateway) ไปยังบัญชีเป้าหมายอื่นๆ ในบัญชีองค์กรช่วยคุม Network เดียวกัน
- **Instance Scheduler:** ระบบประหยัดงบที่ช่วยปิดการทำงานเครื่องบริการ (EC2, RDS) ในช่วงวันหยุดหรือหลังเวลาทำงานปกติแบบตั้งกฎสั่งการอัตโนมัติ
- **Amazon SES:** บริการรับส่งอีเมลจำนวนมาก (Transactional/Marketing Emails) ความปลอดภัยระดับสูงและเชื่อถือได้ดีที่สุด
- **Amazon Pinpoint:** บริการส่งข่าวสาร สื่อสารข้อมูล และจัดแคมเปญนำส่งผู้ใช้อุปกรณ์ผ่านช่องทางที่หลากหลาย เช่น Push Notification, SMS, Email
