# Interview Notes

## 60-second project explanation

โปรเจกต์นี้แก้ปัญหาที่ฝ่ายขายต้องค้นข้อมูลจาก ERP หลายหน้า ผมสร้าง AI Assistant ที่รับคำถามภาษาธรรมชาติและเลือกสองเส้นทาง ถ้าเป็นงานที่นิยามชัด ระบบใช้ Business Rule ที่ทดสอบและรับรองแล้ว ถ้าเป็นคำถามใหม่ ระบบให้ LLM เสนอ SQL แต่ SQL ต้องผ่าน guard, semantic checks และบัญชีฐานข้อมูลแบบ read-only ก่อน execute คำตอบสุดท้ายสร้างจากผล query จริง พร้อม telemetry และกระบวนการ review เพื่อนำคำถามที่ใช้บ่อยกลับมาพัฒนาเป็นกฎถาวร

## Engineering decisions to explain

- ทำไมไม่ให้ LLM เชื่อม SQL Server โดยตรง
- ทำไม Business Rule ยังมีความสำคัญแม้มี Cloud LLM
- แยก technical success ออกจาก business correctness อย่างไร
- ป้องกัน destructive SQL และ credential discovery อย่างไร
- ลด Cloud cost ด้วย rule promotion อย่างไร
- เชื่อมระบบใหม่กับ PHP/ERP เดิมโดยไม่รื้อระบบทั้งหมดอย่างไร
- จัดการ secret, read-only account, audit และ failover อย่างไร

## Demo sequence

1. เริ่มจากคำถาม Business Rule เพื่อแสดงคำตอบที่รวดเร็วและตรวจสอบได้
2. ถามคำถามวิเคราะห์ใหม่เพื่อแสดง LLM → SQL Guard → read-only query
3. เปิดรายละเอียดการประมวลผลเพื่ออธิบาย evidence
4. ใช้คำถามลบข้อมูลเพื่อแสดงว่าระบบ block
5. เปิดหน้าประเมินโมเดลและ Business Review เพื่ออธิบายเส้นทางสร้าง Rule Candidate

## Honest limitations

- คุณภาพคำตอบขึ้นกับ data contract และคุณภาพข้อมูลต้นทาง
- ปีปัจจุบันที่ยังไม่ครบปีต้องสื่อสารเป็นแนวโน้ม
- Business Rule ต้องมี owner และ version เมื่อคำนิยามเปลี่ยน
- LLM output ต้องผ่าน deterministic controls เสมอ
- การทดสอบ portfolio ไม่ใช่หลักฐานผลกระทบทางธุรกิจใน production

## Suggested interview screenshot caption

> Hybrid Enterprise AI Assistant embedded in an existing ERP workflow. Approved business rules handle repeatable tasks, while new analytics questions use a governed Cloud LLM path with SQL Guard and read-only execution.
