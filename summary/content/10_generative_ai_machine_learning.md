# 10 Generative AI & Machine Learning - Deep-Dive Exam Cheat-Sheet

---

## 1. Generative AI & Machine Learning Services

### Deep-Dive Concepts & Sub-features
บริการปัญญาประดิษฐ์ (AI) สำเร็จรูป และแพลตฟอร์มการสร้างระบบ Machine Learning ในระบบคลาวด์ของ AWS

*   **Amazon Bedrock (Generative AI):**
    *   บริการแบบ Serverless สำหรับเข้าถึงโมเดลปัญญาประดิษฐ์พื้นฐานขนาดใหญ่ (Foundation Models - FMs เช่น Claude, Llama 2, Jurassic-2, Command) ของแบรนด์ระดับโลกและของ AWS เอง ผ่านการเรียกใช้ผ่าน **API** เดียวกัน
    *   ช่วยให้แอปสามารถนำโมเดลเหล่านั้นไปรันต่อยอด ค้นหาข้อมูล ปรับแต่งข้อมูลแบบเป็นส่วนตัวโดยไม่ต้องติดตั้งจัดตั้ง Infrastructure และระบบการเข้าถึงข้อมูลปลอดภัยสูงโดยที่ข้อมูลที่ส่งไปป้อนให้โมเดลประมวลผลจะไม่ถูกส่งกลับไปใช้สอนโมเดลเวอร์ชันสาธารณะอย่างเด็ดขาด
*   **Amazon SageMaker:**
    *   แพลตฟอร์มแบบครบวงจร (End-to-End) สำหรับเตรียมข้อมูล จัดเก็บ สร้าง ฝึกสอน (Train) และปล่อยดีพลอยบริการโมเดล Machine Learning ของตัวเองในระบบโปรดักชัน
    *   **SageMaker Canvas:** หน้าจอยูไอแบบ **No-code** ที่ช่วยให้คนทั่วไปที่ไม่ใช่โปรแกรมเมอร์ (เช่น นักวิเคราะห์ธุรกิจ) สามารถสร้างโมเดลและพยากรณ์ข้อมูลได้ง่ายผ่านระบบลากวางภาพกราฟิก
*   **Amazon Rekognition:** บริการ AI วิเคราะห์ข้อมูลรูปภาพและวิดีโออัจฉริยะ (เช่น ตรวจจับวัตถุ, วิเคราะห์ใบหน้าเพื่อแยกอารมณ์, ตรวจจับเนื้อหาที่ไม่เหมาะสมหรือลามกเพื่อทำการบล็อกเนื้อหา, สกัดตัวหนังสือในรูปภาพ)
*   **Amazon Textract:** ระบบสกัดข้อมูลตัวหนังสือ ตาราง หรือฟอร์มกรอกข้อความออกจากไฟล์เอกสารใบเสร็จ PDF หรือรูปภาพการลงทะเบียน ซึ่งมีความแม่นยำสูงและฉลาดกว่าการสแกนด้วยระบบ OCR ทั่วไป
*   **Amazon Kendra:** ระบบสแกนและสืบค้นความรู้ภายในองค์กรอัจฉริยะ โดยใช้พลังงานของ AI ค้นหาในแหล่งข้อมูลเอกสารหลากหลายค่าย (เช่น S3, Salesforce, SharePoint) และให้ผู้ใช้ถามคำถามด้วยภาษาธรรมดา (Natural Language Query) เพื่อพาวิ่งไปหาคำตอบที่ซ่อนอยู่ในย่อหน้าย่อยได้อย่างรวดเร็ว
*   **Amazon Polly:** ระบบแปลงตัวหนังสือ (Text) ให้ออกมาเป็นไฟล์เสียงพูดที่เป็นธรรมชาติ (Text-to-Speech) รองรับหลากหลายสำเนียง เสียงผู้หญิง/ชาย และเสียงธรรมชาติสไตล์เหมือนจริง (Neural TTS)
*   **Amazon Transcribe:** ระบบปัญญาประดิษฐ์แปลงเสียงพูดจากไฟล์วิดีโอ/เสียง หรือการบันทึกสตรีมเสียงพูดสดให้กลายมาเป็นไฟล์ตัวหนังสือตัวอักษรพิมพ์คำแปล (Speech-to-Text)
*   **Amazon Translate:** บริการแปลภาษาต่างประเทศข้ามกันด้วยระดับความถูกต้องแบบธรรมชาติเป็นธรรมชาติ
*   **Amazon Comprehend:** บริการประมวลผลวิเคราะห์หาประเด็น อารมณ์ความรู้สึก (Sentiment Analysis) หรือตรวจจับหาคำที่เป็นข้อมูลส่วนตัว PII ในระดับประโยคตัวอักษร (Natural Language Processing - NLP)
*   **Amazon Comprehend Medical:** ทำหน้าที่วิเคราะห์คำในเอกสารวิชาการแพทย์หรือประวัติการรักษาของคนไข้อย่างแม่นยำและถูกตามเงื่อนไขของมาตรฐานความปลอดภัย HIPAA
*   **Amazon Lex:** ระบบปัญญาประดิษฐ์สำหรับพัฒนาโปรแกรมเสียงตอบโต้หรือหุ่นยนต์สนทนาโต้ตอบ (Chatbot/Voicebot) อัจฉริยะด้วยเทคโนโลยีเดียวกับหูฟังสมาร์ทของ Amazon Alexa
*   **Amazon Connect:** บริการศูนย์บริการลูกค้าทางโทรศัพท์คลาวด์แบบดิจิทัล (Cloud Contact Center) ครบวงจรที่ช่วยจัดทำและประยุกต์ใช้ร่วมกับระบบแชทบอต Lex และระบบเสียงตอบรับอัตโนมัติได้อย่างรวดเร็ว
*   **Amazon Personalize:** ระบบวิเคราะห์พฤติกรรมผู้บริโภคเพื่อคัดกรองจัดสร้างคำแนะนำเสนอแนะสินค้าหรือภาพยนตร์เฉพาะตัวบุคคล (Real-time Recommendation Engine) เลียนแบบพฤติกรรมแบบ Amazon.com
*   **Amazon Forecast:** ระบบคำนวณสถิติทำนายตัวเลขแนวโน้มความต้องการสินค้าหรือการเงินในอนาคตโดยใช้ฐานข้อมูลอนุกรมเวลา (Time-series data forecasting)

### Exam Cheat-Sheet Keywords & Trap Warnings
*   **Keywords:**
    *   *Build application using Foundation Models (FMs) / Serverless API for LLM:* -> **Amazon Bedrock**
    *   *No-code ML tool for business analysts:* -> **Amazon SageMaker Canvas**
    *   *Detect explicit content in images / Face verification:* -> **Amazon Rekognition**
    *   *Extract text from tabular documents / PDF forms:* -> **Amazon Textract**
    *   *Enterprise search engine / Natural language query across multiple sources:* -> **Amazon Kendra**
    *   *Convert text to lifelike speech / Natural voices:* -> **Amazon Polly**
    *   *Convert speech audio file to text:* -> **Amazon Transcribe**
    *   *Customer Sentiment analysis / NLP on customer reviews:* -> **Amazon Comprehend**
    *   *Build conversational chatbot / Alexa-like bot:* -> **Amazon Lex**
    *   *Cloud contact center / Customer service phone system:* -> **Amazon Connect**
*   **Trap Warnings:**
    *   *Rekognition vs Textract:* หากโจทย์บอกว่าต้องการอ่านไฟล์ภาพใบเสร็จเพื่อคัดแยกข้อมูลตัวเลขและเก็บลงตารางดาต้าเบส ห้ามเลือก Rekognition ให้เลือก **Textract** เพราะออกแบบมาเพื่อการแกะข้อมูลเชิงโครงสร้างและตัวเลขตารางในเอกสารได้ตรงจุดกว่า Rekognition (Rekognition มุ่งเน้นไปที่การวิเคราะห์ภาพทั่วไป ตรวจสอบใบหน้า ค้นหาวัตถุ)
    *   *Bedrock vs SageMaker:* หากโจทย์ต้องการสร้างโมเดล ML ของตัวเองตั้งแต่ขั้นตอนการเตรียมข้อมูล ฝึกสอนอัลกอริทึมเองทั้งหมด ให้ตอบ SageMaker; หากเป็นงานเรียกใช้งานโมเดลภาษาขนาดใหญ่ (LLMs) หรือโมเดล GenAI สำเร็จรูปผ่าน API เพื่อประยุกต์ใช้ด่วน ให้ตอบ **Bedrock**

### Real-World Use Cases
*   **Automated Customer Feedback Analysis:** ลูกค้าส่งข้อคิดเห็นหลังใช้บริการเข้ามา -> ระบบดึงข้อความป้อนเข้า Comprehend เพื่อตรวจอารมณ์ (Sentiment) หากอารมณ์ติดลบ (Negative) จะสั่ง SNS ยิงเมลแจ้งด่วนให้หัวหน้าฝ่ายบริการลงมาตรวจสอบ และส่งข้อความไปหา Polly เพื่ออ่านออกเสียงคำเตือนเปิดฟังได้ทันที

### Architecture Integration Pattern: Intelligent RAG (Retrieval-Augmented Generation) Search

```text
             [ User Application ]
                     │
                     ▼ (1) Ask Question
             ┌───────────────┐
             │Amazon Bedrock │ (Foundation Model API)
             └───────┬───────┘
                     │
                     ▼ (2) Search Context (Natural Language Query)
             ┌───────────────┐
             │ Amazon Kendra │ (Enterprise Search Engine)
             └───────┬───────┘
                     │
                     ▼ (3) Retrieve Document Text
             ┌───────────────┐
             │   Amazon S3   │ (Company Knowledge Base)
             └───────┬───────┘
                     │
                     ▼ (4) Return Relevant Context
             ┌───────────────┐
             │Amazon Bedrock │ ───(5) Generate Response ──► [ User ]
             └───────────────┘
```
1.  **Users** พิมพ์ถามคำถามภาษาธรรมชาติผ่านแอปพลิเคชันเพื่อสืบค้นข้อมูลคู่มือทางเทคนิคของบริษัท
2.  คำถามส่งผ่านระบบหลังบ้านเข้ามาสั่งการไปที่ **Amazon Bedrock (FM API)**
3.  เพื่อป้องกันข้อมูลผิดพลาด (Hallucination) Bedrock จะสั่งค้นหาข้อมูลจริงที่เกี่ยวข้องก่อนจากระบบฐานความรู้องค์กร **Amazon Kendra**
4.  Kendra ดึงสิทธิ์ความรู้ค้นหาไฟล์ PDF คู่มือที่เก็บอยู่ภายในถัง **Amazon S3 Bucket** ออกมาแปลงและคัดเลือกเนื้อหาสำคัญข้ามแหล่งข้อมูล
5.  Kendra ส่งผลลัพธ์ย่อหน้าที่เกี่ยวข้องกลับไปให้ Bedrock ใช้ประกอบการอิงข้อมูลบริบท (Context)
6.  Bedrock นำข้อมูลจริงจากคู่มือนั้นมาจัดระเบียบเรียบเรียงและตอบคำถามกลับไปให้ผู้ใช้ด้วยสไตล์ภาษาที่สุภาพและแม่นยำ 100%
