# Security Design

## Security boundaries

1. **Authentication and page permission** — ผู้ใช้งานต้องผ่านระบบเดิมและมีสิทธิ์เข้าหน้า AI Assistant
2. **Intent boundary** — คำขอที่อยู่นอกงานวิเคราะห์ข้อมูลถูกปฏิเสธหรือขอรายละเอียดเพิ่ม
3. **SQL proposal guard** — ตรวจว่าเป็นคำสั่งอ่านข้อมูล ตารางและคอลัมน์อยู่ใน allowlist และไม่มี statement หลายชุด
4. **Semantic preflight** — ตรวจปี ตัวกรอง การจัดอันดับ aggregation และข้อจำกัดผลลัพธ์ตามเจตนาของคำถาม
5. **Read-only executor** — ใช้ SQL login แยกต่างหากและกำหนด timeout/row limit
6. **Grounded response** — คำตอบอ้างอิงผล query ที่ execute จริง
7. **Sanitized telemetry** — เก็บสถานะ เวลา token และ fingerprint โดยไม่บันทึก secret

## Threat examples

| Threat | Control |
|---|---|
| ขอให้ลบหรือแก้ไขข้อมูล | Guard ปฏิเสธ non-SELECT และ executor เป็น read-only |
| ขอ SQL Server login/password | System objects และ sensitive columns ไม่อยู่ใน allowlist |
| Prompt injection ให้ข้ามกฎ | Guard และ executor เป็น deterministic controls แยกจาก LLM |
| Query ขนาดใหญ่ | TOP/row limit, timeout และ governed views |
| Model hallucination | Compose คำตอบจาก returned rows และแสดง evidence |
| Secret รั่วจาก Git | ใช้ external secret files, environment variables และ secret scan ก่อน push |

## Defense in depth

การผ่าน LLM ไม่ถือเป็นการอนุมัติ SQL และการผ่าน SQL Guard ไม่ถือว่าคำตอบถูกต้องทางธุรกิจ ระบบจึงแยก technical success, security decision และ business review ออกจากกัน

## Public portfolio policy

Repository สาธารณะไม่เก็บ:

- API keys, passwords หรือ connection strings จริง
- ชื่อลูกค้า ผู้ติดต่อ หรือเบอร์โทรที่ระบุตัวบุคคลได้
- SQL production, schema จริง หรือ data dictionary
- source code ของ ERP และ Enterprise AI Platform
- database dump, logs หรือ prompts ที่มีข้อมูลภายใน
