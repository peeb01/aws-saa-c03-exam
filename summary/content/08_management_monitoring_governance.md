# 08 Management, Monitoring & Governance - Deep-Dive Exam Cheat-Sheet

---

## 1. Amazon CloudWatch & EventBridge

### Deep-Dive Concepts & Sub-features
เครื่องมือคอยตรวจสอบ ดูแลรักษาระบบ (Monitoring) และเฝ้าระวังเหตุการณ์ภายใน AWS

*   **Amazon CloudWatch:**
    *   **CloudWatch Metrics:** จัดเก็บค่าตัวเลขสถิติของระบบ (เช่น CPU, Network, EBS Read/Write) ตามคาบเวลาปกติ 5 นาที หรือแบบละเอียดพิเศษ (Detailed Monitoring) ทุกๆ **1 นาที** (มีค่าบริการเพิ่ม)
    *   **CloudWatch Logs:** แหล่งเก็บบันทึกข้อมูลตัวหนังสือล็อกการทำงานต่างๆ จากเซิร์ฟเวอร์หรือแอป โดยบน EC2 ต้องลง **CloudWatch Unified Agent** เพื่อเก็บล็อกในระดับ OS (เช่น Memory utilization, Disk space usage) ซึ่งสถิติปกติของ EC2 ไม่มีทางรู้
    *   **CloudWatch Alarms:** ระบบแจ้งเตือนเมื่อระดับ Metric เกินเกณฑ์ (เช่น CPU เกิน 80% นาน 5 นาที) สามารถสั่งยิง SMS ผ่าน SNS, สั่ง ASG ขยายเครื่อง, หรือสั่งปิด/รีสตาร์ทเครื่อง EC2 ตัวนั้นได้
    *   **Container Insights:** บริการเจาะลึกวิเคราะห์สถานะระบบประมวลผลสำหรับตู้คอนเทนเนอร์ (ECS, EKS, Fargate)
    *   **Contributor Insights:** วิเคราะห์ Log ของระบบเพื่อค้นหาและจำแนกผู้ใช้อันดับสูงสุด (Top Contributors) ที่ยิงทราฟฟิกข้อมูลหรือสร้างโหลดหนาแน่นที่สุดในระบบ
*   **Amazon EventBridge (Serverless Event Bus):**
    *   ระบบรับและกระจายความเคลื่อนไหวเหตุการณ์ (Events) เพื่อรันสถาปัตยกรรมไร้รอยต่อ (Event-Driven) ทำงานอิงตามกฎเกณฑ์ (Rules) ตัวอย่างเช่น ดักฟังเหตุการณ์ "EC2 เปลี่ยนสถานะเป็น Terminated" เพื่อส่งคำสั่งไปสั่งรันฟังก์ชัน Lambda หรือส่ง SMS บอกแอดมิน
    *   **EventBridge Pipes:** เครื่องมือสร้างทางเชื่อมโยงตรง (Point-to-Point) คอนฟิกง่าย เพื่อดึงข้อมูลคิวจากแหล่งต้นทาง (เช่น DynamoDB Streams, SQS) ส่งต่อไปจัดรูปแปลงค่าแล้วโยนให้ปลายทางได้สะดวกขึ้น
    *   **Schema Registry:** ศูนย์กลางเก็บบันทึกโครงสร้างไฟล์ JSON ของ Events เพื่ออำนวยความสะดวกในการเขียนโปรแกรมรันโค้ดตอบรับ

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Monitor EC2 memory usage / Monitor free disk space:* -> **CloudWatch Unified Agent** (EC2 Metrics ปกติไม่มีข้อมูล Memory/Disk Space)
    *   *Trigger Lambda on EC2 state change / Trigger event on schedule:* -> **Amazon EventBridge Rule** (หรือ CloudWatch Events)
    *   *Real-time container performance monitoring:* -> **CloudWatch Container Insights**
    *   *Detailed monitoring of EC2 / 1-minute metric resolution:* -> **CloudWatch Detailed Monitoring**
*   **Trap Warnings:**
    *   *Memory Utilization monitoring:* หากโจทย์ถามวิธีการเฝ้าระวังแรมของ EC2 ห้ามเลือกประเมินผ่าน CloudWatch Metric ทั่วไปเด็ดขาด (เพราะ Hypervisor ด้านล่างไม่สามารถมองเห็นแรมภายใน Guest OS ได้) ต้องระบุวิธีการติดตั้งและตั้งค่า **CloudWatch Agent** ภายในตัวอินสแตนซ์เท่านั้น

---

## 2. Audit, Governance & Systems Management

### Deep-Dive Concepts & Sub-features
*   **AWS CloudTrail (API Audit Tool):**
    *   บริการบันทึกรอยประวัติกิจกรรมการทำงานและการเรียกใช้งาน API ทั้งหมดภายในบัญชี (บันทึกว่า ใคร, เรียกใช้งาน API อะไร, เมื่อไหร่, จากที่ไหน, และได้สัญญานตอบกลับอย่างไร)
    *   สามารถส่งข้อมูล Log ไปเก็บบันทึกถาวรลงใน S3 Bucket และล็อคไฟล์ด้วยฟังก์ชัน **CloudTrail Log File Integrity Validation** เพื่อป้องกันไม่ให้คนแอบลบล็อกรอยประวัติตนเอง
*   **AWS Config (Compliance & Configuration History):**
    *   บริการบันทึกประวัติการเปลี่ยนแปลงการตั้งค่าของทรัพยากรทั้งหมดในบัญชี
    *   มีคุณสมบัติในการประเมินความสอดคล้องตามกฎเกณฑ์ความปลอดภัย (**Rules**) เช่น แจ้งเตือนเมื่อเจอผู้เปิดพอร์ต 22 (SSH) สู่สาธารณะ และตั้งเงื่อนไขส่งคำสั่งแก้ไขให้กลับมาปลอดภัยอัตโนมัติ (**Auto-Remediation** ผ่าน Systems Manager Automation)
*   **AWS Systems Manager (SSM):**
    *   **Session Manager:** ล็อกอินเข้าควบคุมระบบปฏิบัติการหลังบ้านของ EC2 (Shell Command) ผ่านทางเว็บบราวเซอร์ได้ทันทีโดยไม่ต้องผ่านช่องทางพอร์ต 22 (SSH) หรือต้องลงทะเบียนคีย์คู่แถมมีความปลอดภัยเพราะใช้ IAM Policy คุมแทน
    *   **Parameter Store:** คลังเก็บบันทึกคีย์การตั้งค่า (Configuration Parameters) และรหัสผ่านเชื่อมต่อแบบปลอดภัยโดยไม่มีการคิดค่าใช้จ่ายเพิ่มในการเข้าใช้ข้อมูลปกติ (ฟรี หากใช้ในระดับ Standard)
    *   **Patch Manager:** บริการช่วยกวาดจัดระเบียบและสั่งการอัปเดตอัปเกรดระบบปฏิบัติการ OS Patching ให้กับกลุ่มเครื่อง EC2 อัตโนมัติตามนัดหมายเวลา
    *   **State Manager:** ดูแลให้แน่ใจว่าเครื่องเซิร์ฟเวอร์ทั้งกลุ่มรักษาโครงสร้างการคอนฟิกที่ถูกต้องสม่ำเสมอตลอดเวลา
*   **AWS Organizations & Governance:**
    *   **AWS Organizations:** รวมบัญชี AWS หลายร้อยรายการเข้ามาเป็นกลุ่มเพื่อชำระค่าบริการร่วมกันเป็นก้อนเดียว (Consolidated Billing) และแบ่งปันสิทธิประโยชน์ด้านส่วนลดปริมาณใช้งาน (Volume Discounts)
    *   **SCPs (Service Control Policies):** นโยบายความปลอดภัยสูงสุดที่เขียนขึ้นตรงระดับองค์กร (Root หรือ OU) เพื่อคุมขีดจำกัดสิทธิ์ของทุกบัญชีย่อยในสังกัด และ SCPs ยังมีสิทธิ์บังคับบล็อกแม้กระทั่งบัญชี **Root User** ของบัญชีย่อยได้ด้วย (มีผลเหนือทุก IAM Policy ภายในบัญชีนั้น)
    *   **Tag Policies:** กฎเกณฑ์ที่บังคับใช้นโยบายการติดแท็ก (Tags) ให้เป็นไปในทิศทางเดียวกันข้ามทุกบัญชี

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *API auditing / Who deleted my resource:* -> **AWS CloudTrail**
    *   *Track configuration changes over time / Compliance rules:* -> **AWS Config**
    *   *Secure shell access without Port 22/SSH key:* -> **SSM Session Manager**
    *   *Store DB password securely for free:* -> **SSM Parameter Store** (SecureString)
    *   *Central billing / Shared volume discount:* -> **AWS Organizations**
    *   *Restrict root user privileges in sub-accounts:* -> **Service Control Policies (SCPs)**
*   **Trap Warnings:**
    *   *CloudTrail vs CloudWatch:* CloudTrail บันทึกเหตุการณ์กิจกรรมการควบคุมใช้งานของแอดมินหรือการเรียกใช้ระบบ (Management/Control plane - Who did what); CloudWatch สนใจประเมินสุขภาพ วัดระดับความเร็ว และความเคลื่อนไหวภายในแอปพลิเคชัน (Data plane/performance)
    *   *SCPs do not grant permissions:* SCPs เป็นแค่ไฟร์วอลล์กรองจำกัดวงสิทธิ์ (Guardrails) เท่านั้น ไม่ได้เป็นตัวมอบสิทธิ์การเข้าใช้งานจริง (ดังนั้น ผู้ใช้งานในบัญชีย่อยยังคงต้องได้สิทธิ์ IAM Policy อนุญาตควบคู่ไปด้วยจึงจะรันคำสั่งสำเร็จ)

---

## 3. Cost Management

### Deep-Dive Concepts & Sub-features
เครื่องมือคอยประเมิน จัดระเบียบการใช้เงินคลาวด์ให้ประหยัดที่สุด

*   **AWS Budgets:** นโยบายสำหรับกำหนดงบประมาณการใช้บริการ (Cost) หรือปริมาณการใช้ทรัพยากร (Usage) โดยระบบจะยิงสัญญานเตือน (Alerts) ทันทีเมื่อเงินใช้จ่ายจริงหรือทราฟฟิกคาดการณ์ในอนาคตมีระดับเกินวงเงินงบประมาณที่ตกลงไว้
*   **AWS Cost Explorer:** แดชบอร์ดสรุปวิเคราะห์การใช้เงินคลาวด์ย้อนหลัง สูงสุด 12 เดือน และช่วยทำนายเทรนด์ทิศทางค่าบริการในอนาคตได้อย่างละเอียด แยกดูได้ตาม Tag, Service หรือ Account
*   **AWS Cost Anomaly Detection:** ใช้ AI คอยสแกนตรวจสอบความผิดปกติหากเจอสไปก์เงินกระโดดสูงขึ้นในบางช่วงเวลาแบบกระทันหัน (เช่น แอดมินเผลอเปิดเครื่องคำนวณทิ้งไว้จนค่าบริการกระโดดขึ้นในวันเสาร์อาทิตย์) เพื่อส่งข้อความแจ้งเตือนด่วนทางอีเมลหรือ Slack
*   **AWS Compute Optimizer:** ใช้ปัญญาประดิษฐ์แนะนำการเลือกเปลี่ยนขนาดเครื่อง (Right-sizing) เพื่อลดค่าใช้จ่ายโดยวิเคราะห์สถิติจริงจาก CloudWatch (เช่น แนะนำให้ลดสเปคเครื่อง EC2 ที่รัน CPU เฉลี่ยแค่ 2% ตลอดสัปดาห์ลงเพื่อความประหยัดงบ)

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Predict future costs / Historical cost breakdown graph:* -> **AWS Cost Explorer**
    *   *Alert when actual or forecasted cost exceeds budget:* -> **AWS Budgets**
    *   *Machine learning cost spike detection:* -> **AWS Cost Anomaly Detection**
    *   *Right-sizing recommendations for EC2/EBS/Lambda:* -> **AWS Compute Optimizer**

### Real-World Use Cases
*   **Enterprise Multi-Account Cost Guardrails:** ตั้งค่าสถาปัตยกรรม AWS Organizations ผูกบัญชีย่อยเข้าด้วยกันทั้งหมด เขียนกฎ SCPs บล็อกการเปิดใช้งานเครื่องตระกูลแพง (เช่น ตระกูล p4, x1) ในทุกบัญชีย่อย และกำหนด AWS Budgets จำกัดงบประจำเดือนของแผนก Dev ไว้ที่ $500 หากเงินคาดคะเนวิ่งทะลุ $500 ให้ส่งสัญญาณเตือนไปที่ Slack ของหัวหน้าทีมทันที

### Architecture Integration Pattern: Automated Configuration Compliance

```text
  [ Sub-account Admin ]
            │
            ▼ (1) Non-compliant Config Change (e.g., Open Port 22)
  ┌──────────────────┐
  │    AWS Config    │ (Detects Non-compliance)
  └─────────┬────────┘
            │
            ▼ (2) Event Notification
  ┌──────────────────┐
  │Amazon EventBridge│
  └─────────┬────────┘
            │
            ▼ (3) Trigger Remediation
  ┌──────────────────┐
  │  SSM Automation  │ (Runbooks Executed)
  └─────────┬────────┘
            │
            ├─────────────────────────┐
            ▼ (4) Revert Config       ▼ (4) Save History & Logs
  ┌──────────────────┐      ┌──────────────────┐
  │  Target Resource │      │ S3 / CloudTrail  │
  └──────────────────┘      └──────────────────┘
```
1.  แอดมินย่อยของบัญชีย่อยเผลอเปลี่ยนค่าเส้นทางตาราง Route Table เปิดสิทธิ์ทางเน็ตเวิร์กเชื่อมกับเครือข่ายภายนอกแบบแปลกๆ
2.  **AWS Config** ดักพบการแก้ไขคอนฟิกนี้และวิเคราะห์พบว่าขัดแย้งกับกฎความปลอดภัยที่องค์กรระบุไว้ (Non-compliant status)
3.  AWS Config ส่งต่อเหตุการณ์สถานะไม่ตรงตามมาตรฐานไปยังระบบ **Amazon EventBridge**
4.  EventBridge เรียกทำงานเครื่องมืออัตโนมัติของ **AWS Systems Manager (SSM) Automation Document**
5.  SSM Automation ทำการรันสคริปต์สิทธิ์แอดมินลบการเชื่อมต่อแปลกปลอมนั้นทิ้ง และกู้ตาราง Route Table กลับมาอยู่ในสถานะปลอดภัยเช่นเดิมทันที (Auto-remediation)
6.  ส่งรายงานสรุปประวัติเหตุการณ์ลักลอบแก้ไขและการกู้กลับคืนนี้บันทึกเข้าถัง **Amazon S3** และทำ Integrity Check บน **CloudTrail** เพื่อใช้สำหรับรายงานรอบตรวจสอบประจำเดือน
