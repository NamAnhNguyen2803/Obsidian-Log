---
# ==========================================
# FRONTMATTER — Obsidian metadata block
# ==========================================
# Đây là "frontmatter" — phần metadata của file.
# Obsidian đọc phần này để:
#   1. Dataview plugin query được (filter theo status, domain, v.v.)
#   2. Graph view biết file liên kết với gì
#   3. Search nhanh theo tags
# Người dùng KHÔNG thấy phần này khi đọc note — chỉ thấy ở Source Mode.
# ==========================================

uc_id: UC-ONB-VN-001
name: Individual Customer eKYC Onboarding (Vietnam)
version: 1.0
status: 🟡 Draft
domain: onboarding
market: VN
product: Digital Wallet (V-Pay)
channel: Mobile App
actors:
  - New Customer
  - eKYC Vendor (VN)
  - BCA C06 Service
  - Risk Engine
  - KYC Agent (manual review)
created: 2026-04-06
updated: 2026-04-06
tags:
  - brd
  - onboarding
  - ekyc
  - cccd
  - nfc
  - vietnam
  - vpay
related:
  - "[[Domain Knowledge/Digital Banking/Business-Rules]]"
  - "[[Domain Knowledge/Digital Banking/Individual-Banking]]"
  - "[[Skills/BRD/BRD-Onboarding]]"
sbv_regulation: Thông tư 16/2020/TT-NHNN, Nghị định 13/2023/NĐ-CP
---

# UC-ONB-VN-001 — Individual Customer eKYC Onboarding (Vietnam)

> **Skill used:** `brd-onboarding` · **Market:** 🇻🇳 Vietnam · **Version:** 1.0 · **Status:** 🟡 Draft

---

## A. USE CASE OVERVIEW

| Field | Content |
|-------|---------|
| **Use Case ID** | UC-ONB-VN-001 |
| **Use Case Name** | Individual Customer eKYC Onboarding — Vietnam (V-Pay) |
| **Version** | 1.0 |
| **Description** | Cho phép khách hàng cá nhân tại Việt Nam đăng ký và xác thực danh tính (eKYC) hoàn toàn tự phục vụ qua ứng dụng V-Pay Mobile. Quy trình bao gồm: đăng ký số điện thoại, chụp ảnh CCCD gắn chip, quét NFC chip, selfie liveness, và xác minh danh tính qua BCA C06. Kết quả thành công: tài khoản nâng lên KYC Level 2 (VERIFIED_CCCD). |
| **Primary Actor** | New Customer (khách hàng cá nhân mới) |
| **Secondary Actors** | eKYC Vendor (OCR + Liveness + Face Match), BCA C06 Service (NFC signature verification), Risk Engine (Decision Matrix), KYC Agent (nếu manual review) |
| **Channel** | Mobile App (Android / iOS) |
| **Market / Regulatory Context** | 🇻🇳 Vietnam — SBV (Ngân hàng Nhà nước), Thông tư 16/2020/TT-NHNN về eKYC, Nghị định 13/2023/NĐ-CP về bảo vệ dữ liệu cá nhân |
| **Preconditions** | (1) Khách hàng chưa có tài khoản V-Pay; (2) Số điện thoại VN chưa đăng ký (10 số, prefix 09/08/07/05/03); (3) CCCD gắn chip còn hiệu lực, chưa liên kết tài khoản khác; (4) Thiết bị hỗ trợ NFC; (5) Ứng dụng V-Pay đã cài đặt |
| **Open Questions** | OQ-01: Thời gian OTP expiry được cấu hình là bao nhiêu phút? (ảnh hưởng UX). OQ-02: Sau bao nhiêu ngày KYC PENDING thì tự động EXPIRED? OQ-03: Số lần retry tối đa cho NFC scan có cấu hình được không? OQ-04: Manual KYC agent xử lý trên platform nào (JITs)? SLA bao lâu? |
| **Validation Rules** | (1) Số điện thoại: 10 chữ số, prefix hợp lệ; (2) CCCD: 12 chữ số, còn hiệu lực, chưa APPROVED/PENDING với tài khoản khác; (3) Tuổi ≥ 18 (tính từ DOB trên chip); (4) Face match score ≥ ngưỡng cấu hình; (5) BCA C06 NFC signature VALID; (6) PIN: 6 số, không trùng lặp, không tuần tự |
| **Notification** | (1) OTP qua SMS khi đăng ký; (2) Push notification khi eKYC auto-approved; (3) In-app + Push khi eKYC pending manual review; (4) Push notification khi KYC Agent approve/reject; (5) In-app banner khi CCCD sắp hết hạn (X ngày trước) |
| **Expected Result** | Khách hàng hoàn thành eKYC thành công → tài khoản ACTIVE, KYC Level 2 (VERIFIED_CCCD) → có thể thực hiện giao dịch theo hạn mức Level 2 |
| **Exception Cases** | (1) CCCD đã liên kết tài khoản khác → BLOCKED; (2) NFC signature BCA C06 invalid → REJECTED; (3) Face match unmatched → Manual KYC queue; (4) Risk score cao → Manual KYC; (5) Drop-off giữa chừng → Resume flow khi mở lại app |

---

## B. USER JOURNEY — MAIN FLOW

> **Happy Path:** Khách hàng tự thực hiện eKYC thành công, được auto-approved.

```
Step 1:  Customer  → Mở ứng dụng V-Pay → tap "Đăng ký"
         System    → Hiển thị màn hình nhập số điện thoại

Step 2:  Customer  → Nhập số điện thoại (10 số)
         System    → Validate format (prefix 09/08/07/05/03)
                  → Kiểm tra số điện thoại chưa đăng ký
                  → Gửi OTP 6 số qua SMS

Step 3:  Customer  → Nhập OTP nhận được
         System    → Verify OTP: đúng → tiếp tục
                  → Ghi nhận phone number đã verify

Step 4:  Customer  → Đọc và chấp nhận Điều khoản & Điều kiện (T&C)
         System    → Ghi nhận consent timestamp

Step 5:  Customer  → Tạo PIN 6 số (nhập lần 1)
         Customer  → Xác nhận PIN (nhập lần 2)
         System    → Validate PIN: không trùng lặp (111111), không tuần tự (123456)
                  → Tạo tài khoản ACTIVE, KYC Level 1 (UNVERIFIED)
                  → Auto-login → Hiển thị màn hình Home cơ bản

Step 6:  System    → Hiển thị banner "Xác thực danh tính để tăng hạn mức"
         Customer  → tap "Xác thực ngay" → vào eKYC flow

Step 7:  Customer  → Chụp ảnh mặt TRƯỚC của CCCD
         System    → Upload lên eKYC Vendor
                  → API: OCR front face → trích xuất họ tên, CCCD số, ngày sinh, giới tính, địa chỉ
                  → API: ID Sanity Check (front) → đánh giá chất lượng ảnh
                  → API: ID Tampering Check (front) → phát hiện ảnh bị chỉnh sửa
                  → Nếu đạt → tiếp tục; nếu không → hiển thị hướng dẫn retry

Step 8:  Customer  → Chụp ảnh mặt SAU của CCCD
         System    → API: OCR back → trích xuất ngày cấp, nơi cấp, đặc điểm nhận dạng
                  → API: ID Sanity Check (back)
                  → API: ID Tampering Check (back)
                  → Nếu đạt → tiếp tục

Step 9:  Customer  → Đặt CCCD lên lưng điện thoại (NFC)
         System    → Đọc NFC chip trên CCCD
                  → API: BCA C06 → Verify NFC signature (dữ liệu chip vs dữ liệu OCR)
                  → Kết quả: VALID → tiếp tục

Step 10: Customer  → Thực hiện Liveness check (nhìn vào camera, làm theo hướng dẫn)
         System    → API: eKYC Vendor Liveness → kết quả: True (live person)

Step 11: Customer  → Chụp selfie
         System    → API: Face Matching → so sánh selfie với ảnh trên CCCD chip
                  → Kết quả: Matched / Unmatched (xem Alt flows)

Step 12: System    → Validate tuổi ≥ 18 từ DOB trên chip
                  → Kiểm tra CCCD chưa APPROVED/PENDING với tài khoản khác (1 CCCD - 1 phone rule)
                  → Chạy Decision Matrix: tất cả checks PASS → kết quả: AUTO-APPROVED
                  → Cập nhật KYC Level: UNVERIFIED → VERIFIED_CCCD (Level 2)
                  → Ghi audit log: thời gian, kết quả, các score, eKYC application ID

Step 13: System    → Gửi Push notification: "Tài khoản đã được xác thực thành công!"
         Customer  → Thấy màn hình Success, hạn mức giao dịch đã tăng
```

---

## C. ALTERNATIVE FLOWS

### ALT-01 | OTP sai hoặc hết hạn | Trigger: OTP không đúng | At Step 3

```
3.1  System    → Hiển thị "Mã OTP không đúng. Bạn còn {Y} lần thử."
3.2  Customer  → Nhập lại OTP
               → Sau 3 lần sai (Level 1) → Khóa 10 phút → hiển thị thời gian đếm ngược
               → Sau 5 lần sai (Level 2) → Khóa 15 phút
3.3  Customer  → tap "Gửi lại OTP"
               → Level 1: tối đa 4 lần resend → khóa 5 phút
               → Level 2: tổng 6 lần → khóa 15 phút (1 ngày)
               → Level 3: tổng 16 lần → khóa 10 ngày
```

### ALT-02 | PIN không hợp lệ hoặc không khớp | Trigger: PIN vi phạm rule | At Step 5

```
5.1  System    → Validate PIN lần 1:
               → Nếu trùng lặp (111111) hoặc tuần tự (123456) → "Mã PIN không hợp lệ. Vui lòng chọn PIN khác."
5.2  System    → Validate lần 2 (confirm):
               → Nếu không khớp lần 1 → "Mã PIN không khớp. Vui lòng nhập lại."
5.3  Customer  → Nhập lại từ đầu
```

### ALT-03 | Ảnh CCCD không hợp lệ (blur, hết hạn, không rõ) | At Step 7–8

```
7.1  System    → ID Sanity Check FAIL (blurry/partial) → "Ảnh chụp chưa rõ. Vui lòng chụp lại."
               → Hiển thị hướng dẫn: đủ ánh sáng, không che góc, không phản chiếu
7.2  Customer  → Chụp lại (tối đa {max_retry} lần, configurable)
               → Vượt quá → "Vui lòng thử lại sau" → về Home
7.3  System    → ID Tampering Check FAIL → "Ảnh CCCD có dấu hiệu bị chỉnh sửa. eKYC bị từ chối."
               → Ghi log: REJECTED, lý do: tampering detected
               → Thông báo to Customer: liên hệ hỗ trợ
```

### ALT-04 | NFC scan thất bại | At Step 9

```
9.1  System    → NFC read FAIL (chip không đọc được) → "Không thể đọc chip. Vui lòng thử lại."
               → Hiển thị video hướng dẫn: cách cầm điện thoại, vị trí đặt CCCD
9.2  Customer  → Retry (tối đa {max_retry} lần)
9.3  System    → BCA C06 NFC signature INVALID → "Xác thực NFC thất bại."
               → Kết quả: REJECTED (không thể override NFC failure)
               → Thông báo khách hàng khắc phục: liên hệ CCCD có chip hỏng → cơ quan công an
```

### ALT-05 | Face Match không khớp | At Step 11

```
11.1 System    → Face Match: Unmatched
               → Không auto-approved → chuyển sang Manual KYC queue
               → Cập nhật eKYC application status: PENDING_REVIEW
11.2 System    → Gửi In-app + Push: "Hồ sơ đang được xem xét. Thời gian xử lý: X ngày làm việc."
11.3 KYC Agent → Xem xét hồ sơ trên JITs portal: ảnh CCCD + selfie + dữ liệu chip
               → Approve → KYC Level 2, thông báo Customer
               → Reject → Thông báo Customer lý do; Customer được phép thực hiện lại eKYC
```

### ALT-06 | CCCD đã liên kết tài khoản khác | At Step 12

```
12.1 System    → Kiểm tra: CCCD này đang APPROVED/PENDING với account khác
               → Hiển thị: "CCCD này đã được liên kết với tài khoản khác. Vui lòng liên hệ hỗ trợ."
               → eKYC application: REJECTED
               → Ghi log audit: CCCD duplicate attempt, phone number, timestamp
```

### ALT-07 | Khách hàng thoát app giữa chừng (Drop-off) | Bất kỳ step nào

```
X.1  Customer  → Đóng app giữa eKYC flow
X.2  System    → Lưu trạng thái hiện tại (step dừng, dữ liệu đã nhập)
               → eKYC application status: PROGRESSING
X.3  Customer  → Mở lại app (trong vòng {resume_window} ngày, configurable)
               → System hiển thị: "Bạn có hồ sơ eKYC chưa hoàn thành. Tiếp tục?"
               → Resume từ step dừng
X.4  System    → Nếu quá {resume_window} → application status: EXPIRED
               → Customer phải bắt đầu lại từ đầu
```

---

## D. TECHNICAL & COMPLIANCE NOTES

### D.1 System Integrations

| System | Purpose | Trigger Step | Protocol | Notes |
|--------|---------|--------------|----------|-------|
| OTP Gateway (SMS) | Gửi OTP đăng ký | Step 2 | REST (async) | Timeout: {D} phút |
| eKYC Vendor | OCR front/back, ID Sanity, ID Tampering, Liveness, Face Match | Step 7–11 | REST (sync) | SLA: < 10s per call |
| BCA C06 Service | NFC chip signature verification | Step 9 | REST (sync) | National eKYC infrastructure |
| Risk Engine (Decision Matrix) | Tổng hợp kết quả → Auto-approve / Manual | Step 12 | Internal | Rules engine, configurable thresholds |
| Core Banking / Wallet System | Tạo tài khoản, cập nhật KYC Level | Step 5, 12 | REST/MQ | Sync |
| JITs Portal | KYC Agent manual review | ALT-05 | Web Portal | Async — SLA: {X} ngày làm việc |
| Audit Logging | Ghi toàn bộ sự kiện eKYC | Tất cả steps | Internal | Mandatory per SBV regulation |

### D.2 RBAC & Authorization

| Role | Quyền | Giới hạn |
|------|-------|---------|
| New Customer | Thực hiện eKYC self-service | Chỉ trên tài khoản của mình |
| KYC Agent | Review và quyết định Manual KYC cases | Không thể approve auto-flow |
| System (Risk Engine) | Auto-approve khi Decision Matrix PASS | Không có human override trừ Manual queue |

### D.3 Audit & Compliance Logging

Các sự kiện BẮT BUỘC ghi log (per SBV Thông tư 16/2020):
- Timestamp mỗi bước eKYC
- Kết quả từng API call (score, pass/fail)
- eKYC application ID
- CCCD number (masked trong logs: `XXXX****XXXX`)
- Phone number, Device ID
- Decision Matrix result + lý do
- Agent action (nếu manual review)

Lưu trữ: tối thiểu **5 năm** sau khi đóng tài khoản.

### D.4 Data Masking & Sensitivity

| Field | Masking Context | Rule |
|-------|----------------|------|
| CCCD số | UI hiển thị | `XXXX****XXXX` |
| CCCD số | API Response / Log | Không hiển thị toàn bộ |
| Số điện thoại | UI sau xác thực | `09X****XXX` |
| Ngày sinh | Log | Ghi đầy đủ nhưng restricted access |
| Ảnh selfie / face | Storage | Encrypted at rest, access controlled |
| Ảnh CCCD | Storage | Encrypted at rest, 5-year retention |

---

## E. DATA FIELD DEFINITION TABLE

| Field Name           | Rule / Format           | Validation                                         | Required (Y/N) | Source               | Notes                          |
| -------------------- | ----------------------- | -------------------------------------------------- | -------------- | -------------------- | ------------------------------ |
| Số điện thoại        | 10 chữ số               | Prefix: 09/08/07/05/03; chưa đăng ký               | Y              | User input           | Unique per account             |
| OTP                  | 6 chữ số                | One-time use; hết hạn sau {D} phút                 | Y              | SMS Gateway          |                                |
| PIN                  | 6 chữ số                | Không tuần tự (123456), không trùng lặp (111111)   | Y              | User input           | Encrypted khi lưu              |
| CCCD Số              | 12 chữ số               | Còn hiệu lực; chưa APPROVED/PENDING tài khoản khác | Y              | OCR + NFC chip       | Unique per account             |
| Họ và Tên            | Max 100 ký tự, chữ Việt | Khớp dữ liệu trên chip                             | Y              | NFC chip (auto-fill) | Editable nếu OCR sai           |
| Ngày sinh            | DD/MM/YYYY              | Tuổi ≥ 18                                          | Y              | NFC chip             | Readonly                       |
| Giới tính            | Nam / Nữ                | Enum                                               | Y              | NFC chip             | Readonly                       |
| Địa chỉ              | Text, max 200 ký tự     | —                                                  | Y              | NFC chip (editable)  | Customer có thể chỉnh          |
| Ngày cấp             | DD/MM/YYYY              | Phải trước ngày hiện tại                           | Y              | NFC chip             | Readonly                       |
| Ngày hết hạn         | DD/MM/YYYY              | Phải sau ngày hiện tại                             | Y              | NFC chip             | Trigger banner khi sắp hết hạn |
| Nghề nghiệp          | Dropdown                | Từ danh sách cấu hình                              | Y              | User selection       |                                |
| Chức vụ              | Dropdown                | Từ danh sách cấu hình                              | Y              | User selection       |                                |
| Mã số thuế           | 12 chữ số               | = CCCD số                                          | Y              | Auto-fill (= CCCD)   | Readonly                       |
| Nguồn thu nhập       | Enum                    | Mặc định: Tiền lương                               | Y              | Auto-fill            | Readonly                       |
| OCR Confidence Score | Float 0–1               | —                                                  | Internal       | eKYC Vendor          | Lưu trong audit log            |
| Liveness Score       | Float 0–1               | ≥ threshold (configurable)                         | Internal       | eKYC Vendor          |                                |
| Face Match Score     | Float 0–1               | ≥ threshold (configurable)                         | Internal       | eKYC Vendor          |                                |
| BCA C06 Result       | VALID / INVALID         | Must be VALID                                      | Internal       | BCA C06 Service      |                                |
| Device ID            | String                  | Unique per device                                  | Y              | System (auto)        | Ghi nhận khi PIN tạo           |
| eKYC Application ID  | UUID                    | Auto-generated                                     | Internal       | System               | Track toàn bộ eKYC session     |

---

## F. ERROR MESSAGE TABLE

| Error Description | Error Message (English) | Error Message (Vietnamese) |
|-----------------|------------------------|---------------------------|
| Sai định dạng SĐT | Incorrect phone number format. Please re-enter. | Định dạng số điện thoại không đúng. Vui lòng kiểm tra lại. |
| SĐT đã đăng ký | This phone number is already registered. | Số điện thoại này đã được đăng ký. |
| OTP không đúng | Invalid OTP. You have {Y} attempt(s) left. | Mã OTP không đúng. Bạn còn {Y} lần thử. |
| OTP hết hạn | OTP expired. Please request a new one. | Mã OTP đã hết hạn. Vui lòng yêu cầu mã mới. |
| OTP bị khóa | Account temporarily blocked for {Z} minutes. | Tài khoản bị khóa tạm thời trong {Z} phút. |
| PIN không hợp lệ | PIN is not valid. Please choose a different one. | Mã PIN không hợp lệ. Vui lòng chọn mã PIN khác. |
| PIN không khớp | PIN codes do not match. Please try again. | Mã PIN không khớp. Vui lòng nhập lại. |
| Ảnh CCCD không rõ | ID photo is unclear. Please retake. | Ảnh CCCD chưa rõ. Vui lòng chụp lại. |
| CCCD bị chỉnh sửa | ID photo has been tampered. eKYC rejected. | Ảnh CCCD có dấu hiệu bị chỉnh sửa. eKYC bị từ chối. |
| NFC scan thất bại | Unable to read chip. Please retry. | Không thể đọc chip. Vui lòng thử lại. |
| NFC signature invalid | NFC verification failed. eKYC rejected. | Xác thực NFC thất bại. eKYC bị từ chối. |
| Face match failed | Face does not match ID. Manual review required. | Khuôn mặt không khớp. Hồ sơ được chuyển xét duyệt thủ công. |
| CCCD đã liên kết | This ID is already linked to another account. | CCCD này đã được liên kết với tài khoản khác. |
| CCCD đang pending | This ID has a pending eKYC application. | CCCD này đang có hồ sơ eKYC chờ xử lý. |
| Tuổi không đủ | Minimum age requirement is 18 years old. | Bạn phải đủ 18 tuổi để đăng ký. |
| CCCD hết hạn | ID card is expired. Please renew before proceeding. | CCCD đã hết hạn. Vui lòng gia hạn trước khi tiếp tục. |
| eKYC session expired | eKYC session expired. Please start over. | Phiên eKYC đã hết hạn. Vui lòng bắt đầu lại. |
| Hệ thống lỗi | System error. Please try again later. | Lỗi hệ thống. Vui lòng thử lại sau. |

---

*Generated using `brd-onboarding` skill · Market: VN · References: onboarding-market-comparison.md §13-VN, regulatory.md §SBV*
*Next steps (khi sẵn sàng): [[Skills/BRD/BRD-Reviewer]] → 8-lens review · [[Skills/BRD/BRD-TestCaseWriter]] → generate test cases*
