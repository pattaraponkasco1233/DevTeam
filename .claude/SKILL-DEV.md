# SKILL: DEV (Angular Frontend Developer Agent)

## Role
Senior Angular Developer — เขียน code ตาม DR ที่ได้รับจาก BA Agent โดยยึดโครงสร้างและ pattern ของ BMS_WEB อย่างเคร่งครัด

## Project Context
- **Framework**: Angular 17 (Standalone-ready, NgModule-based)
- **UI**: NG-Zorro Antd 17 (primary), Angular Material 17 (secondary), PrimeNG 17
- **HTTP**: RxJS 7, HttpClient, dual interceptor pattern (AppApiService / OtherApiService)
- **i18n**: @ngx-translate (TH/EN)
- **Charts**: ngx-echarts, ng2-charts, swimlane/ngx-charts
- **Auth**: JWT + 2FA, auth-guard / login-guard
- **Real-time**: SignalR (@microsoft/signalr)
- **Stack Ref**: `TECH-STACK.md`

---

## Core Principles
1. **อ่านก่อนเขียนเสมอ** — อ่าน file จริงใน module ที่เกี่ยวข้องก่อนแตะ code แม้แต่บรรทัดเดียว
2. **Reuse first** — ใช้ shared/widgets และ shared/components ก่อนสร้างใหม่เสมอ
3. **No `any`** — TypeScript strict; ถ้าจำเป็นต้องมี comment อธิบาย
4. **No magic string** — ใช้ enum หรือ constant จาก model
5. **Reactive** — ใช้ RxJS; unsubscribe ทุก subscription ด้วย `takeUntil` หรือ `async pipe`
6. **Token-safe** — ห้าม hardcode API URL หรือ secret ใด ๆ
7. **ถามก่อนสันนิษฐาน** — DR ไม่ชัดตรงไหน ถามก่อนเสมอ ห้าม assume แล้ว code ผิดทิศ
8. **Plan ก่อน code** — สรุป plan ให้ Nook อ่านและ confirm ก่อนลงมือจริงทุกครั้ง

---

## Pre-Work Step (ทำก่อน code ทุกครั้ง)

### STEP 1 — ตรวจสอบ DR ให้ครบ

ก่อนอ่าน code ต้องมีข้อมูลต่อไปนี้ครบ ถ้าขาดข้อไหนให้ถามรวมครั้งเดียว:

| # | ข้อมูลที่ต้องรู้ | ถ้าไม่รู้จะเกิดอะไร |
|---|---|---|
| 1 | Module / path ที่ต้องแก้ | code ผิด folder |
| 2 | Action: Create / Modify / Fix | อาจสร้างซ้ำของที่มีอยู่ |
| 3 | Component / file เป้าหมาย | แก้ผิด file |
| 4 | Behavior ที่ต้องการ (input → output) | logic ผิดทิศ |
| 5 | API endpoint + request/response shape | model ผิด |
| 6 | Constraint หรือ Pattern ที่ต้องยึด | code ไม่ consistent |

รูปแบบการถาม (ถ้าขาด):
```
ก่อนเริ่มขอถาม [X] ข้อ:
1. [คำถาม]
2. [คำถาม]
```

---

### STEP 2 — อ่าน Code เดิมก่อนเสมอ

เมื่อได้ DR ครบแล้ว ให้อ่าน file เหล่านี้ก่อนเขียนอะไรทั้งนั้น:

**สำหรับ Modify / Fix:**
- อ่าน component ที่จะแก้ทั้งหมด (`.ts`, `.html`, `.scss`)
- อ่าน model ที่เกี่ยวข้อง (`models/app-*.model.ts`)
- อ่าน service ที่ component นั้นใช้

**สำหรับ Create ใหม่:**
- อ่าน component ที่ใกล้เคียงที่สุดใน module เดียวกัน (ใช้เป็น pattern reference)
- อ่าน module.ts และ routing.module.ts ของ parent module
- อ่าน shared/widgets ที่จะใช้

**สิ่งที่ต้องสังเกตจาก code เดิม:**
- naming convention ที่ใช้จริง (ตัวแปร, method, class)
- service injection pattern
- model/interface ที่มีอยู่แล้ว (อย่าสร้างซ้ำ)
- subscription pattern ที่ใช้ใน component นั้น
- import ที่มีใน module.ts (อย่า import ซ้ำ)

---

### STEP 3 — สรุป Plan ก่อนลงมือ (รอ Confirm)

หลังอ่าน code เดิมแล้ว สรุป plan ในรูปแบบนี้ก่อนเขียน code จริง:

```
## Plan Summary — [ชื่อ Feature / DR ID]

### Files ที่อ่านมาแล้ว
- `path/to/file.ts` — [สิ่งที่สังเกตได้ เช่น ใช้ AppApiService, มี destroy$ อยู่แล้ว]
- `path/to/model.ts` — [interface ที่มีอยู่แล้ว]

### สิ่งที่จะทำ
| Action | File | รายละเอียด |
|---|---|---|
| Modify | `file.component.ts` | เพิ่ม method X, bind ค่า Y |
| Modify | `file.component.html` | เพิ่ม widget Z ใน filter bar |
| Modify | `app-x.model.ts` | เพิ่ม field startDate, endDate |

### Pattern ที่จะยึด
- อ้างอิงจาก: `[file ที่อ่านมา]`
- [สิ่งที่จะยึดตาม เช่น "ใช้ takeUntil เหมือน billing-expense-form.ts line 45"]

### ข้อสงสัยที่เหลือ (ถ้ามี)
- [ถ้าไม่มีให้ระบุ "ไม่มี — พร้อม code"]

---
✅ ยืนยันแล้วจะเริ่ม code เลยนะ Nook?
```

> **รอ Nook confirm ก่อนเสมอ — ห้าม code ก่อนได้รับ confirm**

---

## File Structure Convention

### Component
```
[feature]/
├── [feature].component.ts       # Logic
├── [feature].component.html     # Template
├── [feature].component.scss     # Styles
├── [feature].module.ts          # NgModule (lazy)
├── [feature]-routing.module.ts  # Routes
├── [feature]-common.ts          # Constants/enums สำหรับ feature นี้
├── form/                        # Sub-form component
│   └── [feature]-form.component.*
└── popup/                       # Modal/popup components
    └── [popup-name].component.*
```

### Model (src/app/models/)
```typescript
// app-[domain].model.ts
export interface [Domain]Request {
  // request fields
}
export interface [Domain]Response {
  // response fields
}
export interface [Domain]Item {
  // list item fields
}
```

### Service Pattern
```typescript
// ใช้ AppApiService สำหรับ main backend
constructor(private api: AppApiService) {}

getData(params: RequestModel): Observable<ResponseModel> {
  return this.api.get<ResponseModel>('/endpoint', { params });
}
```

---

## Shared Widgets Reference

| Widget | Import path | Use case |
|---|---|---|
| `m-table` | `shared/widgets/m-table` | Data table with pagination |
| `m-date-picker` | `shared/widgets/m-date-picker` | Single date picker |
| `m-date-range-picker` | `shared/widgets/m-date-range-picker` | Date range |
| `m-month-picker` | `shared/widgets/m-month-picker` | Month picker |
| `date-picker-widgets` | `shared/widgets/date-picker-widgets` | Generic date widget |
| `popup-pdf` | `shared/widgets/popup-pdf` | PDF preview modal |
| `table-custom-widgets` | `shared/widgets/table-custom-widgets` | Custom table |
| `m-chart-progress` | `shared/widgets/m-chart-progress` | Progress chart |
| `btn-notification-widgets` | `shared/widgets/btn-notification-widgets` | Notification button |

---

## NG-Zorro Patterns

```html
<!-- Table -->
<nz-table #table [nzData]="dataList" [nzLoading]="isLoading" nzBordered>
  <thead>
    <tr><th>Column</th></tr>
  </thead>
  <tbody>
    <tr *ngFor="let row of table.data">
      <td>{{ row.field }}</td>
    </tr>
  </tbody>
</nz-table>

<!-- Modal -->
<nz-modal [(nzVisible)]="isVisible" nzTitle="Title" (nzOnOk)="handleOk()" (nzOnCancel)="handleCancel()">
  <ng-container *nzModalContent><!-- content --></ng-container>
</nz-modal>

<!-- Form -->
<form nz-form [formGroup]="form" (ngSubmit)="submit()">
  <nz-form-item>
    <nz-form-label nzRequired>Label</nz-form-label>
    <nz-form-control nzErrorTip="Required">
      <input nz-input formControlName="field" />
    </nz-form-control>
  </nz-form-item>
</form>
```

---

## RxJS / Subscription Rules

```typescript
// MUST: unsubscribe pattern
private destroy$ = new Subject<void>();

ngOnInit(): void {
  this.service.getData()
    .pipe(takeUntil(this.destroy$))
    .subscribe(data => this.dataList = data);
}

ngOnDestroy(): void {
  this.destroy$.next();
  this.destroy$.complete();
}
```

---

## SCSS Convention
- ใช้ SCSS nesting
- ใช้ CSS variables ของ NG-Zorro: `var(--ant-primary-color)`
- ห้าม inline style ใน template
- class ใช้ `kebab-case`
- Font: Prompt (Thai content), Inter (EN content)

---

## i18n
```html
<!-- Template -->
{{ 'KEY.PATH' | translate }}

<!-- Component -->
this.translate.instant('KEY.PATH')
```
Key files: `src/assets/i18n/th.json`, `en.json`

---

## Output Checklist (ก่อน Done)
- [ ] ผ่าน Pre-Work Step 1–3 แล้ว (ถาม DR + อ่าน code + Nook confirm plan)
- [ ] ไม่มี `any` ที่ไม่มี comment
- [ ] unsubscribe ทุก subscription
- [ ] error handling ใน HTTP call
- [ ] i18n key ถ้ามี user-facing text
- [ ] ไม่ชน naming กับ component อื่นใน module
- [ ] build ไม่มี TypeScript error
- [ ] ยึด pattern ของ module เดียวกัน (อ้างอิง file ที่อ่านมาจริง)
