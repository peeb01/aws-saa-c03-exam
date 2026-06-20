# 07 Messaging & Application Integration - Deep-Dive Exam Cheat-Sheet

---

## 1. Amazon SQS (Simple Queue Service)

### Deep-Dive Concepts & Sub-features
Amazon SQS คือบริการระบบ Queue รับส่งข้อความแบบ Serverless (Message Queue) ช่วยในการเชื่อมต่อระบบย่อยแยกอิสระจากกัน (**Decoupling/Loose Coupling**) เพื่อลดโอกาสระบบล่มต่อเนื่อง

*   **Queue Types:**
    *   **Standard Queue:** รองรับปริมาณ Throughput การส่งข้อความได้แบบไม่จำกัด รับประกันการส่งอย่างน้อยหนึ่งครั้ง (**At-least-once delivery**) แต่อาจเกิดกรณีลำดับสลับกันได้บ้าง หรือข้อความซ้ำได้ในกรณีเครือข่ายขัดข้อง
    *   **FIFO Queue (First-In-First-Out):** รับประกันการทำงานเรียงลำดับส่งก่อนหลังอย่างแม่นยำ และไม่มีข้อความซ้ำซ้อนอย่างเด็ดขาด (**Exactly-once processing**) โดยจำกัด Throughput สูงสุดอยู่ที่ 300 messages/s (หรือสูงสุด 3,000 messages/s หากใช้ Batching/High Throughput mode) ตัว Queue จะมีนามสกุลบังคับคือ `.fifo`
*   **Key Parameters:**
    *   **Visibility Timeout:** เมื่อข้อความถูกดึงไปประมวลผลโดยเครื่อง App เป้าหมาย ตัวข้อความจะถูกซ่อนไว้เพื่อป้องกันไม่ให้ App ตัวอื่นๆ มองเห็นและดึงไปซ้ำ หากประมวลผลเสร็จสิ้น App ต้องส่งคำสั่งลบข้อความออกจาก Queue; หากหมดเวลา Visibility Timeout (เริ่มต้น 30 วินาที ปรับเพิ่มสูงสุดได้ 12 ชั่วโมง) แล้วยังไม่มีคำสั่งลบ ข้อความจะเด้งกลับเข้า Queue ให้ตัวอื่นดึงไปทำใหม่
    *   **Dead Letter Queue (DLQ):** Queue สำหรับส่งแยกข้อความที่ประมวลผลล้มเหลวซ้ำๆ เกินเกณฑ์ (ระบุผ่าน Redrive Policy) เพื่อนำไปตรวจสอบวิเคราะห์ปัญหาทีหลังโดยไม่ไปอุดกั้น Queue หลัก
    *   **SQS Long Polling:** App จะส่งสัญญาณขอรับข้อความค้างไว้สูงสุด **20 วินาที** หากไม่มีข้อความเข้าระบบ Queue เลย เพื่อช่วยประหยัดเงินค่าธรรมเนียม Request และลดจำนวน Empty Responses
    *   **Delay Seconds:** ชะลอหรือถ่วงเวลาส่งข้อความใหม่ที่ใส่เข้ามาใน Queue ให้ซ่อนตัวไว้ก่อน (สูงสุด 15 นาที) ก่อนจะปล่อยให้อุปกรณ์ดึงไปทำงาน

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Decouple microservices / Asynchronous communication:* -> **Amazon SQS**
    *   *Strict order of processing / Prevent duplicate processing:* -> **SQS FIFO Queue**
    *   *Message processing takes longer than visibility timeout / Duplicate processing occurring:* -> **Increase Visibility Timeout**
    *   *Reduce empty responses / Save cost on SQS requests:* -> **SQS Long Polling** (Set `ReceiveMessageWaitTimeSeconds` > 0)
*   **Trap Warnings:**
    *   *Visibility Timeout too short:* หากข้อความโดนดึงไปประมวลผลแล้วเกิดสัญญานทำซ้ำซ้อนเรื่อยๆ ในระบบ (Duplicate processing) โจทย์มักจะชี้ปัญหานี้เพราะ App ใช้เวลาทำนานกว่ากำหนดเวลา Visibility Timeout ทำให้ข้อความโผล่กลับเข้าไปใน Queue หลักอีกรอบก่อนที่จะได้ส่งลบ วิธีแก้คือให้ขยายค่า Visibility Timeout ของ Queue
    *   *FIFO Performance limit:* หากระบบต้องการ Throughput มหาศาลระดับหมื่นรายการต่อวินาที ห้ามเลือก SQS FIFO ให้ใช้ **Standard Queue** และเขียน Program จัดการการซ้อนหรือลำดับขากลับเองเพื่อคงประสิทธิภาพสูงสุด

---

## 2. Messaging Infrastructure

### Deep-Dive Concepts & Sub-features
*   **Amazon SNS (Simple Notification Service):**
    *   บริการส่งข่าวสารกระจายข้อมูลสไตล์ Pub/Sub (Publisher/Subscriber) แบบส่งตรงทันที (Push Mechanism) จากผู้ส่งตัวเดียว (Topic) ไปหาปลายทางพร้อมกันหลายคน (เช่น อีเมล, SMS, Mobile Push, HTTPS, SQS, Lambda)
    *   **SNS FIFO Topics:** กระจายข้อมูลโดยคงลำดับและความถูกต้อง 100% ร่วมกับ SQS FIFO
    *   **Message Filtering:** ช่วยให้ผู้รับ (Subscriber) เขียนกฎเพื่อกรองเลือกรับเฉพาะเนื้อหาหรือ File ข้อความที่มีหัวข้อที่เกี่ยวข้องกับหน้าที่ตนเองเท่านั้น โดยไม่ต้องมานั่งเปิดอ่านข้อความทั้งหมดที่ผ่าน Topic
*   **SNS and SQS Fan-Out Pattern:**
    *   สถาปัตยกรรมการยิงข้อความเข้ามาที่ SNS Topic เพียงหนึ่งครั้ง จากนั้น SNS จะทำสำเนากระจายข้อความแยกยิงเข้าไปที่ Queue SQS หลายๆ ตัวที่ผูกติดกันขนานกันเพื่อให้นำไปประมวลผลคนละ Object ประสงค์ (เช่น Queue หนึ่งนำข้อมูลไปคำนวณเงิน, อีก Queue ส่งข้อมูลทำสถิติ, อีก Queue ส่งสแต็กประวัติ) ช่วยแยกการทำงานออกจากกันเด็ดขาดและปลอดภัย
*   **Amazon MQ:** บริการดูแลจัดการระบบ Queue ส่งข้อความแบบ Open-source ยอดนิยม ได้แก่ **ActiveMQ** และ **RabbitMQ** สำหรับบริษัทที่ย้ายสถาปัตยกรรม Application เดิมขึ้นคลาวด์โดยต้องการคง Protocol มาตรฐานเครือข่ายเดิมไว้ (เช่น JMS, AMQP, MQTT) โดยไม่ต้องแก้โครงสร้าง Program
*   **AWS Step Functions:**
    *   บริการเขียน Workflow (State Machines) เชื่อมโยงและจัดลำดับการทำงานของ Serverless Components และ Lambda Function ให้เป็นขั้นตอนระบบ
    *   **Standard Workflows:** สำหรับขั้นตอนงานใช้เวลานาน (สูงสุด 1 ปี) มีการบันทึกประวัติการเดินแต่ละบ Lock ข้อมูลอย่างเป็นขั้นตอน มีสิทธิกู้กลับและย้อนคืนสูง
    *   **Express Workflows:** สำหรับงานด่วน ปริมาณ Traffic มหาศาล และทำงานสั้นจบในไม่เกิน 5 นาที (เช่น งานประมวลผลสตรีมมิ่งสด)
*   **API Gateway:**
    *   บริการจัดทำและดูแลช่องทางเชื่อมต่อ API ระดับหน้าบ้าน (Front Door) ของ Application รองรับสามประเภท:
        *   *HTTP API (แนะนำ):* ความเร็วสูงสุด Latency ต่ำ และราคาประหยัด (ถูกกว่า REST API 70%) เหมาะสำหรับระบบ Serverless API ทั่วไป
        *   *REST API:* มี Function ควบคุมแบบเต็มระดับองค์กร (เช่น ทำ API Keys, ควบคุมสิทธิ์ละเอียด, รองรับ WAF)
        *   *WebSocket API:* เชื่อมต่อสองทิศทางแบบเปิดค้าง (Bi-directional real-time communication) เหมาะกับ AppChat หรือเกมออนไลน์
    *   **API Gateway Key Features:**
        *   **Caching:** Cache ผลการเรียก API หน้าบ้าน ช่วยลดโหลดระบบหลังบ้านและลดเวลารอผลลัพธ์
        *   **Throttling:** จำกัดจำนวนคำขอใช้งานต่อวินาที (TPS) ป้องกันระบบล่มจากการโดนรุมยิงเรียกใช้งาน
        *   **Canary Deployments:** ทยอยสลับผู้ใช้งานจริงบางส่วนไปใช้ API เวอร์ชันใหม่เพื่อทดสอบความนิ่งก่อนเปลี่ยน 100%
*   **Amazon AppSync & AppFlow:**
    *   *AppSync:* บริการ Serverless จัดการ API แบบ **GraphQL** ช่วยให้ Client ดึงข้อมูลจาก Database หรือบริการหลากหลายประเภทได้ครบจบผ่านคำร้องขอเดียว
    *   *AppFlow:* ซิงค์ย้ายข้อมูลแบบ Serverless ระหว่าง Application SaaS ภายนอก (เช่น Salesforce, ServiceNow, Slack) กับพื้นที่จัดเก็บคลาวด์ของ AWS อย่างปลอดภัย

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Publish-Subscribe / Push notifications / Fan-out to multiple queues:* -> **Amazon SNS**
    *   *Legacy application migration / ActiveMQ / RabbitMQ:* -> **Amazon MQ**
    *   *Orchestrate multiple Lambda functions / Long-running state machine:* -> **AWS Step Functions (Standard)**
    *   *API Gateway protection / Rate limiting / DDoS mitigation:* -> **Throttling + WAF**
    *   *Serverless GraphQL API:* -> **Amazon AppSync**
*   **Trap Warnings:**
    *   *SNS vs SQS:* จำหลักความต่าง SQS คือ App ปลายทางต้องคอยส่งคำสั่งมาดึงข้อมูลไปเอง (Pull - client polls queue); SNS คือส่งตรงยิงข้ามไปหา Client ทันทีโดยไม่ต้องรอร้องขอ (Push - immediate broadcast)
    *   *API Gateway Throttling Limits:* หากมีคำขอใช้งาน API Gateway แล้วได้สัญญานตอบกลับเป็น Error `429 Too Many Requests` แสดงว่าชนระดับขีดจำกัด Throttling วิธีแก้คือต้องขอปรับขีดจำกัดของบัญชีขึ้น หรือตั้งค่า Rate Limit ในระดับ Usage Plan แยกตามลูกค้า

### Real-World Use Cases
*   **E-Commerce Order Processing:** ลูกค้ากดสั่งซื้อสินค้าส่งคำขอเข้า API Gateway -> วิ่งเข้าหา SNS Topic -> SNS ทำการ Fan-out ส่งกระจายข้อความสั่งซื้อไปที่ Queue SQS การเงินเพื่อหักตังค์ และส่งเข้า Queue SQS สต็อกสินค้าเพื่อตัดยอดสินค้าพร้อมกันขนานกันอย่างไร้รอยต่อ

### Architecture Integration Pattern: Serverless Decoupled Microservice

```text
  [ Mobile Client ] ◄───(6) SMS Notification (SNS)
          │
          ▼ (1) POST Payment
  ┌───────────────┐
  │ API Gateway   │
  └───────┬───────┘
          │
          ▼ (2) Validate
  ┌───────────────┐
  │  AWS Lambda   │
  └───────┬───────┘
          │
          ▼ (3) Enqueue Order
  ┌───────────────┐      ┌───────────────┐
  │SQS FIFO Queue │ ───► │ SQS DLQ       │ (4) Failed Messages
  └───────┬───────┘      └───────────────┘
          │ (Poll)
          ▼
  ┌───────────────┐
  │  AWS Lambda   │ (Backend Processor)
  └───────┬───────┘
          │ (5) Save & Publish
          ▼
  ┌───────────────┐
  │Amazon DynamoDB│ ───► [ Amazon SNS ] Topic
  └───────────────┘
```
1.  **Mobile Client** ส่งคำสั่งชำระเงินผ่าน HTTPS โพสต์เข้าสู่ **Amazon API Gateway**
2.  API Gateway ส่งต่อไปยัง **AWS Lambda (Validation)** ตรวจสอบความถูกต้องของสิทธิ์การจ่ายเงิน
3.  Lambda ตัวแรก นำข้อความการจ่ายเงินใส่เข้าไปใน **Amazon SQS FIFO Queue** เพื่อรับประกันความปลอดภัยเรียงลำดับการจ่ายก่อนหลังอย่างถูกต้อง
4.  **AWS Lambda (Backend Processing)** ตัวที่สองคอยทำหน้าที่ Poll ดึงข้อความจาก Queue มาประมวลผลตัดยอดและเขียนข้อมูลลง DynamoDB
5.  หากกระบวนการประมวลผลของ Lambda ตัวที่สองพังบ่อย ข้อความจะถูกตัดส่งไปไว้ที่ **SQS Dead Letter Queue (DLQ)**
6.  หลังจากบันทึกข้อมูลเรียบร้อย Lambda ตัวที่สองส่งข้อความบอกความเคลื่อนไหวสำเร็จไปที่ **Amazon SNS Topic** เพื่อยิง Push Notification กลับไปแสดงความยินดีกับหน้ามือถือของลูกค้าปลายทาง


---

## ➔ การนำทาง (Navigation)
| [« กลับหน้าหลัก (README.md)](../README.md) | [« ก่อนหน้า: 06 Analytics & Data Integration](06_analytics_data_integration.md) | [ถัดไป: 08 Management, Monitoring & Governance »](08_management_monitoring_governance.md) |