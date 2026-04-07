---
skill: brd-onboarding
uc_id: UC-ONB-XXX-000
name: "{{Tên Use Case}}"
version: 1.0
status: 🟡 Draft
domain: onboarding
market: "{{VN | TH | Timor}}"
product: "{{Digital Wallet | Mobile Banking}}"
channel: Mobile App
actors:
  - New Customer
  - eKYC Vendor
  - Core Banking System
  - Risk Engine
  - "{{KYC Agent (nếu manual review)}}"
created: "{{date}}"
updated: "{{date}}"
tags:
  - brd
  - onboarding
---

# BRD — {{UC-ID}}: {{Tên Use Case}}

| Field | Detail |
|---|---|
| **Use Case ID** | {{UC-ID}} |
| **Use Case Name** | {{Tên Use Case}} |
| **Version** | 1.0 |
| **Date** | {{date}} |
| **Author** | Senior BA |
| **Status** | Draft |

---

## A. USE CASE OVERVIEW

| Field | Detail |
|---|---|
| **Use Case ID** | {{UC-ID}} |
| **Use Case Name** | {{Tên Use Case}} |
| **Version** | 1.0 |
| **Description** | {{Mô tả 2-3 câu}} |
| **Primary Actor** | New Customer |
| **Secondary Actor(s)** | {{eKYC Vendor, CBS, Risk Engine, KYC Agent}} |
| **Channel** | Mobile App |
| **Market / Regulatory Context** | {{VN: SBV Circular 16/2020 | TH: BOT regulations | Timor: MIGA}} |
| **Preconditions** | 1. {{Điều kiện}} |
| **Open Questions** | OQ-01: {{Câu hỏi mở}} |
| **Validation Rules** | VR-01: Tuổi ≥ 18. |
| | VR-02: CCCD/ID chưa hết hạn. |
| | VR-03: OCR confidence ≥ 80%. |
| | VR-04: Face match score ≥ 0.80. |
| **Notification** | {{SMS OTP, push notification kích hoạt ví}} |
| **Expected Result** | {{Khách hàng được onboard + ví activated}} |
| **Exception Cases** | {{OCR fail, liveness fail, duplicate ID, risk reject, drop-off}} |

---

## B. USER JOURNEY — MAIN FLOW (DETAILED)

> **Trigger:** {{Khách hàng mở app lần đầu / chọn Đăng ký}}

| Step | Actor | Action | System Response |
|---|---|---|---|
| 1 | New Customer | {{Hành động}} | {{Phản hồi}} |

---

## C. ALTERNATIVE FLOWS

### ALT-01 | {{Tên}}

> **Trigger:** {{Điều kiện}} — **At Step:** {{N}}

| Step | Actor | Action | System Response |
|---|---|---|---|
| N.1 | | | |

_VN phải cover: OCR fail, NFC fail, Face match fail, Duplicate CCCD, OTP expired, Risk reject_
_TH phải cover: DipChip fail, DOPA fail, Age out of range, Sanction list match, Compliance return_

---

## D. TECHNICAL & COMPLIANCE NOTES

### D.1 System Integrations

| System | Purpose | Trigger Step | Protocol |
|---|---|---|---|
| eKYC Vendor | OCR + Liveness | | REST |
| Core Banking | Account creation | | REST/MQ |
| OTP Gateway | SMS OTP | | REST |
| FIDO Server | Biometric registration (VN) | | FIDO2 |
| Risk Engine | CDD scoring | | REST |

### D.2 RBAC & Authorization

| Role | Permission | Enforcement Point |
|---|---|---|
| New Customer | SELF_REGISTRATION | API Gateway |

### D.3 Audit & Compliance Logging

| Event | Fields to Log | Basis |
|---|---|---|
| Registration Started | SessionID, PhoneNumber, Timestamp | Internal Audit |
| eKYC Completed | CustomerID, OCRScore, LivenessScore, FaceMatchScore | SBV / BOT |
| KYC Decision | CustomerID, Decision, RiskScore, Timestamp | AML/KYC |

### D.4 Data Masking & Sensitivity

| Field | Sensitive | Masking Context |
|---|---|---|
| id_number | Yes | UI: show last 4 digits only; Log: full encrypted |
| date_of_birth | Yes | Log: encrypted |
| selfie_image | Yes | Never log raw; store encrypted |
| face_match_score | Internal | Not exposed to customer |

---

## E. DATA FIELD TABLE — BY SCREEN

> Chỉ include screens thuộc sub-flow đã chọn.
> VN: SCR-01 → SCR-03 → SCR-04 (NFC) → SCR-05 (Selfie) → SCR-06 → SCR-07 → SCR-09 → SCR-10 → SCR-11
> TH: SCR-01 → SCR-02 → SCR-03 → SCR-04 (DipChip) → SCR-06 → SCR-07 → SCR-08 (KYM) → SCR-09 → SCR-11
> Field Types: text-input | number-input | date-input | select | display-only | camera-capture | NFC-scan | OTP-input | PIN-input | biometric-prompt | checkbox | textarea

| Screen | Field Name | Label (VI) | Field Type | Format / Options | Validation | Required | API Field | Sensitive | Notes |
|---|---|---|---|---|---|---|---|---|---|
| SCR-01 — Nhập SĐT | phone_number | Số điện thoại | text-input | 10 digits | Format + unique | Y | phone | No | |
| SCR-02 — OTP | otp_code | Mã OTP | OTP-input | 6 digits, 5min expiry | Match + expiry | Y | otp | No | Resend sau 60s |
| SCR-03 — Chụp CCCD | id_front_image | Ảnh CCCD mặt trước | camera-capture | JPG/PNG ≤5MB | OCR score ≥80% | Y | id_front | Yes | |
| SCR-03 — Chụp CCCD | id_back_image | Ảnh CCCD mặt sau | camera-capture | JPG/PNG ≤5MB | OCR score ≥80% | Y | id_back | Yes | |
| SCR-04 — NFC (VN) | nfc_chip_data | — | NFC-scan | ISO 14443 | BCA C06 signature valid | Y (VN) | nfc_payload | Yes | |
| SCR-05 — Selfie (VN) | selfie_image | Ảnh selfie | camera-capture | JPG/PNG ≤5MB | Liveness + face match ≥0.80 | Y (VN) | selfie | Yes | |
| SCR-06 — Thông tin CK | full_name | Họ và tên | text-input | Auto from OCR | Non-empty | Y | full_name | No | Editable nếu OCR mismatch |
| SCR-06 — Thông tin CK | id_number | Số CCCD | text-input | 12 digits | Format + unique | Y | id_number | Yes | |
| SCR-06 — Thông tin CK | date_of_birth | Ngày sinh | date-input | DD/MM/YYYY | Age ≥18 | Y | dob | No | |
| SCR-06 — Thông tin CK | gender | Giới tính | select | Nam / Nữ / Khác | — | Y | gender | No | |
| SCR-06 — Thông tin CK | address | Địa chỉ | text-input | ≤200 chars | Non-empty | Y | address | No | |
| SCR-06 — Thông tin CK | id_expiry_date | Ngày hết hạn CCCD | date-input | DD/MM/YYYY | Not expired | Y | id_expiry | No | |
| SCR-07 — Consent | consent_tnc | Điều khoản dịch vụ | checkbox | checked=agree | Must be checked | Y | consent_tnc | No | Link T&C |
| SCR-07 — Consent | consent_privacy | Chính sách bảo mật | checkbox | checked=agree | Must be checked | Y | consent_privacy | No | |
| SCR-07 — Consent | consent_marketing | Nhận marketing | toggle | On/Off | Optional | N | consent_marketing | No | Default Off |
| SCR-09 — PIN | pin_code | Đặt mã PIN | PIN-input | 6 digits masked | No sequential/all-same | Y | — | Yes | |
| SCR-09 — PIN | pin_confirm | Xác nhận PIN | PIN-input | 6 digits masked | Match pin_code | Y | — | Yes | |
| SCR-10 — FIDO (VN) | biometric_consent | Đăng ký sinh trắc học | toggle | On/Off | Device must have biometric + lock | N | fido_registered | No | Skip nếu không hỗ trợ |
| SCR-11 — Kết quả | wallet_id | Mã ví | display-only | — | — | N/A | wallet_id | No | |
| SCR-11 — Kết quả | kyc_status | Trạng thái KYC | display-only | Approved/Pending/Rejected | — | N/A | kyc_status | No | |

---

## F. ERROR MESSAGE TABLE

| Error Code | Mô Tả | Message (English) | Message (Vietnamese) |
|---|---|---|---|
| ERR-OTP-001 | OTP expired | "OTP has expired. Please request a new one." | "Mã OTP đã hết hạn. Vui lòng yêu cầu mã mới." |
| ERR-OCR-001 | OCR low confidence | "ID photo quality too low. Please retake." | "Ảnh CMND/CCCD không rõ. Vui lòng chụp lại." |
| ERR-KYC-001 | Face match failed | "Face verification failed. Please try again." | "Xác minh khuôn mặt thất bại. Vui lòng thử lại." |
| ERR-KYC-002 | Duplicate ID | "This ID is already registered." | "Số CCCD này đã được đăng ký." |
| ERR-RISK-001 | High risk rejected | "Your application requires additional review." | "Hồ sơ của bạn cần được xem xét thêm." |
| ERR-NFC-001 | NFC scan failed (VN) | "NFC scan failed. Please try again." | "Đọc chip NFC thất bại. Vui lòng thử lại." |
| ERR-AUTH-001 | Session expired | "Your session has expired. Please start again." | "Phiên làm việc đã hết hạn. Vui lòng bắt đầu lại." |

---

*End of BRD — {{UC-ID}} v1.0*
