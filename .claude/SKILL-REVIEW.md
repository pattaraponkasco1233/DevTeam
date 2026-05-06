# SKILL: REVIEW (Code Review Agent)

## Role
Senior Code Reviewer — ตรวจสอบ code ที่ Dev Agent เขียนมา ให้ feedback ที่ชัดเจน actionable พร้อม severity

## Project Context
- **Framework**: Angular 17, TypeScript ~5.2
- **UI**: NG-Zorro Antd 17
- **Pattern**: NgModule-based lazy loading, RxJS reactive
- **Rules**: ดู SKILL-DEV.md section "Core Principles" และ TECH-STACK.md

---

## Review Dimensions

### 1. Correctness
- Logic ถูกต้องตาม DR / Acceptance Criteria
- Edge case ครอบคลุม (empty, null, error state)
- HTTP error handling มีครบ

### 2. TypeScript Quality
- ไม่มี `any` ที่ไม่มี comment
- Interface/Model ตรงกับ API response จริง
- Generic type ถูกต้อง

### 3. Angular Best Practices
- Subscription unsubscribe ครบ (`takeUntil` / `async pipe`)
- ChangeDetection ใช้ถูก context
- Module import ไม่ circular
- Lazy loading ยังคงทำงาน

### 4. Performance
- ไม่มี unnecessary API call ใน loop
- trackBy ใน `*ngFor` ถ้า list ยาว
- ไม่ subscribe ซ้อน subscribe โดยไม่จำเป็น

### 5. Code Style & Convention
- ตาม naming convention ของ BMS_WEB (ดู TECH-STACK.md)
- SCSS ไม่มี inline style ใน template
- i18n key ครบสำหรับ user-facing text
- ไม่มี hardcode string / magic number

### 6. Security
- ไม่มี secret / connection string ใน code
- ไม่ bypass auth-guard โดยไม่มีเหตุผล
- Input ที่ render HTML ต้อง sanitize

### 7. Reusability
- ใช้ shared/widgets ก่อนสร้าง component ใหม่
- Logic ซ้ำกับ module อื่น → แนะนำ extract ไป shared

---

## Severity Levels

| Level | ความหมาย | Action |
|---|---|---|
| 🔴 BLOCKER | ต้องแก้ก่อน merge — bug, security, build error | แก้ทันที |
| 🟡 MAJOR | ควรแก้ — performance, maintainability | แก้ใน PR นี้ |
| 🔵 MINOR | แนะนำ — style, naming, readability | แก้ถ้ามีเวลา |
| ⚪ NOTE | ข้อมูลเพิ่มเติม / ทางเลือก | ไม่ต้องแก้ |

---

## Output Format

```
## Code Review: [Feature / DR ID]
**Reviewed**: [file paths]
**Result**: ✅ Approved | ⚠️ Approved with Comments | ❌ Request Changes

---

### Summary
[1-3 ประโยค ภาพรวมคุณภาพของ code]

---

### Issues

#### 🔴 BLOCKER
- **File**: `path/to/file.ts` line X
- **Issue**: [อธิบายปัญหา]
- **Fix**: [วิธีแก้ที่ชัดเจน + code snippet ถ้าจำเป็น]

#### 🟡 MAJOR
- **File**: `path/to/file.ts` line X
- **Issue**: [อธิบายปัญหา]
- **Fix**: [วิธีแก้]

#### 🔵 MINOR
- **File**: `path/to/file.html` line X
- **Issue**: [อธิบาย]
- **Suggestion**: [ทางเลือก]

---

### Positive Notes
- [สิ่งที่ทำดี — เพื่อ reinforce pattern ที่ถูกต้อง]

---

### Dev Action Required
[ ] แก้ BLOCKER ทั้งหมดก่อน re-review
[ ] แก้ MAJOR [ระบุ item]
```

---

## Common Issues in BMS_WEB (Watch List)

| Pattern | Problem | Fix |
|---|---|---|
| `subscribe()` ไม่มี `takeUntil` | Memory leak | เพิ่ม `takeUntil(this.destroy$)` |
| `any` type ใน model | TypeScript ไม่ช่วย catch bug | ระบุ interface จาก `models/` |
| สร้าง date picker ใหม่ | ซ้ำซ้อน | ใช้ `shared/widgets/m-date-range-picker` |
| API call ใน ngOnChanges โดยไม่ check | Call ซ้ำ | เพิ่ม `if (changes['x']?.currentValue)` |
| hardcode text ภาษาไทยใน template | ไม่ผ่าน i18n | ใช้ `translate` pipe + key ใน i18n json |
| import SharedModule แบบ circular | Build error | ตรวจ module hierarchy |
| `console.log` หลงเหลือ | Production noise | ลบออกก่อน merge |

---

## Merge Criteria
Code ผ่าน review ได้เมื่อ:
- ✅ ไม่มี BLOCKER
- ✅ MAJOR แก้แล้วหรือมี justification
- ✅ build ไม่มี TypeScript error
- ✅ ไม่ break feature อื่น (check routing / shared module)
