# 02 Compute & Scaling - Deep-Dive Exam Cheat-Sheet

---

## 1. Amazon EC2 & AMIs

### Deep-Dive Concepts & Sub-features
Amazon EC2 (Elastic Compute Cloud) คือบริการเซิร์ฟเวอร์เสมือนบนระบบคลาวด์ มีฟีเจอร์สำคัญสำหรับสถาปัตยกรรมดังนี้:

*   **EC2 Instance Families:**
    *   **General Purpose (T/M):** ความคุ้มค่าและสมดุลของ CPU, RAM, Network เหมาะสำหรับเซิร์ฟเวอร์ทั่วไป และเว็บแอปพลิเคชัน
    *   **Compute Optimized (C):** พลังประมวลผล CPU สูงสุด เหมาะกับงานคำนวณซับซ้อน, Batch Processing, High Performance Web
    *   **Memory Optimized (R/X/Z):** แรมขนาดใหญ่พิเศษ สำหรับ In-memory Databases (เช่น Redis), Real-time Big Data Processing
    *   **Accelerated Compute (P/G):** มี GPU สำหรับรันงาน Deep Learning, Graphics Processing, Video Encoding
    *   **Storage Optimized (I/D/H):** มีความเร็วและพื้นที่อ่านเขียนดิสก์สูง (IOPS/Throughput) เหมาะสำหรับ NoSQL Databases (เช่น Cassandra) และ Distributed File Systems
*   **AMI (Amazon Machine Image):** เทมเพลตสำหรับสร้างเครื่อง EC2 ประกอบด้วย OS, แอปพลิเคชัน และสิทธิ์เริ่มต้น
    *   **Public AMIs:** อิมเมจทั่วไปจาก AWS หรือผู้ใช้อื่นเปิดให้ใช้งาน
    *   **Private AMIs:** สร้างและใช้งานส่วนตัวภายในบัญชีตัวเอง
    *   **Marketplace AMIs:** อิมเมจที่มีลิขสิทธิ์ซอฟต์แวร์เชิงพาณิชย์จากภายนอก (เช่น SAP, Check Point)
*   **EC2 User Data:** สคริปต์แบบ Shell หรือ Cloud-init ที่รันเฉพาะการเปิดเครื่อง (Boot) ครั้งแรกเท่านั้นเพื่อลงซอฟต์แวร์หรือตั้งค่าเบื้องต้น
*   **IMDS (Instance Metadata Service):**
    *   **IMDSv1:** ขอข้อมูลเมทาดาต้าของเครื่อง (เช่น IP, Role) ได้ทันทีผ่านคำสั่ง HTTP GET ทั่วไป ไปยังที่อยู่ไอพี `http://169.254.169.254/latest/meta-data/`
    *   **IMDSv2:** เพิ่มความปลอดภัยสูงขึ้นโดยบังคับให้สร้าง Token ผ่าน HTTP PUT ก่อน แล้วนำ Token นั้นไปเรียกข้อมูลผ่าน GET อีกทีเพื่อป้องกันช่องโหว่ SSRF (Server-Side Request Forgery)
*   **EC2 Instance Store:** สตอเรจระดับกายภาพติดกับตัวบอร์ดเซิร์ฟเวอร์ มี Throughput มหาศาลและ IOPS สูงมาก แต่เป็นข้อมูลชั่วคราว (**Ephemeral**) ข้อมูลจะสูญหายถาวรเมื่อเครื่องหยุดทำงาน (Stopped) หรือถูกยกเลิก (Terminated)
*   **Placement Groups:**
    *   **Cluster:** อินสแตนซ์วางอยู่ในแร็คเดียวกันในโซน AZ เดียวกัน เน็ตเวิร์กระหว่างเครื่องมีความเร็วสูงและ Latency ต่ำมาก เหมาะกับงานคำนวณวิทยาศาสตร์ HPC
    *   **Spread:** วางอินสแตนซ์กระจายแร็คฟิสิกส์กันเด็ดขาด (จำกัดสูงสุด 7 เครื่องต่อ AZ) เหมาะกับเครื่องต้องการความน่าเชื่อถือสูงที่ไม่ควรล่มพร้อมกัน
    *   **Partition:** แบ่งเครื่องเป็นกลุ่ม (Partition) กระจายแร็คกัน เหมาะกับแอปกระจายตัวประมวลผลข้อมูล เช่น Big Data Hadoop, Kafka, Cassandra
*   **EC2 Hibernate:** บันทึกข้อมูลในแรม (RAM) ลงในรูทวอลุ่ม EBS แล้วปิดเครื่อง เมื่อเปิดใหม่สถานะแรมจะถูกกู้คืนกลับมาทันที ทำให้เครื่องพร้อมใช้งานอย่างรวดเร็ว (ไม่ต้องบูต OS ใหม่)

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *High Performance Computing (HPC) / Low-latency networking:* -> **Cluster Placement Group**
    *   *Critical instances on separate racks / Minimize hardware failure risk:* -> **Spread Placement Group**
    *   *Hadoop / Kafka / Big data partitions:* -> **Partition Placement Group**
    *   *Metadata security / Prevent SSRF vulnerability:* -> **IMDSv2** (Force Token)
    *   *High IOPS / Temporary storage / Scratch pad:* -> **EC2 Instance Store** (Ephemeral)
*   **Trap Warnings:**
    *   *การกู้คืนข้อมูล Instance Store:* ห้ามใช้ Instance Store เก็บข้อมูลสำคัญที่กู้คืนไม่ได้ หากเครื่องโดนปิด (Stop) ข้อมูลจะหายทันที (ถ้าต้องการข้อมูลทนทานให้ใช้ EBS)
    *   *Hibernate requirement:* การเปิด Hibernate รูทวอลุ่มต้องเป็น **EBS** และถูกเข้ารหัส (Encrypted) เสมอ และพื้นที่เก็บต้องใหญ่พอรองรับขนาดแรมของเครื่อง

---

## 2. EC2 Pricing Models

### Deep-Dive Concepts & Sub-features
การประเมินราคา EC2 เพื่อเปรียบเทียบในข้อสอบประกอบด้วย:

*   **On-Demand:** จ่ายตามจริงรายวินาที/นาที ไม่ผูกมัด เหมาะกับแอปเริ่มพัฒนา หรือทราฟฟิกพยากรณ์ไม่ได้
*   **Spot Instances:** ใช้ส่วนเกินของ AWS ประหยัดสูงสุด 90% แต่ AWS สามารถส่งสัญญาณขอเครื่องคืนล่วงหน้าได้ **2 นาที** (Spot Instance Interruption Notice)
*   **Spot Fleet:** ชุดกลุ่มที่รวม Spot และ On-demand เข้าด้วยกันอัตโนมัติเพื่อให้ได้ราคาและเป้าหมายกำลังคำนวณตามงบประมาณที่กำหนดไว้
*   **Reserved Instances (RI):** สัญญาจองใช้งาน 1 หรือ 3 ปี แลกส่วนลดสูงสุด 72%
    *   **Standard RI:** ลดเยอะสุด เปลี่ยนขนาดอินสแตนซ์ได้ในตระกูลเดียวกัน ห้ามเปลี่ยนภูมิภาคหรือข้ามตระกูล
    *   **Convertible RI:** ยืดหยุ่นกว่า เปลี่ยนข้ามตระกูลระบบปฏิบัติการ หรือข้ามประเภทได้โดยจ่ายค่าส่วนต่าง
*   **Savings Plans:** สัญญาจ่ายเงินเป็นยอดเงินคงที่รายชั่วโมง (เช่น จ่ายขั้นต่ำ $10/ชั่วโมง) รองรับข้ามบริการคำนวณ (EC2, Fargate, Lambda) ยืดหยุ่นสูงสุดในปัจจุบัน
*   **Dedicated Instances:** รันเครื่องบนฮาร์ดแวร์เฉพาะของผู้ใช้ แต่เครื่องอื่นๆ ในบัญชีเดียวกันยังสามารถใช้โฮสต์กายภาพร่วมกันได้
*   **Dedicated Hosts:** เช่าเครื่องเซิร์ฟเวอร์กายภาพเดี่ยวแบบผูกขาด มีความต้องการด้าน Licensing (BYOL - Bring Your Own License) หรือตอบโจทย์กฎระเบียบบริษัทที่มีความต้องการการเข้าถึงซอฟต์แวร์ในระดับ Core/Socket

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Bring Your Own License (BYOL) / Physical server isolation:* -> **Dedicated Hosts**
    *   *Cheapest compute / Batch jobs / Stateless workloads / Fault-tolerant:* -> **Spot Instances**
    *   *Consistent workload / Commitment for 1-3 years / Flexible compute:* -> **Compute Savings Plans**
*   **Trap Warnings:**
    *   *ความแตกต่างระหว่าง Dedicated Instance และ Dedicated Host:* ให้จำว่า **Dedicated Host** จะได้สิทธิ์ควบคุมและเห็นเครื่องกายภาพในระดับ Hardware Socket/Core สำหรับลงสิทธิ์ซอฟต์แวร์เดิม (License-bound) ส่วน Dedicated Instance แค่แยกไม่ให้มีผู้ใช้บัญชีอื่นมาร่วมรันเครื่องกายภาพด้วยเฉยๆ

---

## 3. Auto Scaling Group (ASG)

### Deep-Dive Concepts & Sub-features
Auto Scaling Group (ASG) ดูแลจัดการเพิ่มหรือลดจำนวนเครื่อง EC2 อัตโนมัติตามความต้องการของระบบ

*   **Scaling Policies:**
    *   **Target Tracking Scaling:** ปรับระดับตามระดับเกณฑ์ Metric ตัวอย่างเช่น พยายามรักษาระดับการใช้งาน CPU ให้อยู่ที่ 50% ตลอดเวลา
    *   **Step Scaling:** ขยายขนาดแบบเป็นขั้นตามความรุนแรง เช่น หากใช้ CPU > 70% ให้เพิ่มเครื่อง 2 เครื่อง, หาก CPU > 85% ให้เพิ่มอีก 4 เครื่อง
    *   **Simple Scaling:** ปรับระดับด้วยค่าคงที่ชุดเดียวทันทีเมื่อมีสัญญาณเตือน จากนั้นต้องรอพ้นช่วงพักตัว (**Cooldown Period**) ก่อนประมวลผลเพิ่มอีกรอบ
    *   **Predictive Scaling:** ใช้ปัญญาประดิษฐ์ (ML) วิเคราะห์แนวโน้มประวัติการใช้งานย้อนหลังเพื่อทำการเตรียมเครื่องขยายรอไว้ล่วงหน้าก่อนทราฟฟิกจริงจะมาถึง
    *   **Scheduled Scaling:** ตั้งค่าปรับขยายระบบล่วงหน้าตามวันเวลาที่แน่นอน เช่น ทุกบ่ายวันศุกร์เวลา 15:00 น.
*   **Launch Templates vs Launch Configurations:**
    *   *Launch Configurations:* ระบบรุ่นเดิม (Deprecated) ตั้งค่าได้เวอร์ชันเดียว แก้ไขไม่ได้
    *   *Launch Templates (แนะนำ):* รองรับการจัดเก็บการตั้งค่าเวอร์ชันที่หลากหลาย (Versioning), รองรับการระบุการใช้ Spot และ On-demand ผสมกัน, รองรับฟังก์ชันใหม่ๆ ของ EC2
*   **Warm Pools:** จัดเตรียมอินสแตนซ์ที่บูต OS และติดตั้งโปรแกรมเสร็จแล้ว รอไว้ในสถานะพัก (Stopped) เพื่อช่วยย่นเวลาการขยายเครื่องใหม่กะทันหันแทนที่จะต้องบูตเริ่มใหม่ตั้งแต่ต้น
*   **Lifecycle Hooks:** บังคับให้การทำงานช่วงเปิดเครื่อง (Pending) หรือปิดเครื่อง (Terminating) หยุดลงชั่วคราวเพื่อส่งคำสั่งไปที่ Lambda เพื่อรันเตรียมระบบ เช่น ดึงการตั้งค่า, แบ็กอัปข้อมูลล็อกลง S3 ก่อนเครื่องปิดไปจริงๆ
*   **Instance Refresh:** ทยอยอัปเดตเครื่องในกลุ่มเป็นเวอร์ชัน AMI ใหม่แบบอัตโนมัติ (Rolling Update) โดยไม่รบกวนการทำงานหลักของระบบ

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Predictable daily spike / Friday evening traffic:* -> **Scheduled Scaling**
    *   *Automate server updates without downtime:* -> **ASG Instance Refresh**
    *   *Reduce initialization time / Boot speed:* -> **Warm Pools**
    *   *Run custom scripts on shutdown / Backup logs before termination:* -> **ASG Lifecycle Hooks**
*   **Trap Warnings:**
    *   *Cooldown Period:* หากโจทย์บอกว่าระบบเพิ่มเครื่องบ่อยเกินไปจนเกินงบประมาณ (Rapid scaling fluctuation) ให้แก้ไขโดยเพิ่มระยะเวลา Cooldown Period เพื่อให้เครื่องใหม่ที่เพิ่มเข้าไปช่วยเบาแรงและระบบนิ่งลงก่อนจะส่งสัญญานขยายเครื่องถัดไป

---

## 4. AWS Lambda & Serverless Compute

### Deep-Dive Concepts & Sub-features
AWS Lambda คือบริการประมวลผลแบบ Serverless (FaaS) รันโค้ดเมื่อมี Event มากระตุ้นการทำงานโดยปลายทางไม่ต้องควบคุมดูแลโครงสร้างเซิร์ฟเวอร์ด้านหลังเลย

*   **Lambda Limits & Parameters:**
    *   **Execution Timeout:** รันได้สูงสุด **15 นาที** ต่อหนึ่งการทำงาน (ห้ามใช้ Lambda สำหรับประมวลผลที่กินเวลานานเกินนี้)
    *   **Temporary Storage (/tmp):** พื้นที่เก็บข้อมูลชั่วคราวในโฟลเดอร์ `/tmp` ขยายได้ตั้งแต่ 512MB สูงสุดถึง **10GB**
    *   **Memory allocation:** เลือกแรมได้ตั้งแต่ 128MB ถึง 10GB โดย CPU จะปรับเพิ่มอัตโนมัติตามอัตราแรมที่เลือกไว้
*   **Lambda Features:**
    *   **Lambda Layers:** ไฟล์ ZIP ที่บรรจุ Dependencies, Library หรือสคริปต์พิเศษที่นำมาเมาท์เพิ่มเข้าไปในตัว Lambda ทำให้แชร์โค้ดข้ามฟังก์ชันและลดขนาดแพ็กเกจหลักได้
    *   **Provisioned Concurrency:** บูตตัวรันฟังก์ชันเตรียมไว้ล่วงหน้าเพื่อขจัดปัญหาหน่วงเวลาเปิดฟังก์ชันครั้งแรก (**Cold Start**) เหมาะกับระบบหน้าบ้านที่ต้องการ Latency ต่ำสม่ำเสมอ
    *   **SnapStart:** ออกแบบมาสำหรับ Java รันไทม์ โดยจะถ่ายภาพ Snapshot ของสภาวะแอปพลิเคชันที่บูตเริ่มต้นเสร็จแล้วเก็บไว้ ทำให้ครั้งถัดไปเปิดพร้อมทำงานได้เร็วขึ้นแบบไม่มีปัญหา Cold Start
    *   **Function URLs:** ให้ที่อยู่ไอพีเชื่อมต่อ HTTPS ปลายทางโดยตรงกับฟังก์ชัน Lambda ทำให้ประหยัดค่าใช้จ่ายและเวลาในการเปิด API Gateway ร่วมด้วย
    *   **Lambda in VPC:** ตามปกติ Lambda จะไม่อยู่ในซับเน็ตส่วนตัว หากต้องการให้ Lambda สามารถต่อฐานข้อมูล (เช่น RDS Multi-AZ) ภายใน Private Subnet ต้องเลือกเปิด VPC Configuration บนฟังก์ชัน ซึ่งปัจจุบันรันผ่านกลไกความเร็วสูงอย่าง Hyperplane ENI

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *No server management / Event-driven compute:* -> **AWS Lambda**
    *   *Eliminate Lambda cold start / Java execution latency:* -> **Provisioned Concurrency** / **SnapStart**
    *   *Direct HTTPS endpoint / Cheap microservice without API Gateway:* -> **Lambda Function URLs**
    *   *Long execution time (> 15 mins):* -> ห้ามใช้ Lambda ให้เลือกใช้ **AWS Fargate** หรือ **AWS Batch** แทน
*   **Trap Warnings:**
    *   *Lambda ใน VPC:* การให้ Lambda ต่อใน VPC ไม่ได้เปิดโอกาสให้มันออกสู่อินเทอร์เน็ตสาธารณะได้เอง หาก Lambda ตัวนั้นยังต้องการออกอินเทอร์เน็ตเพื่อต่อ API ภายนอก จะต้องเซ็ตเส้นทางซับเน็ตนั้นให้วิ่งผ่าน **NAT Gateway** ไปยังอินเทอร์เน็ตเท่านั้น

---

## 5. AWS Batch & Elastic Beanstalk

### Deep-Dive Concepts & Sub-features
*   **AWS Batch:**
    *   บริการจัดการและวางลำดับรันงานประมวลผลชุดคำสั่งแบบ Batch (Batch Processing) ในสเกลขนาดใหญ่ในรูปแบบ Container
    *   **Job Queues:** คิวรับเรียงส่งงานตามลำดับความต้องการ
    *   **Compute Environments:** จัดการระดับการประมวลผลโดยเลือกขนาดและควบคุมประเภทเครื่องรัน (Fargate, EC2, หรือ Spot Instances) ให้ปรับหดขึ้นลงอัตโนมัติตามจำนวนคิวงานที่ค้างอยู่
*   **AWS Elastic Beanstalk:**
    *   แพลตฟอร์มพร้อมรันแบบ PaaS ผู้พัฒนาอัปโหลดเฉพาะ Source code แอปพลิเคชัน (เช่น Node.js, Python, Java) จากนั้น Beanstalk จะจัดแจงเตรียมของ (EC2, ASG, Load Balancer, Databases) ให้เองและคอยอัปเดตแพตช์ของแพลตฟอร์มระบบปฏิบัติการให้
    *   **Deployment Strategies:**
        *   **All at Once:** อัปเดตทุกเครื่องพร้อมกัน มีราคาประหยัดและเร็วที่สุด แต่จะเกิดปัญหา **Downtime** ชั่วคราวระหว่างลงโปรแกรมใหม่
        *   **Rolling:** ทยอยอัปเดตเครื่องทีละชุด (เช่น ทีละ 2 เครื่อง) ไม่เกิดภัยระบบล่มทั้งหมด (No Downtime) แต่กำลังการให้บริการรวมจะลดลงชั่วคราวระหว่างทำงาน
        *   **Rolling with Additional Batch:** เปิดเครื่องใหม่มารองรับการทยอยอัปเดต ทำให้มีจำนวนเครื่องให้บริการรวมคงที่ 100% ตลอดเวลาการดีพลอย
        *   **Immutable:** เปิดเครื่องเซิร์ฟเวอร์กลุ่มใหม่ทั้งหมดขนานกันมาทดสอบเวอร์ชันใหม่ เมื่อเสร็จและผ่านจึงย้ายทราฟฟิกมา และลบเครื่องเก่าทิ้ง ปลอดภัยสุด ย้อนกลับง่าย
        *   **Blue/Green:** สร้างสภาพแวดล้อมใหม่ขึ้นมาแยกกันอย่างชัดเจน เมื่อเวอร์ชันใหม่สมบูรณ์จึงสลับเส้นทางผู้ใช้ผ่านการปรับค่า DNS CNAME หรือ Route 53

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *PaaS / Focus on code / Automate infrastructure provisioning:* -> **AWS Elastic Beanstalk**
    *   *Run containerized batch jobs / Dynamic scaling of batch clusters:* -> **AWS Batch**
    *   *Immutable deployment / Clean environment rollbacks:* -> **Beanstalk Immutable** หรือ **Blue/Green**
*   **Trap Warnings:**
    *   *Beanstalk Deployments สำหรับงาน Database:* ไม่แนะนำให้เปิดและผูกฐานข้อมูล (RDS) ไว้ภายในสภาพแวดล้อม Beanstalk โดยตรงสำหรับการรันใน Production เพราะการลบหรือสลับ Environment อาจทำลายฐานข้อมูลจริงนั้นได้ (ทางแก้คือสร้างฐานข้อมูลภายนอกแล้วใช้ Environment Properties ส่งสิทธิ์การเชื่อมต่อเข้าไป)

### Real-World Use Cases
*   **Elastic Beanstalk Web Application:** อัปโหลดโปรแกรม PHP ของบล็อกบริษัทขึ้น Beanstalk รันโดยระบบ ALB และ ASG อัตโนมัติ ปล่อยให้ระบบจัดการดูแล OS Patching และ Auto Scaling เองโดยแอดมินไม่ต้องเขียนสคริปต์
*   **AWS Batch Image Processing:** ระบบอัปโหลดรูปภาพใบเสร็จเข้าระบบ S3 แล้วมี Event ไปเพิ่มงานในคิว AWS Batch จากนั้น AWS Batch จะขยายเครื่อง Spot EC2 จำนวนมากมารันตัวประมวลผลแปลงรูปภาพเป็นข้อมูลตัวอักษรแล้วเซฟเก็บฐานข้อมูลก่อนจะปิดเครื่องทิ้งเพื่อความประหยัด

### Architecture Integration Pattern: Serverless Ingestion & Batch Processing

```text
   [ Mobile Client ]
           │
           ▼ (1) Upload Video
   ┌───────────────┐
   │ Amazon S3 Buc │ (Upload Bucket)
   └───────┬───────┘
           │
           ▼ (2) Event Notification (S3 PUT)
   ┌───────────────┐
   │  AWS Lambda   │
   └───────┬───────┘
           │
           ▼ (3) Submit Job
   ┌───────────────┐
   │AWS Batch Queue│
   └───────┬───────┘
           │
           ▼ (4) Process Job (Read Upload / Write Output)
   ┌───────────────┐
   │  AWS Fargate  │ ◄───► [ S3 Output Bucket ]
   └───────────────┘ (Serverless Container)
```
1.  **Mobile Client** อัปโหลดไฟล์วิดีโอดิบไปยัง **Amazon S3 Bucket**
2.  **S3 Event Notification** ดักความเคลื่อนไหวอัปโหลดเสร็จแล้วทำการเรียกใช้งานฟังก์ชัน **AWS Lambda**
3.  **AWS Lambda** ทำการแปลงค่าความต้องการและทำการส่งคำสั่งรันงานต่อไปยังระบบจัดคิวของ **AWS Batch Job Queue**
4.  **AWS Batch** สั่งการและขยายจำนวนเครื่องประมวลผลประเภท **Fargate (Serverless Container)** เข้ามาหยิบไฟล์วิดีโอนั้นจาก S3 มาประมวลผลตัดต่อ และเซฟวิดีโอผลลัพธ์กลับไปเก็บบน S3 ปลายทางเพื่อส่งมอบให้ลูกค้าต่อไป
