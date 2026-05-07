# DR-20260507-001: Employee Master

**Module**: `src/app/modules/transport/modules/employee-master`
**Type**: New Feature
**Priority**: P1

---

## Background
ต้องการหน้าจัดการข้อมูลพนักงานใหม่ใน Master menu เพื่อให้ผู้ใช้ทุก Role สามารถค้นหา เพิ่ม แก้ไข และลบข้อมูลพนักงานได้

## Scope

**FE:**
- สร้าง module `employee-master` (lazy loading) + routing
- สร้าง list component พร้อม search bar (เลขพนักงาน, ชื่อ) + ปุ่ม Search / Clear / เพิ่ม
- สร้าง table แสดงข้อมูลพนักงาน
- สร้าง Modal form สำหรับ Add / Edit พร้อม validation และ NzMessageService
- สร้าง model `app-employee.model.ts`
- สร้าง service เรียก mock API
- เพิ่ม route ใน parent transport routing
- เพิ่ม menu item ใน Master menu

**BE (mock):**
- Mock endpoints ไว้ก่อน รอ BE จริง

## Out of Scope
- Import/Export Excel
- ประวัติการแก้ไข (audit log)

## Acceptance Criteria
- [ ] หน้า list แสดงตารางพนักงานพร้อม pagination
- [ ] Search กรอง เลขพนักงาน / ชื่อพนักงาน ได้ถูกต้อง
- [ ] ปุ่ม Clear รีเซ็ต filter และโหลดข้อมูลใหม่
- [ ] กดปุ่ม "เพิ่ม" เปิด Modal form ว่าง
- [ ] กดปุ่ม "แก้ไข" เปิด Modal form พร้อมข้อมูลเดิม
- [ ] Form validate ทุกช่อง — ถ้ายังไม่กรอกครบแสดง error ใต้ field
- [ ] Submit สำเร็จ → NzMessageService แสดง "บันทึกข้อมูลสำเร็จ"
- [ ] กดลบ → confirm dialog → ลบ → แจ้ง "ลบข้อมูลสำเร็จ"
- [ ] สถานะแสดงเป็น tag สี (Active = เขียว, Inactive = เทา)
- [ ] `npm run deploy:dev` ผ่านไม่มี TypeScript error

## UI/UX Notes
- Search bar: input เลขพนักงาน + input ชื่อพนักงาน + ปุ่ม Search + ปุ่ม Clear อยู่แถวเดียวกัน
- ปุ่ม "เพิ่ม" อยู่ขวาบนของ section
- Table columns: Actions (แก้ไข/ลบ) | ลำดับที่ | รหัสพนักงาน | ชื่อพนักงาน | ตำแหน่ง | เบอร์โทร | Email | สถานะ
- Modal form fields (ทุก field required): รหัสพนักงาน, ชื่อพนักงาน, ตำแหน่ง, เบอร์โทร, Email, สถานะ (Select: Active/Inactive)
- รูปแบบ Modal ยึดตาม pattern หน้า Master อื่นๆ ใน BMS_WEB

## API / Data (Mock)

| Method | Endpoint | หน้าที่ |
|---|---|---|
| GET | `/api/employee` | ดึงรายการ (query: employeeCode, name, page, pageSize) |
| POST | `/api/employee` | เพิ่มพนักงาน |
| PUT | `/api/employee/{id}` | แก้ไขพนักงาน |
| DELETE | `/api/employee/{id}` | ลบพนักงาน |

- Model: `app-employee.model.ts`

---

## Dev Command

```
@Dev — docs/dr/DR-20260507-001-employee-master.md

DR: DR-20260507-001
Module: src/app/modules/transport/modules/employee-master
Action: Create

FE Tasks:
1. สร้าง src/app/models/app-employee.model.ts
   - EmployeeItem, EmployeeRequest, EmployeeResponse, EmployeeSearchRequest

2. สร้าง folder src/app/modules/transport/modules/employee-master/
   employee-master.component.ts
   employee-master.component.html
   employee-master.component.scss
   form/
     employee-master-form.component.ts
     employee-master-form.component.html
     employee-master-form.component.scss

3. Config ไฟล์ transport (ต้องแก้ทั้ง 3 ไฟล์):
   - src/app/modules/transport/transport-common.ts — เพิ่ม route constant
   - src/app/modules/transport/transport-routing.module.ts — เพิ่ม lazy route
   - src/app/modules/transport/transport.module.ts — register ถ้าจำเป็น

4. Table: m-table widget, สถานะใช้ nz-tag (Active=green, Inactive=default)
5. Form: nz-modal + nz-form ใน employee-master-form.component, validate required ทุก field, NzMessageService แจ้งผล

หมายเหตุ: เมนูเพิ่มผ่าน Database — ไม่ใช่ FE task

BE Tasks (Mock):
- ยังไม่ต้องทำ BE จริง — ให้ service return mock Observable ก่อน

Constraints:
- State: isLoading / dataList / isEmpty ตาม pattern
- HTTP: catchError + NzMessageService + finalize ทุก call
- ใช้ AppApiService (เมื่อต่อ BE จริง)
- TypeScript: no any without comment
- ยึด pattern จาก module master อื่นใน BMS_WEB

Test:
- Search / Clear ทำงานถูกต้อง
- Add / Edit Modal เปิด-ปิด + validate + แจ้งผลสำเร็จ
- Delete confirm + แจ้งผลสำเร็จ
- npm run deploy:dev ผ่าน
```
