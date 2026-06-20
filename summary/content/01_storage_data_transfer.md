# 01 Storage & Data Transfer - Deep-Dive Exam Cheat-Sheet

---

## 1. Amazon S3 (Simple Storage Service)

### Deep-Dive Concepts & Sub-features
Amazon S3 คือบริการ Object Storage ที่เก็บข้อมูลในลักษณะ Key-Value มีความทนทาน (Durability) ระดับ 11 Nines (99.999999999%) โดยการกระจายข้อมูลไปอย่างน้อย 3 Availability Zones (AZ) อัตโนมัติ (ยกเว้น One Zone-IA และ Express One Zone)

*   **S3 Storage Classes:**
    *   **Standard:** สำหรับข้อมูลที่เข้าถึงบ่อย มี Latency ต่ำและ Throughput สูง
    *   **Intelligent-Tiering:** เหมาะกับข้อมูลที่ไม่ทราบ Pattern การเข้าถึงแน่ชัด โดยจะย้ายข้อมูลระหว่าง Access Tiers (Frequent, Infrequent, Archive) อัตโนมัติตามระยะเวลาที่ไม่มีการเรียกใช้ เพื่อลดราคาเก็บข้อมูลโดยไม่มีค่าธรรมเนียมย้าย Class และไม่มีผลเสียเรื่อง Latency
    *   **Standard-IA (Infrequent Access):** สำหรับข้อมูลที่เข้าถึงไม่บ่อย (อย่างน้อยเดือนละครั้ง) แต่เมื่อต้องการใช้ต้องได้ทันที (Millisecond latency) คิดค่าเก็บรักษาถูกกว่าแต่มีค่าธรรมเนียมการดึงข้อมูล (Retrieval Fee) และคิดระยะเวลาเก็บขั้นต่ำ 30 วัน
    *   **One Zone-IA:** คล้าย Standard-IA แต่เก็บข้อมูลใน AZ เดียวเพื่อประหยัดค่าใช้จ่ายลงอีก 20% เหมาะสำหรับข้อมูลสำรองที่สามารถสร้างขึ้นใหม่ได้ง่ายหาก AZ นั้นเสียหาย
    *   **Glacier Instant Retrieval:** สำหรับข้อมูล Archive ที่เข้าถึงไม่เกินปีละ 2-3 ครั้ง แต่ยังคงสามารถดึงข้อมูลออกมาใช้ได้ภายในระดับมิลลิวินาที เก็บขั้นต่ำ 90 วัน
    *   **Glacier Flexible Retrieval:** สำหรับข้อมูลเก็บถาวร (Archive) ดึงข้อมูลได้ 3 โหมด: *Expedited* (1-5 นาที), *Standard* (3-5 ชั่วโมง), *Bulk* (5-12 ชั่วโมง) เก็บขั้นต่ำ 90 วัน
    *   **Glacier Deep Archive:** Class ที่ราคาประหยัดที่สุดสำหรับ Compliance Data ดึงข้อมูลได้ 2 โหมด: *Standard* (12 ชั่วโมง) และ *Bulk* (48 ชั่วโมง) เก็บขั้นต่ำ 180 วัน
    *   **S3 Express One Zone:** Class ที่ออกแบบมาสำหรับงานประสิทธิภาพสูงมาก Latency ต่ำระดับ Single-digit millisecond เก็บข้อมูลใน Directory Bucket ใน AZ เดียว เหมาะกับ Big Data, AI/ML Training
*   **Lifecycle Policies:**
    *   **Transition Actions:** กฎสำหรับการย้าย Class ข้อมูลตามระยะเวลา เช่น ย้ายจาก Standard -> Standard-IA หลังผ่านไป 30 วัน -> Glacier หลังผ่านไป 90 วัน
    *   **Expiration Actions:** ลบข้อมูลหรือลบเวอร์ชันเก่าของ Object โดยอัตโนมัติ เช่น ลบ Object เวอร์ชันเก่าหลังจากผ่านไป 180 วัน
*   **S3 Object Lock & Vault Lock:**
    *   **Compliance Mode:** Lock ไม่ให้แก้ไขหรือลบ Object โดยเด็ดขาด แม้แต่ Root User ก็ไม่สามารถ bypass หรือเปลี่ยนค่า Lock นี้ได้จนกว่าจะครบระยะเวลา Retention
    *   **Governance Mode:** Lock ป้องกันการลบจากผู้ใช้ทั่วไป แต่ยอมให้ผู้ใช้ที่มีสิทธิ์ IAM พิเศษ (เช่น `s3:BypassGovernanceRetention`) สามารถ Unlock หรือลบ Object ได้
    *   **Legal Holds:** Lock Object แบบไม่มีกำหนดเวลาสิ้นสุด Unlock ได้เมื่อผู้ใช้ที่มีสิทธิ์สั่งยกเลิกเท่านั้น
    *   **Glacier Vault Lock:** กำหนดเงื่อนไขนโยบายแบบ WORM (Write Once, Read Many) ลงใน Glacier Vault และ Lock แบบถาวร ไม่สามารถแก้ไขหรือลบได้อีกเลยหลังจากดำเนินการเสร็จสิ้น 24 ชม.
*   **S3 Features:**
    *   **Versioning:** บันทึกประวัติการเปลี่ยนแปลงของ Object ทุกๆ การทับจะกลายเป็น Version ใหม่ การสั่งลบ (Delete) จะสร้าง **Delete Marker** แปะหน้าไว้เฉยๆ สามารถยกเลิกการลบได้โดยลบตัว Delete Marker ทิ้ง
    *   **S3 Replication:** คัดลอกข้อมูล Asynchronously ไปยังปลายทางโดยต้องการให้เปิด Versioning ทั้งต้นทางและปลายทาง
        *   **CRR (Cross-Region Replication):** คัดลอกข้าม Region เพื่อทำ DR หรือกฎหมาย Data Residency
        *   **SRR (Same-Region Replication):** คัดลอกภายใน Region เดียวกันข้ามบัญชีหรือ AZ เพื่อ Object ประสงค์ในการวิเคราะห์แยกส่วนหรือทำ Logging
    *   **Presigned URLs:** ให้สิทธิ์ชั่วคราวแก่ภายนอกในการเขียน (PUT) หรืออ่าน (GET) ผ่าน URL พิเศษที่ลงชื่อโดยสิทธิ์ IAM ของเจ้าของ
    *   **S3 Transfer Acceleration:** อัปโหลดข้อมูลข้ามประเทศได้เร็วขึ้นผ่านการส่งข้อมูลเข้า Edge Location ของ CloudFront แล้ววิ่งบน Private Backbone Network ของ AWS
    *   **S3 Select & Object Lambda:** *S3 Select* ใช้กรองและดึงข้อมูลเฉพาะบางแถว/คอลัมน์ของ CSV, JSON, Parquet โดยใช้คำสั่ง SQL (ประหยัด CPU หลังบ้านและ Bandwidth); *Object Lambda* รันโค้ด Lambda แปลงข้อมูลของ Object เช่น การทำ Masking ข้อมูล PII ทันทีที่มีการร้องขอ (GET)
    *   **Multipart Upload:** อัปโหลด File ขนาดใหญ่แยกเป็นส่วนๆ แบบขนาน แนะนำให้เปิดใช้งานสำหรับ File ที่ใหญ่กว่า 100MB และบังคับใช้กับ File ที่ใหญ่กว่า 5GB

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Unpredictable access patterns / No transition overhead:* -> **S3 Intelligent-Tiering**
    *   *Archive / Long-term compliance / Retrieval within minutes:* -> **Glacier Flexible Retrieval** (Expedited retrieval option)
    *   *Cheapest archive storage / 12-48 hours retrieval:* -> **Glacier Deep Archive**
    *   *Prevent deletion (WORM) / Ransomware protection:* -> **S3 Object Lock (Compliance Mode)**
    *   *Access S3 privately from VPC / No internet:* -> **VPC Gateway Endpoint for S3** (ฟรี ไม่มีค่า Data Transfer)
*   **Trap Warnings:**
    *   *ระวังโจทย์ถามการย้าย Class ใน Lifecycle:* S3 Standard-IA ต้องอยู่ครบ 30 วันก่อนย้ายไป Glacier เสมอ หากย้ายเร็วกว่านั้นจะโดนคิดเงินขั้นต่ำ 30 วัน
    *   *ระวังเรื่อง S3 One Zone-IA:* หากโจทย์บอกว่าห้ามข้อมูลสูญหายเด็ดขาด (No data loss tolerated) ห้ามเลือก One Zone-IA เพราะเก็บใน AZ เดียว
    *   *โจทย์ที่ต้องการความเร็วอัปโหลดข้ามโลก:* ห้ามตอบเพิ่ม Internet สปีด ให้ตอบ **S3 Transfer Acceleration** หรือ **Snowball** หากข้อมูลมีขนาดใหญ่มากๆ (ระดับหลาย TB/PB)

### Real-World Use Cases
*   **Static Website Hosting:** เก็บ File HTML, CSS, JS และรูปภาพบน S3 แล้วเปิด Function Static Web Hosting เพื่อให้บริการเว็บราคาประหยัดและรองรับโหลดได้ไม่จำกัด
*   **Data Lake Storage:** ใช้ S3 Standard หรือ Intelligent-Tiering เป็นถังพักและเก็บข้อมูลขนาดใหญ่ (Raw Data) สำหรับทำ Big Data Pipeline ร่วมกับ Athena และ Glue
*   **Backup & Disaster Recovery:** เก็บ File Backup Database และ Snapshot ต่างๆ ไว้บน S3 Glacier Deep Archive เพื่อให้สอดคล้องกับมาตรฐานกฎหมายไอที

### Architecture Integration Pattern: Static Media Delivery

```text
 [ Client/User ]
        │
        ▼ (1) Request Media (Signed URL)
 ┌──────────────┐
 │  CloudFront  │ ◄───(4) Authenticate via OAC
 └──────┬───────┘
        │
        ▼ (2) Cache Miss (Fetch Origin)
 ┌──────────────┐
 │  S3 Bucket   │ (Private, OAC Policy Allowed)
 └──────────────┘
```
1.  **Client/User** ส่งคำขอเข้าชมรูปภาพหรือวิดีโอผ่าน **Amazon CloudFront (CDN)**
2.  **CloudFront** ตรวจสอบ File ใน Edge Cache หากไม่มี (Cache Miss) จะดึงข้อมูลจาก **Amazon S3 Bucket (Origin)**
3.  เปิดใช้งาน **Origin Access Control (OAC)** บน CloudFront และปิดการเข้าถึงสาธารณะบน S3 เพื่อบังคับให้ผู้ใช้เข้าผ่าน CloudFront เท่านั้น
4.  Script หลังบ้านสามารถสร้าง **S3 Presigned URL** หรือ **CloudFront Signed URL** เพื่อจำกัดสิทธิ์ผู้เข้าชมรูปภาพส่วนบุคคลได้อย่างปลอดภัย

---

## 2. Amazon EBS (Elastic Block Store)

### Deep-Dive Concepts & Sub-features
EBS คือ Block Storage (Virtual Disk) ที่นำไปติดตั้ง (Attach) กับ Instance EC2 โดยตัว EBS จะอยู่ใน AZ เดียวกันกับ Instance เสมอและมีการสำรองข้อมูลภายใน AZ แบบอัตโนมัติ

*   **Volume Types:**
    *   **gp2/gp3 (General Purpose SSD):** สมดุลราคาและประสิทธิภาพ *gp2* ประสิทธิภาพผูกติดกับขนาด (3 IOPS ต่อ GB); *gp3* ออกแบบใหม่ แยกการปรับขนาดพื้นที่เก็บ (GB), IOPS (สูงสุด 16,000) และ Throughput (สูงสุด 1,000 MB/s) ออกจากกันอย่างเป็นอิสระ ช่วยประหยัดค่าใช้จ่ายได้ดีกว่า
    *   **io1/io2/io2 Block Express (Provisioned IOPS SSD):** สำหรับงานที่ต้องการ IOPS สูงมากและ Latency ต่ำเป็นพิเศษ *io2* ทนทานกว่า *io1* ถึง 100 เท่า; *io2 Block Express* รองรับประสิทธิภาพสูงสุดระดับ SAN (สูงสุด 256,000 IOPS) เหมาะกับงาน ERP และ Database ขนาดใหญ่มาก
    *   **st1 (Throughput Optimized HDD):** ฮาร์ดดิสก์ราคาถูก เน้นส่งข้อมูลเร็วเชิงเส้น (Throughput) เหมาะสำหรับ Big Data, MapReduce, Kafka, Log processing (ห้ามนำมาใช้ Boot ระบบ)
    *   **sc1 (Cold HDD):** ราคาถูกที่สุดสำหรับข้อมูลที่ไม่ค่อยได้ใช้งานแต่ยังต้องการแบบ Block Storage (ห้าม Boot ระบบ)
*   **EBS Multi-Attach:** ยอมให้ Instance EC2 สูงสุด 16 ตัวใน AZ เดียวกันเข้าเชื่อมต่อวอลุ่ม EBS (ประเภท io1/io2 เท่านั้น) พร้อมกันได้แบบ Read/Write เหมาะกับ Clustered App ที่เข้าใจการทำ Lock Manager
*   **Fast Snapshot Restore (FSR):** แก้ไขปัญหาคอขวดตอนสร้าง EBS วอลุ่มใหม่จาก Snapshot โดย FSR จะจัดเตรียมทรัพยากรไว้ล่วงหน้าเพื่อให้วอลุ่มใหม่ทำงานด้วยความเร็วสูงสุดทันที (ไม่ต้องมีขั้นตอน Pre-warming/Initialization)

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *High performance Database / Low latency:* -> **EBS Provisioned IOPS (io2 / io2 Block Express)**
    *   *Throughput-heavy / Big Data / MapReduce:* -> **EBS Throughput Optimized HDD (st1)**
    *   *EC2 auto-backup / AMI creation:* -> **Data Lifecycle Manager (DLM)**
    *   *Share EBS volume across multiple instances in the same AZ:* -> **EBS Multi-Attach**
*   **Trap Warnings:**
    *   *ระวังเรื่องขอบเขต AZ:* EBS volume ไม่สามารถแชร์ข้าม AZ ได้ (หากต้องการแชร์ข้าม AZ ต้องใช้ **EFS** หรือคัดลอก Snapshot ของ EBS ไปสร้างใหม่ใน AZ อื่น)
    *   *EBS Snapshot:* Snapshot เก็บใน S3 แบบ Incremental (เก็บเฉพาะส่วนต่าง) เสมอ แต่การลบ Snapshot เก่าจะไม่ทำให้ข้อมูล Snapshot ใหม่หายเพราะระบบจะจัดการย้ายบ Lock ข้อมูลที่จำเป็นไปไว้ตัวล่าสุดให้เองอัตโนมัติ

### Real-World Use Cases
*   **Boot Volume:** ใช้ gp3 เป็น Root วอลุ่มสำหรับรัน OS ของ Web Server
*   **Relational Database Storage:** ติดตั้งระบบ Database SQL Server หรือ MySQL บน EC2 แล้วเชื่อมข้อมูลหลักลงใน EBS Provisioned IOPS (io2) เพื่อความเสถียรและทนทานสูง

### Architecture Integration Pattern: EBS Data Protection

```text
  [ EC2 Instance ] ◄───(Uses KMS Key for Encryption)
        │
        ▼ (1) Write Data
 ┌──────────────┐
 │  EBS Volume  │
 └──────┬───────┘
        │
        ▼ (2) Auto Snapshot via DLM
 ┌──────────────┐
 │ EBS Snapshot │ (Stored in S3)
 └──────┬───────┘
        │
        ▼ (3) Cross-Region Copy
 ┌────────────────────────────────────────────────────────┐
 │ DR Region: Recreate EBS Volume (4) ──► New EC2 Instance │
 └────────────────────────────────────────────────────────┘
```
1.  **EC2 Instance** เขียนข้อมูลลงใน **EBS Volume** ที่เปิดการเข้ารหัส (EBS Encryption) โดยใช้คีย์ใน **AWS KMS**
2.  **AWS Data Lifecycle Manager (DLM)** รันตามช่วงเวลาที่กำหนดเพื่อสร้าง **EBS Snapshot** ลงใน Amazon S3 แบบอัตโนมัติ
3.  หากระบบใน AZ หลักพัง สามารถคัดลอก **EBS Snapshot** ข้ามภูมิภาค (Cross-Region Copy) ไปยัง Region สำรอง
4.  ที่ Region สำรอง ทำการกู้คืน Snapshot ออกมาเป็น **EBS Volume** ตัวใหม่และติดตั้งเข้ากับ **EC2 Instance** ปลายทางเพื่อรันระบบต่อ

---

## 3. Amazon EFS (Elastic File System)

### Deep-Dive Concepts & Sub-features
EFS คือ Shared File System บนเครือข่ายสำหรับ OS Linux (NFSv4) ขยายและหดขนาดได้เองอัตโนมัติ (Elastic) สามารถเชื่อมต่อกับ EC2, ECS, EKS หรือ Lambda ได้พร้อมกันหลายพันตัวข้ามหลาย AZ

*   **Performance & Throughput Modes:**
    *   **Elastic Throughput (แนะนำ):** ปรับขยายกำลังส่งข้อมูลตามปริมาณการใช้งานจริง เหมาะกับงานที่ตรวจจับพฤติกรรมความเร็วการใช้ File ได้ยาก
    *   **Provisioned Throughput:** กำหนดความเร็วในการอ่านเขียนล่วงหน้าโดยไม่ต้องพึ่งพาขนาดเนื้อที่เก็บ
    *   **General Purpose Mode:** Latency ต่ำที่สุด เหมาะสำหรับ Web hosting, CMS (เช่น WordPress)
    *   **Max I/O Mode:** ให้ Throughput และระดับการส่งข้อมูลพร้อมกันสูงสุด ยอมแลกกับ Latency ที่เพิ่มขึ้นเล็กน้อย เหมาะกับ Big Data และงานวิเคราะห์ขนาน
*   **Storage Classes:**
    *   **EFS Standard:** เก็บข้อมูลในหลากหลาย AZ เพื่อความทนทานสูงสุด
    *   **EFS One Zone:** เก็บข้อมูลใน AZ เดียว ประหยัดกว่า Standard 47% เหมาะกับงาน Dev/Test
    *   **Lifecycle Management:** ตั้งให้ย้ายข้อมูลที่ไม่มีการเข้าถึงย้ายไป Class **EFS Infrequent Access (IA)** หรือ **EFS Archive** เพื่อลดต้นทุนสูงสุด 92%

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Shared file system / Linux / POSIX compliant / Thousands of instances:* -> **Amazon EFS**
    *   *Auto-scale file storage / Serverless file system:* -> **Amazon EFS**
    *   *WordPress media uploads / Shared Content Management:* -> **EFS (General Purpose)**
*   **Trap Warnings:**
    *   *ระวังเรื่อง Windows Client:* EFS ไม่รองรับ Protocol SMB ของ Windows (หากโจทย์ระบุว่าใช้ Windows Server ต้องตอบ **FSx for Windows File Server** เท่านั้น)
    *   *ความเร็วประมวลผลสูง:* EFS มี Latency สูงกว่า EBS (EBS เข้าถึงระดับบ Lock ความเร็วระดับมิลลิวินาทีต่ำกว่า EFS) หากเป็นงาน Database ประสิทธิภาพสูง ให้เลือก EBS หรือ RDS แทน EFS

### Real-World Use Cases
*   **WordPress Cluster:** ใช้ EFS เก็บ Folder `wp-content/uploads` ร่วมกันเพื่อให้เครื่อง EC2 ใน Auto Scaling Group หลายๆ AZ สามารถอ่านและเขียนรูปภาพ/ปลั๊กอินเดียวกันได้แบบเรียลไทม์
*   **CI/CD Build Tools:** เก็บ File Source Code และ Artifacts ใน EFS เพื่อให้ Server Jenkins หรือ Build Container หลายตัวดึงไปประมวลผลพร้อมกัน

### Architecture Integration Pattern: Shared Web Content

```text
             [ Users ]
                 │
                 ▼ (1) HTTP/HTTPS Requests
        ┌──────────────────┐
        │  Load Balancer   │ (ALB)
        └────────┬─────────┘
                 │
        ┌────────┼────────┐ (2) Distribute Traffic
        ▼        ▼        ▼
     [ EC2 ]  [ EC2 ]  [ EC2 ]  (Auto Scaling Group - Multi-AZ)
        │        │        │
        └────────┼────────┘ (3) Mount EFS (NFSv4)
                 ▼
        ┌──────────────────┐
        │    Amazon EFS    │ (Shared File System)
        └──────────────────┘
```
1.  **Users** เรียกใช้เว็บผ่าน **Application Load Balancer (ALB)**
2.  ALB กระจายคำขอไปยังเครื่อง **EC2 Web Servers** ที่อยู่ใน **Auto Scaling Group (ASG)** ข้าม 3 Availability Zones
3.  **EC2 ทุกตัว** ทำการเมาท์ไดรฟ์เก็บข้อมูลร่วมกันไปที่ **Amazon EFS** ผ่านเครือข่ายภายในโดยใช้ EFS Mount Target ในแต่ละ AZ
4.  เมื่อมีการอัปเดต File ภาพหรือโค้ดบน EFS โดย Instance ตัวใดตัวหนึ่ง Instance อื่นๆ ทั้งหมดจะเห็น File นั้นทันทีในทันที

---

## 4. Amazon FSx

### Deep-Dive Concepts & Sub-features
FSx คือ Shared File System เชิงพาณิชย์ประสิทธิภาพสูงที่ AWS จัดการดูแลโครงสร้างระบบให้ มีสี่ประเภทหลัก:

1.  **FSx for Windows File Server:**
    *   รองรับ Protocol **SMB** เต็มรูปแบบ ทำงานร่วมกับ **Microsoft Active Directory (AD)** ได้ทั้งแบบ Managed AD บน AWS หรือเชื่อมต่อ AD ของเครื่อง On-premises
    *   รองรับสิทธิ์การเข้าถึง File NTFS และมีฟีเจอร์การลดข้อมูลซ้ำซ้อน (Data Deduplication) เพื่อประหยัดพื้นที่จัดเก็บ
2.  **FSx for Lustre:**
    *   ระบบ File ประสิทธิภาพสูงเป็นพิเศษสำหรับงานคำนวณประมวลผลความเร็วสูง (HPC), Machine Learning, และการประมวลผลวิดีโอ
    *   สามารถเชื่อมโยงตรงเข้ากับ **S3 Bucket** โดยจะดึง File จาก S3 มาประมวลผลแบบ Lazy loading และเขียนผลลัพธ์กลับลง S3 ได้ทันที
3.  **FSx for NetApp ONTAP:**
    *   ยกระบบจัดการของ NetApp (NFS/SMB/iSCSI) มารันบน AWS ช่วยให้ย้ายระบบงานเดิมขึ้นคลาวด์ได้ทันทีโดยคง Function และคำสั่งควบคุมของ NetApp ไว้ครบถ้วน
4.  **FSx for OpenZFS:**
    *   ระบบ File ประสิทธิภาพสูงสำหรับงานย้าย Application ที่พัฒนาบนฐานระบบ OpenZFS บน Linux

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Windows shared storage / SMB / Microsoft Active Directory:* -> **FSx for Windows**
    *   *HPC / High performance computing / Sub-millisecond latency / Connect to S3:* -> **FSx for Lustre**
    *   *NetApp ONTAP migration / Multiprotocol (NFS/SMB/iSCSI):* -> **FSx for NetApp ONTAP**
*   **Trap Warnings:**
    *   *ระวังความสับสนระหว่าง FSx Windows กับ EFS:* หากมีคำว่า "Windows" หรือ "Active Directory" หรือ "SMB" ห้ามเลือก EFS ให้เลือก FSx Windows เท่านั้น

### Real-World Use Cases
*   **Windows User Home Directories:** ใช้ FSx Windows เก็บ File โปร File ส่วนตัวของพนักงานในองค์กรที่ Login ผ่านระบบ Microsoft Active Directory
*   **ML Model Training Pipeline:** ใช้ FSx for Lustre เชื่อมโยงกับ S3 bucket เพื่อป้อนข้อมูลภาพถ่ายขนาดใหญ่เข้า Cluster EC2 GPU (เช่น p3 instances) รันประมวลผลสอน Model ML

### Architecture Integration Pattern: HPC with S3 Link

```text
  ┌──────────────┐
  │  Amazon S3   │ ◄───(1) Raw Data Upload / (4) Export Results
  └──────┬───────┘
         │
         ▼ (2) Direct Data Repository Association
  ┌──────────────┐
  │FSx for Lustre│ (High Performance Cache File System)
  └──────┬───────┘
         │
         ▼ (3) Mount (High Bandwidth / Sub-ms Latency)
  [ EC2 HPC Cluster ]
```
1.  **Raw Data** ขนาดหลายสิบ TB ถูกอัปโหลดเข้ามาเก็บไว้ใน **Amazon S3**
2.  สร้าง **FSx for Lustre** และเชื่อมโยงข้อมูลโดยตรงเข้ากับ S3 Bucket ดังกล่าว (Data Repository Association)
3.  Cluster **EC2 Compute Instances (HPC)** ทำการเมาท์ FSx for Lustre เข้ากับ OS และเริ่มคำนวณด้วย Bandwidth ระดับ Gigabit ต่อวินาที
4.  เมื่อรันคำนวณเสร็จสิ้น ส่งคำสั่ง Export ผลลัพธ์กลับจาก **FSx for Lustre** ลงไปบันทึกถาวรไว้ที่ **Amazon S3** เช่นเดิม

---

## 5. AWS Backup

### Deep-Dive Concepts & Sub-features
AWS Backup คือบริการแบบ Serverless สำหรับบริหารจัดการ สำรองข้อมูล และกู้คืนข้อมูลทรัพยากรของ AWS ทั้งหมดจากจุดศูนย์กลางเดียว (เช่น EBS, RDS, DynamoDB, EFS, FSx, S3, EC2)

*   **Backup Plans:** นโยบายที่ระบุความถี่ในการทำ Backup (เช่น ทุกวัน เวลาเที่ยงคืน), ระยะเวลาสำรองข้อมูล (Retention), Class เก็บข้อมูล (Transition to Cold Storage) และ Region ปลายทางสำหรับการคัดลอกข้ามเขต
*   **Cross-Account & Cross-Region Backup:**
    *   คัดลอกข้อมูลสำรองข้ามบัญชี (Cross-Account) ภายในองค์กร **AWS Organizations** เพื่อป้องกันกรณีบัญชีหลักโดน Ransomware หรือถูกผู้ไม่หวังดีลบข้อมูลทิ้ง
    *   คัดลอกข้ามภูมิภาค (Cross-Region) เพื่อลดความเสี่ยงจากภัยพิบัติทางธรรมชาติระดับพื้นที่
*   **Vault Lock:** บังคับใช้นโยบายความปลอดภัยของที่เก็บข้อมูลสำรอง (Backup Vault) แบบ WORM ป้องกันไม่ให้ Admin หรือแม้กระทั่ง Root User สั่งลบ FileBackup ก่อนกำหนดเวลาที่นโยบายระบุไว้เด็ดขาด

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Centralized backup management / Automate backups across services:* -> **AWS Backup**
    *   *Prevent deletion of backups / Compliance storage:* -> **Backup Vault Lock**
    *   *Backup copy to another AWS account:* -> **AWS Backup (Cross-Account Backup)**
*   **Trap Warnings:**
    *   *ระวังความแตกต่างกับ Snapshot ธรรมดา:* Snapshots ของ EBS/RDS ธรรมดาไม่สามารถสั่ง Lock แบบ WORM ป้องกัน Admin ลบได้เองโดยตรง หากโจทย์ต้องการระบบตรวจสอบความปลอดภัยสูงสุด ให้แนะนำการใช้งาน **AWS Backup Vault Lock**

### Real-World Use Cases
*   **Enterprise Compliance Audits:** ใช้ AWS Backup จัดระบบสำรองข้อมูล Database RDS และระบบ File EFS ทั้งหมดพร้อมทำ Vault Lock ไว้ 7 ปีเพื่อผ่านการตรวจสอบมาตรฐานความปลอดภัยทางการเงิน

---

## 6. Hybrid Storage (AWS Storage Gateway)

### Deep-Dive Concepts & Sub-features
Storage Gateway เชื่อมต่อ Application ฝั่ง On-premises เข้ากับบริการพื้นที่จัดเก็บข้อมูลบน AWS cloud ได้อย่างราบรื่นผ่าน Protocol มาตรฐาน

*   **S3 File Gateway:**
    *   แปลงการเชื่อมต่อ Shared File ระดับโลคอล Protocol **NFS** หรือ **SMB** ไปเก็บเป็น File Object บน **S3 Bucket** แบบตรงไปตรงมา
    *   มี Local Cache บนระบบ On-premises เพื่อให้อ่าน File ยอดนิยมได้ด้วยความเร็วระดับโลคอล Network
*   **FSx File Gateway:**
    *   คล้าย S3 File Gateway แต่เปลี่ยนเป็นเชื่อมต่อไปยังพื้นที่จัดเก็บข้อมูล **FSx for Windows File Server** บนคลาวด์แทน เหมาะสำหรับองค์กรที่ใช้ Windows Client
*   **Volume Gateway:**
    *   ให้บริการเชื่อมต่อผ่าน ProtocolBlock Storage **iSCSI** เพื่อสร้างดิสก์เสมือนให้กับ ServerOn-premises
    *   **Cached Volumes:** เก็บข้อมูลหลักทั้งหมดไว้บน S3 และ Cache เฉพาะข้อมูลที่เรียกใช้บ่อยไว้ในอุปกรณ์ในเครื่อง (เหมาะสำหรับลดการซื้อฮาร์ดแวร์ในบริษัท)
    *   **Stored Volumes:** เก็บข้อมูลทั้งหมดแบบ 100% ไว้ที่ On-premises ของตัวเอง และทำการ Backup ส่งข้อมูลส่วนต่าง (Asynchronous snapshot) ไปเก็บใน S3 เพื่อทำ DR
*   **Tape Gateway:**
    *   ให้บริการระบบจัดเก็บเทปเสมือน (VTL) ผ่าน Protocol iSCSI แทนที่ตู้เก็บเทปกายภาพเดิม ส่งข้อมูลสำรองตรงเข้า S3 Standard, S3 Glacier หรือ S3 Glacier Deep Archive

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *On-premises to S3 via NFS/SMB:* -> **S3 File Gateway**
    *   *iSCSI disk backup to S3 / Low local storage capacity:* -> **Volume Gateway (Cached Mode)**
    *   *On-premises primary storage / Async backup to AWS:* -> **Volume Gateway (Stored Mode)**
    *   *Replace physical tape library:* -> **Tape Gateway (VTL)**
*   **Trap Warnings:**
    *   *ระวังการเลือกประเภท Gateway:* หากโจทย์ระบุว่ามีพื้นที่ฝั่ง On-prem จำกัด ให้เลือก Cached Mode เสมอ; หากโจทย์บอกต้องการเก็บข้อมูลทั้งหมดไว้ที่บริษัท (Data Residency) ให้เลือก Stored Mode

### Real-World Use Cases
*   **Legacy Backup System Migration:** เชื่อมต่อ Software สำรองข้อมูลเดิมในองค์กร (เช่น Veeam) เข้ากับ Tape Gateway ผ่าน iSCSI เพื่อยกเลิกการซื้อเทปฟิสิกส์และส่ง File ไปเก็บใน S3 Glacier Deep Archive แทน

---

## 7. Data Migration & Transfer

### Deep-Dive Concepts & Sub-features
บริการที่ใช้ในการขนย้ายและซิงค์ข้อมูลปริมาณมหาศาลเข้า-ออกคลาวด์ แบ่งเป็นสองโหมด: Offline (ทางกายภาพ) และ Online (ผ่าน Internet)

*   **AWS Snow Family (Offline Data Transfer):** สำหรับพื้นที่ที่ Internet ช้า จำกัด หรือต้องการความปลอดภัยสูงสุด
    *   **Snowcone:** อุปกรณ์ขนาดเล็กที่สุด น้ำหนักเบา (4.5 ปอนด์) ความจุใช้งาน 8TB ทนแรงกระแทก เหมาะกับการพกพาไปในป่า หรือทหารรันประมวลผลขั้นต้น
    *   **Snowball Edge Storage Optimized:** เน้นพื้นที่จัดเก็บข้อมูล (80TB/100TB) สำหรับขนส่งข้อมูลขนาดใหญ่ เช่น วิดีโอ หรือ Database เดิมขึ้น AWS
    *   **Snowball Edge Compute Optimized:** เน้นพลังประมวลผลสูง (vCPUs/GPUs) สำหรับรันงาน Edge Computing, วิเคราะห์ภาพ, หรือทำ ML Inference นอกสถานที่ก่อนส่งอุปกรณ์กลับ
    *   **Snowmobile:** รถบรรทุก Container ความจุสูงสุด 100PB สำหรับการย้ายระบบระดับ Exabyte (ปัจจุบันมักถูกยกเลิกแล้วเน้นใช้ Snowball หลายเครื่องแทน)
*   **Online Transfer Services:**
    *   **AWS DataSync:** ซิงค์ข้อมูลความเร็วสูงอัตโนมัติผ่านเครือข่ายระหว่าง On-premises กับ AWS (S3, EFS, FSx) ทำงานร่วมกับ DataSync Agent ในเครื่องโลคอล
    *   **AWS Transfer Family:** บริการโอนย้าย File ผ่าน Protocol มาตรฐาน **SFTP, FTPS, FTP, AS2** โดยหลังบ้านจะถูกนำไปเก็บเป็น Object ใน S3 หรือ EFS โดยตรงโดยผู้พัฒนาไม่ต้องดูแลระบบหลังบ้านเอง

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *No network bandwidth / Terabytes of data migration:* -> **AWS Snowball Edge**
    *   *Fast online sync / Over the network / On-premises to AWS:* -> **AWS DataSync**
    *   *Exabyte scale migration:* -> **AWS Snowmobile** (หรือ Snowball Edge หลายเครื่อง)
    *   *Migration of SFTP server / Serverless SFTP:* -> **AWS Transfer Family**
*   **Trap Warnings:**
    *   *คำนวณเวลาส่งข้อมูล:* หากโจทย์บอกว่าข้อมูลมีขนาด 100TB และมีคู่สายเน็ตความเร็ว 10Mbps การส่งปกติใช้เวลากว่า 3 ปี ให้เลือก **Snowball Edge** เสมอ
    *   *DataSync vs Storage Gateway:* DataSync ใช้สำหรับการซิงค์หรือย้ายข้อมูลเป็นรอบๆ (One-time or scheduled sync); Storage Gateway ใช้สำหรับเปิดช่องแชร์ File ทำงานแบบต่อเนื่องตลอดเวลา (Continuous hybrid access)

### Real-World Use Cases
*   **Data Center Decommissioning:** ใช้ Snowball Edge Storage Optimized จำนวน 5 เครื่องเพื่อย้ายข้อมูล File ดิบขนาด 400TB จากศูนย์ข้อมูลเดิมที่กำลังจะปิดตัวลง ไปบันทึกถาวรลง S3
*   **Automated Daily Log Sync:** ติดตั้ง DataSync Agent บนเครื่อง Server หลักที่บริษัทเพื่อกวาดส่ง File Log ที่สร้างขึ้นใหม่ทุกวันเวลาเที่ยงคืนส่งตรงเข้า S3 Bucket เพื่อทำ Queue รีวิเคราะห์ผ่าน Athena

### Architecture Integration Pattern: Hybrid File Synchronization

```text
 [ On-premises Storage ]
         │
         ▼ (1) Logs Written
 ┌───────────────┐
 │DataSync Agent │ (VMware/Hyper-V)
 └───────┬───────┘
         │
         ▼ (2) Encrypt & Send (Internet / DX / VPN)
 ┌───────────────┐
 │AWS DataSync Svc│ (On AWS)
 └───────┬───────┘
         │
         ├──────────────────────────┐
         ▼ (3) Store                ▼ (3) Store
 ┌───────────────┐          ┌───────────────┐
 │   Amazon S3   │          │  Amazon EFS   │
 └───────────────┘          └───────────────┘
```
1.  **On-premises Storage** มีการเขียนข้อมูล Log file ใหม่ๆ ทุกชั่วโมง
2.  **AWS DataSync Agent** ที่รันอยู่บน Hypervisor (VMware/Hyper-V) ฝั่ง On-prem ทำการตรวจจับ File ที่เปลี่ยนแปลง
3.  **DataSync Agent** เข้ารหัสข้อมูลและส่งผ่านระบบ Network ออนไลน์ (หรือ VPN/Direct Connect) มายัง **AWS DataSync Service** บน AWS
4.  **AWS DataSync Service** บันทึกข้อมูลลงสู่ **Amazon S3 Bucket** ต้นทางของระบบวิเคราะห์ หรือเก็บเข้า **Amazon EFS** เพื่อให้ App บน EC2 เรียกใช้ต่อ


---

## ➔ การนำทาง (Navigation)
| [« กลับหน้าหลัก (README.md)](../README.md) | **01 Storage & Data Transfer** | [ถัดไป: 02 Compute & Scaling »](02_compute_scaling.md) |