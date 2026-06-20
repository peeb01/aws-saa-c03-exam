# 03 Networking & Content Delivery - Deep-Dive Exam Cheat-Sheet

---

## 1. VPC (Virtual Private Cloud)

### Deep-Dive Concepts & Sub-features
Amazon VPC คือระบบเครือข่ายส่วนตัวเสมือนบน AWS คอยกำหนดขอบเขตความปลอดภัยและเส้นทางการไหลของทราฟฟิก

*   **Subnets:**
    *   **Public Subnet:** ซับเน็ตที่มีตารางเส้นทาง (Route Table) ระบุทราฟฟิก `0.0.0.0/0` ชี้ตรงไปยัง **Internet Gateway (IGW)** เครื่องภายในจะมี Public IP หรือ Elastic IP เพื่อสื่อสารออกนอกโลกและให้คนภายนอกเรียกเข้าหาได้
    *   **Private Subnet:** ไม่มีเส้นทางตรงไป IGW แต่ต้องการสื่อสารออกอินเทอร์เน็ตเพื่อโหลดอัปเดต โดยจะชี้ทราฟฟิก `0.0.0.0/0` ไปที่ **NAT Gateway** (ซึ่งตั้งอยู่ใน Public Subnet) คนภายนอกไม่สามารถยิงคำขอตรงเข้าหา Private Subnet ได้
    *   **Isolated Subnet:** ซับเน็ตปิดกั้นเครือข่ายภายนอกโดยสมบูรณ์ ไม่มีทั้งการชี้ไป IGW หรือ NAT Gateway เหมาะสำหรับ Database
*   **NAT Devices:**
    *   **NAT Gateway (Managed):** บริการ Managed Service ขยายแบนด์วิดท์อัตโนมัติ (สูงสุด 100 Gbps) รองรับทราฟฟิก IPv4 แนะนำให้เปิดใช้งาน 1 NAT Gateway ต่อ 1 Availability Zone (AZ) เพื่อป้องกันระบบเครือข่ายพังพร้อมกันข้ามโซน (**NAT Gateway HA Regional Setup**)
    *   **NAT Instance:** เครื่อง EC2 ที่ลงคอนฟิกนำผ่านเน็ต ต้องปิดการตรวจสอบต้นทาง/ปลายทาง (Source/Destination Check) ปัจจุบันไม่แนะนำเพราะเสี่ยงเป็นคอขวดและต้องสเกลเอง
    *   **Egress-Only Internet Gateway:** บริการช่วยทำหน้าที่คล้าย NAT Gateway แต่สำหรับทราฟฟิก **IPv6** ขาออกเท่านั้น ป้องกันการเชื่อมต่อไร้ทิศทางขากลับเข้ามา
*   **Bastion Hosts:** เครื่อง EC2 ใน Public Subnet เพื่อทำหน้าที่เป็นสะพานเชื่อมต่อผ่าน SSH/RDP เข้าไปจัดการเครื่องใน Private Subnets ได้อย่างปลอดภัย
*   **Firewalls in VPC:**
    *   **Security Groups:** ไฟร์วอลล์ระดับอินสแตนซ์ (EC2/ENI) ทำงานแบบ **Stateful** (คำขอเข้าที่ผ่านจะได้รับอนุญาตให้ออกอัตโนมัติ และขาออกที่ยอมจะยอมขาเข้าอัตโนมัติ) รองรับเฉพาะกฎ **Allow** เท่านั้น
    *   **NACLs (Network Access Control Lists):** ไฟร์วอลล์ระดับซับเน็ต ทำงานแบบ **Stateless** (ต้องกำหนดทราฟฟิกขาเข้า Inbound และขาออก Outbound แยกจากกันอย่างชัดเจน) รองรับกฎทั้งแบบ **Allow และ Deny** เรียงลำดับความสำคัญของกฎตามเลขหมาย (เช่น กฎหมายเลข 100 ทำงานก่อนหมายเลข 200)
*   **VPC Connection & Endpoints:**
    *   **VPC Peering:** เชื่อมต่อ 2 VPC เข้าหากันแบบจับคู่ตรงบนโครงข่าย AWS ปลอดภัยและไม่ใช้อินเทอร์เน็ต ไม่รองรับการเชื่อมต่อข้ามตัวกลาง (**Non-Transitive Routing**)
    *   **AWS Transit Gateway:** อุปกรณ์เราเตอร์กลางสำหรับเชื่อม VPC และออนพรีมิสเน็ตเวิร์กหลายร้อยรายการเข้าด้วยกันแบบ Hub-and-Spoke Mesh ช่วยจัดการเครือข่ายระดับองค์กรได้สะดวกและรวมศูนย์
    *   **AWS Direct Connect (DX) & DX Gateway:** *Direct Connect* คือคู่สายสัญญาณไฟเบอร์กายภาพเฉพาะเชื่อมจากออนพรีมิสตรงเข้าจุด AWS เพื่อตัดปัญหาวิ่งผ่านเน็ตเวิร์กอินเทอร์เน็ตสาธารณะ; *Direct Connect Gateway* ช่วยให้เชื่อมต่อไปยัง VPC ใน Region ต่างๆ ข้ามบัญชีได้
    *   **AWS Site-to-Site VPN:** ลิงก์ VPN ความปลอดภัยสูงเชื่อมผ่านโปรโตคอล IPsec วิ่งบนอินเทอร์เน็ตสาธารณะ (สร้างได้เร็วกว่า DX แต่ความเสถียรขึ้นกับอินเทอร์เน็ต)
    *   **Gateway Endpoints:** จุดเชื่อมต่อสำหรับเข้าใช้ **Amazon S3** และ **DynamoDB** เท่านั้น (บริการฟรี ไม่มีค่าทราฟฟิก ไม่ใช้ ENI ทำงานผ่านระดับ Route Table)
    *   **Interface Endpoints (AWS PrivateLink):** จุดเชื่อมสำหรับต่อบริการอื่นๆ ของ AWS หรือ SaaS นอกระบบ โดยจะสร้าง **Elastic Network Interface (ENI)** และจ่าย Private IP ภายใน VPC มีการคิดค่าธรรมเนียมตามปริมาณข้อมูลที่ส่งผ่าน
*   **VPC Monitoring & Inspection:**
    *   **VPC Flow Logs:** บันทึกข้อมูลเมทาดาต้าของทราฟฟิก IP ขาเข้าและขากลับทั้งหมดภายใน VPC ส่งไปเก็บที่ S3 หรือ CloudWatch Logs
    *   **Traffic Mirroring:** คัดลอกสำเนาข้อมูลทราฟฟิกในระดับ ENI ส่งไปให้ระบบตรวจสอบ เช่น IDS/IPS หรือเครื่องมือตรวจจับมัลแวร์ภายนอกวิเคราะห์
    *   **VPC Reachability Analyzer:** เครื่องมือทดสอบจำลองเส้นทางทราฟฟิกช่วยสืบสวนหาคอขวดที่ตั้งค่าผิดพลาด (เช่น ติดที่ SG, NACL หรือ Route)
    *   **AWS Network Firewall:** บริการไฟร์วอลล์ระดับ Layer 3-7 สำหรับตรวจคัดกรองข้อมูลทราฟฟิกเข้าออก VPC ในระดับละเอียด (DPI)
    *   **Amazon VPC Lattice:** เลเยอร์เครือข่ายสำหรับ Microservices เชื่อมโยงและติดตามแอปพลิเคชันโดยไม่ขึ้นกับปัญหาทับซ้อนไอพีซับเน็ต

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Block specific IP address / Deny rules:* -> **NACLs** (Security Group ทำไม่ได้เพราะมีแต่ Allow เท่านั้น)
    *   *Free / Privately access S3/DynamoDB from VPC:* -> **Gateway Endpoint**
    *   *Privately access third-party SaaS from VPC:* -> **AWS PrivateLink** (Interface Endpoint)
    *   *Hub-and-spoke mesh / Connect multiple VPCs and on-premises:* -> **AWS Transit Gateway**
    *   *Establish private connection / No public internet / High throughput:* -> **AWS Direct Connect (DX)**
    *   *Backup connection for Direct Connect / Cost-effective DR connection:* -> **AWS Site-to-Site VPN**
*   **Trap Warnings:**
    *   *Stateless NACL Return Traffic:* หากเปลี่ยนพอร์ต Inbound ใน NACL อย่าลืมเปิดพอร์ตขากลับ Outbound (Ephemeral Ports `1024-65535`) ไม่เช่นนั้นทราฟฟิกจะไม่สามารถตอบกลับไปยังเครื่องลูกข่ายได้เนื่องจาก NACL ทำงานแบบ Stateless
    *   *VPC Peering limits:* VPC Peering ไม่สามารถแชร์ข้าม VPC 3 ตัวในแบบห่วงโซ่ได้ (เช่น A ต่อ B, B ต่อ C แต่ A จะสื่อสารกับ C ไม่ได้ หากต้องการต้องทำ Peering A-C ด้วย หรือใช้ Transit Gateway)

---

## 2. Elastic Load Balancing (ELB)

### Deep-Dive Concepts & Sub-features
ELB คือระบบกระจายโหลดทราฟฟิกการเข้าใช้งานไปยังเป้าหมายหลังบ้าน (เช่น EC2, ECS, IP, Lambda) มีสามประเภทหลักสำหรับทำข้อสอบ:

*   **Application Load Balancer (ALB - Layer 7):**
    *   วิเคราะห์ทราฟฟิกในระดับแอปพลิเคชัน (HTTP/HTTPS)
    *   รองรับการเราต์ตามข้อมูลโครงสร้าง: **Path-based** (เช่น `/api` ไปกลุ่มหนึ่ง, `/images` ไปอีกกลุ่ม), **Host-based** (เช่น `api.example.com`), Query parameters, HTTP headers
    *   เหมาะสำหรับสถาปัตยกรรม Microservices และ Web Application
*   **Network Load Balancer (NLB - Layer 4):**
    *   วิเคราะห์ทราฟฟิกในระดับโปรโตคอลขนส่งข้อมูล (TCP/UDP/TLS)
    *   มีความเร็วและประสิทธิภาพสูงมาก Latency ต่ำระดับ Microsecond รองรับทราฟฟิกได้หลายล้านคำขอต่อวินาที
    *   มีจุดเด่นคือจะได้รับที่อยู่ **Static IP** (หรือ Elastic IP) คงที่สำหรับแต่ละ AZ (ALB จะได้มาเป็นชื่อ Domain Name ที่ไอพีสลับไปมาตลอดเวลา)
*   **Gateway Load Balancer (GWLB - Layer 3):**
    *   ออกแบบมาสำหรับการเราต์ทราฟฟิกทั้งหมดผ่านตู้ตรวจสอบความปลอดภัยเสมือน (Virtual Security Appliances) เช่น ไฟร์วอลล์ หรือระบบตรวจจับผู้บุกรุก (IDS/IPS) ก่อนปล่อยทราฟฟิกผ่านไปหน้าแอป
*   **ELB Configuration Features:**
    *   **Cross-Zone Load Balancing:** เมื่อเปิดใช้งาน ELB จะกระจายโหลดเฉลี่ยไปยังเป้าหมายปลายทางในทุก AZ เท่ากันโดยไม่ต้องอิงตามจำนวนเครื่องใน AZ นั้นๆ (ALB เปิดใช้โดยค่าเริ่มต้น, NLB ต้องสั่งเปิดเอง)
    *   **Sticky Sessions (Session Affinity):** ช่วยผูกมัดผู้ใช้รายเดิมให้ถูกส่งไปยังเซิร์ฟเวอร์เป้าหมายหลังบ้านเครื่องเดิมตลอดอายุเซสชันโดยใช้งาน Cookie (สนับสนุนบน ALB)
    *   **SSL/TLS Termination:** ถอดรหัสใบรับรองความปลอดภัยที่ตัว ELB ช่วยผ่อนเบาการประมวลผลเซิร์ฟเวอร์หลังบ้าน
    *   **Deregistration Delay (Connection Draining):** หน่วงเวลาชะลอการดึงเครื่องหลังบ้านออกจากระบบบาลานเซอร์ เพื่อเปิดโอกาสให้คำขอเก่าที่ทำงานค้างอยู่ประมวลผลเสร็จสิ้นเรียบร้อยก่อนปิดตัวเครื่องไป

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Path-based routing / HTTP/HTTPS routing / Microservices:* -> **ALB**
    *   *Millions of requests / Extreme performance / Static IP required / TCP/UDP:* -> **NLB**
    *   *Deploy firewall appliances / Layer 3 inspection:* -> **GWLB**
    *   *Maintain user session on the same server:* -> **Sticky Sessions**
*   **Trap Warnings:**
    *   *NLB Target Group IP address:* หาก NLB ชี้เป้าหมายหลังบ้านด้วยไอพี (IP targets) จะไม่สามารถเห็น IP จริงของ User ต้นทางผ่าน TCP packet ได้ (IP จะถูกแปลงเป็น Private IP ของ NLB แทน) วิธีแก้คือต้องเขียนโค้ดเปิดอ่านค่าผ่าน **Proxy Protocol v2**

---

## 3. Route 53

### Deep-Dive Concepts & Sub-features
Amazon Route 53 คือบริการระบบแปลชื่อโดเมน (DNS) ที่มีความเสถียรและทนทานสูง

*   **Record Types:**
    *   **CNAME:** ชี้ชื่อโดเมนไปยังโดเมนอื่น (เช่น `www.example.com` -> `lb-123.amazonaws.com`) แต่ชี้ที่ระดับ Root Domain (เช่น `example.com`) ไม่ได้
    *   **Alias:** เรคคอร์ดพิเศษของ Route 53 ชี้ Root Domain ไปยังทรัพยากรของ AWS (เช่น CloudFront, ALB) ได้โดยตรงและฟรี (แถมรองรับการเปลี่ยนไอพีปลายทางอัตโนมัติ)
*   **Routing Policies (นโยบายการเราต์ออกสอบ):**
    *   **Simple:** เราต์ไปไอพีปลายทางเดียวหรือส่งคืนชุดไอพีกลับไปให้บราวเซอร์เลือกสุ่มใช้เอง (ไม่สามารถผูกติดกับ Health Checks ได้)
    *   **Weighted:** กำหนดเป้าหมายส่งทราฟฟิกตามสัดส่วนค่าน้ำหนัก % ที่ระบุ (เหมาะกับการทำ Canary Deployments)
    *   **Latency-based:** คัดเลือกส่งผู้ใช้งานไปยัง AWS Region ที่มีระดับ Latency ต่ำที่สุดสำหรับพื้นที่ของผู้ใช้นั้นๆ
    *   **Failover (Active-Passive):** ผูกมัดกับ Health Checks หากระบบหลัก (Primary) ล่มจะสลับเราต์พาผู้ใช้ไปที่ระบบสำรอง (Secondary/DR) ทันที
    *   **Geolocation:** เลือกเราต์ทราฟฟิกโดยตรวจสอบจากตำแหน่งประเทศหรือทวีปของคนเรียกใช้งานโดเมน (เช่น คนยุโรปเข้าหน้าเว็บภาษาอังกฤษ, คนไทยเข้าหน้าภาษาไทย)
    *   **Geoproximity:** เราต์โดยคำนวณระยะทางฟิสิกส์ระหว่างผู้ใช้กับทรัพยากร และสามารถกำหนดเปลี่ยนขอบเขตพื้นที่บริการได้ผ่านค่า Bias
    *   **IP-based:** เลือกทราฟฟิกตามช่วง CIDR block ของ IP ต้นทางที่เรียกเข้ามา
    *   **Multi-value Answer:** คล้าย Simple แต่สามารถผูกติดกับ Health Checks เพื่อคัดเลือกส่งคืนที่อยู่ไอพีที่มีสุขภาพดีสูงสุด 8 ตัวกลับไปให้บราวเซอร์สุ่มใช้งาน
*   **Route 53 Resolver:** ช่วยทำหน้าแปลและเชื่อมส่ง DNS Queries ระหว่างเครือข่าย VPC ในคลาวด์และ Active Directory/DNS ในเครือข่ายออนพรีมิส (Hybrid Cloud DNS)

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Active-Passive Disaster Recovery / Shift traffic on failure:* -> **Failover Routing Policy**
    *   *Canary testing / Percent of traffic split:* -> **Weighted Routing Policy**
    *   *Lowest network latency for users:* -> **Latency-based Routing Policy**
    *   *Map root domain to AWS load balancer:* -> **Alias Record** (ห้ามตอบ CNAME)
    *   *Hybrid DNS / Translate on-premise domain names from VPC:* -> **Route 53 Resolver (Inbound/Outbound Endpoints)**
*   **Trap Warnings:**
    *   *CNAME on Root Domain:* จำกฎเหล็กไว้ว่า **ห้ามนำ CNAME ไปตั้งค่าที่ระดับ Root domain (Zone Apex)** เช่น `example.com` หากโจทย์กำหนดให้ยิง Root domain ชี้เข้าโหลดบาลานเซอร์ ต้องใช้ **Alias Record** เท่านั้น

---

## 4. CloudFront & AWS Global Accelerator

### Deep-Dive Concepts & Sub-features
บริการสองตัวนี้ใช้เครือข่ายความเร็วสูงระดับโลกของ AWS (Global Edge Network) เพื่อลดความหน่วงระยะทางสำหรับผู้ใช้

*   **Amazon CloudFront (Content Delivery Network - CDN):**
    *   แคชข้อมูลเว็บ (Static/Dynamic content) ไว้ที่ Edge Locations ใกล้ตัวผู้ใช้ทั่วโลก ช่วยเร่งการโหลดและลดปริมาณงาน (Offload) บนเว็บเซิร์ฟเวอร์หลัก (Origin)
    *   **Cache Behaviors & Invalidation:** จัดการกฎการแคชแยกตาม Path; หากระบบมีการอัปเดตไฟล์สำคัญและต้องการเคลียร์แคช Edge ทันที สามารถสั่งทำ **Cache Invalidation** (มีค่าบริการเพิ่มหากสั่งบ่อยเกินโควตา)
    *   **Origin Groups & Origin Shield:** *Origin Groups* ช่วยตั้งค่าเซิร์ฟเวอร์หลัก 2 ตัวทำ Failover อัตโนมัติ; *Origin Shield* เลเยอร์แคชกลางช่วยลดจำนวนคำขอวิ่งเข้า Origin
    *   **Edge Computing:**
        *   **CloudFront Functions:** สคริปต์ JavaScript เบาและเร็วมาก รันระดับ Edge สำหรับปรับปรุง Header, Redirect URL หรือเปลี่ยนทิศทาง (ไม่สามารถต่อฐานข้อมูลภายนอกได้)
        *   **Lambda@Edge:** ฟังก์ชัน Node.js/Python รันประมวลผลงานซับซ้อน เช่น การแก้ไขดัดแปลงข้อมูล การเรียก API หรือการดึงข้อมูลจากภายนอก
*   **AWS Global Accelerator:**
    *   ใช้สำหรับบริการที่ไม่สามารถแคชได้ หรือแอปที่ไม่ได้อิง HTTP/HTTPS (เช่น เกมออนไลน์, VoIP, IoT, API)
    *   มอบที่อยู่ไอพีคงที่ 2 ตัวแบบ **Anycast IP** เมื่อผู้ใช้ร้องขอ ทราฟฟิกจะถูกดูดเข้าสู่ Edge Location ที่ใกล้ที่สุด แล้ววิ่งตรงผ่าน Private Backbone Network ของ AWS ไปยังปลายทางแทนที่จะวิ่งอ้อมบนเครือข่ายอินเทอร์เน็ตสาธารณะ

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Static/Dynamic Web Cache / Low-latency HTTP delivery:* -> **Amazon CloudFront**
    *   *Non-HTTP traffic (TCP/UDP) / VoIP / Gaming / Static IP for global endpoints:* -> **AWS Global Accelerator**
    *   *Modify HTTP requests at the edge / Sub-millisecond execution:* -> **CloudFront Functions**
    *   *Complex computations at the edge / Fetch external data / Image resizing:* -> **Lambda@Edge**
*   **Trap Warnings:**
    *   *CloudFront vs Global Accelerator:* CloudFront ปรับปรุงความเร็วเน็ตผ่านการ **Cache** ข้อมูลเว็บ (HTTP/S); Global Accelerator ปรับปรุงความเร็วเน็ตผ่านการเราต์ทราฟฟิกผ่าน **AWS Backbone Network** โดยเหมาะกับงานประเภท non-HTTP หรือทราฟฟิกสดที่ไม่ใช่การแคช

### Real-World Use Cases
*   **Global Video Streaming:** เก็บไฟล์วิดีโอต้นฉบับใน S3 แล้วใช้ CloudFront แคชไฟล์วิดีโอนี้ที่ Edge Locations ทั่วโลก ช่วยให้ผู้เล่นวิดีโอดูได้ไหลลื่นไม่มีสะดุด
*   **Global Game Server Connection:** ใช้ Global Accelerator เพื่อกำหนด Anycast IP ให้กับผู้เล่นเกมมือถือจากต่างประเทศ โดยทราฟฟิกจะวิ่งผ่านโครงข่าย AWS เข้าไปรันประมวลผลในเซิร์ฟเวอร์ที่สิงคโปร์ได้อย่างเสถียรที่สุด

### Architecture Integration Pattern: High Availability Multi-Region API Gateway

```text
             [ Global Users ]
                     │
                     ▼ (1) Request API (DNS Query)
             ┌───────────────┐
             │   Route 53    │ (Latency + Health Checks)
             └───────┬───────┘
                     │
         ┌───────────┴───────────┐ (2) Route to Lowest Latency
         ▼                       ▼
 ┌───────────────┐       ┌───────────────┐
 │API Gateway R1 │       │API Gateway R2 │ (Multi-Region)
 └───────┬───────┘       └───────┬───────┘
         │                       │
         ▼ (3) Failover          ▼ (3) Failover
 ┌───────────────┐       ┌───────────────┐
 │ Database R1   │ ◄───► │ Database R2   │ (Replicated DB)
 └───────────────┘       └───────────────┘
```
1.  **Global Users** ส่งคำขอใช้บริการ API
2.  **Route 53** ทำการเราต์คำขอผ่านนโยบาย **Latency-based Routing** ร่วมกับ **Health Checks**
3.  ทราฟฟิกวิ่งเข้าสู่ **API Gateway** ที่กระจายตัวอยู่ในภูมิภาคที่ใกล้ที่สุด (เช่น `us-east-1` และ `ap-southeast-1`)
4.  กรณีที่ Region ใดล่ม Route 53 จะตัดการสลับชี้เป้าหมายไปหาอีก Region อัตโนมัติ และข้อมูลหลังบ้านจะถูกซิงค์ผ่านฐานข้อมูลข้ามเขตแบบเรียลไทม์
