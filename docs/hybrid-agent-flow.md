# Hybrid Agent Flow

## Decision flow

```mermaid
sequenceDiagram
    actor User as ผู้ใช้งาน
    participant UI as ERP AI Assistant
    participant Router as Hybrid Router
    participant Rule as Business Rule
    participant LLM as Cloud LLM
    participant Guard as SQL Guard
    participant DB as Read-only SQL
    participant Composer as Response Composer

    User->>UI: ถามด้วยภาษาธรรมชาติ
    UI->>Router: question + permitted context
    alt มี Business Rule ที่รับรองแล้ว
        Router->>Rule: execute known rule
        Rule-->>Composer: structured result
    else เป็นคำถามวิเคราะห์ใหม่
        Router->>LLM: schema contract + question
        LLM-->>Guard: proposed SQL
        alt SQL ไม่ผ่าน
            Guard-->>UI: blocked safely
        else SQL ผ่าน
            Guard->>DB: execute read-only query
            DB-->>Composer: result rows
        end
    end
    Composer-->>UI: grounded natural-language answer
    UI-->>User: answer + processing evidence
```

## Why hybrid

| Requirement | Business Rule | LLM path |
|---|---|---|
| ผลทำซ้ำได้ | สูง | ต้องควบคุมด้วย guard และ evaluation |
| รองรับคำถามใหม่ | จำกัด | สูง |
| Latency | ต่ำ | สูงกว่า |
| Cloud cost | ไม่มี | มีตามการใช้งาน |
| อธิบาย SQL | กำหนดไว้ล่วงหน้า | ตรวจจาก proposal และ execution evidence |

Hybrid routing จึงใช้ Business Rule เป็นเส้นทางแรก และใช้ LLM เมื่อต้องการความยืดหยุ่นเพิ่มเติม

## Rule promotion lifecycle

```mermaid
flowchart LR
    Q[คำถามใหม่] --> E[LLM Proposal]
    E --> G[Guarded Execution]
    G --> R[Business Review]
    R -->|รับรอง| C[Rule Candidate]
    C --> T[Regression Test]
    T --> V[Approved Business Rule]
    R -->|ไม่รับรอง| F[Feedback / Reject]
```

แนวทางนี้ทำให้ผลการใช้งานจริงกลายเป็นข้อมูลสำหรับปรับปรุงระบบ โดยไม่เปลี่ยน SQL ที่โมเดลเสนอให้เป็น production rule โดยอัตโนมัติ
