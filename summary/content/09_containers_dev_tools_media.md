# 09 Containers, Dev Tools & Media - Deep-Dive Exam Cheat-Sheet

---

## 1. Amazon ECS, EKS & Fargate (Containers)

### Deep-Dive Concepts & Sub-features
บริการดูแลรันแอปพลิเคชันรูปแบบตู้คอนเทนเนอร์ (Container Orchestration)

*   **Amazon ECS (Elastic Container Service):**
    *   บริการรันระบบคอนเทนเนอร์ที่ใช้งานง่ายและทำงานร่วมกับฟังก์ชันต่างๆ ของ AWS ได้อย่างลึกซึ้ง (เช่น เชื่อม IAM Role แยกเฉพาะสำหรับแต่ละ Task ได้ปลอดภัย)
    *   **ECS Task Definitions:** ไฟล์ข้อกำหนดการคอนฟิก (สไตล์ JSON) เพื่อระบุความต้องการในการรันตู้คอนเทนเนอร์ เช่น เลือก Docker Image, จัดแบ่งการใช้งาน CPU/RAM, ตั้งค่าตัวแปรสภาพแวดล้อม (Environment Variables), เมาท์ไดรฟ์ข้อมูล และจัดเรียงการแมปพอร์ตเน็ตเวิร์ก
    *   **ECS Task vs Service:** *Task* คือตัวตนคอนเทนเนอร์ที่รันประมวลผลจริง (คล้าย 1 Instance); *Service* ทำหน้าที่คุมจำนวน Task ให้อยู่ครบในสภาพแวดล้อมที่กำหนด และเชื่อมต่อเข้ากับ Application Load Balancer (ALB)
*   **ECS Launch Types:**
    *   **EC2 Launch Type:** รันคอนเทนเนอร์ลงบนกลุ่มเครื่องเซิร์ฟเวอร์ EC2 ที่ผู้ใช้ต้องเป็นคนคอยลงทะเบียน ดูแลความปลอดภัย อัปเกรดแพตช์ และสั่งสเกลขนาดเครื่อง EC2 ด้วยตัวเอง (ผู้ใช้ควบคุมเครื่องได้ 100%)
    *   **Fargate Launch Type (Serverless):** ผู้ใช้ไม่จำเป็นต้องจัดการเครื่องเซิร์ฟเวอร์ด้านล่างอีกต่อไป รันตู้คอนเทนเนอร์เปล่าๆ ได้ทันทีโดยจ่ายค่าบริการตามจำนวน CPU และ RAM จริงที่ตู้คอนเทนเนอร์เรียกใช้รันงานเป็นวินาที (ขยายตัวง่าย ปลอดภัย และลดภาระดูแลระบบสูงสุด)
*   **Amazon EKS (Elastic Kubernetes Service):** บริการบริหารจัดการระบบ Kubernetes คลัสเตอร์สำเร็จรูปบน AWS เหมาะสำหรับการย้ายระบบงานหรือสถาปัตยกรรมเดิมที่รันบนฐานระบบ Kubernetes นอกคลาวด์มาทำงานบน AWS ได้อย่างราบรื่นโดยไม่ต้องแก้ไขสถาปัตยกรรมคำสั่งควบคุมเดิม
*   **Amazon ECR (Elastic Container Registry):** คลังจัดเก็บอิมเมจคอนเทนเนอร์ (Docker Registry) ความปลอดภัยสูง มีคุณสมบัติสั่งสแกนหาช่องโหว่ความปลอดภัยของอิมเมจอัตโนมัติเมื่ออัปโหลดไฟล์เข้ามา (Image Scanning)

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *No infrastructure management / Serverless container / Pay per task resource:* -> **AWS Fargate**
    *   *Kubernetes compatible / Move Kubernetes workloads to AWS:* -> **Amazon EKS**
    *   *ECS task security / Restrict database access per container:* -> **ECS Task IAM Role** (ห้ามตอบ Instance Role)
    *   *Private Docker Registry with vulnerability scanning:* -> **Amazon ECR**
*   **Trap Warnings:**
    *   *Task Role vs Execution Role:* ใน ECS มีคีย์เวิร์ดชวนสับสนสองข้อ:
        *   **Task Execution Role:** สิทธิ์ที่ให้แก่ตัว ECS Agent เพื่อไปทำหน้าที่เตรียมระบบรันตู้ (เช่น ไปขอดึงรหัสผ่าน DB จาก Secrets Manager, ไปดึงอิมเมจระบบจาก ECR)
        *   **Task Role:** สิทธิ์ที่ตัวแอปพลิเคชันภายในตู้ใช้สั่งการระบบ (เช่น ใช้สิทธิ์นี้บันทึกภาพลง S3 หรือคิวรีข้อมูล DynamoDB)

---

## 2. Developer Tools & Media Services

### Deep-Dive Concepts & Sub-features
*   **AWS Amplify:** แพลตฟอร์มสำเร็จรูปสำหรับนักพัฒนาโมบายและเว็บ โดยช่วยจัดตั้งระบบหลังบ้านแบบ Serverless (Auth, DB, Storage) และอำนวยความสะดวกในการดีพลอยหน้าบ้านเว็บแบบ CI/CD (Hosting) เพียงเชื่อมต่อ Source control เช่น GitHub
*   **Amazon Q Developer & CodeWhisperer:** เครื่องมือ AI ช่วยเขียนและแนะนำชุดคำสั่งโปรแกรมอิงตามคอมเมนต์คำนิยามในโปรเจกต์คู่ใจนักพัฒนา
*   **AWS Device Farm:** บริการช่วยทดสอบคุณภาพการทำงานของแอปมือถือกับเครื่องโทรศัพท์สมาร์ทโฟนของจริงหลากหลายร้อยแบรนด์รุ่นบนระบบคลาวด์ เพื่อตรวจสอบหน้าต่างและพฤติกรรมการแสดงผลหาบั๊กก่อนส่งขึ้น App Store
*   **AWS MediaConvert:** บริการระดับ Serverless แปลงฟอร์แมตไฟล์วิดีโอคุณภาพสูง (เช่น แปลงไฟล์วิดีโอดิบขนาดใหญ่ไปเป็นฟอร์แมตที่เหมาะสำหรับเล่นบนหน้าจอทีวี, แท็บเล็ต หรือสมาร์ทโฟนตามความเร็วอินเทอร์เน็ตของผู้เล่น) เพื่อใช้สำหรับบริการ Streaming ทั่วไป
*   **Elastic Transcoder:** บริการจัดการแปลงวิดีโอรุ่นเก่า ปัจจุบันในข้อสอบมักถูกแทนที่ด้วยข้อเสนอที่คุ้มค่าและดีกว่าอย่าง **AWS MediaConvert**

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Full-stack web application hosting / CI/CD deploy from GitHub:* -> **AWS Amplify**
    *   *Test mobile app on real physical devices in the cloud:* -> **AWS Device Farm**
    *   *Broadcast-grade video transcoding / Serverless video conversion:* -> **AWS MediaConvert** (ห้ามเลือก Elastic Transcoder)

### Real-World Use Cases
*   **Mobile App DevOps Pipeline:** นักพัฒนาเขียนแอปพลิเคชันเสร็จแล้วดันโค้ดขึ้น GitHub -> ระบบ Amplify ตรวจพบการเคลื่อนไหวแล้วทำการสั่งรัน Build ตรวจสอบคุณภาพ และส่งต่อให้ Device Farm สั่งทดสอบการคลิกบนหน้าจอโทรศัพท์จริงของแอปกว่า 50 รุ่น หากผ่านเกณฑ์ทั้งหมดจึงปล่อยดีพลอยขึ้นระบบ Hosting ให้ผู้ใช้จริงเข้ามาใช้งานตัวล่าสุด

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
1.  **Content Creator** อัปโหลดไฟล์วิดีโอดิบความคมชัดสูงผ่านหน้าเว็บเข้าสู่ **Amazon S3 Bucket (Upload Bucket)**
2.  ไฟล์อัปโหลดเสร็จสมบูรณ์ไปกระตุ้นสัญญานส่งต่อมาที่ **AWS Lambda (Trigger Function)**
3.  Lambda ทำการตรวจสเปคไฟล์และส่งพารามิเตอร์สั่งจ้างรันงานประมวลผลแปลงไฟล์ที่ **AWS Elemental MediaConvert**
4.  MediaConvert ทำการแปลงไฟล์วิดีโอออกเป็นหลายระดับความละเอียดพร้อมทำฟังก์ชันปรับขนาดแบนด์วิดท์ตามเครื่องเล่น (HLS formats) และนำไฟล์ผลลัพธ์ไปเก็บบันทึกถาวรลงใน **Amazon S3 (Delivery Bucket)**
5.  **Users** ทั่วโลกเรียกชมคลิปผ่านเครื่องเล่นวิดีโอโดยทราฟฟิกจะวิ่งรับชมผ่านระบบแคชความเร็วสูง **Amazon CloudFront** เพื่อลดภาระการโหลดไฟล์ซ้ำจากถังเก็บหลัก S3
