---
skill: transaction-brd
uc_id: UC-TXN-XXX
name: "{{Tên Use Case}}"
version: 1.0
status: 🟡 Draft
domain: transaction
market: "{{VN | General}}"
product: "{{Digital Wallet (V-Smart Pay)}}"
channel: Mobile Wallet App
actors:
  - Individual Customer (Sender)
  - "{{Receiver / Partner}}"
  - Core Banking System (CBS)
  - GL Posting Module
created: "{{date}}"
updated: "{{date}}"
tags:
  - brd
  - transaction
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
| **Channel** | Mobile Wallet App |
| **Preconditions** | 1. {{Điều kiện}} |
| **Open Questions** | OQ-01: {{Câu hỏi mở}} |
| **Validation Rules** | VR-01: {{Rule}} |
| **Fee Rules** | Case A — {{điều kiện}}: {{phí}}. Case B — {{điều kiện}}: {{phí}}. |
| **Notification** | {{Push notification / in-app}} |
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

_Cover: Insufficient balance, Timeout, Duplicate, Partner API failure, Auth failure_

---

## D. TECHNICAL & COMPLIANCE NOTES

### D.1 System Integrations

| System | Purpose | Trigger Step | Protocol |
|---|---|---|---|
| Core Banking (CBS) | Debit/credit, balance | | REST / ISO 8583 |

### D.2 RBAC & Authorization

| Role | Permission | Enforcement Point |
|---|---|---|
| Customer | INITIATE_TRANSACTION | Session + role check |

### D.3 Audit & Compliance Logging

| Event | Fields to Log | Basis |
|---|---|---|
| Transaction Initiated | TxnID, UserID, Amount, Timestamp | Internal Audit |
| GL Posting | TxnID, GL Code, Dr/Cr, Amount, ValueDate | Finance Ops |

---

## E1. AMOUNT LIMIT MATRIX

| Limit Type | Scope | Value | Config Level | Notes |
|---|---|---|---|---|
| Minimum per Transaction | Per Txn | 1.000 VND | System | VR-01 |
| Daily Limit | Per User | 50.000.000 VND | Role | |
| Monthly Limit | Per User | 100.000.000 VND | Role | |
| Group Daily Limit | P2P + Bank + Payment | 50.000.000 VND | System | |
| Group Monthly Limit | P2P + Bank + Payment | 100.000.000 VND | System | |

---

## E2. FEE, TAX & DISTRIBUTION

### E2.1 Fee Structure

| Case | Điều Kiện | Fee | Charged To | Timing |
|---|---|---|---|---|
| Case A | {{điều kiện}} | {{Miễn phí / amount}} | N/A | N/A |
| Case B | {{điều kiện}} | {{Fee formula}} | Customer | T+0 |

### E2.2 Tax

> Net Revenue = Fee / 1.1 | Output VAT = Fee / 1.1 × 0.1

### E2.3 Fee Distribution

| Party | Share | GL Account | Notes |
|---|---|---|---|
| V-Smart Pay (Revenue) | Net fee sau VAT | 5113200000 | |
| Tax Authority (Output VAT) | 10% of fee | 3331110000 | |

---

## E3. GL POSTING RULES

### E3.1 Bút Toán GL

| Step | Dr/Cr | GL Code | GL Name | Formula / Amount |
|---|---|---|---|---|
| Step 1 — Block Money | Dr | 3388200000 | Wallet – Source Customer | Principal ± Fee |
| | Cr | 1131060001 | Suspense Bank | Principal ± Fee |
| Step 2 — Release | Dr | 1131060001 | Suspense Bank | Principal |
| | Cr | {{1121060002}} | {{Master Account – BIDV}} | Principal |

### E3.2 Reversal GL Rules

- Đảo ngược toàn bộ entries theo thứ tự ngược, reference TxnID gốc.

---

## E4. SETTLEMENT

### E4.1 Settlement Method

| Type | Method | Timing | System |
|---|---|---|---|
| {{Loại}} | {{Book transfer / Clearing}} | T+0 | CBS ↔ {{Partner}} |

---

## E5. RECONCILIATION & EXCEPTION HANDLING

### E5.2 Exception Scenarios

| Exception | Trigger | System Behaviour | Resolution |
|---|---|---|---|
| CBS Timeout | >30s | PENDING_SETTLEMENT | Recon job T+5min |
| Partner No ACK | No ack | SETTLEMENT_PENDING | Finance Ops manual |
| GL Posting Failure | GL lỗi | GL_POSTING_PENDING | Retry 3x / 10min |
| Duplicate | Same params 5min | Block | ERR-TXN-001 |

### E5.4 Reconciliation SLA

| Break Type | Detection | Auto-Resolution | Manual Escalation |
|---|---|---|---|
| CBS Timeout | T+5 min | T+30 min | T+2 giờ |
| Partner No ACK | Post-cycle | N/A | T+4 giờ |
| GL Posting Failure | Real-time | T+30 min | T+2 giờ |

---

## F. ERROR MESSAGE TABLE

| Error Code | Mô Tả | Message (English) | Message (Vietnamese) |
|---|---|---|---|
| ERR-BAL-001 | Insufficient balance | "Insufficient balance. Please top up your wallet." | "Số dư ví không đủ. Vui lòng nạp thêm tiền." |
| ERR-LMT-001 | Daily limit exceeded | "Transaction exceeds your daily limit." | "Giao dịch vượt hạn mức trong ngày." |
| ERR-LMT-002 | Monthly limit exceeded | "Transaction exceeds your monthly limit." | "Giao dịch vượt hạn mức trong tháng." |
| ERR-TXN-001 | Duplicate detected | "Duplicate transaction detected. Please verify." | "Phát hiện giao dịch trùng lặp. Vui lòng kiểm tra lại." |
| ERR-CBS-001 | CBS timeout | "Transaction is being processed. Please check status later." | "Giao dịch đang được xử lý. Vui lòng kiểm tra lại sau." |
| ERR-GL-001 | GL posting failure | "Transaction settled but accounting entry pending." | "Giao dịch đã thanh toán nhưng hạch toán đang chờ xử lý." |
| ERR-AUTH-001 | Session expired | "Your session has expired. Please log in again." | "Phiên làm việc đã hết hạn. Vui lòng đăng nhập lại." |

---

## G. DATA FIELD TABLE — BY SCREEN

> Map tất cả input + display fields theo từng màn hình.
> Field Types: text-input | number-input | select | display-only | PIN-input | biometric-prompt

| Screen | Field Name | Label (VI) | Field Type | Format / Options | Validation | Required | API Field | Notes |
|---|---|---|---|---|---|---|---|---|
| Screen 1 — Nhập người nhận | receiver_id | Số điện thoại / Wallet ID | text-input | 10 digits | VR-05: tồn tại + ACTIVE | Y | beneficiary_id | |
| Screen 1 — Nhập người nhận | receiver_name | Tên người nhận | display-only | — | — | N/A | beneficiary_name | Sau lookup thành công |
| Screen 2 — Nhập số tiền | amount | Số tiền | number-input | Integer, VND | VR-01: ≥1,000; hạn mức | Y | transaction_amount | Hiển thị số dư cạnh field |
| Screen 2 — Nhập số tiền | note | Nội dung | text-input (optional) | ≤200 ký tự | Optional | N | transfer_note | |
| Screen 3 — Xác nhận | amount_display | Số tiền | display-only | xxx,xxx VND | — | N/A | — | |
| Screen 3 — Xác nhận | fee_display | Phí giao dịch | display-only | xxx,xxx VND | — | N/A | — | "Miễn phí" nếu Case A |
| Screen 3 — Xác nhận | total_deduct | Tổng trừ ví | display-only | xxx,xxx VND | — | N/A | — | Amount + Fee |
| Screen 4 — Xác thực | auth | — | PIN-input / biometric-prompt | PIN: 6 digits masked | 3 fail → 5min lock; 6 → permanent | Y | — | FIDO2 nếu biometric |
| Screen 5 — Kết quả | txn_id | Mã giao dịch | display-only | TXN-XXXXXXXX | — | N/A | transaction_id | |
| Screen 5 — Kết quả | status | Trạng thái | display-only | COMPLETED / FAILED | — | N/A | transaction_status | |
| Screen 5 — Kết quả | remaining_balance | Số dư còn lại | display-only | xxx,xxx VND | — | N/A | wallet_balance | |

---

*End of BRD — {{UC-ID}} v1.0*
