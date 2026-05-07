# REVIEW-20260507-DR-20260507-001-employee-master

## Code Review: DR-20260507-001 (Employee Master)
**Reviewed**:
- `src/app/models/app-employee.model.ts`
- `src/app/modules/transport/modules/employee-master/employee-master.component.ts`
- `src/app/modules/transport/modules/employee-master/employee-master.component.html`
- `src/app/modules/transport/modules/employee-master/form/employee-master-form.component.ts`
- `src/app/modules/transport/modules/employee-master/form/employee-master-form.component.html`
- `src/app/modules/transport/transport-common.ts`
- `src/app/modules/transport/transport-routing.module.ts`
- `src/app/modules/transport/transport.module.ts`

**Result**: ⚠️ Approved with Comments

---

### Summary
โครงสร้าง code ถูกต้องตาม BMS_WEB pattern: extends BasePageComponent, state naming (isLoading/dataList/isEmpty), takeUntil + catchError + finalize ครบทุก call, modal pattern ผ่าน NzModalService, typed model namespace ไม่มี `any` มี 2 MAJOR ที่ต้องแก้ก่อน merge: hardcoded Thai text (i18n deviation) และ delete mutation order ที่จะทำให้เกิด bug ตอนต่อ BE จริง

---

### Issues

#### 🟡 MAJOR #1 — i18n: Hardcoded Thai text ในทุก user-facing string

- **Files**: `employee-master.component.html` line 12, 27, 42, 49, 56, 73–80 / `employee-master-form.component.html` line 8, 17, 26, 35, 44, 57, 75, 78
- **Issue**: ทุก label, button text, placeholder ใช้ภาษาไทย hardcode ตรง ขัดกับ BMS_WEB convention ที่ใช้ `{{ 'key' | translate }}` เปรียบเทียบกับ `truck-master.component.html` ที่ใช้ `{{ 'license_no' | translate }}`
- **Fix**: เพิ่ม key ใน `src/assets/i18n/th.json` + `en.json` แล้วเปลี่ยน template:
```html
<!-- ก่อน -->
<label class="ant-text-label">รหัสพนักงาน</label>
<!-- หลัง -->
<label class="ant-text-label">{{ 'employee_code' | translate }}</label>

<!-- ก่อน -->
placeholder="กรอกรหัสพนักงาน"
<!-- หลัง -->
placeholder="{{ 'please_input' | translate }}{{ 'employee_code' | translate }}"
```
Keys ที่ต้องเพิ่ม: `employee_code`, `employee_name`, `position`, `phone`, `email`, `status`, `employee_master`

---

#### 🟡 MAJOR #2 — delete() mutates mockSource ก่อน API success

- **File**: `employee-master.component.ts` line 136
- **Issue**:
```typescript
this.mockSource = this.mockSource.filter(e => e.id !== item.id); // ← mutate ก่อน API call
of({ message: 'ลบข้อมูลสำเร็จ' }).pipe(...).subscribe(res => {
  // ถ้า BE จริง error → item หายไปแล้วจาก local state → UI inconsistent
});
```
เมื่อเชื่อม BE จริง ถ้า API ตอบ error → item ถูกลบออกจาก state ไปแล้ว แต่ยังอยู่ใน DB
- **Fix**: ย้าย state mutation เข้าไปใน subscribe callback:
```typescript
private delete(item: AppEmployeeModel.EmployeeItem): void {
  this.isLoading = true;
  of({ message: 'ลบข้อมูลสำเร็จ' } as AppEmployeeModel.DeleteResponse).pipe(
    takeUntil(this.destroy$),
    catchError(err => {
      this._messageService.error(err?.error?.message ?? 'เกิดข้อผิดพลาด');
      return EMPTY;
    }),
    finalize(() => this.isLoading = false)
  ).subscribe(res => {
    this.mockSource = this.mockSource.filter(e => e.id !== item.id); // ← ย้ายมาตรงนี้
    this._messageService.success(res.message);
    this.filter();
  });
}
```

---

#### 🔵 MINOR #1 — `_translate` inject โดยไม่ใช้งาน

- **File**: `employee-master-form.component.ts` line 7, 36
- **Issue**: `TranslateService` injected แต่ไม่มีการเรียก `this._translate.instant()` ใน component นี้เลย
- **Suggestion**: ลบ import + injection ออก (จะได้ใช้อีกตอนทำ MAJOR #1 ถ้าต้องการ translate ใน TS)

---

#### 🔵 MINOR #2 — status type เป็น `string` แทน union type

- **File**: `app-employee.model.ts` line 24, 34
- **Issue**: `status: string` ยอมรับค่าอะไรก็ได้ — ควรใช้ union type
- **Suggestion**: `status: 'Active' | 'Inactive' = 'Active';`

---

#### 🔵 MINOR #3 — `trackBy` missing ใน table ngFor

- **File**: `employee-master.component.html` line 84
- **Issue**: `ngFor` ไม่มี `trackBy` ทำให้ Angular re-render ทุก row เมื่อ list เปลี่ยน
- **Suggestion**: เพิ่ม `trackBy: trackById` + method ใน .ts:
```typescript
trackById(_: number, item: AppEmployeeModel.EmployeeItem): number {
  return item.id;
}
```

---

#### 🔵 MINOR #4 — statusOptions ซ้ำซ้อนกับ CommonConfig.DEFAULT_STATUS

- **File**: `employee-master-form.component.ts` line 28–31
- **Issue**: `[{ label: 'Active', value: 'Active' }, ...]` ซ้ำซ้อนกับ `CommonConfig.DEFAULT_STATUS` ที่มีอยู่แล้ว
- **Suggestion**: Map จาก CommonConfig:
```typescript
statusOptions = CommonConfig.DEFAULT_STATUS.map(s => ({ label: s.key, value: s.key }));
```

---

#### ⚪ NOTE #1 — modal.afterClose subscription

- **File**: `employee-master.component.ts` line 97, 115
- **Note**: `modal.afterClose.subscribe(...)` ไม่มี `takeUntil` — แต่ NzModal `afterClose` emit ครั้งเดียวแล้ว complete เมื่อ modal ปิด subscription จึง auto-clear ไม่มี memory leak ✅ ไม่ต้องแก้

---

#### ⚪ NOTE #2 — EmployeeMasterFormComponent ไม่ export RouterConfig

- **File**: `transport-common.ts`
- **Note**: Form component ถูก import แต่ไม่มี RouterConfig (ถูกต้องแล้ว — form เปิดผ่าน NzModal ไม่ใช่ route) ✅

---

### Positive Notes
- ✅ `isLoading / dataList / isEmpty` state naming ถูกต้องตาม SKILL-DEV pattern
- ✅ ทุก observable ใช้ `takeUntil(this.destroy$)` + `catchError` + `finalize` ครบ
- ✅ `destroy$` Subject + `ngOnDestroy` implement ถูกต้อง
- ✅ `nzData: { data: { stage: 'new'/'edit' } }` pattern ตรงกับ BMS_WEB convention
- ✅ `inject(NZ_MODAL_DATA)` typed อย่างถูกต้อง ไม่ใช้ `any`
- ✅ `_modalRef.close(true/false)` pattern ถูกต้อง
- ✅ Email: template-level error message แยก required vs format อย่างดี
- ✅ 3 config files แก้ครบ (transport-common, routing, module)
- ✅ `npm run deploy:dev` ผ่านไม่มี TypeScript error

---

### Dev Action Required
- [ ] แก้ MAJOR #1: เพิ่ม i18n keys ใน th.json + en.json และเปลี่ยน template ใช้ `| translate`
- [ ] แก้ MAJOR #2: ย้าย `mockSource` mutation เข้าไปใน subscribe callback
