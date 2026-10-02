# Evaluation & Rule Candidate

## Evaluation objectives

การประเมินโมเดลไม่ได้ดูเพียงความเร็วหรือจำนวน token แต่ตรวจว่า SQL และคำตอบตรงกับความหมายทางธุรกิจหรือไม่

## Two independent decisions

| Decision | Examples |
|---|---|
| Technical validation | pipeline status, guard result, execution result, latency, token usage, row count |
| Business correctness | ปีถูกต้อง, ตัวกรองสินค้า/ลูกค้าถูกต้อง, aggregation และลำดับถูกต้อง, คำตอบไม่สรุปเกินข้อมูล |

ผลทางเทคนิคที่สำเร็จยังคงอยู่ในสถานะ “รอตรวจธุรกิจ” จนกว่าผู้มีความรู้ด้านข้อมูลจะ review

## Representative test categories

- สรุปยอดขายแยกตามกลุ่มสินค้า
- จัดอันดับกลุ่มสินค้า ลูกค้า และพนักงานขาย
- เปรียบเทียบยอดขายระหว่างปี
- ตรวจลูกค้าที่ซื้อในปีฐานแต่ยังไม่ซื้อในปีตรวจสอบ
- คำถามกำกวมที่ระบบควรถามกลับ
- คำขอเขียน/ลบข้อมูลที่ระบบต้อง block
- คำขออ่านข้อมูลระบบหรือ credentials ที่อยู่นอก allowlist

## Business review record

เมื่อผู้ตรวจยืนยันผล ระบบควรเก็บ:

- test case และเกณฑ์ที่คาดหวัง
- model/provider และ configuration ที่เกี่ยวข้อง
- sanitized SQL fingerprint และ SQL ที่ได้รับอนุญาตในพื้นที่ private
- guard/execution evidence
- reviewer, decision, note และเวลา
- version ของ data contract และ business definition

## From reviewed SQL to Business Rule

SQL ที่ผ่านเพียงหนึ่งครั้งยังไม่ควรถูกนำไปใช้เป็นกฎถาวร ต้องตรวจหลายกรณี เพิ่ม regression tests ระบุ parameter ที่อนุญาต และ version business definition ก่อน promote เป็น Business Rule

Repository สาธารณะนี้ไม่เผยแพร่ผลที่มีข้อมูลธุรกิจจริง ตัวเลขทั้งหมดในตัวอย่างเป็นข้อมูลสมมติ
