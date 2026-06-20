# 06 Analytics & Data Integration - Deep-Dive Exam Cheat-Sheet

---

## 1. Data Analytics & Warehousing

### Deep-Dive Concepts & Sub-features
บริการวิเคราะห์ประมวลผลข้อมูลระดับวิสาหกิจ (Enterprise Data Analytics) และระบบคลังข้อมูล (Data Warehouse)

*   **Amazon Redshift (Relational OLAP):**
    *   บริการคลังข้อมูลวิเคราะห์ประสิทธิภาพสูงระดับ Petabyte ทำงานในสไตล์คอลัมน์ (**Columnar Storage**) เพื่อความเร็วในการรันคำสั่งวิเคราะห์ทางคณิตศาสตร์แบบรวมผล (Aggregations)
    *   **RA3 Nodes:** โครงสร้างที่ช่วยให้ผู้ใช้สามารถปรับขนาดพลังประมวลผล (Compute) และสตอเรจจัดเก็บไฟล์แยกกันอย่างเป็นอิสระ โดยดึงข้อมูลประวัติเย็นลงไปแช่เก็บใน S3 เพื่อความประหยัด
    *   **Redshift Spectrum:** ความสามารถยิงคิวรี SQL ไปยังข้อมูลดิบที่เก็บอยู่ใน S3 Data Lake ได้โดยตรงโดยไม่ต้อง Import ข้อมูลเข้ามาเก็บในตัว Redshift
    *   **Concurrency Scaling:** เพิ่มคลัสเตอร์ระบบคำนวณรองรับแบบอัตโนมัติเมื่อเกิดสภาวะผู้ใช้งานยิงคำสั่งคิวรีวิเคราะห์หนาแน่นพร้อมกันโดยไม่ต้องจัดตั้งเครื่องรอไว้
    *   **Redshift Serverless:** คิวรีวิเคราะห์ได้โดยไม่ต้องตั้งคลัสเตอร์เครื่องเซิร์ฟเวอร์ จ่ายเงินตามปริมาณทรัพยากรคำนวณที่ใช้รันงานจริง (RPU-hours)
    *   **Redshift Data Sharing:** สิทธิพิเศษในการแชร์ตารางข้อมูลภายใน Redshift ข้ามคลัสเตอร์หรือบัญชีโดยไม่มีการคัดลอกไฟล์ข้อมูลซ้ำซ้อน
*   **Amazon Athena:**
    *   บริการคิวรีข้อมูลบน S3 โดยใช้ภาษา SQL แบบ **Serverless** 100% คิดเงินตามขนาดข้อมูลที่สแกนอ่านไฟล์ผ่านคำสั่ง
    *   **Athena Federated Query:** ความสามารถเขียน SQL บน Athena เพื่อข้ามไปดึงและรวมผลข้อมูลจากระบบเก็บข้อมูลอื่นที่ไม่ใช่ S3 (เช่น RDS, DynamoDB, DocumentDB) ผ่านการแปลงข้ามของ AWS Lambda
    *   **Athena Workgroups & Notebooks:** *Workgroups* ใช้แบ่งสิทธิ์จำกัดงบปริมาณสแกนข้อมูลแยกทีม; *Notebooks* เขียน Python/Jupyter notebook รันคู่กับคิวรี
*   **AWS Glue:**
    *   บริการเตรียมข้อมูล ย้ายรูปแบบข้อมูลประมวลผล (ETL - Extract, Transform, Load) แบบ Serverless
    *   **Glue Data Catalog:** คลังรวบรวมจัดเก็บคำนิยามโครงสร้าง Metadata ของตารางข้อมูลทั้งหมดของ Data Lake
    *   **Glue Crawlers:** ระบบสแกนประเภทโครงสร้างฐานข้อมูล ค้นหาความเคลื่อนไหว และจำแนกประเภทไฟล์ใน S3 เพื่อนำโครงสร้างนั้นไปบันทึกลงใน Data Catalog อัตโนมัติ
    *   **Glue DataBrew & Studio:** *DataBrew* ใช้ทำความสะอาดและจัดระเบียบข้อมูล (Visual data preparation) โดยไม่ต้องเขียนสคริปต์; *Studio* สถาปัตยกรรมลากวางเส้นทางเชื่อมต่อ ETL
*   **AWS Lake Formation:** ระบบช่วยจัดตั้ง ดูแล และกำหนดระดับความปลอดภัยระดับแถวและคอลัมน์ (Row/Column-level security) ให้กับโครงสร้าง Data Lake
*   **Amazon EMR (Elastic MapReduce):** คลัสเตอร์จัดการเครื่องประมวลผลขนาดใหญ่สำหรับรองรับเครื่องมืออย่าง Apache Spark, Hadoop, Hive, Presto สำหรับการทำ Big Data หรือคำนวณซับซ้อนขนานกันจำนวนมาก
*   **Amazon QuickSight:** บริการสร้างแดชบอร์ดจำลองภาพ (BI Visualization) ประสิทธิภาพสูง รองรับการเข้าถึงข้อมูลจากระบบเก็บไฟล์ของ AWS และออนพรีมิส

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Serverless SQL queries on S3 files:* -> **Amazon Athena**
    *   *Format CSV/JSON on S3 into tables / Build schema registry:* -> **AWS Glue Crawler + Data Catalog**
    *   *OLAP / Columnar storage / Enterprise Data Warehouse:* -> **Amazon Redshift**
    *   *Query S3 data lake using Redshift cluster:* -> **Redshift Spectrum**
    *   *Hadoop / Spark / Managed big data cluster:* -> **Amazon EMR**
*   **Trap Warnings:**
    *   *Athena vs Redshift:* ในข้อสอบ หากโจทย์ต้องการวิเคราะห์คิวรีแบบด่วนเป็นครั้งคราว (Ad-hoc query) บนไฟล์ดิบใน S3 โดยไม่ต้องจัดการ Infrastructure ให้ตอบ **Athena**; หากเป็นงานสร้างรายงานธุรกิจที่มีความสัมพันธ์สเกลใหญ่ มีการทำ Joins ซับซ้อนต้องการ Latency สม่ำเสมอผ่านระบบคลังข้อมูลหลัก ให้ตอบ **Redshift**
    *   *Athena Cost Optimization:* วิธีลดค่าใช้จ่ายการใช้งาน Athena ที่ดีที่สุดคือจัดเก็บข้อมูลแบบบีบอัดฟอร์แมตที่เป็น Columnar เช่น **Apache Parquet** หรือ ORC และจัดแบ่งพาร์ทิชันโฟลเดอร์ใน S3 (Partitioning) เพื่อลดขนาดการสแกนไฟล์ผ่านคิวรี

---

## 2. Streaming & Pipelines

### Deep-Dive Concepts & Sub-features
*   **Amazon Kinesis:** แพลตฟอร์มจัดการรวบรวมข้อมูลสตรีมมิ่งความเร็วสูงระดับเรียลไทม์
    *   **Kinesis Data Streams:**
        *   บริการรับส่งข้อมูลสตรีมมิ่งสด มี Latency ต่ำมากในระดับมิลลิวินาที (1-200ms)
        *   แบ่งสเกลกำลังงานด้วยจำนวนช่องสัญญาณ (**Shards**) โดย 1 Shard รองรับ Ingest ข้อมูลได้ 1MB/s หรือ 1,000 write records/s และรองรับการดึงข้อมูลออก 2MB/s
        *   ข้อมูลสามารถคงค้างรอประมวลผลใน Stream ได้ตั้งแต่ 24 ชั่วโมง ถึง 365 วัน
    *   **Kinesis Data Firehose (Delivery):**
        *   บริการ Serverless สำหรับกวาดรวบรวมข้อมูลสตรีมมิ่งส่งตรงไปบันทึกปลายทาง เช่น S3, Redshift, OpenSearch หรือโปรแกรมวิเคราะห์ข้อมูลภายนอก (เช่น Datadog, New Relic)
        *   สามารถแปลงรูปแบบไฟล์ได้อัตโนมัติขณะไหลผ่าน (เช่น แปลง JSON -> Parquet) และมี Latency ระดับนาที (เนื่องจากต้องรอรวบรวมบัฟเฟอร์ข้อมูลตามขนาดหรือช่วงเวลาขั้นต่ำ 60 วินาทีก่อนส่งมอบ)
    *   **Kinesis Data Analytics:** ช่วยให้สามารถเขียนคำสั่งภาษา SQL หรือโค้ด Apache Flink สืบคืนกรองวิเคราะห์ข้อมูลที่ไหลสดอยู่ใน Kinesis Streams ได้เรียลไทม์
    *   **Kinesis Video Streams:** สำหรับดูดข้อมูลวิดีโอจากกล้องโทรทัศน์หรืออุปกรณ์เคลื่อนที่เพื่อนำไปประมวลผลวิเคราะห์ระดับวิดีโอ (เช่น ตรวจสอบความปลอดภัย หรือสอน AI)
*   **Amazon MSK (Managed Streaming for Apache Kafka):** บริการบริหารจัดการแพลตฟอร์มรับส่งสตรีมมิ่งขนาดใหญ่ของ Apache Kafka แบบสมบูรณ์แบบโดยไม่ต้องดูแลโครงสร้างเซิร์ฟเวอร์ด้านหลัง เหมาะกับองค์กรที่ต้องการย้ายสถาปัตยกรรม Kafka เดิมมาคลาวด์โดยไม่ต้องการแก้โค้ดโปรแกรม
*   **Amazon OpenSearch Service:** บริการค้นหา คอยจัดหมวดหมู่ และสืบค้นล็อกการทำงานต่างๆ ในระดับบิ๊กดาต้า (ต่อยอดมาจาก Elasticsearch) ทำงานร่วมกับแดชบอร์ดดูภาพสรุปอย่าง OpenSearch Dashboards (Kibana)

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Real-time data ingestion / Millisecond latency / Shard scaling:* -> **Kinesis Data Streams**
    *   *Near real-time streaming / Load data directly into S3/Redshift / Serverless delivery:* -> **Kinesis Data Firehose**
    *   *Stream analysis using SQL/Flink:* -> **Kinesis Data Analytics**
    *   *Managed Apache Kafka cluster / Migration from Kafka:* -> **Amazon MSK**
    *   *Log search engine / Operational analytics / Kibana visualization:* -> **Amazon OpenSearch Service**
*   **Trap Warnings:**
    *   *Real-time vs Near Real-time:* หากโจทย์ต้องการระบบส่งข้อมูลที่มีความเร็วสูงสุดระดับมิลลิวินาที (Real-time milliseconds) ให้เลือก Kinesis Data Streams; หากเป็นงานดักโหลดข้อมูลกวาดไฟล์ลง Data Lake เป็นรอบๆ (Near real-time / Buffer limits / 60 seconds) ให้เลือก Kinesis Data Firehose

### Real-World Use Cases
*   **IoT Smart Meter Pipeline:** เซ็นเซอร์วัดไฟฟ้าล้านตัวทั่วเมืองยิงทราฟฟิกข้อมูลขนาดเล็กทุกวินาที ข้อมูลวิ่งเข้าสู่ Kinesis Data Streams ขนานกันในระดับหลายร้อย Shard จากนั้นมีระบบ Kinesis Data Analytics กรองเฝ้าระวังหาบ้านที่มีระดับการใช้ไฟสไปก์แรงผิดปกติเพื่อส่งสัญญาณเตือนภัย

### Architecture Integration Pattern: Real-Time Log Analytics Pipeline

```text
 [ EC2 App Servers ] (Kinesis Agent)
          │
          ▼ (1) Stream Logs
 ┌─────────────────┐
 │ Kinesis Streams │ (Ingestion - Milliseconds)
 └────────┬────────┘
          │
          ▼ (2) Buffer & Compress
 ┌─────────────────┐
 │ Kinesis Firehose│ (Delivery - Serverless)
 └────────┬────────┘
          │
          ├────────────────────────┐
          ▼ (3) Delivery           ▼ (3) Delivery
 ┌─────────────────┐      ┌─────────────────┐
 │ Amazon S3 Lake  │      │ OpenSearch Svc  │ (Search & Dashboard)
 └────────┬────────┘      └─────────────────┘
          │
          ▼ (4) Query SQL
 ┌─────────────────┐
 │  Amazon Athena  │
 └─────────────────┘
```
1.  **Application Servers (EC2)** ติดตั้ง Kinesis Agent คอยดึงไฟล์ Log ยิงสดเข้าสู่ **Kinesis Data Streams**
2.  ท่อ Streams ขยายช่องสัญญาณกระจายข้อมูลส่งต่อให้ **Kinesis Data Firehose (Delivery Stream)**
3.  Kinesis Data Firehose แปลงรูปแบบไฟล์และรวมบัฟเฟอร์ขนาดไฟล์ จากนั้นส่งมอบลงไปจัดเก็บบันทึกถาวรลงใน **Amazon S3 Data Lake**
4.  ขณะเดียวกัน Firehose ทำการสำเนารายการข้อมูลส่งต่อไปยัง **Amazon OpenSearch Service** เพื่อให้ผู้ดูแลระบบสามารถค้นหาตรวจสอบเหตุการณ์ผิดปกติผ่านแดชบอร์ดหน้าจอของ OpenSearch Dashboards ได้แบบสะดวกสบาย
5.  ผู้ใช้ระดับวิเคราะห์สามารถรันคำสั่งภาษา SQL ด้วย **Amazon Athena** ค้นหาข้อมูลเชิงลึกย้อนหลังจากระบบไฟล์ S3 Data Lake ได้โดยตรง


---

## ➔ การนำทาง (Navigation)
| [« กลับหน้าหลัก (README.md)](../README.md) | [« ก่อนหน้า: 05 Security, Identity & Compliance](05_security_identity_compliance.md) | [ถัดไป: 07 Messaging & Application Integration »](07_messaging_application_integration.md) |