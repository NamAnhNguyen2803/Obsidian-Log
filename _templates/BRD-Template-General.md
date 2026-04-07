---
skill: create-brd
uc_id: UC-XXX-000
name: "{{Tên Use Case}}"
version: 1.0
status: 🟡 Draft
domain: "{{account | approval | card | reporting | ...}}"
market: "{{VN | General}}"
channel: "{{Internet Banking | Mobile | Back Office | API}}"
actors:
  - "{{Primary Actor}}"
  - "{{Secondary Actor}}"
created: "{{date}}"
updated: "{{date}}"
tags:
  - brd
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
| **Description** | {{Mô tả 2-3 câu. Nếu >1 journey, ghi tên từng case.}} |
| **Primary Actor** | {{Actor chính}} |
| **Secondary Actor(s)** | {{Các actor phụ}} |
| **Channel** | {{Channel}} |
| **Preconditions** | 1. {{Điều kiện}} |
| **Open Questions** | OQ-01: {{Câu hỏi mở}} |
| **Validation Rules** | VR-01: {{Rule}} |
| **Notification** | {{Push notification / in-app / email}} |
| **Expected Result** | {{Kết quả thành công}} |
| **Exception Cases** | {{Các exception chính}} |

---

## B. USER JOURNEY — MAIN FLOW (DETAILED)

> **Trigger:** {{Sự kiện kích hoạt}}

| Step | Actor | Action | System Response |
|---|---|---|---|
| 1 | {{Actor}} | {{Hành động}} | {{Phản hồi}} |

---

## C. ALTERNATIVE FLOWS

### ALT-01 | {{Tên}}

> **Trigger:** {{Điều kiện}} — **At Step:** {{N}}

| Step | Actor | Action | System Response |
|---|---|---|---|
| N.1 | | | |

_Cover: Integration failure, user cancellation, business rule violation, data conflict_

---

## D. TECHNICAL & COMPLIANCE NOTES

### D.1 System Integrations

| System | Purpose | Trigger Step | Protocol |
|---|---|---|---|
| | | | |

### D.2 RBAC & Authorization

| Role | Permission | Enforcement Point |
|---|---|---|
| | | |

### D.3 Audit & Compliance Logging

| Event | Fields to Log | Basis |
|---|---|---|
| | | |

---

## E. DATA FIELD TABLE — BY SCREEN

> Map tất cả input + display fields theo từng màn hình.
> Field Types: text-input | number-input | date-input | select | multi-select | toggle | display-only | file-upload | OTP-input | PIN-input | biometric-prompt | checkbox | textarea

| Screen | Field Name | Label (VI) | Field Type | Format / Options | Validation | Required | API Field | Notes |
|---|---|---|---|---|---|---|---|---|
| Screen 1 — {{Tên}} | {{field_name}} | {{Nhãn}} | {{type}} | {{format}} | {{rule}} | Y/N | {{api_field}} | |
| Screen 2 — {{Tên}} | | | | | | | | |

---

## F. ERROR MESSAGE TABLE

| Error Code | Mô Tả | Message (English) | Message (Vietnamese) |
|---|---|---|---|
| ERR-{{CAT}}-001 | | | |

---

*End of BRD — {{UC-ID}} v1.0*
