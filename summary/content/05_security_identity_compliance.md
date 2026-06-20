# 05 Security, Identity & Compliance - Deep-Dive Exam Cheat-Sheet

---

## 1. IAM (Identity and Access Management) & Governance

### Deep-Dive Concepts & Sub-features
AWS IAM จัดการความปลอดภัยสำหรับการเข้าถึงทรัพยากรต่างๆ ของ AWS

*   **Identities & Policies:**
    *   **IAM Users & Groups:** Users แทนบุคคลหรือระบบ; Groups รวบรวมสิทธิ์ผู้ใช้เพื่อให้ง่ายต่อการจัดการ
    *   **IAM Roles:** มอบสิทธิ์การเข้าถึงชั่วคราวแก่เครื่องบริการ (เช่น EC2) หรือผู้ใช้จากนอกบัญชีผ่านระบบยืนยันตนชั่วคราว (STS) โดยไม่มีการเก็บรหัสผ่านถาวร
    *   **IAM Policies (JSON):** นโยบายระบุสิทธิ์ ประกอบด้วย:
        *   `Effect`: Allow หรือ Deny
        *   `Principal`: ผู้ที่ได้รับนโยบายนี้
        *   `Action`: คำสั่งบริการ (เช่น `s3:GetObject`)
        *   `Resource`: ARN ของทรัพยากรปลายทาง (เช่น `arn:aws:s3:::my-bucket/*`)
        *   `Condition`: เงื่อนไขเพิ่มเติม (เช่น จำกัด Source IP หรือ MFA เท่านั้น)
*   **Policy Evaluation Logic (กฎเหล็กการประเมินสิทธิ์):**
    1.  **Explicit Deny:** มีกฎปฏิเสธชัดเจน -> บ Lock ทันทีเด็ดขาด (ชนะทุก Allow)
    2.  **Explicit Allow:** มีกฎอนุญาตชัดเจน -> อนุญาตให้เข้าถึงได้
    3.  **Default Deny (Implicit Deny):** ไม่มีเขียนสิทธิ์อนุญาตเลย -> ปฏิเสธการเข้าใช้โดยอัตโนมัติ
*   **Permissions Boundaries:** นโยบายขั้นสูงสุดที่กำหนดไว้เพื่อควบคุมสิทธิ์ของ IAM User หรือ Role ไม่ให้สามารถยกระดับหรือมีสิทธิ์เกินกว่าเกณฑ์นี้ได้ แม้ว่าจะไปได้รับนโยบาย Administrator Access ในภายหลังก็ตาม (มักใช้ควบคุมสิทธิ์ที่ Admin ย่อยไปแจกจ่ายให้คนอื่น)
*   **IAM Identity Center (AWS SSO):** ระบบรวมศูนย์เชื่อมต่อการลงชื่อเข้าใช้งานทรัพยากรและคอนโซลของ AWS ทั้งหมดร่วมกับ Active Directory ขององค์กร
*   **AWS Control Tower:** บริการจัดตั้งพื้นที่ทำงานข้ามบัญชีอย่างปลอดภัย (Landing Zone) บังคับใช้กฎเกณฑ์ความปลอดภัยแบบอัตโนมัติ (Guardrails) ภายใต้โครงสร้าง AWS Organizations

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Temporary credentials / EC2 accessing S3:* -> **IAM Role** (ห้ามเก็บ Access Key ไว้ในเครื่องเด็ดขาด)
    *   *Limit maximum possible permissions / Delegate admin safely:* -> **Permissions Boundary**
    *   *Single Sign-On / Centralized Active Directory integration:* -> **IAM Identity Center**
    *   *Deploy multi-account landing zone / Compliance guardrails:* -> **AWS Control Tower**
*   **Trap Warnings:**
    *   *Access Key leaks:* หากโจทย์ระบุว่าเจอ Access Key หลุดไปสาธารณะ (e.g. GitHub) หรือต้องให้ Program เข้าถึง AWS ห้ามเก็บ Access Key ไว้ใน FileProgram หรือเครื่อง EC2 ให้เปิดใช้งาน **IAM Role** และสวมสิทธิ์ให้กับ Instance ผ่าน Instance Profile เสมอ

---

## 2. AWS KMS & Encryption Keys

### Deep-Dive Concepts & Sub-features
AWS KMS (Key Management Service) จัดทำและควบคุมกุญแจเข้ารหัสลับข้อมูล (Encryption Keys) ทำงานร่วมกับทรัพยากรส่วนใหญ่ของ AWS ในการเข้ารหัสแบบ Rest/Transit

*   **Key Types:**
    *   **AWS Managed Keys:** กุญแจที่บริการ AWS สร้างขึ้นและหมุนเวียนรอบกุญแจให้แบบอัตโนมัติ ไม่คิดค่าบริการรายเดือนและผู้ใช้ปรับนโยบายความปลอดภัยไม่ได้
    *   **Customer Managed Keys (CMK):** สร้างและควบคุมโดยผู้ใช้ ปรับปรุงนโยบายการเข้าใช้ (Key Policies) และควบคุมการสลับรอบกุญแจ (Key Rotation) ได้ตามกฎระเบียบองค์กร
    *   **Symmetric vs Asymmetric:**
        *   *Symmetric (สมมาตร):* กุญแจตัวเดียวใช้ทั้งเข้ารหัสและถอดรหัส (รองรับการขยายโดยตรงผ่านคำสั่ง KMS API)
        *   *Asymmetric (อสมมาตร):* มี Public Key เพื่อเข้ารหัส/ตรวจสอบลายเซ็น และ Private Key เพื่อถอดรหัส/สร้างลายเซ็น (จำเป็นต้องส่งกุญแจ Public ไปให้ระบบภายนอกใช้ได้)
    *   **Multi-Region Keys:** กุญแจเข้ารหัสที่มีคุณสมบัติแชร์และจำลองคีย์เดียวกัน (เดียวกันทั้ง Key ID และ Key Material) ไปใช้ในภูมิภาคอื่นได้ ช่วยให้การเคลื่อนย้ายข้อมูลที่เข้ารหัสข้ามเขตสะดวกขึ้นโดยไม่ต้องถอดรหัสและเข้ารหัสซ้ำด้วยกุญแจใหม่
*   **Custom Key Store (CloudHSM Integration):** เป็นระบบที่ต่อยอด KMS ให้ใช้ตู้เก็บกุญแจระดับฮาร์ดแวร์จริงตัวช่วยอย่าง AWS CloudHSM แทนการเก็บในคีย์แชร์ระบบปกติ เพื่อตอบโจทย์ความปลอดภัยที่เข้มงวดที่สุดระดับสากล

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Encrypt database backup / customer managed key rotation:* -> **KMS CMK**
    *   *Share encrypted AMI across accounts:* -> **KMS Custom Key Policy (Share key with target account)**
    *   *FIPS 140-2 Level 3 compliance / Single-tenant cryptography:* -> **CloudHSM**
    *   *Replicate encrypted database without re-encryption overhead:* -> **KMS Multi-Region Keys**
*   **Trap Warnings:**
    *   *KMS Key Policies:* IAM Policy เพียงอย่างเดียวไม่สามารถปลดสิทธิ์ KMS CMK ได้ หากต้องการให้ Instance ข้ามบัญชีสามารถอ่าน File ที่เข้ารหัสได้ ต้องระบุการยอมรับสิทธิ์ที่หน้า **KMS Key Policy** ของกุญแจนั้นด้วย (Key Policy คือประตูด่านแรกของ KMS)

---

## 3. Data Protection, Compliance & Firewalls

### Deep-Dive Concepts & Sub-features
*   **Network Security & Firewalls:**
    *   **AWS WAF (Web Application Firewall):** บ Lock ภัยคุกคามหน้าเว็บระดับ Application (Layer 7) ป้องกันการสแกน SQL Injection, Cross-Site Scripting (XSS), บ Lock ตาม IP ประเทศ หรือจำกัดปริมาณการคลิกป่วน (Rate Limiting) สามารถนำไปเปิดใช้งานกับ CloudFront, ALB, API Gateway หรือ AppSync
    *   **AWS Shield:**
        *   *Shield Standard:* บริการฟรี ป้องกันภัย DDoS ระดับ Layer 3/4 (เช่น SYN Floods) ให้บริการอัติโนมัติแก่ทุกเครื่อง
        *   *Shield Advanced:* บริการรายเดือนราคาแพง ป้องกันภัยระดับสูง มีทีมผู้เชี่ยวชาญช่วยรบแก้ต่าง DDoS 24/7 (DRT team) และชดเชยค่าบริการส่วนเกินที่เกิดจากการโจมตียิง Traffic พังระบบ
    *   **AWS Firewall Manager:** ระบบกลางควบคุมการแจกจ่ายกฎไฟร์วอลล์ (WAF, Shield, SG) ข้ามทุกบัญชีผู้ย่อยในองค์กร Organizations
*   **Threat Detection & Vulnerability Scanning:**
    *   **AWS GuardDuty:** ปัญญาประดิษฐ์ (ML) วิเคราะห์หาความผิดปกติของพฤติกรรมและการโจมตีระบบโดยคอยแอบอ่านจาก Log สำคัญ (VPC Flow Logs, DNS Query Logs, CloudTrail Logs) โดยไม่สร้างโหลดให้กับระบบประมวลผล Server
    *   **AWS Inspector:** เครื่องมือแสกนหาช่องโหว่ความปลอดภัย (Vulnerability) และความสอดคล้องนโยบายของ OSServer EC2, ภาพ Image ใน ECR และ Function Lambda
    *   **Amazon Macie:** ใช้ AI ค้นหา วิเคราะห์ และจัดกลุ่มประเภทข้อมูลส่วนบุคคลที่อ่อนไหว เช่น รหัสบัตรประชาชน รหัสผ่าน หรือเบอร์โทรศัพท์ (PII - Personally Identifiable Information) ที่หลุดไปจัดเก็บอยู่ภายในถังเก็บ S3 Bucket
    *   **AWS Security Hub:** บัญชีแดช Board ส่วนกลางรวบรวมข้อมูลสถานะและการผ่านเกณฑ์ความปลอดภัยของทรัพยากรทั้งหมดใน AWS
    *   **Amazon Detective:** ระบบกราฟจำลองช่วยเจาะสืบหาต้นเหตุและวิเคราะห์ความปลอดภัยย้อนหลังจากสัญญานตรวจพบความผิดปกติ
*   **Identity Federation & Directory Services:**
    *   **AWS Directory Service:**
        *   *Managed Microsoft AD:* ติดตั้งและรัน AD จริงบน AWS เหมาะกับ App ที่พึ่งพาการตั้งค่า AD
        *   *AD Connector:* ทำหน้าที่เป็นสะพานผ่านการเชื่อมต่อไปที่ AD ในเครื่อง On-premises ของบริษัท โดยไม่มีการจำลองหรือซิงค์ข้อมูลผู้ใช้งานขึ้นมาเก็บในระบบของคลาวด์ (Proxy queries)
        *   *Simple AD:* ระบบ AD ขนาดเล็กราคาประหยัดบน Linux (Samba-based) สำหรับงานทั่วไปที่ไม่ซับซ้อน
    *   **Cognito:**
        *   **User Pools (CUP):** บริการระบบสมัครสมาชิก ยืนยันตัวตน และลงชื่อเข้าสู่หน้า Application (Authentication) สนับสนุนการเข้าใช้งานผ่าน Social Login (Facebook, Google, Apple) หรือระบบ Enterprise (SAML/OIDC)
        *   **Identity Pools (Federated Identities):** นำ Token ที่ยืนยันตนจาก User Pools มาแลกรับชุดกุญแจยืนยันสิทธิ์ความปลอดภัยชั่วคราวของ AWS (STS credentials) เพื่อให้อุปกรณ์หรือ Client สามารถดึง File จาก S3 หรือเซฟข้อมูลใน DynamoDB ได้โดยตรงโดยไม่ต้องมี Application หลังบ้านคั่นกลาง (Authorization)
*   **Compliance Tools:**
    *   **AWS Artifact:** พอทัลให้ดาวน์โหลดเอกสารมาตรฐานความปลอดภัย (เช่น ISO, PCI DSS, SOC reports) เพื่อใช้ยื่นรายงานผู้ตรวจสอบ
    *   **AWS Audit Manager:** ระบบช่วยรวบรวมรอยประวัติหลักฐานทางเทคนิคในระบบเพื่อทำรายงานสอดคล้องมาตรฐานกฎหมายความปลอดภัย

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Block SQL injection / Block IP addresses / Rate limiting Web App:* -> **AWS WAF**
    *   *Analyze S3 data for Personally Identifiable Information (PII) / credit cards:* -> **Amazon Macie**
    *   *Continuous vulnerability scanning of EC2/Container images:* -> **AWS Inspector**
    *   *Intelligent threat detection / DNS and VPC log analysis:* -> **AWS GuardDuty**
    *   *Active Directory proxy to on-premises AD without syncing:* -> **AD Connector**
    *   *Mobile App Authentication (Identity Provider):* -> **Cognito User Pools**
    *   *Mobile App Authorization (Access AWS resources directly):* -> **Cognito Identity Pools**
    *   *Download compliance reports / SOC 2 / ISO:* -> **AWS Artifact**
*   **Trap Warnings:**
    *   *WAF vs Shield:* หากเป้าหมายคือสกัดกั้น Traffic หน้าเว็บ SQL Injection, XSS, HTTP flood, หรือคัดกรองพฤติกรรมการเรียกเว็บ ให้ตอบ WAF; หากเป็นสัญญานยิงถล่มระดับ NetworkTraffic ขนาดใหญ่ (DDoS Layer 3/4) ให้ตอบ Shield Advanced
    *   *Cognito User Pools vs Identity Pools:* CUP ทำหน้าที่ยืนยันตนผู้ใช้ว่าผู้ใช้คือใคร (Authentication - Sign Up / Sign In); Identity Pools ทำหน้าที่แจกสิทธิ์เข้าถึงของ AWS ให้ผู้ใช้รายนั้นเอาสิทธิ์ชั่วคราวไปสั่งการทรัพยากร (Authorization - Access S3/DynamoDB)

### Real-World Use Cases
*   **SaaS App Mobile Login:** สร้าง Application มือถือโดยใช้งาน Cognito User Pools ในการทำระบบสมัครสมาชิกและ Login จากนั้นผู้ใช้ที่ผ่านเข้าระบบสำเร็จจะได้รับสิทธิ์ชั่วคราวจาก Cognito Identity Pools เพื่ออัปโหลด File ภาพโปร File ของตัวเองขึ้นสู่ S3 Folder ส่วนตัวได้อย่างปลอดภัย

### Architecture Integration Pattern: Secure Web Ingestion & Inspection

```text
  [ Mobile Client ]
          │
          ▼ (1) Send Request
  ┌───────────────┐
  │  CloudFront   │ ◄───(Inspect Layer 7 via AWS WAF)
  └───────┬───────┘
          │
          ▼ (2) Route Request
  ┌───────────────┐
  │ Load Balancer │ (ALB)
  └───────┬───────┘
          │
          ▼ (3) Forward Request
  ┌───────────────┐
  │  EC2 Web Svr  │ (Private Subnet)
  └───────────────┘
          ▲
          │ (5) Update Security Group (Block Attacker IP)
  ┌───────┴───────┐
  │  AWS Lambda   │ ◄───(4) Trigger Remediation (EventBridge)
  └───────────────┘
          ▲
          │ (VPC Flow Logs Threat Detected)
  [GuardDuty / SecHub]
```
1.  **Mobile Client** ส่งคำขอเขียนโพสต์หน้าเว็บผ่าน **Amazon CloudFront**
2.  CloudFront ถูกเชื่อมโยงเข้ากับ **AWS WAF** เพื่อสกัดภัยคุกคาม ล้างข้อมูล SQL Injection และประเมินประเทศปลายทางของ IPBrowser
3.  คำขอที่ปลอดภัยจะถูกส่งต่อเข้าสู่ระบบหลังบ้าน **Application Load Balancer (ALB)** และส่งต่อเข้าเครื่อง **EC2 (Web Server)**
4.  **Amazon GuardDuty** แอบคอยมอนิเตอร์ Log ขาเข้าผ่าน VPC Flow Logs เพื่อตรวจสอบว่าระบบไม่เจอบ Lock ข้อมูลสั่งสแกนเครือข่ายแปลกๆ หรือแอบเจาะ Port หลังบ้าน
5.  หากตรวจพบสิ่งผิดปกติในระดับวิกฤต GuardDuty จะส่งสัญญาณเหตุการณ์ไปยัง **Amazon EventBridge** เพื่อไปกระตุ้น **AWS Lambda** ให้เปลี่ยนค่า SG เพื่อบ LockIP ที่โจมตีเข้ามาทันที (Auto-remediation)


---

## ➔ การนำทาง (Navigation)
| [« กลับหน้าหลัก (README.md)](../README.md) | [« ก่อนหน้า: 04 Databases](04_databases.md) | [ถัดไป: 06 Analytics & Data Integration »](06_analytics_data_integration.md) |