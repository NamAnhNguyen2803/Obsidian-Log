---
uc_id: UC-TXN-002
name: Chuyển Tiền Ví Ra Ngân Hàng BIDV
version: 1.0
status: 🟡 Draft
domain: transaction
market: VN
product: Digital Wallet (V-Smart Pay)
channel: Mobile Wallet App
actors:
  - Individual Customer (Sender)
  - BIDV Account Holder (Receiver)
  - Core Banking System (CBS)
  - BIDV Clearing System
  - GL Posting Module
created: 2026-04-06
updated: 2026-04-07
tags:
  - brd
  - transaction
  - bank-transfer
  - bidv
---

# BRD — UC-TXN-002: Chuyển Tiền Từ Ví Ra Ngân Hàng BIDV (Wallet to Bank Transfer)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-TXN-002 |
| **Use Case Name** | Chuyển Tiền Ví Ra Ngân Hàng BIDV |
| **Version** | 1.0 |
| **Date** | 2026-04-06 |
| **Author** | Senior BA |
| **Status** | Draft |

---

## A. USE CASE OVERVIEW

| Field | Detail |
|---|---|
| **Use Case ID** | UC-TXN-002 |
| **Use Case Name** | Chuyển Tiền Ví Ra Ngân Hàng BIDV |
| **Version** | 1.0 |
| **Description** | Khách hàng cá nhân thực hiện chuyển tiền từ ví điện tử V-Smart Pay sang tài khoản ngân hàng BIDV bất kỳ. Hệ thống xử lý qua Master Account BIDV với 2 case phân biệt theo ngưỡng số tiền: (A) ≤ 20.000.000 VND — miễn phí; (B) > 20.000.000 VND — có phí giao dịch + VAT 10%. |
| **Primary Actor** | Khách hàng cá nhân (Sender) |
| **Secondary Actor(s)** | Chủ tài khoản BIDV (Receiver), Core Banking System (CBS), BIDV Clearing System, GL Posting Module |
| **Channel** | Mobile Wallet App |
| **Preconditions** | 1. Sender đã đăng nhập thành công và phiên còn hiệu lực. |
| | 2. Ví Sender ở trạng thái ACTIVE. |
| | 3. Số dư ví Sender ≥ Số tiền chuyển + Phí (nếu có). |
| | 4. Giao dịch không vượt hạn mức ngày/tháng/nhóm. |
| | 5. Số tài khoản BIDV đích hợp lệ và tồn tại. |
| **Open Questions** | OQ-01: Có cần verify tên chủ tài khoản BIDV trước khi Sender xác nhận không? |
| | OQ-02: Fee cụ thể (VND hoặc %) cho giao dịch > 20M được cấu hình ở đâu (product config)? |
| | OQ-03: Có giới hạn số lần chuyển BIDV/ngày không? |
| | OQ-04: Cut-off time BIDV clearing có ảnh hưởng đến T+0 không? |
| **Validation Rules** | VR-01: Số tiền ≥ 1.000 VND. |
| | VR-02: Hạn mức ngày: 50.000.000 VND (nhóm P2P + BankTransfer + Payment). |
| | VR-03: Hạn mức tháng: 100.000.000 VND (nhóm). |
| | VR-04: Số tài khoản BIDV phải đúng định dạng và tồn tại. |
| | VR-05: Số dư ví ≥ Amount + Fee (nếu amount > 20M). |
| | VR-06: Fee tính trên tổng số tiền chuyển (không tính trên phần vượt 20M). |
| **Fee Rules** | Case A — Amount ≤ 20.000.000 VND: Miễn phí. |
| | Case B — Amount > 20.000.000 VND: Phí = theo product config (VND hoặc %); VAT = 10% trên phí (Output VAT). |
| | Công thức: Net Revenue = Fee / 1.1; Output VAT = Fee / 1.1 x 0.1. |
| **Notification** | Sender: Push notification + In-app confirmation "Chuyển tiền thành công". |
| | (Receiver nhận thông báo từ BIDV, ngoài phạm vi hệ thống.) |
| **Expected Result** | Số dư ví Sender giảm đúng (Amount + Fee). Tài khoản BIDV Receiver được ghi có. Giao dịch COMPLETED. GL posting hoàn thành. |
| **Exception Cases** | Số dư không đủ, vượt hạn mức, tài khoản BIDV không hợp lệ, CBS timeout, BIDV no ACK, GL lỗi, giao dịch trùng lặp. |

---

## B. USER JOURNEY — MAIN FLOW (DETAILED)

### Case A — Amount ≤ 20.000.000 VND (Không Phí)

> **Trigger:** Sender chọn "Chuyển tiền ra ngân hàng" trên Wallet App.

| Step | Actor | Action | System Response |
|---|---|---|---|
| 1 | Sender | Mở Wallet App, chọn **"Chuyển tiền ra ngân hàng"** | Hiển thị màn hình nhập thông tin chuyển tiền |
| 2 | Sender | Chọn ngân hàng **BIDV**, nhập **số tài khoản** Receiver | Hệ thống gọi BIDV API để verify tài khoản |
| 3 | System | Xác thực tài khoản BIDV | Kiểm tra tài khoản tồn tại, ACTIVE → Hiển thị tên chủ tài khoản để Sender xác nhận |
| 4 | Sender | Xác nhận đúng Receiver, nhập **số tiền** chuyển (≤ 20M) | Hiển thị số tiền; system xác định Case A (no fee) |
| 5 | System | Kiểm tra validation | VR-01 đến VR-05: số dư ≥ Amount, hạn mức ngày/tháng/nhóm |
| 6 | System | Hiển thị màn hình xác nhận | Thông tin: Ngân hàng BIDV, Số TK, Tên Receiver, Số tiền, Phí: Miễn phí |
| 7 | Sender | Nhập **nội dung chuyển tiền** (tuỳ chọn) | Ghi nhận nội dung (≤ 200 ký tự) |
| 8 | Sender | Xác nhận giao dịch bằng **PIN / Biometric** | Xác thực danh tính Sender |
| 9 | System | Tạo TxnID, ghi nhận trạng thái **PROCESSING** | Log: TxnID, SenderID, BeneficiaryAccount, Amount, Timestamp |
| 10 | System | **GL Step 1 — Block Money:** Dr 3388200000 (Sender), Cr 1131060001 (Suspense Bank) | Số dư ví Sender giảm; tiền giữ tại Suspense |
| 11 | System | Gọi CBS: Debit Master Account BIDV instruction | CBS tạo lệnh chuyển tiền sang BIDV |
| 12 | System | **GL Step 2 — Release to BIDV:** Dr 1131060001 (Suspense), Cr 1121060002 (Master BIDV) | Suspense được giải phóng; Master BIDV tăng |
| 13 | System | CBS gửi lệnh clearing tới **BIDV Clearing System** | BIDV ghi có vào tài khoản Receiver |
| 14 | System | BIDV xác nhận clearing thành công (ACK nhận được) | Trả về BIDV Reference Number |
| 15 | System | Cập nhật trạng thái giao dịch → **COMPLETED** | Lưu transaction record đầy đủ |
| 16 | System | Gửi **push notification** cho Sender | Sender: "Chuyển tiền thành công" |
| 17 | Sender | Xem màn hình kết quả | Hiển thị: TxnID, số tiền, tên Receiver, BIDV ref, thời gian, số dư còn lại |

---

### Case B — Amount > 20.000.000 VND (Có Phí + VAT 10%)

> **Trigger:** Sender nhập số tiền > 20.000.000 VND tại Step 4.

| Step | Actor | Action | System Response |
|---|---|---|---|
| 1–3 | — | Giống Case A Steps 1–3 | — |
| 4 | Sender | Xác nhận đúng Receiver, nhập **số tiền** chuyển (> 20M) | System xác định Case B; tính Fee theo product config |
| 5 | System | Tính phí: Fee = [theo config]; Net Revenue = Fee / 1.1; Output VAT = Fee / 1.1 x 0.1 | Kiểm tra số dư ≥ Amount + Fee |
| 6 | System | Kiểm tra validation | VR-01 đến VR-06 |
| 7 | System | Hiển thị màn hình xác nhận | Thông tin: Số tiền, Phí giao dịch, VAT (10% trên phí), Tổng trừ ví |
| 8 | Sender | Nhập nội dung chuyển tiền (tuỳ chọn) | Ghi nhận nội dung (≤ 200 ký tự) |
| 9 | Sender | Xác nhận giao dịch bằng **PIN / Biometric** | Xác thực danh tính Sender |
| 10 | System | Tạo TxnID, ghi nhận trạng thái **PROCESSING** | Log: TxnID, SenderID, BeneficiaryAccount, Amount, Fee, Timestamp |
| 11 | System | **GL Step 1 — Block Money (Principal + Fee):** Dr 3388200000 (Sender), Cr 1131060001 (Suspense Bank) | Amount = Principal + Fee |
| 12 | System | **GL Step 2 — Release Principal to BIDV:** Dr 1131060001 (Suspense), Cr 1121060002 (Master BIDV) | Amount = Principal only |
| 13 | System | **GL Step 3 — Recognize Fee Revenue (ex-VAT):** Dr 1131060001 (Suspense), Cr 5113200000 (Transaction Fee Revenue) | Amount = Fee / 1.1 |
| 14 | System | **GL Step 4 — Recognize Output VAT:** Dr 1131060001 (Suspense), Cr 3331110000 (Output VAT) | Amount = Fee / 1.1 x 0.1 |
| 15 | System | CBS gửi lệnh clearing tới **BIDV Clearing System** | BIDV ghi có vào tài khoản Receiver (Principal only) |
| 16 | System | BIDV xác nhận clearing thành công (ACK nhận được) | Trả về BIDV Reference Number |
| 17 | System | Cập nhật trạng thái giao dịch → **COMPLETED** | Lưu transaction record đầy đủ |
| 18 | System | Gửi **push notification** cho Sender | Sender: "Chuyển tiền thành công" |
| 19 | Sender | Xem màn hình kết quả | Hiển thị: TxnID, số tiền chuyển, phí, VAT, tổng đã trừ, BIDV ref, số dư còn lại |

---

## C. ALTERNATIVE FLOWS

### ALT-01 | Tài Khoản BIDV Không Hợp Lệ / Không Tồn Tại

> **Trigger:** Step 3 — BIDV API trả về tài khoản không tìm thấy hoặc inactive
> **At Step:** 3

| Step | Actor | Action | System Response |
|---|---|---|---|
| 3.1 | System | BIDV verify thất bại | Hiển thị lỗi ERR-ACC-003: "Số tài khoản BIDV không hợp lệ hoặc không tồn tại." |
| 3.2 | Sender | Nhập lại số tài khoản | Quay lại Step 2 |

---

### ALT-02 | Số Dư Ví Không Đủ

> **Trigger:** Step 5 — số dư < Amount (Case A) hoặc số dư < Amount + Fee (Case B)
> **At Step:** 5

| Step | Actor | Action | System Response |
|---|---|---|---|
| 5.1 | System | Kiểm tra số dư thất bại | Hiển thị lỗi ERR-BAL-001: "Số dư ví không đủ. Vui lòng nạp thêm tiền." |
| 5.2 | Sender | Chọn Nạp tiền hoặc Huỷ | Redirect sang luồng Cash In hoặc thoát |

---

### ALT-03 | Vượt Hạn Mức

> **Trigger:** Step 5 — vượt hạn mức ngày, tháng, hoặc nhóm
> **At Step:** 5

| Step | Actor | Action | System Response |
|---|---|---|---|
| 5.1 | System | Kiểm tra hạn mức thất bại | Hiển thị lỗi ERR-LMT-001 (ngày) / ERR-LMT-002 (tháng) / ERR-LMT-003 (nhóm) |
| 5.2 | Sender | Xem hạn mức còn lại, điều chỉnh số tiền hoặc huỷ | Màn hình hiển thị hạn mức đã dùng / còn lại |

---

### ALT-04 | Xác Thực PIN / Biometric Thất Bại

> **Trigger:** Step 8 (Case A) / Step 9 (Case B) — xác thực không thành công
> **At Step:** 8 hoặc 9

| Step | Actor | Action | System Response |
|---|---|---|---|
| N.1 | System | Xác thực thất bại lần 1–2 | Hiển thị "Xác thực không thành công. Còn [N] lần thử." |
| N.2 | System | Xác thực thất bại lần 3 | Khoá giao dịch 5 phút |
| N.3 | System | Tổng thất bại 6 lần | Khoá tài khoản vĩnh viễn; yêu cầu liên hệ hỗ trợ |

---

### ALT-05 | CBS Timeout Khi Block Money

> **Trigger:** Step 10 (Case A) / Step 11 (Case B) — CBS không phản hồi > 30s
> **At Step:** 10 hoặc 11

| Step | Actor | Action | System Response |
|---|---|---|---|
| N.1 | System | CBS timeout | Trạng thái PENDING_SETTLEMENT; hiển thị ERR-CBS-001 |
| N.2 | System | Recon job sau T+5 phút | Query CBS để xác nhận trạng thái |
| N.3 | System | CBS xác nhận debit thành công | Tiếp tục luồng GL và clearing BIDV |
| N.4 | System | CBS xác nhận debit thất bại | Trạng thái FAILED; số dư ví không thay đổi |

---

### ALT-06 | BIDV No ACK (Clearing Timeout)

> **Trigger:** Step 13–14 (Case A) / Step 15–16 (Case B) — BIDV không phản hồi sau clearing
> **At Step:** 13 hoặc 15

| Step | Actor | Action | System Response |
|---|---|---|---|
| N.1 | System | BIDV no ACK sau cut-off | Trạng thái SETTLEMENT_PENDING; alert Finance Ops |
| N.2 | Finance Ops | Tra cứu trên BIDV portal | Xác nhận thủ công trạng thái giao dịch tại BIDV |
| N.3 | Finance Ops | BIDV đã ghi có → update system | Cập nhật trạng thái COMPLETED qua back-office tool |
| N.4 | Finance Ops | BIDV chưa ghi có → reversal | Thực hiện reversal: hoàn tiền về ví Sender |

---

### ALT-07 | GL Posting Thất Bại

> **Trigger:** GL Module lỗi sau khi CBS/BIDV đã xác nhận thành công
> **At Step:** 10–14 (Case A) hoặc 11–14 (Case B)

| Step | Actor | Action | System Response |
|---|---|---|---|
| N.1 | System | GL posting thất bại | Status SETTLED; GL GL_POSTING_PENDING; alert Finance Ops |
| N.2 | System | Retry job 3 lần x 10 phút | Ghi log mỗi lần retry |
| N.3 | System | Vẫn thất bại sau 3 lần | Escalate Finance Ops; manual intervention queue |
| N.4 | Finance Ops | Xử lý manual GL entry | Post bút toán thủ công; cập nhật trạng thái |

---

### ALT-08 | Giao Dịch Trùng Lặp

> **Trigger:** Phát hiện giao dịch trùng (cùng Sender + BeneficiaryAccount + Amount trong 5 phút)
> **At Step:** 9 (Case A) / 10 (Case B)

| Step | Actor | Action | System Response |
|---|---|---|---|
| N.1 | System | Phát hiện duplicate | Block giao dịch; hiển thị ERR-TXN-001 |
| N.2 | Sender | Xác nhận hoặc huỷ | Nếu cố ý: chờ 5 phút rồi thử lại |

---

## D. TECHNICAL & COMPLIANCE NOTES

### D.1 System Integrations

| System | Purpose | Trigger Step | Protocol |
|---|---|---|---|
| Core Banking System (CBS) | Debit ví Sender, tạo lệnh chuyển BIDV | Step 10–13 (A) / 11–15 (B) | REST / ISO 8583 |
| BIDV Clearing System | Ghi có tài khoản Receiver tại BIDV | Step 13 (A) / 15 (B) | NAPAS/BIDV clearing protocol |
| BIDV Account Verify API | Kiểm tra tài khoản BIDV trước giao dịch | Step 3 | REST |
| GL Posting Module | Ghi bút toán kép Dr/Cr (1–4 entries) | Step 10–12 (A) / 11–14 (B) | Internal service call |
| Push Notification Service | Thông báo Sender | Step 16 (A) / 18 (B) | FCM / APNs |
| Audit Log Service | Ghi nhận mọi sự kiện giao dịch | Suốt luồng | Async event |

### D.2 RBAC & Authorization

| Role | Permission | Enforcement Point |
|---|---|---|
| Individual Customer | INITIATE_BANK_TRANSFER | Session token + role check tại API Gateway |
| System | AUTO_GL_POSTING | Post CBS confirmation |
| Finance Ops | MANUAL_GL_ENTRY, UPDATE_TXN_STATUS | Back-office admin console |

### D.3 Audit & Compliance Logging

| Event | Fields to Log | Basis |
|---|---|---|
| Transaction Initiated | TxnID, SenderID, BeneficiaryAccount, BeneficiaryBank, Amount, Fee, Timestamp | Internal Audit |
| Authentication | TxnID, UserID, AuthMethod, Result, Timestamp | Security Policy |
| GL Block Money | TxnID, Dr_GL (3388200000), Cr_GL (1131060001), Amount+Fee, ValueDate | Finance Ops |
| GL Release to BIDV | TxnID, Dr_GL (1131060001), Cr_GL (1121060002), Principal, ValueDate | Finance Ops |
| GL Fee Revenue | TxnID, Dr_GL (1131060001), Cr_GL (5113200000), Net Fee, ValueDate | Finance Ops |
| GL Output VAT | TxnID, Dr_GL (1131060001), Cr_GL (3331110000), VAT Amount, ValueDate | Tax Compliance |
| BIDV Clearing | TxnID, BIDV_RefNo, Amount, ClearingStatus, Timestamp | Finance Ops / SBV |
| Transaction Completed | TxnID, FinalStatus, Timestamp | Compliance |

---

## E1. AMOUNT LIMIT MATRIX

| Limit Type | Scope | Value | Config Level | Notes |
|---|---|---|---|---|
| Minimum per Transaction | Per Txn | 1.000 VND | System | VR-01 |
| Maximum per Transaction | Per Txn | Không giới hạn | System | Per bảng hạn mức dịch vụ |
| Daily Limit | Per User (Bank Transfer) | 50.000.000 VND | Role | Tính chung nhóm |
| Monthly Limit | Per User (Bank Transfer) | 100.000.000 VND | Role | Tính chung nhóm |
| Group Daily Limit | P2P + Bank Transfer + Payment | 50.000.000 VND | System | ERR-LMT-001 khi vượt |
| Group Monthly Limit | P2P + Bank Transfer + Payment | 100.000.000 VND | System | ERR-LMT-002 khi vượt |
| Fee Threshold | Per Transaction | 20.000.000 VND | System | Dưới ngưỡng: miễn phí; trên ngưỡng: có phí |

---

## E2. FEE, TAX & DISTRIBUTION

### E2.1 Fee Structure

| Case | Điều Kiện | Fee | Charged To | Timing |
|---|---|---|---|---|
| Case A | Amount ≤ 20.000.000 VND | Miễn phí | N/A | N/A |
| Case B | Amount > 20.000.000 VND | Theo product config (VND hoặc %) | Customer (Sender) | T+0 tại thời điểm giao dịch |

### E2.2 Tax (Case B only)

| Tax Type | Rate | Applied On | Basis |
|---|---|---|---|
| Output VAT (GTGT) | 10% | Transaction Fee | Thông tư 219/2013/TT-BTC |

> Công thức tính:
> - Net Revenue = Fee / 1.1
> - Output VAT = Fee / 1.1 x 0.1
> - Tổng trừ ví Sender = Principal + Fee (bao gồm VAT)

### E2.3 Fee Distribution (Case B)

| Party | Share | GL Account | Notes |
|---|---|---|---|
| V-Smart Pay (Revenue) | Net fee sau VAT | 5113200000 Transaction Fee Revenue | Cr tại GL Step 3 |
| Tax Authority (Output VAT) | 10% của phí | 3331110000 Output VAT | Cr tại GL Step 4; nộp thuế định kỳ |

---

## E3. GL POSTING RULES

### E3.1 Bút Toán GL — Case A (Amount ≤ 20M, Không Phí)

| Step | Dr/Cr | GL Code | GL Name | Formula / Amount |
|---|---|---|---|---|
| **Step 1 — Block Money** | Dr | 3388200000 | Wallet — Individual Customer (Sender) | Principal |
| | Cr | 1131060001 | Suspense Bank | Principal |
| **Step 2 — Release to BIDV** | Dr | 1131060001 | Suspense Bank | Principal |
| | Cr | 1121060002 | Master Account — BIDV | Principal |
| **Core Banking (BIDV side)** | Dr | Master Account BIDV | — | Principal |
| | Cr | Deposits Account — Receiver | — | Principal |

### E3.2 Bút Toán GL — Case B (Amount > 20M, Có Phí + VAT)

| Step | Dr/Cr | GL Code | GL Name | Formula / Amount |
|---|---|---|---|---|
| **Step 1 — Block Money** | Dr | 3388200000 | Wallet — Individual Customer (Sender) | Principal + Fee |
| | Cr | 1131060001 | Suspense Bank | Principal + Fee |
| **Step 2 — Release Principal** | Dr | 1131060001 | Suspense Bank | Principal |
| | Cr | 1121060002 | Master Account — BIDV | Principal |
| **Step 3 — Recognize Fee Revenue** | Dr | 1131060001 | Suspense Bank | Fee / 1.1 |
| | Cr | 5113200000 | Transaction Fee Revenue | Fee / 1.1 |
| **Step 4 — Recognize Output VAT** | Dr | 1131060001 | Suspense Bank | Fee / 1.1 x 0.1 |
| | Cr | 3331110000 | Output VAT | Fee / 1.1 x 0.1 |
| **Core Banking (BIDV side)** | Dr | Master Account BIDV | — | Principal |
| | Cr | Deposits Account — Receiver | — | Principal |

> **Kiểm tra cân bằng Suspense sau Case B:**
> Suspense nhận vào = Principal + Fee
> Suspense giải phóng = Principal + (Fee / 1.1) + (Fee / 1.1 x 0.1) = Principal + Fee ✓

### E3.3 Reversal GL Rules

- Reversal đảo ngược toàn bộ bút toán theo thứ tự ngược.
- **Case A Reversal:** Dr 1121060002 (Master BIDV) → Cr 1131060001 (Suspense) → Dr 1131060001 → Cr 3388200000 (Sender).
- **Case B Reversal:** Đảo ngược tất cả 4 GL steps; hoàn trả đủ Principal + Fee về ví Sender.
- Reversal entry phải mang TxnID gốc làm reference.
- GL failure sau CBS confirm → manual intervention queue.

### E3.4 GL Posting Technical Rules

- Posting trigger sau khi CBS xác nhận debit ví Sender thành công.
- BIDV clearing ACK phải nhận được trước khi đánh dấu COMPLETED.
- Mỗi GL entry mang: TxnID, ValueDate, Amount, Currency (VND), PostingTimestamp, BeneficiaryBank (BIDV).
- Suspense Account (1131060001) phải về 0 sau khi hoàn tất tất cả GL steps.

---

## E4. SETTLEMENT

### E4.1 Settlement Method

| Type | Method | Timing | System |
|---|---|---|---|
| Wallet → BIDV | CBS → BIDV Clearing (interbank) | T+0 intraday | CBS + BIDV Clearing System |

> Tiền rời hệ thống ví qua Master Account BIDV. Clearing phụ thuộc vào cut-off time của BIDV.

### E4.2 Settlement Flow

```
Sender xác nhận giao dịch (PIN/Biometric)
    → System tạo TxnID → Trạng thái: PROCESSING
    → GL Step 1: Dr 3388200000 / Cr 1131060001 (Block Money)
    → CBS tạo lệnh chuyển tiền đến BIDV
    → GL Step 2: Dr 1131060001 / Cr 1121060002 (Release to BIDV)
    → [Case B only] GL Step 3 + 4: Fee Revenue + Output VAT
    → BIDV Clearing: ghi có tài khoản Receiver
    → BIDV ACK nhận được
    → Trạng thái → COMPLETED
    → Push Notification gửi Sender
```

### E4.3 Settlement SLA

| Event | SLA |
|---|---|
| CBS debit confirmation | T+0 real-time |
| BIDV clearing ACK | T+0 intraday (phụ thuộc BIDV cut-off) |
| GL posting hoàn tất | T+0 sau CBS confirm |
| Transaction COMPLETED | Sau BIDV ACK |

---

## E5. RECONCILIATION & EXCEPTION HANDLING

### E5.1 Reconciliation Reports

| Report Name | Purpose | Key Fields |
|---|---|---|
| Transaction Ledger Report | Tất cả giao dịch Wallet→BIDV trong kỳ | TxnID, SenderID, BeneficiaryAccount, Amount, Fee, Status, ValueDate |
| GL Posting Report | Bút toán Dr/Cr đã hạch toán | TxnID, Dr_GL, Cr_GL, Amount, PostingTimestamp |
| BIDV Settlement File | Xác nhận clearing từ BIDV | BIDV_RefNo, Amount, ClearingDate, Status |
| Suspense Account Report | Kiểm tra số dư tài khoản 1131060001 | GL Code, Balance, UnclaredItems |
| Fee & VAT Report | Kiểm tra doanh thu phí và VAT đầu ra | TxnID, FeeAmount, NetRevenue, OutputVAT, ValueDate |

### E5.2 Exception Scenarios

| Exception | Trigger | System Behaviour | Resolution |
|---|---|---|---|
| CBS Timeout (Block) | CBS không phản hồi > 30s | Status PENDING_SETTLEMENT | Recon job T+5min; auto-update COMPLETED/FAILED |
| BIDV No ACK | BIDV không xác nhận sau clearing | Status SETTLEMENT_PENDING; alert Finance Ops | Finance Ops tra cứu BIDV portal; update manual |
| GL Posting Failure | GL Module lỗi | Status SETTLED; GL GL_POSTING_PENDING | Retry 3x / 10 phút; escalate Finance Ops |
| Duplicate Transaction | Cùng Sender+BeneficiaryAccount+Amount trong 5 phút | Block giao dịch thứ 2 | Alert ERR-TXN-001 |
| Suspense Not Cleared | Suspense balance > 0 sau EOD | Flagged trong Suspense Report | Finance Ops điều tra; post manual journal |
| Amount Mismatch | System amount khác BIDV statement | Break trong recon report | Finance Ops điều tra; adjustment entry nếu cần |

### E5.3 Adjustment & Reversal Procedures

**Khi nào Reversal:**
- CBS đã debit ví Sender nhưng BIDV clearing thất bại.
- Sender khiếu nại chuyển nhầm tài khoản (trường hợp BIDV đồng ý hoàn tiền).
- GL posting lỗi không thể auto-recover sau 3 retries.

**Reversal Steps:**
1. Finance Ops xác định TxnID cần reversal.
2. Raise Adjustment Request: TxnID, lý do, GL entries gốc, BIDV confirmation (nếu có).
3. Finance Manager duyệt.
4. Post reversal journal đảo ngược tất cả GL steps theo thứ tự ngược.
5. Nếu Case B: hoàn trả đủ Principal + Fee về ví Sender.
6. Cập nhật status REVERSED tại back-office.
7. Thông báo Sender; phối hợp BIDV nếu tiền đã ghi có phía BIDV.

### E5.4 Reconciliation SLA

| Break Type | Detection | Auto-Resolution | Manual Escalation |
|---|---|---|---|
| CBS Timeout | T+5 min | T+30 min | T+2 giờ |
| BIDV No ACK | Post-cycle | N/A | T+4 giờ |
| GL Posting Failure | Real-time alert | T+30 min (3 retries) | T+2 giờ |
| Suspense Not Cleared | EOD | N/A | T+1 business day |
| Amount Mismatch | EOD | N/A | T+1 business day |

---

## F. ERROR MESSAGE TABLE

| Error Code | Mô Tả | Message (English) | Message (Vietnamese) |
|---|---|---|---|
| ERR-BAL-001 | Số dư không đủ | "Insufficient balance. Please top up your wallet." | "Số dư ví không đủ. Vui lòng nạp thêm tiền." |
| ERR-LMT-001 | Vượt hạn mức ngày | "Transaction exceeds your daily limit." | "Giao dịch vượt hạn mức trong ngày." |
| ERR-LMT-002 | Vượt hạn mức tháng | "Transaction exceeds your monthly limit." | "Giao dịch vượt hạn mức trong tháng." |
| ERR-LMT-003 | Vượt hạn mức nhóm | "Total transactions exceed group limit." | "Tổng giao dịch vượt hạn mức nhóm." |
| ERR-LMT-004 | Dưới mức tối thiểu | "Amount is below the minimum allowed." | "Số tiền thấp hơn mức tối thiểu cho phép." |
| ERR-TXN-001 | Giao dịch trùng lặp | "Duplicate transaction detected. Please verify." | "Phát hiện giao dịch trùng lặp. Vui lòng kiểm tra lại." |
| ERR-CBS-001 | CBS timeout | "Transaction is being processed. Please check status later." | "Giao dịch đang được xử lý. Vui lòng kiểm tra lại sau." |
| ERR-CBS-002 | CBS debit thất bại | "Unable to process debit. Please try again." | "Không thể thực hiện trừ tiền. Vui lòng thử lại." |
| ERR-PTN-002 | BIDV no ACK / clearing error | "Bank connection error. Please try again later." | "Lỗi kết nối ngân hàng BIDV. Vui lòng thử lại sau." |
| ERR-GL-001 | GL posting thất bại | "Transaction settled but accounting entry pending. Finance team notified." | "Giao dịch đã thanh toán nhưng hạch toán đang chờ xử lý." |
| ERR-ACC-001 | Ví Sender không hoạt động | "Your wallet account is inactive. Please contact support." | "Tài khoản ví không hoạt động. Vui lòng liên hệ hỗ trợ." |
| ERR-ACC-003 | Tài khoản BIDV không hợp lệ | "Recipient bank account not found or invalid." | "Số tài khoản ngân hàng không hợp lệ hoặc không tồn tại." |
| ERR-AUTH-001 | Phiên hết hạn | "Your session has expired. Please log in again." | "Phiên làm việc đã hết hạn. Vui lòng đăng nhập lại." |
| ERR-AUTH-003 | Xác thực thất bại nhiều lần | "Too many failed attempts. Account temporarily locked." | "Xác thực thất bại nhiều lần. Tài khoản tạm thời bị khoá." |

---

*End of BRD — UC-TXN-002 v1.0*