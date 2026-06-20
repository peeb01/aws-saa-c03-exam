# 11 Migration, Architecture Frameworks & Advanced Patterns - Deep-Dive Exam Cheat-Sheet

---

## 1. Migration Tools

### Deep-Dive Concepts & Sub-features
บริการที่ใช้ในการโยกย้ายระบบ Server และ Database จาก On-premises ขึ้นสู่ระบบคลาวด์ของ AWS

*   **AWS Migration Hub:** Port ัลกลางเพื่อรวบรวมข้อมูลสถานะ บันทึกแผนการย้าย และติดตามความคืบหน้าของกระบวนการ Migrate ทั้งหมดข้ามหลายเครื่องมือ
*   **AWS Application Migration Service (MGN):**
    *   เครื่องมือหลักสำหรับการทำระบบย้ายระบบ Server แบบ **Lift-and-Shift** (Rehost) ในระดับ Block-level replication
    *   ติดตั้งตัว Agent ลงในเครื่อง Server กายภาพหรือ VM ต้นทาง (Windows/Linux) จากนั้นระบบจะซิงค์ถ่ายสำเนา OS และข้อมูลทั้งหมดมารันเป็นเครื่อง **EC2** บน AWS แบบอัตโนมัติ ช่วยลดความผิดพลาดและทำได้รวดเร็ว
*   **AWS Database Migration Service (DMS):**
    *   บริการย้าย Database อย่างปลอดภัย รองรับทั้งการย้ายค่ายแบบค่ายเดียวกัน (Homogeneous) หรือข้ามค่าย (Heterogeneous)
    *   มีจุดเด่นคือสนับสนุนการทำซิงค์ต่อเนื่องแบบ **CDC (Change Data Capture)** ช่วยให้ Database ต้นทางยังคงเปิดให้บริการรันธุรกรรมได้ปกติระหว่างกำลังคัดลอกข้อมูลไปปลายทาง (ทำให้เกิด **Near-Zero Downtime** ตอนสลับเปลี่ยนระบบจริง)
*   **DMS Schema Conversion Tool (SCT):** เครื่องมือสำหรับแปลงโครงสร้าง โค้ด Configure และ Schema ของ Database กรณีที่ต้องการเปลี่ยนค่ายยักษ์ใหญ่ (เช่น ย้ายจาก Oracle/Microsoft SQL Server ไปใช้ Amazon Aurora) เพื่อเตรียมความพร้อมก่อนสั่งให้ DMS ย้ายข้อมูลจริง
*   **VMware Cloud on AWS:** ระบบช่วยยกเครื่อง VM ของ VMware vSphere ทั้ง Cluster จากเครื่องบริษัทมาเปิดรันประมวลผลบนคลาวด์ของ AWS ได้อย่างรวดเร็วโดยไม่ต้องดัดแปลงสถาปัตยกรรม VM เดิมเลย

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Lift-and-shift physical/virtual servers to EC2 / Minimum downtime:* -> **AWS Application Migration Service (MGN)**
    *   *Migrate database with zero downtime / Continuous replication:* -> **AWS Database Migration Service (DMS)**
    *   *Migrate database to different engine (e.g. Oracle to Aurora) / Translate schemas:* -> **DMS Schema Conversion Tool (SCT)**
    *   *Migrate VMware workloads directly to AWS:* -> **VMware Cloud on AWS**
*   **Trap Warnings:**
    *   *SCT vs DMS:* จำกฎการย้าย Database ข้ามค่ายเสมอ: ต้องสั่งแปลงโครงสร้างตารางด้วย **SCT** ก่อนเป็นด่านแรก จากนั้นจึงค่อยใช้ **DMS** ในการย้ายข้อมูลตามไป (ห้ามตอบว่าใช้ DMS แปลงโครงสร้างโดยตรงเพราะ DMS ทำไม่ได้ตัวคนเดียว)

---

## 2. Real-World Architecture Patterns (กรณีศึกษาโจทย์ออกสอบบ่อย)

### 1. MyClothes.com Pattern (Stateless Web App)
*   **สถานการณ์:** เว็บขายเสื้อผ้าที่เริ่มล่มเนื่องจากเก็บข้อมูลตู้สินค้าหรือ Login ของผู้ใช้ไว้ในหน่วยความจำ Server (Stateful) พอผู้ใช้เปลี่ยนเครื่องหลังบ้านผ่าน Load Balancer ตะกร้าสินค้าจะสูญหายทันที
*   **ทางแก้ (Stateless Architecture):** ปรับโครงสร้างเว็บให้เป็น **Stateless** โดยนำข้อมูล User Session หรือ Shopping Cart ออกไปจัดเก็บไว้ภายนอก Server ผ่านระบบ **Amazon ElastiCache for Redis** หรือ **Amazon DynamoDB** แทน ทำให้เครื่อง Web Server หลังบ้านสามารถถูกเปิด/ปิด หรือ Scale ขยายขนาดเพิ่มลดได้อย่างอิสระโดยข้อมูลตะกร้าสินค้าของลูกค้าไม่สูญหาย
*   **Data Flow:** Client -> ALB -> EC2 (Web) -> Session data read/write to Redis/DynamoDB.

### 2. WhatsTheTime.com Pattern (Edge Caching API)
*   **สถานการณ์:** บริการ API ยอดนิยมที่คนทั่วโลกเข้ามากดขอเวลาปัจจุบันเป็นวินาที Traffic พุ่งสูงมากจน Database หลังบ้านรับโหลดไม่ไหว (ส่วนใหญ่มาดึงข้อมูลเดิมซ้ำๆ)
*   **ทางแก้ (Cache-Optimized):** วางระบบกระจายข้อมูลหน้าบ้าน **Amazon CloudFront** ไว้ด้านหน้าสุดของระบบ API Gateway เพื่อทำ Edge Cache สำหรับคำขออ่านข้อมูลทั่วไปที่มีเนื้อหาเหมือนกัน และทำการตั้งค่าระยะเวลา Cache (**TTL**) ให้สั้นพอเหมาะ (เช่น 1-5 วินาที) ช่วยป้องกัน Traffic วิ่งไปกระแทก Database หลังบ้านโดยตรง และลดเวลารอผลลัพธ์ลงได้สูงสุด
*   **Data Flow:** User -> CloudFront (Cache TTL) -> API Gateway -> Lambda -> DynamoDB.

### 3. MyWordPress.com Pattern (High Availability Shared Content)
*   **สถานการณ์:** เว็บบ Lock WordPress ต้องการความทนทานสูง (HA) ข้ามหลากหลาย AZ และระบบ Shared File ต้องมองเห็นภาพอัปโหลดเดียวกันทั้งหมด
*   **ทางแก้ (Multi-AZ Shared FS):** จัดทำระบบ ALB วางด้านหน้าสุดกระจายโหลดไปยังเครื่อง EC2 Web Servers ใน Auto Scaling Group แยกกันอยู่คนละ AZ โดยให้ EC2 ทุกตัวทำสิทธิ์เชื่อมโยงเมาท์ไดรฟ์เก็บข้อมูลร่วมกันไปยัง **Amazon EFS** (Shared File System) และ Database หลักใช้ **Amazon RDS (Multi-AZ)** เพื่อความทนทานปลอดภัยสูงสุด
*   **Data Flow:** Client -> Route 53 -> ALB (Public Subnet) -> EC2 Web Servers (Private Subnet / Auto-scaled) -> Shared file storage on EFS / Database transactions on RDS Multi-AZ.

### 4. Ingestion Pipeline Pattern (Big Data Real-time Processing)
*   **สถานการณ์:** Application ต้องการระบบดูดข้อมูลกิจกรรมการใช้งาน แฟลชดีล หรือสถิติเซ็นเซอร์จำนวนมหาศาลเพื่อบันทึกและวิเคราะห์ย้อนหลังในระดับ Big Data
*   **ทางแก้ (Decoupled Stream-to-Lake):** ออกแบบ API Gateway ทำหน้าที่เปิดรับ Traffic ข้อมูล -> ยิงข้อมูลเข้าไปฝากไว้ที่ **Kinesis Data Streams** เพื่อรับข้อมูลเข้าแบบเรียลไทม์ -> ใช้ **Kinesis Data Firehose** ดึงข้อมูลออกมาแปลงรูปแบบ File (เช่น บีบอัดเป็น Parquet) -> ส่งข้อมูลลงไปเก็บที่ **Amazon S3** (Data Lake) เพื่อวิเคราะห์ย้อนหลังด้วย **Athena**
*   **Data Flow (1, 2, 3, 4):**
    1.  Application หรือเซ็นเซอร์ส่งข้อมูล Traffic ขนาดใหญ่ยิงเข้าหา **Amazon API Gateway**
    2.  API Gateway ส่งต่อเหตุการณ์อย่างรวดเร็วเข้าสู่ **Kinesis Data Streams** (Queue รับ Traffic แยก Shard)
    3.  **Kinesis Data Firehose** ดักกวาดข้อมูลจาก Streams ทำการแปลงรูปแบบและจัดกลุ่ม Buffer ขนาด File
    4.  Firehose ปล่อย File ผลลัพธ์ลงไปจัดเก็บถาวรใน **Amazon S3 Data Lake** และให้ผู้ใช้เริ่มรันประมวลผลคำสั่งผ่าน **Amazon Athena**

---

## 3. Cloud Architecture Frameworks & Governance

### Deep-Dive Concepts & Sub-features
*   **AWS Well-Architected Framework (6 Pillars):**
    1.  **Operational Excellence:** มุ่งเน้นการรันระบบและมอนิเตอร์เพื่อพัฒนาคุณค่าการทำงานอย่างต่อเนื่อง (เน้นทำทุกอย่างเป็นโค้ด - Infrastructure as Code, ทำบ่อยๆ ทำทีละเล็กๆ เพื่อย้อนกลับง่าย)
    2.  **Security:** มุ่งเน้นการปกป้องข้อมูล ระบบ และสินทรัพย์คลาวด์ (เน้นระบุสิทธิ์ตามหลัก Least Privilege, บังคับใช้การเข้ารหัสในทุกจุด, ตรวจจับเหตุการณ์ความปลอดภัยอัตโนมัติ)
    3.  **Reliability:** มุ่งเน้นความน่าเชื่อถือและการกู้คืนระบบจากการขัดข้องให้กลับมาบริการได้ปกติ (เน้น Scale ระบบข้ามหลาย AZ/Region, ใช้ระบบหลีกเลี่ยง Single Point of Failure)
    4.  **Performance Efficiency:** มุ่งเน้นการเลือกใช้ทรัพยากรคำนวณอย่างมีประสิทธิภาพสูงสุดตามความต้องการ (เลือกประเภท Storage และคำนวณให้เข้ากับชนิด Application, ทดลองเทคโนโลยีใหม่บ่อยๆ)
    5.  **Cost Optimization:** มุ่งเน้นการคุมค่าใช้จ่ายให้คุ้มค่าประหยัดที่สุด (วิเคราะห์ขนาดเครื่องให้ไม่บวม, หันมาใช้ Serverless เมื่อทำได้, รวมศูนย์การจ่ายเงินเพื่อเอาส่วนลด)
    6.  **Sustainability:** มุ่งเน้นการรักษาสิ่งแวดล้อมโดยลดทรัพยากรคาร์บอน (ออกแบบ App ให้ใช้พลังงานคำนวณต่ำสุด, ยกเลิกการเปิดเครื่องทิ้งไว้เปล่าๆ)
*   **AWS Well-Architected Tool:** บริการตัวช่วยสแกนตรวจแบบฟอร์มสถาปัตยกรรมของโครงการเทียบกับเสาหลัก Well-Architected เพื่อชี้แนะจุดอ่อนที่มีความเสี่ยงสูง (High Risk Issues)
*   **AWS Trusted Advisor:** บริการสแกนระบบ AWS ของผู้ใช้ในมิติต่างๆ แบบอัตโนมัติ เพื่อให้คำชี้แนะในเรื่อง:
    *   *Cost Optimization:* ชี้ช่องลดรายจ่าย เช่น เจอเครื่องว่าง หรือ EBS Snapshot เก่า
    *   *Security:* เตือนเมื่อเจอเปิด Port สาธารณะแปลกๆ หรือถัง S3 เปิดเป็น Public
    *   *Fault Tolerance:* แนะนำให้ทำระบบสำรอง Multi-AZ ของ Database หรือ Load Balancer
    *   *Performance:* ชี้ช่องคอขวดที่เครื่องใช้งาน CPU/Disk หนักเกินเกณฑ์
    *   *Service Limits:* แจ้งเตือนเมื่อการใช้ Quota ของบัญชีใกล้ชนเพดาน Limit ของระบบ

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Review workload against AWS best practices / Identify high risk issues:* -> **AWS Well-Architected Tool**
    *   *Automated check of S3 bucket permissions / Port security / Cost saving suggestions:* -> **AWS Trusted Advisor**

---

## 4. Global & Advanced Corporate Network Patterns

### Deep-Dive Concepts & Sub-features
*   **Hybrid Cloud Identity Integration:**
    *   **Managed Microsoft AD:** ติดตั้งตู้ AD สำเร็จรูปจริงบน AWS เพื่อให้ระบบ Windows ในคลาวด์สามารถใช้ชื่อและรหัสผ่าน Login ของ AD ได้ทันที
    *   **AD Connector:** ไม่มีการซิงค์ข้อมูลผู้ใช้ขึ้นคลาวด์ ทำหน้าที่เป็นทางผ่านเชื่อมต่อประสานงาน (Proxy requests) วิ่งกลับมาตรวจสอบสิทธิ์ Login กับ Active Directory ที่บริษัทโดยตรง (ตอบโจทย์เรื่องความปลอดภัยสูงสุดของข้อมูลพนักงาน)
    *   **Identity Federation (SAML 2.0):** เปิดโอกาสให้พนักงานขององค์กรสามารถ Login ระบบ Portal เดิมของบริษัท (เช่น Okta, Ping Identity, Active Directory) แล้วสามารถเด้งเข้ามาจัดการทรัพยากรบนคอนโซล AWS ได้ชั่วคราวผ่าน Token ความปลอดภัยโดยไม่ต้องสร้างบัญชี IAM User ให้พนักงานแต่ละคนใหม่ (Corporate Single Sign-On)
*   **Advanced Cross-Account Sharing:**
    *   **Encrypted AMI Sharing Process:** หากต้องการส่งแชร์ AMI ของ Application ที่ได้รับการเข้ารหัส (Encrypted) ไปยังบัญชีผู้ใช้อื่น:
        1.  ต้องระบุแชร์ตัว AMI นั้นไปหาบัญชีปลายทาง
        2.  ต้องแชร์สิทธิ์การเข้าถึงคีย์ **AWS KMS Key** (คีย์กำหนดสิทธิ์เอง - CMK) ที่ใช้เข้ารหัสรูปภาพนั้นไปหาปลายทางด้วย (และปลายทางต้องกวาดสร้าง Snapshot ใหม่พร้อมนำกุญแจไปใช้งาน Boot เครื่อง)
    *   **AWS RAM (Resource Access Manager):** บริการช่วยแชร์ทรัพยากรเครือข่ายของ AWS (เช่น Private Subnet, Transit Gateway) ข้ามบัญชีผู้ใช้ย่อยภายในองค์กรอย่างปลอดภัย ช่วยให้หลายบัญชีรันระบบ IP เครือข่าย Subnet เดียวกันได้โดยไม่ต้องทำ Network Peering ซ้ำซ้อน
*   **Special Purpose Infrastructure:**
    *   **Instance Scheduler on AWS:** ระบบตั้งการทำงานปิด/เปิดเครื่อง EC2 และ RDS ตามปฏิทินเวลาของบริษัทเพื่อเซฟงบประมาณ
    *   **Amazon SES (Simple Email Service):** บริการส่งและรับอีเมลในระดับขนาดใหญ่พิเศษ (เช่น ส่งอีเมลยืนยันรายการสั่งซื้อ, ส่งข้อมูลจดหมายข่าวของบริษัท)
    *   **Amazon Pinpoint:** บริการออกแบบเส้นทางแคมเปญนำส่งข่าวสารและข้อความกระตุ้นผู้บริโภคผ่านช่องทางที่เหมาะสม เช่น SMS, อีเมล หรือแจ้งเตือน App

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Sign in to AWS Console using Corporate Credentials / Enterprise SSO:* -> **SAML 2.0 Federated Identity Center**
    *   *Share Subnets across multiple AWS accounts:* -> **AWS Resource Access Manager (RAM)**
    *   *Send bulk transactional emails / Marketing emails:* -> **Amazon SES**
    *   *Automate starting and stopping of EC2 instances based on schedules:* -> **Instance Scheduler on AWS**

### Real-World Use Cases
*   **Corporate Active Directory Single Sign-On:** บริษัทต้องการให้วิศวกร 200 คน Login เข้าใช้งานคอนโซล AWS โดยห้ามสร้างบัญชี IAM User รายบุคคลในคลาวด์ จึงเลือกเชื่อมต่อ Identity Federation ผ่าน SAML 2.0 ร่วมกับ AD Connector เพื่อให้พนักงานทุกคนใช้งานบัญชีบริษัทชุดเดียวกันและมีสิทธิ์ตามหน้าที่ควบคุมของ IAM Roles

### Architecture Integration Pattern: Cross-Account Shared Network Architecture

```text
   [ Central Network Account (NetAdmin) ]
                    │
                    ▼ (1) Create Subnets & share via RAM
   ┌───────────────────────────────────┐
   │    AWS Resource Access Manager    │
   └─────────────────┬─────────────────┘
                     │
                     ▼ (2) Link Account Organization
   ┌───────────────────────────────────┐
   │    Application Account (AppDev)   │
   └─────────────────┬─────────────────┘
                     │
                     ▼ (3) Deploy EC2 in Shared Subnet
   [ EC2 Instance ] ──► [ Shared Private Subnet ] (No VPC Peering needed)
```
1.  **Network Administrator** ในบัญชีหลัก (Network/Central Account) จัดตั้งเครือข่ายใหญ่ **VPC** พร้อมออกแบบระบบการเดินสาย Subnet
2.  บัญชีหลักใช้งาน **AWS Resource Access Manager (RAM)** เพื่อทำการส่งแชร์ **Private Subnets** ไปยังบัญชีนักพัฒนาอื่นๆ (App Accounts) ภายใต้โครงสร้าง AWS Organizations
3.  **Application Developer** ในบัญชีปลายทาง เข้ามา Login และมองเห็น Private Subnets ดังกล่าวได้อัตโนมัติ และสั่งเปิด Instance เครื่อง **EC2 (Application Server)** ลงใน Subnet ที่ได้รับการแชร์มา
4.  เครื่อง EC2 จากหลากหลายบัญชีที่รันใน Subnet แชร์เดียวกัน สามารถสื่อสารผ่าน Private IP เครือข่ายส่วนตัวเดียวกันได้อย่างปลอดภัย สะดวก และสแกน Network จัดระเบียบรวมศูนย์ได้ง่ายโดยไม่ต้องเซ็ต VPC Peering ข้ามบัญชีให้ปวดหัว


---

## ➔ การนำทาง (Navigation)
| [« กลับหน้าหลัก (README.md)](../README.md) | [« ก่อนหน้า: 10 Generative AI & Machine Learning](10_generative_ai_machine_learning.md) | **11 Migration & Architecture Frameworks** |