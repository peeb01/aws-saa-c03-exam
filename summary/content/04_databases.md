# 04 Databases - Deep-Dive Exam Cheat-Sheet

---

## 1. Amazon RDS & Aurora

### Deep-Dive Concepts & Sub-features
Amazon RDS (Relational Database Service) และ Aurora คือบริการฐานข้อมูลแบบ SQL (Relational Database) ที่ AWS ดูแลจัดการระบบให้

*   **RDS High Availability & Scaling:**
    *   **Multi-AZ Deployment:** กู้คืนข้อมูลแบบซิงโครนัส (**Synchronous**) ไปยังเครื่องสำรอง (Standby) ใน AZ อื่นแบบเรียลไทม์ หากเครื่องหลัก (Primary) เสียหาย ระบบจะทำ Auto-failover สลับไป Standby ทันที (ไอพีเดิม DNS เดิม) จุดประสงค์เพื่อ **High Availability (HA) & Disaster Recovery (DR)** เท่านั้น (เครื่อง Standby อ่าน/เขียนไม่ได้)
    *   **Multi-AZ DB Cluster:** มี 1 Primary และ 2 Readable Standby Instances (เครื่องสำรองสามารถประมวลผลคำสั่งอ่านข้อมูลได้) ช่วยให้เวลา Failover เร็วกว่าแบบเดิม
    *   **Read Replicas:** คัดลอกข้อมูลแบบอซิงโครนัส (**Asynchronous**) ไปยังเครื่องสำเนารีด เพื่อขยายขีดความสามารถการอ่านข้อมูล (**Read Scaling**) ปลายทางเป็นคนละ DNS กับเครื่องหลัก สามารถทำได้สูงสุด 5 ตัวใน AZ เดียวกัน ข้าม AZ หรือข้ามภูมิภาค (Cross-Region) สามารถยกฐานะ (Promote) ขึ้นเป็นฐานข้อมูลหลักเดี่ยวได้
    *   **RDS Custom:** สำหรับฐานข้อมูลที่ยังต้องใช้สิทธิ์เข้าควบคุมระบบปฏิบัติการด้านล่าง (OS access) เช่น Oracle หรือ SQL Server ที่จำเป็นต้องใช้ฟังก์ชันการตั้งค่าซอฟต์แวร์จำเพาะ
    *   **RDS Proxy:** ระบบคัดแยกบริหารเครือข่ายเชื่อมต่อฐานข้อมูล (Database Connection Pooling) ป้องกันการเปิด Connection ค้างมากเกินไปจากแอปพลิเคชันแบบ Serverless (เช่น Lambda หลายพันฟังก์ชันบูตพร้อมกัน)
*   **Amazon Aurora:**
    *   ฐานข้อมูล SQL ระดับ Enterprise ที่ AWS พัฒนาขึ้นมาโดยแบ่งแยกพื้นที่คำนวณ (Compute) และระบบแชร์ไฟล์จัดเก็บข้อมูล (Storage) ออกจากกัน ข้อมูลจะถูกบันทึก 6 ชุดกระจายใน 3 AZ อัตโนมัติ เร็วกว่า MySQL 5 เท่า และเร็วกว่า PostgreSQL 3 เท่า
    *   **Aurora Serverless v2:** ปรับระดับและขยายหน่วยคำนวณ (ACUs - Aurora Capacity Units) ขึ้นลงอัตโนมัติเป็นวินาทีตามความจำเป็นจริง เหมาะสำหรับแอปพลิเคชันที่มีภาระงานพยากรณ์ความสมดุลได้ยาก
    *   **Aurora Global Database:** ติดตั้งฐานข้อมูลหลักและตัวอ่าน (Read Replica) ข้ามภูมิภาค ซิงค์ข้อมูลข้ามโลกด้วยเน็ตเวิร์กพิเศษภายใน 1 วินาที เหมาะสำหรับการทำ Disaster Recovery (DR) ระดับโลก
    *   **Aurora Write Forwarding:** อนุญาตให้เซิร์ฟเวอร์สำรอง (Read Replicas) ในภูมิภาคอื่นยอมรับคำสั่งเขียนจากไคลเอ็นต์ แล้วส่งคำสั่งเขียนนั้นข้ามกลับมาประมวลผลที่เครื่อง Primary ในภูมิภาคหลักให้โดยอัตโนมัติ

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *High Availability (HA) / Disaster Recovery (DR) / Synchronous:* -> **RDS Multi-AZ**
    *   *Read Scaling / Reporting DB / Asynchronous:* -> **RDS Read Replicas**
    *   *Lambda database connection exhaustion / Connection pooling:* -> **RDS Proxy**
    *   *Enterprise performance SQL / Serverless auto-scaling SQL:* -> **Amazon Aurora / Aurora Serverless v2**
    *   *Global DR database / Sub-second cross-region replication:* -> **Aurora Global Database**
*   **Trap Warnings:**
    *   *Multi-AZ vs Read Replicas:* ในข้อสอบ หากโจทย์บอกว่าต้องการเพิ่มประสิทธิภาพในการประมวลผลคิวรีรายงาน (Reporting performance / Read queries bottleneck) ห้ามตอบ Multi-AZ เพราะ Standby Instance ไม่เปิดให้อ่านข้อมูลเด็ดขาด ต้องเลือกใช้ **Read Replicas** แทน
    *   *Database Connection Limits:* เมื่อมี Lambda เรียกใช้ฐานข้อมูลตรงๆ และฐานข้อมูลล่มเนื่องจาก Connection เต็ม (Too many connections) ให้ตอบ **RDS Proxy** เสมอ

---

## 2. Amazon DynamoDB & Purpose-Built Databases

### Deep-Dive Concepts & Sub-features
*   **Amazon DynamoDB:**
    *   ฐานข้อมูล NoSQL แบบ Key-Value/Document ไร้เซิร์ฟเวอร์ (Serverless) ให้ประสิทธิภาพคงที่ความหน่วงระดับ Single-digit millisecond ในทุกสเกลข้อมูล
    *   **DynamoDB Accelerator (DAX):** แคชแบบ In-memory ชั้นหน้าสำหรับ DynamoDB ช่วยเร่งการดึงข้อมูลอ่านเร็วกว่าเดิม 10 เท่า ลดความหน่วงเหลือระดับไมโครวินาที (**Microseconds**)
    *   **Global Tables:** ฐานข้อมูลแบบ Active-Active ทั่วโลก ซิงค์ข้อมูลสองทิศทางข้ามภูมิภาคอัตโนมัติ เขียนและอ่านที่ Region ไหนก็ได้ใกล้ตัวผู้ใช้
    *   **DynamoDB Streams:** เก็บบันทึกประวัติการเปลี่ยนแปลงของตาราง (Create/Update/Delete) แถวต่อแถวแบบเรียลไทม์ เพื่อกระตุ้นให้ Lambda ทำงานต่อเนื่อง (เช่น ส่งอีเมลยืนยันตัวตนเมื่อข้อมูลถูกเพิ่ม)
    *   **Time to Live (TTL):** กำหนดเวลาสิ้นอายุของแถวข้อมูล เมื่อหมดเวลาระบบ DynamoDB จะสแกนลบข้อมูลนั้นออกถาวรโดยอัตโนมัติเพื่อประหยัดพื้นที่จัดเก็บโดยไม่คิดค่าบริการคำนวณ
    *   **Indexes:**
        *   **LSI (Local Secondary Index):** ใช้ Partition Key เดียวกันกับตารางหลัก แต่เปลี่ยน Sort Key (สร้างได้เฉพาะตอนสร้างตารางเท่านั้น)
        *   **GSI (Global Secondary Index):** เปลี่ยนทั้ง Partition Key และ Sort Key ใหม่ได้อย่างอิสระ (สร้างหรือลบได้ตลอดเวลา)
*   **ElastiCache (In-memory caching):**
    *   **Redis:** รองรับโครงสร้างข้อมูลที่ซับซ้อน (Lists, Sets, Sorted Sets), มีความทนทานของข้อมูล (Persistence), สนับสนุนการทำ Multi-AZ พร้อม Auto-failover
    *   **Memcached:** แคชแบบง่าย (Simple Key-Value), ทำงานแบบ Multi-threaded เน้นการสเกลแชร์ข้อมูลในแนวนอน ไม่มีคุณสมบัติเก็บรักษาข้อมูลถาวร
*   **Purpose-Built Databases:**
    *   **Amazon MemoryDB:** ฐานข้อมูลหลัก (Primary Database) ที่รันด้วยความเร็วของหน่วยความจำ (Redis compatible) ร่วมกับระบบจัดเก็บบันทึกธุรกรรมที่มีความคงทนทนทานสูง
    *   **Amazon DocumentDB:** บริการฐานข้อมูลประเภทเอกสารที่สามารถทำงานร่วมกับเครื่องมือหรือ API ของ **MongoDB** ได้ครบถ้วน
    *   **Amazon Keyspaces:** บริการรองรับฐานข้อมูลสไตล์ Cassandra
    *   **Amazon Neptune:** ฐานข้อมูลกราฟ (Graph Database) เน้นวิเคราะห์ความสัมพันธ์เชื่อมโยงของข้อมูล เช่น เครือข่ายการโกงบัตรเครดิต หรือโซเชียลมีเดีย
    *   **Amazon QLDB (Quantum Ledger Database):** บริการสมุดบันทึกบัญชีแยกประเภทส่วนกลาง ข้อมูลแก้ไขไม่ได้ (**Immutable**) และการันตีประวัติย้อนหลังผ่านการเข้าสิทธิ์เข้ารหัส (Cryptographically verifiable) สำหรับการทำธุรกรรมการเงิน
    *   **Amazon Timestream:** สำหรับงานเก็บข้อมูลอนุกรมเวลาเรียงตามลำดับ (Time-series) เช่น อุปกรณ์ IoT Sensor หรือล็อกพฤติกรรมการคลิกเว็บไซต์

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Serverless NoSQL / Sub-millisecond latency caching:* -> **DynamoDB + DAX**
    *   *Active-Active Multi-Region Database:* -> **DynamoDB Global Tables**
    *   *Auto-delete expired session data / cost savings:* -> **DynamoDB TTL**
    *   *Real-time database change triggers / Trigger Lambda on DB update:* -> **DynamoDB Streams**
    *   *Immutable ledger / Financial audit trail / Cryptographically verifiable:* -> **Amazon QLDB**
    *   *Graph database / Social network mapping:* -> **Amazon Neptune**
    *   *IoT sensor data / Time-series telemetry:* -> **Amazon Timestream**
*   **Trap Warnings:**
    *   *Redis vs Memcached:* หากโจทย์ต้องการฐานข้อมูลแคชธรรมดาที่เน้นสเกลแนวนอนง่ายๆ ไม่มีฟังก์ชันซับซ้อน ให้ตอบ Memcached; หากต้องการฟังก์ชันซับซ้อน จัดหมวดหมู่ได้ ทำประวัติทนทาน หรือทำ HA สลับหน้าเครื่อง ให้ตอบ Redis
    *   *Index Selection:* GSI สามารถสร้างหลังจากตารางหลักรันไปแล้วได้ แต่ LSI ต้องตัดสินใจสร้างพร้อมตารางหลักตอนเริ่มต้นเท่านั้น

### Real-World Use Cases
*   **E-Commerce Session Store:** ใช้ DynamoDB TTL เก็บข้อมูลตะกร้าสินค้าชั่วคราวของลูกค้า โดยระบบจะลบข้อมูลทิ้งอัตโนมัติหากไม่มีความเคลื่อนไหวภายใน 2 ชั่วโมงเพื่อไม่ให้บวมและประหยัดงบ
*   **Database Query Cache:** วาง ElastiCache for Redis ไว้ด้านหน้าฐานข้อมูล RDS PostgreSQL เพื่อเก็บแคชข้อมูลสินค้าหน้าเว็บที่มีการเรียกอ่านซ้ำๆ วันละหลายล้านครั้ง ช่วยให้เซิร์ฟเวอร์ RDS ทำงานได้เบาลง

### Architecture Integration Pattern: Serverless Real-Time Data Processing

```text
  [ Client Application ] ◄───(6) SMS Alert (SNS)
            │
            ▼ (1) Submit Order
  ┌──────────────────┐
  │ Amazon API Gateway│
  └─────────┬────────┘
            │
            ▼ (2) Process
  ┌──────────────────┐      (5) Cache Read (DAX)
  │    AWS Lambda    │ ◄───────────────────────────┐
  └─────────┬────────┘                             │
            │                                      │
            ▼ (3) Write Order                      │
  ┌──────────────────┐                             │
  │ Amazon DynamoDB  │ ◄───────────────────────────┤
  └─────────┬────────┘                             │
            │                                      │
            ▼ (4) Stream changes                   │
  ┌──────────────────┐                             │
  │ DynamoDB Streams │                             │
  └─────────┬────────┘                             │
            │                                      │
            ▼ (5) Trigger Notification             │
  ┌──────────────────┐                             │
  │    AWS Lambda    │ ───► [ Amazon SNS ] ────────┘
  └──────────────────┘
```
1.  **Client Application** ส่งข้อมูลสั่งซื้อสินค้าเข้ามาที่ **Amazon API Gateway**
2.  API Gateway ส่งต่อไปยัง **AWS Lambda** เพื่อตรวจสอบโครงสร้างข้อมูล
3.  Lambda ทำการบันทึกรายการคำสั่งซื้อลงในตาราง **Amazon DynamoDB**
4.  ตารางเปิดใช้งาน **DynamoDB Streams** เมื่อมีข้อมูลใหม่จะไปสั่งงาน **AWS Lambda (Notification Function)** ตัวที่สอง
5.  Lambda ตัวที่สอง ทำการดึงเบอร์โทรของลูกค้าส่งให้ **Amazon SNS** เพื่อส่ง SMS ยืนยันการสั่งซื้อสำเร็จไปยังมือถือของลูกค้าทันที
6.  การสืบค้นข้อมูลใบสั่งซื้อของลูกค้าย้อนหลังด่วน จะถูกดึงผ่าน **DynamoDB Accelerator (DAX)** เพื่อรับค่าความหน่วงในระดับไมโครวินาที


---

## ➔ การนำทาง (Navigation)
| [« กลับหน้าหลัก (README.md)](../README.md) | [« ก่อนหน้า: 03 Networking & Content Delivery](03_networking_content_delivery.md) | [ถัดไป: 05 Security, Identity & Compliance »](05_security_identity_compliance.md) |