# CLAUDE.md

## Project

Maintain and develop existing web application.
Stack: Angular 17 (FE) · .NET Core 8 (BE) · MSSQL

## Team

| Agent | Skill File | หน้าที่ |
|---|---|---|
| **BA** | `.claude/SKILL-BA.md` | รับ requirement → แปลเป็น DR + Dev Command |
| **Dev** | `.claude/SKILL-DEV.md` | รับ DR → เขียน Angular code ตาม BMS_WEB pattern |
| **Review** | `.claude/SKILL-REVIEW.md` | ตรวจ code → ให้ feedback พร้อม severity |

**Reference**: `TECH-STACK.md` — Architecture และ Tech Stack ของ BMS_WEB

## Git Workflow

```
main      → production (PR only, 1 review required)
develop   → integration
feature/  → daily work
hotfix/   → urgent fixes
```

Commit format: `feat|fix|refactor|docs: short description`

## Project Structure

```
/frontend    → Angular 17 app
/backend     → .NET Core 8 API
/docs/adr    → Architecture Decision Records
/scripts     → DB migrations, deployment
/.claude     → AI skill files (FE, BE, Review)
```

## Hard Rules

* Never push directly to `main` or `develop`
* No unit-test skip for business logic
* No `any` type in TypeScript without comment
* No secrets or connection strings in code

## Session Handoff

`HANDOFF.md` — ไฟล์ส่งต่อ context ระหว่าง 2 เครื่อง sync ผ่าน Git

**เริ่ม session ใหม่**: อ่าน `HANDOFF.md` ก่อนเสมอ เพื่อรู้ว่า session ก่อนหน้าทำอะไรไปถึงไหน

**คำสั่ง "บันทึกข้อมูลวันนี้"**: เมื่อผู้ใช้พิมพ์คำนี้ ให้ทำตามขั้นตอนนี้ทันที:
1. สรุปสิ่งที่ทำใน session นี้จาก conversation history
2. เขียนทับ `HANDOFF.md` ด้วยข้อมูลล่าสุด (วันที่, branch, สิ่งที่ทำ, ค้างอยู่, next steps, context สำคัญ)
3. `git add HANDOFF.md && git commit -m "docs: handoff update YYYY-MM-DD" && git push`

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.
* 
* \## Repository
* 
* \*\*DevTeam\*\* — owned by \[@pattaraponkasco1233](https://github.com/pattaraponkasco1233/DevTeam)
* 
* Remote: `https://github.com/pattaraponkasco1233/DevTeam`
* 
* \## Git Workflow
* 
* ```bash
* git add <files>
* git commit -m "message"
* git push origin master
* ```
* 
* \## Claude Code Permissions
* 
* Configured in `.claude/settings.local.json` — allowed commands include `git`, `gh`, `rtk git`, and `rtk gh`.

