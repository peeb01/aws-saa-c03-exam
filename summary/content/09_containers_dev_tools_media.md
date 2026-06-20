# 09 Containers, Dev Tools & Media - Deep-Dive Exam Cheat-Sheet

---

## 1. Amazon ECS, EKS & Fargate (Containers)

### Deep-Dive Concepts & Sub-features
บริการดูแลรัน Application รูปแบบตู้ Container (Container Orchestration)

*   **Amazon ECS (Elastic Container Service):**
    *   บริการรันระบบ Container ที่ใช้งานง่ายและทำงานร่วมกับ Function ต่างๆ ของ AWS ได้อย่างลึกซึ้ง (เช่น เชื่อม IAM Role แยกเฉพาะสำหรับแต่ละ Task ได้ปลอดภัย)
    *   **ECS Task Definitions:** File ข้อกำหนดการ Configure (สไตล์ JSON) เพื่อระบุความต้องการในการรันตู้ Container เช่น เลือก Docker Image, จัดแบ่งการใช้งาน CPU/RAM, ตั้งค่าตัวแปรสภาพแวดล้อม (Environment Variables), เมาท์ไดรฟ์ข้อมูล และจัดเรียงการแมป PortNetwork
    *   **ECS Task vs Service:** *Task* คือตัวตน Container ที่รันประมวลผลจริง (คล้าย 1 Instance); *Service* ทำหน้าที่คุมจำนวน Task ให้อยู่ครบในสภาพแวดล้อมที่กำหนด และเชื่อมต่อเข้ากับ Application Load Balancer (ALB)
*   **ECS Launch Types:**
    *   **EC2 Launch Type:** รัน Container ลงบนกลุ่มเครื่อง Server EC2 ที่ผู้ใช้ต้องเป็นคนคอยลงทะเบียน ดูแลความปลอดภัย อัปเกรด Patch และสั่ง Scale ขนาดเครื่อง EC2 ด้วยตัวเอง (ผู้ใช้ควบคุมเครื่องได้ 100%)
    *   **Fargate Launch Type (Serverless):** ผู้ใช้ไม่จำเป็นต้องจัดการเครื่อง Server ด้านล่างอีกต่อไป รันตู้ Container เปล่าๆ ได้ทันทีโดยจ่ายค่าบริการตามจำนวน CPU และ RAM จริงที่ตู้ Container เรียกใช้รันงานเป็นวินาที (ขยายตัวง่าย ปลอดภัย และลดภาระดูแลระบบสูงสุด)
*   **Amazon EKS (Elastic Kubernetes Service):** บริการบริหารจัดการระบบ Kubernetes Cluster สำเร็จรูปบน AWS เหมาะสำหรับการย้ายระบบงานหรือสถาปัตยกรรมเดิมที่รันบนฐานระบบ Kubernetes นอกคลาวด์มาทำงานบน AWS ได้อย่างราบรื่นโดยไม่ต้องแก้ไขสถาปัตยกรรมคำสั่งควบคุมเดิม
*   **Amazon ECR (Elastic Container Registry):** คลังจัดเก็บ ImageContainer (Docker Registry) ความปลอดภัยสูง มีคุณสมบัติสั่งสแกนหาช่องโหว่ความปลอดภัยของ Image อัตโนมัติเมื่ออัปโหลด File เข้ามา (Image Scanning)

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *No infrastructure management / Serverless container / Pay per task resource:* -> **AWS Fargate**
    *   *Kubernetes compatible / Move Kubernetes workloads to AWS:* -> **Amazon EKS**
    *   *ECS task security / Restrict database access per container:* -> **ECS Task IAM Role** (ห้ามตอบ Instance Role)
    *   *Private Docker Registry with vulnerability scanning:* -> **Amazon ECR**
*   **Trap Warnings:**
    *   *Task Role vs Execution Role:* ใน ECS มีคีย์เวิร์ดชวนสับสนสองข้อ:
        *   **Task Execution Role:** สิทธิ์ที่ให้แก่ตัว ECS Agent เพื่อไปทำหน้าที่เตรียมระบบรันตู้ (เช่น ไปขอดึงรหัสผ่าน DB จาก Secrets Manager, ไปดึง Image ระบบจาก ECR)
        *   **Task Role:** สิทธิ์ที่ตัว Application ภายในตู้ใช้สั่งการระบบ (เช่น ใช้สิทธิ์นี้บันทึกภาพลง S3 หรือ Queue รีข้อมูล DynamoDB)

---

## 2. Developer Tools & Media Services

### Deep-Dive Concepts & Sub-features
*   **AWS Amplify:** Platform สำเร็จรูปสำหรับนักพัฒนา Mobile และเว็บ โดยช่วยจัดตั้งระบบหลังบ้านแบบ Serverless (Auth, DB, Storage) และอำนวยความสะดวกในการ Deploy หน้าบ้านเว็บแบบ CI/CD (Hosting) เพียงเชื่อมต่อ Source control เช่น GitHub
*   **Amazon Q Developer & CodeWhisperer:** เครื่องมือ AI ช่วยเขียนและแนะนำชุดคำสั่ง Program อิงตามคอมเมนต์คำนิยามในโปรเจกต์คู่ใจนักพัฒนา
*   **AWS Device Farm:** บริการช่วยทดสอบคุณภาพการทำงานของ App มือถือกับเครื่องโทรศัพท์สมาร์ทโฟนของจริงหลากหลายร้อยแบรนด์รุ่นบนระบบคลาวด์ เพื่อตรวจสอบหน้าต่างและพฤติกรรมการแสดงผลหาบั๊กก่อนส่งขึ้น App Store
*   **AWS MediaConvert:** บริการระดับ Serverless แปลงฟอร์แมต File วิดีโอคุณภาพสูง (เช่น แปลง File วิดีโอดิบขนาดใหญ่ไปเป็นฟอร์แมตที่เหมาะสำหรับเล่นบนหน้าจอทีวี, แท็บเล็ต หรือสมาร์ทโฟนตามความเร็ว Internet ของผู้เล่น) เพื่อใช้สำหรับบริการ Streaming ทั่วไป
*   **Elastic Transcoder:** บริการจัดการแปลงวิดีโอรุ่นเก่า ปัจจุบันในข้อสอบมักถูกแทนที่ด้วยข้อเสนอที่คุ้มค่าและดีกว่าอย่าง **AWS MediaConvert**

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Full-stack web application hosting / CI/CD deploy from GitHub:* -> **AWS Amplify**
    *   *Test mobile app on real physical devices in the cloud:* -> **AWS Device Farm**
    *   *Broadcast-grade video transcoding / Serverless video conversion:* -> **AWS MediaConvert** (ห้ามเลือก Elastic Transcoder)

### Real-World Use Cases
*   **Mobile App DevOps Pipeline:** นักพัฒนาเขียน Application เสร็จแล้วดันโค้ดขึ้น GitHub -> ระบบ Amplify ตรวจพบการเคลื่อนไหวแล้วทำการสั่งรัน Build ตรวจสอบคุณภาพ และส่งต่อให้ Device Farm สั่งทดสอบการคลิกบนหน้าจอโทรศัพท์จริงของ App กว่า 50 รุ่น หากผ่านเกณฑ์ทั้งหมดจึงปล่อย Deploy ขึ้นระบบ Hosting ให้ผู้ใช้จริงเข้ามาใช้งานตัวล่าสุด

### Architecture Integration Pattern: Serverless Video Upload & Transcoding Portal

```text
 [ Content Creator ]
          │
          ▼ (1) Upload Video
 ┌─────────────────┐
 │S3 Upload Bucket │
 └────────┬────────┘
          │
          ▼ (2) Event (S3 PUT)
 ┌─────────────────┐
 │   AWS Lambda    │
 └────────┬────────┘
          │
          ▼ (3) Transcode Job
 ┌─────────────────┐
 │  MediaConvert   │
 └────────┬────────┘
          │
          ▼ (4) Save HLS Output
 ┌─────────────────┐
 │S3 Delivery Buck │
 └────────┬────────┘
          │
          ▼ (5) Stream Media
 ┌─────────────────┐
 │   CloudFront    │ ◄─── [ Users ]
 └─────────────────┘
```
1.  **Content Creator** อัปโหลด File วิดีโอดิบความคมชัดสูงผ่านหน้าเว็บเข้าสู่ **Amazon S3 Bucket (Upload Bucket)**
2.  File อัปโหลดเสร็จสมบูรณ์ไปกระตุ้นสัญญานส่งต่อมาที่ **AWS Lambda (Trigger Function)**
3.  Lambda ทำการตรวจ SpecFile และส่ง Parameter สั่งจ้างรันงานประมวลผลแปลง File ที่ **AWS Elemental MediaConvert**
4.  MediaConvert ทำการแปลง File วิดีโอออกเป็นหลายระดับความละเอียดพร้อมทำ Function ปรับขนาด Bandwidth ตามเครื่องเล่น (HLS formats) และนำ File ผลลัพธ์ไปเก็บบันทึกถาวรลงใน **Amazon S3 (Delivery Bucket)**
5.  **Users** ทั่วโลกเรียกชมคลิปผ่านเครื่องเล่นวิดีโอโดย Traffic จะวิ่งรับชมผ่านระบบ Cache ความเร็วสูง **Amazon CloudFront** เพื่อลดภาระการโหลด File ซ้ำจากถังเก็บหลัก S3


---

## ➔ การนำทาง (Navigation)
| [« กลับหน้าหลัก (README.md)](../README.md) | [« ก่อนหน้า: 08 Management, Monitoring & Governance](08_management_monitoring_governance.md) | [ถัดไป: 10 Generative AI & Machine Learning »](10_generative_ai_machine_learning.md) |