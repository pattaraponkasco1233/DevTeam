# DevTeam — คู่มือการใช้งาน

> AI DevTeam สำหรับพัฒนา BMS_WEB (Angular 17 TMS)
> ประกอบด้วย 3 Agent: BA · Dev · Review

---

## Agents & Skills

| Agent | Skill File | หน้าที่ |
|---|---|---|
| **BA** | `.claude/SKILL-BA.md` | รับ requirement → ถามให้ครบ → DR + Dev Command (แยก BE/FE) → บันทึก `docs/dr/` |
| **Dev** | `.claude/SKILL-DEV.md` | รับ DR file → อ่าน code → สรุป Plan → รอ confirm → เขียน code → build check |
| **Review** | `.claude/SKILL-REVIEW.md` | รับ files + DR → ตรวจ 7 dimension → feedback พร้อม severity → บันทึก `docs/review/` |

**Reference**: `TECH-STACK.md` — Architecture และ Tech Stack ของ BMS_WEB

---

## Workflow Overview

```
Nook บอก requirement
        ↓
   BA Agent
   - ถามข้อที่ขาด (รวมครั้งเดียว)
   - เขียน DR + Dev Command (แยก FE/BE scope)
   - บันทึก docs/dr/DR-YYYYMMDD-SEQ-slug.md
        ↓
   DEV Agent
   - อ่าน DR file
   - อ่าน code เดิมใน module
   - สรุป Plan → รอ Nook confirm ✋
   - เขียน code ตาม plan
   - npm run deploy:dev ผ่าน
   - แจ้ง files ที่แก้
        ↓
   REVIEW Agent
   - อ่าน files + DR
   - ตรวจ 7 dimension + Merge Criteria
   - บันทึก docs/review/REVIEW-...md
   - ❌ Request Changes → ส่งกลับ DEV
   - ✅ Approved → พร้อม merge
        ↓
   DEV Agent (ถ้ามี BLOCKER/MAJOR)
   - แก้ตาม review
   - commit + push branch
        ↓
   Nook → สร้าง PR → merge to develop
```

---

## Step-by-Step

### STEP 1 — BA Agent: แปลง requirement → DR

```
@BA [requirement ที่ต้องการ]
```

**ตัวอย่าง:**
```
@BA อยากเพิ่มหน้า billing-expense ให้ filter วันที่แบบ range ได้
ตอนนี้ค้นหาทีละวัน ไม่สะดวก
```

หรือแนบรูปภาพ / mockup / screenshot ได้เลย — BA จะวิเคราะห์รูปและถามเฉพาะจุดที่ไม่ชัด

**BA จะทำ:**
1. ตรวจ Requirement Checklist — ถามข้อที่ขาดรวมครั้งเดียว
2. เขียน DR พร้อม Acceptance Criteria
3. เขียน Dev Command แยก FE / BE scope
4. **บันทึกอัตโนมัติ** → `docs/dr/DR-YYYYMMDD-001-[feature-slug].md`
5. แจ้ง path ของไฟล์ที่บันทึก

**Priority ที่ BA จะถาม:**

| Level | ความหมาย |
|---|---|
| P0 | ระบบพัง / ใช้งานไม่ได้ → ทำทันที |
| P1 | สำคัญ ต้องทำใน sprint นี้ |
| P2 | ไม่เร่ง → เข้า backlog |

---

### STEP 2 — DEV Agent: เขียน code ตาม DR

ส่ง path ของ DR file ที่ BA บันทึกไว้:

```
@Dev docs/dr/DR-YYYYMMDD-001-[feature-slug].md
```

**Dev จะทำ (ตามลำดับ):**
1. อ่าน DR file ให้ครบ
2. อ่าน code เดิมใน module ที่เกี่ยวข้อง
3. **สรุป Plan** — แสดงรายการ files ที่จะแก้ + pattern ที่จะยึด

**⚠️ ตรงนี้ Dev จะหยุดรอ Confirm จาก Nook ก่อนเสมอ**

```
✅ ยืนยันแล้วจะเริ่ม code เลยนะ Nook?
```

4. หลัง Nook confirm → เขียน code
5. รัน `npm run deploy:dev` — ต้องผ่านก่อนส่งต่อ
6. แจ้ง: `✅ DEV Done — ส่งให้ REVIEW Agent ได้เลย` พร้อม files + DR ID

---

### STEP 3 — REVIEW Agent: ตรวจ code

ส่ง files ที่ Dev แก้ + DR ID:

```
@Review
Files: [path/to/file1.ts], [path/to/file2.html]
DR: docs/dr/DR-YYYYMMDD-001-[feature-slug].md
```

**Review จะตรวจ 7 dimension:**
1. Correctness (logic, state pattern, error handling)
2. TypeScript Quality
3. Angular Best Practices
4. Performance
5. Code Style & Convention
6. Security
7. Reusability

**Output format:**
```
## Code Review: DR-XXXXXX
Result: ✅ Approved | ⚠️ Approved with Comments | ❌ Request Changes

🔴 BLOCKER  — ต้องแก้ก่อน merge
🟡 MAJOR    — ควรแก้ใน PR นี้
🔵 MINOR    — แนะนำ ไม่บังคับ
⚪ NOTE     — ข้อมูลเพิ่มเติม
```

Review บันทึกผลอัตโนมัติ → `docs/review/REVIEW-YYYYMMDD-[DR-ID]-[slug].md`

**Merge Criteria (ต้องผ่านทุกข้อก่อน Approve):**
- ไม่มี BLOCKER
- MAJOR แก้แล้วหรือมี justification
- `npm run deploy:dev` ผ่าน
- ไม่ break feature อื่น

---

### STEP 4 — DEV Agent: แก้ตาม Review (ถ้ามี BLOCKER/MAJOR)

```
@Dev แก้ตาม review: docs/review/REVIEW-[...].md
```

Dev จะอ่าน review file และแก้เฉพาะ BLOCKER + MAJOR — จากนั้น REVIEW ตรวจรอบใหม่

---

### STEP 5 — Commit & Push

```
@Dev commit และ push
message: feat: [สิ่งที่ทำ]
branch: feature/[ชื่อ branch]
```

**Dev จะรัน:**
```bash
git checkout -b feature/[branch-name]
git add [files]
git commit -m "feat: [description]"
git push origin feature/[branch-name]
```

---

## Output Files ที่เกิดขึ้นในแต่ละ step

```
docs/
├── dr/
│   └── DR-YYYYMMDD-001-[feature-slug].md   ← BA สร้าง
└── review/
    └── REVIEW-YYYYMMDD-[DR-ID]-[slug].md   ← REVIEW สร้าง
```

---

## Git Workflow

```
main      → production  (PR only, 1 review required)
develop   → integration
feature/  → daily work   ← Dev สร้าง branch นี้
hotfix/   → urgent fix
```

Commit format: `feat|fix|refactor|docs: short description`

---

## Quick Commands Reference

| ต้องการอะไร | พิมพ์ |
|---|---|
| แปล requirement เป็น DR | `@BA [requirement หรือแนบรูป]` |
| สั่ง Dev ทำงาน | `@Dev docs/dr/DR-[id].md` |
| ตรวจ code | `@Review Files: [...] DR: docs/dr/DR-[id].md` |
| Dev แก้ตาม review | `@Dev แก้ตาม review: docs/review/REVIEW-[...].md` |
| Commit & Push | `@Dev commit: [msg] branch: feature/[name]` |
| บันทึก session | พิมพ์: `บันทึกข้อมูลวันนี้` |

---

## Tips

- **BA** — บอกสั้นได้ แนบรูปได้ BA จะถามเองถ้าขาดข้อมูล
- **Dev** — ส่ง path DR file ตรงๆ ไม่ต้อง copy Dev Command เอง
- **Plan Confirm** — อย่า skip ขั้น confirm plan — ช่วยจับ misunderstanding ก่อน code จริง
- **Review** — ระบุ files changed ให้ครบ Review จะตรวจได้ครอบคลุม
- **หลาย task** — แยก DR ทีละอัน อย่ายัด feature หลายอันใน DR เดียว
- **ดู DR/Review ย้อนหลัง** — เปิดดูได้ที่ `docs/dr/` และ `docs/review/`

---

*DevTeam setup: May 2026 | Project: BMS_WEB | Stack: Angular 17 + .NET Core 8 + MSSQL*
