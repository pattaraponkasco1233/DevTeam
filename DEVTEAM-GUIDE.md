# DevTeam — คู่มือการใช้งาน

> AI DevTeam สำหรับพัฒนา BMS_WEB (Angular 17 TMS)
> ประกอบด้วย 3 Agent: BA · Dev · Review

---

## Agents & Skills

| Agent | Skill File | หน้าที่ |
|---|---|---|
| **BA** | `.claude/SKILL-BA.md` | รับ requirement → แปลเป็น DR + Dev Command |
| **Dev** | `.claude/SKILL-DEV.md` | รับ DR → เขียน Angular code ตาม BMS_WEB pattern |
| **Review** | `.claude/SKILL-REVIEW.md` | ตรวจ code → ให้ feedback พร้อม severity |

**Reference**: `TECH-STACK.md` — Architecture และ Tech Stack ของ BMS_WEB

---

## เริ่มต้นใช้งาน

```bash
cd C:\Users\patta\Desktop\DevTeam
claude
```

Claude Code จะอ่าน `CLAUDE.md` อัตโนมัติ — รู้จัก project rules และ skill files ทันที

---

## Workflow Overview

```
Nook พูด requirement
       ↓
  @BA  →  DR + Dev Command
       ↓
  @Dev  →  เขียน code
       ↓
  @Review  →  BLOCKER / MAJOR / MINOR
       ↓
  @Dev  →  แก้ตาม review
       ↓
  @Dev  →  commit + push branch
       ↓
  Nook  →  สร้าง PR → merge to develop
```

---

## Step-by-Step

### STEP 1 — บอก BA (ภาษาพูดได้เลย)

```
@BA [requirement ที่ต้องการ]
```

**ตัวอย่าง:**
```
@BA อยากเพิ่มหน้า billing-expense ให้ filter วันที่แบบ range ได้
ตอนนี้ค้นหาทีละวัน ไม่สะดวก
```

**BA จะ output:**
- `DR-YYYYMMDD-XXX` — Design Requirement (background, scope, AC, API)
- `Dev Command` — คำสั่งพร้อมส่ง Dev ทันที

> ถ้า requirement ไม่ชัด BA จะถามกลับ 1 คำถามก่อนเขียน DR

---

### STEP 2 — ส่งงานให้ Dev

Copy Dev Command จาก BA output แล้วส่งต่อ:

```
@Dev [วาง Dev Command จาก BA]
```

**ตัวอย่าง:**
```
@Dev — Add Date Range Filter: Billing Expense

DR: DR-20260506-001
Module: src/app/modules/transport/modules/billing-expense
Action: Modify

Tasks:
1. เพิ่ม m-date-range-picker ใน billing-expense.component.html
2. bind start/end date ใน component.ts
3. ส่งเป็น query param ใน API call
4. update app-billing-expense.model.ts

Constraints:
- ใช้ shared/widgets/m-date-range-picker
- ห้ามใช้ any กับ date type
```

**Dev จะ:**
1. อ่าน `SKILL-DEV.md` + `TECH-STACK.md` ก่อน
2. อ่าน code ปัจจุบันของ module นั้นก่อนเขียน
3. เขียน code ตาม BMS_WEB pattern
4. แจ้ง file ที่แก้ไขทั้งหมด

---

### STEP 3 — ส่ง Review

```
@Review ตรวจ code ที่ Dev เพิ่งทำมา

Files changed:
- [path/to/file1.ts]
- [path/to/file2.html]
- [path/to/model.ts]

DR: [DR-ID] ([ชื่อ feature])
```

**Review จะ output:**

```
## Code Review: DR-XXXXXX
Result: ✅ Approved | ⚠️ Approved with Comments | ❌ Request Changes

🔴 BLOCKER  — ต้องแก้ก่อน merge
🟡 MAJOR    — ควรแก้ใน PR นี้
🔵 MINOR    — แนะนำ ไม่บังคับ
⚪ NOTE     — ข้อมูลเพิ่มเติม
```

---

### STEP 4 — Dev แก้ตาม Review (ถ้ามี BLOCKER/MAJOR)

```
@Dev แก้ตาม review:

🔴 [อธิบาย BLOCKER ที่ต้องแก้]
🟡 [อธิบาย MAJOR ที่ต้องแก้]
```

---

### STEP 5 — Commit & Push

```
@Dev commit และ push:
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

## Git Workflow (ทบทวน)

```
main      → production  (PR only, 1 review required)
develop   → integration
feature/  → daily work   ← Dev จะสร้าง branch นี้
hotfix/   → urgent fix
```

Commit format: `feat|fix|refactor|docs: short description`

---

## Quick Commands Reference

| ต้องการอะไร | พิมพ์ |
|---|---|
| แปล requirement เป็น DR | `@BA [requirement]` |
| สั่ง Dev ทำงาน | `@Dev [Dev Command จาก BA]` |
| ตรวจ code | `@Review ตรวจ [files] DR: [id]` |
| Dev แก้ตาม review | `@Dev แก้ตาม review: [issues]` |
| Commit & Push | `@Dev commit: [msg] branch: [name]` |
| ถาม Tech Stack | `@Dev อ่าน TECH-STACK.md แล้วบอกว่า [คำถาม]` |

---

## Tips

- **BA** — บอกสั้นได้ ไม่ต้องครบ 100% BA จะถามเองถ้าขาด
- **Dev** — ระบุ module path ยิ่งชัด ยิ่งเร็ว
- **Review** — ระบุ files changed ให้ครบ Review จะตรวจได้ครอบคลุม
- **หลาย task** — แยก DR ทีละอัน อย่ายัดหลาย feature ใน DR เดียว

---

*DevTeam setup: May 2026 | Project: BMS_WEB | Stack: Angular 17 + .NET Core 8 + MSSQL*
