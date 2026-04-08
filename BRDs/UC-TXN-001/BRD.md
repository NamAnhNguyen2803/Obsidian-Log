---
uc_id: UC-TXN-001
name: Chuyển Tiền Nội Bộ Ví — P2P (Wallet-to-Wallet)
version: 1.0
status: 🟡 Draft
domain: transaction
market: General
product: Digital Wallet (V-Smart Pay)
channel: Mobile Wallet App
actors:
  - Individual Customer (Sender)
  - Individual Customer (Receiver)
  - Core Banking System (CBS)
  - GL Posting Module
created: 2026-04-06
updated: 2026-04-06
tags:
  - brd
  - transaction
  - p2p
---

# BRD — UC-TXN-001: Giao Dịch Chuyển Tiền Nội Bộ Ví (P2P Internal Transfer)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-TXN-001 |
| **Use Case Name** | Chuyển Tiền Nội Bộ Ví — P2P (Wallet-to-Wallet) |
| **Version** | 1.0 |
| **Date** | 2026-04-06 |
| **Author** | Senior BA |
| **Status** | Draft |

---

## A. USE CASE OVERVIEW

| Field | Detail |
|---|---|
| **Use Case ID** | UC-TXN-001 |
| **Use Case Name** | Chuyển Tiền Nội Bộ Ví — P2P (Wallet-to-Wallet) |
| **Version** | 1.0 |
| **Description** | Khách hàng cá nhân thực hiện chuyển tiền từ ví điện tử của mình sang ví điện tử của một khách hàng cá nhân khác trong cùng hệ thống V-Smart Pay. Giao dịch xử lý nội bộ, không qua ngân hàng trung gian, không phát sinh phí. |
| **Primary Actor** | Khách hàng cá nhân (Sender) |
| **Secondary Actor(s)** | Khách hàng cá nhân (Receiver), Core Banking System (CBS), GL Posting Module |
| **Channel** | Mobile Wallet App |
| **Preconditions** | 1. Sender đã đăng nhập thành công và phiên còn hiệu lực. |
| | 2. Ví Sender ở trạng thái ACTIVE. |
| | 3. Ví Receiver ở trạng thái ACTIVE và tồn tại trong hệ thống. |
| | 4. Số dư ví Sender ≥ Số tiền chuyển. |
| | 5. Giao dịch không vượt hạn mức ngày/tháng/nhóm. |
| **Open Questions** | OQ-01: Xác thực giao dịch bằng PIN hay Biometric (FIDO)? |
| | OQ-02: Cần OTP cho giao dịch lớn (ví dụ > 5M VND)? |
| | OQ-03: Giới hạn số lượng giao dịch P2P/ngày có không? |
| **Validation Rules** | VR-01: Số tiền ≥ 1.000 VND. |
| | VR-02: Số tiền không giới hạn max/txn (per bảng hạn mức). |
| | VR-03: Hạn mức ngày: 50.000.000 VND (nhóm P2P + BankTransfer + Payment). |
| | VR-04: Hạn mức tháng: 100.000.000 VND (nhóm). |
| | VR-05: Receiver phone/wallet ID phải tồn tại và ACTIVE. |
| | VR-06: Sender không được chuyển cho chính mình. |
| **Notification** | Sender: Push notification + In-app confirmation "Chuyển tiền thành công". Receiver: Push notification "Bạn vừa nhận được [amount] VND từ [sender_name]". |
| **Expected Result** | Số dư ví Sender giảm đúng số tiền. Số dư ví Receiver tăng đúng số tiền. Giao dịch trạng thái COMPLETED. GL posting hoàn thành. |
| **Exception Cases** | Số dư không đủ, vượt hạn mức, ví không tồn tại/inactive, giao dịch trùng lặp, CBS timeout, GL lỗi. |

---

## B. USER JOURNEY — MAIN FLOW (DETAILED)

> **Trigger:** Sender chọn chức năng "Chuyển tiền" trên màn hình chính Wallet App.

| Step | Actor  | Action                                                                    | System Response                                                                    |
| ---- | ------ | ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| 1    | Sender | Mở Wallet App, chọn **"Chuyển tiền"**                                     | Hiển thị màn hình nhập thông tin chuyển tiền                                       |
| 2    | Sender | Nhập số điện thoại / Wallet ID của Receiver                               | Hệ thống tra cứu ví theo số điện thoại/ID                                          |
| 3    | System | Xác thực Receiver                                                         | Kiểm tra: ví tồn tại, trạng thái ACTIVE → Hiển thị tên Receiver để Sender xác nhận |
| 4    | Sender | Xác nhận đúng Receiver, nhập **số tiền** chuyển                           | Hiển thị số tiền, kiểm tra format (số dương, ≥ 1.000 VND)                          |
| 5    | System | Kiểm tra validation                                                       | VR-01 đến VR-06: số dư, hạn mức ngày/tháng/nhóm, không chuyển chính mình           |
| 6    | Sender | Nhập **nội dung chuyển tiền** (tuỳ chọn)                                  | Ghi nhận nội dung (≤ 200 ký tự)                                                    |
| 7    | Sender | Xác nhận giao dịch bằng **PIN / Biometric**                               | Xác thực danh tính Sender                                                          |
| 8    | System | Tạo Transaction ID (TxnID), ghi nhận trạng thái **PROCESSING**            | Log: TxnID, SenderID, ReceiverID, Amount, Timestamp                                |
| 9    | System | Gọi CBS: **Debit ví Sender** (Dr 3388200000 — Sender)                     | CBS trừ số dư ví Sender                                                            |
| 10   | System | CBS xác nhận debit thành công                                             | Trả về CBS Confirmation Code                                                       |
| 11   | System | Gọi CBS: **Credit ví Receiver** (Cr 3388200000 — Receiver)                | CBS cộng số dư ví Receiver                                                         |
| 12   | System | CBS xác nhận credit thành công                                            | Trả về CBS Confirmation Code                                                       |
| 13   | System | Trigger **GL Posting**: Dr 3388200000 (Sender) / Cr 3388200000 (Receiver) | GL Module ghi nhận bút toán kép                                                    |
| 14   | System | Cập nhật trạng thái giao dịch → **COMPLETED**                             | Lưu transaction record đầy đủ                                                      |
| 15   | System | Gửi **push notification** cho Sender và Receiver                          | Sender: thành công; Receiver: nhận tiền                                            |
| 16   | Sender | Xem màn hình kết quả giao dịch                                            | Hiển thị: TxnID, số tiền, tên Receiver, thời gian, số dư còn lại                   |

---

## C. ALTERNATIVE FLOWS

### ALT-01 | Receiver Không Tồn Tại / Inactive

> **Trigger:** Kết quả tra cứu tại Step 3 — ví không tìm thấy hoặc trạng thái INACTIVE
> **At Step:** 3

| Step | Actor | Action | System Response |
|---|---|---|---|
| 3.1 | System | Tra cứu ví thất bại | Hiển thị lỗi: "Số điện thoại/ví không tồn tại hoặc chưa kích hoạt." |
| 3.2 | Sender | Nhập lại thông tin Receiver | Quay lại Step 2 |

---

### ALT-02 | Số Dư Ví Không Đủ

> **Trigger:** Validation tại Step 5 — số dư < số tiền chuyển
> **At Step:** 5

| Step | Actor | Action | System Response |
|---|---|---|---|
| 5.1 | System | Kiểm tra số dư thất bại | Hiển thị lỗi ERR-BAL-001: "Số dư ví không đủ. Vui lòng nạp thêm tiền." |
| 5.2 | Sender | Chọn Nạp tiền hoặc Huỷ | Redirect sang luồng Cash In hoặc thoát |

---

### ALT-03 | Vượt Hạn Mức

> **Trigger:** Validation tại Step 5 — vượt hạn mức ngày, tháng, hoặc nhóm
> **At Step:** 5

| Step | Actor | Action | System Response |
|---|---|---|---|
| 5.1 | System | Kiểm tra hạn mức thất bại | Hiển thị lỗi: ERR-LMT-001 (ngày) / ERR-LMT-002 (tháng) / ERR-LMT-003 (nhóm) |
| 5.2 | Sender | Xem thông tin hạn mức còn lại, điều chỉnh số tiền hoặc huỷ | Màn hình hiển thị hạn mức đã dùng / còn lại |

---

### ALT-04 | Xác Thực PIN / Biometric Thất Bại

> **Trigger:** Step 7 — xác thực không thành công
> **At Step:** 7

| Step | Actor | Action | System Response |
|---|---|---|---|
| 7.1 | System | Xác thực thất bại lần 1–2 | Hiển thị "Xác thực không thành công. Còn [N] lần thử." |
| 7.2 | System | Xác thực thất bại lần 3 | Khoá giao dịch 5 phút; hiển thị: "Vui lòng thử lại sau 5 phút." |
| 7.3 | System | Tổng thất bại 6 lần | Khoá tài khoản vĩnh viễn; yêu cầu liên hệ hỗ trợ |

---

### ALT-05 | CBS Timeout (Debit hoặc Credit)

> **Trigger:** Step 9 hoặc Step 11 — CBS không phản hồi trong 30 giây
> **At Step:** 9 hoặc 11

| Step | Actor | Action | System Response |
|---|---|---|---|
| 9.1 | System | CBS timeout khi debit Sender | Trạng thái chuyển sang PENDING_SETTLEMENT; hiển thị ERR-CBS-001 |
| 9.2 | System | Recon job kích hoạt sau T+5 phút | Query CBS để xác nhận trạng thái debit |
| 9.3 | System | CBS xác nhận debit thành công | Giao dịch tiếp tục luồng bình thường từ Step 11 |
| 9.4 | System | CBS xác nhận debit thất bại | Trạng thái chuyển sang FAILED; số dư Sender không thay đổi |

---

### ALT-06 | Giao Dịch Trùng Lặp

> **Trigger:** Step 8 — phát hiện giao dịch trùng (cùng Sender + Receiver + Amount trong 5 phút)
> **At Step:** 8

| Step | Actor | Action | System Response |
|---|---|---|---|
| 8.1 | System | Phát hiện duplicate | Block giao dịch; hiển thị ERR-TXN-001: "Phát hiện giao dịch trùng lặp. Vui lòng kiểm tra lại." |
| 8.2 | Sender | Xác nhận hoặc huỷ | Nếu xác nhận cố ý: Sender chờ 5 phút rồi thử lại |

---

### ALT-07 | GL Posting Thất Bại

> **Trigger:** Step 13 — GL Module không phản hồi sau khi CBS xác nhận thành công
> **At Step:** 13

| Step | Actor | Action | System Response |
|---|---|---|---|
| 13.1 | System | GL posting thất bại | Trạng thái CBS: SETTLED; trạng thái GL: GL_POSTING_PENDING |
| 13.2 | System | Retry job: 3 lần x 10 phút | Ghi log mỗi lần retry |
| 13.3 | System | Sau 3 lần retry vẫn thất bại | Alert Finance Ops; giao dịch flagged trong manual intervention queue |
| 13.4 | Finance Ops | Xử lý manual GL entry | Post bút toán thủ công; cập nhật trạng thái |

---

### ALT-08 | Sender Tự Chuyển Cho Chính Mình

> **Trigger:** Step 5 — SenderID = ReceiverID
> **At Step:** 5

| Step | Actor | Action | System Response |
|---|---|---|---|
| 5.1 | System | Phát hiện Sender = Receiver | Hiển thị lỗi: "Không thể chuyển tiền cho chính bạn." |
| 5.2 | Sender | Nhập lại thông tin Receiver | Quay lại Step 2 |

---

## D. TECHNICAL & COMPLIANCE NOTES

### D.1 System Integrations

| System | Purpose | Trigger Step | Protocol |
|---|---|---|---|
| Core Banking System (CBS) | Debit/Credit số dư ví, xác nhận balance | Step 9, 11 | REST / ISO 8583 |
| GL Posting Module | Ghi nhận bút toán kép Dr/Cr | Step 13 | Internal service call |
| Push Notification Service | Thông báo Sender và Receiver | Step 15 | FCM / APNs |
| Audit Log Service | Ghi nhận mọi sự kiện giao dịch | Step 8, 14 | Async event |

### D.2 RBAC & Authorization

| Role | Permission | Enforcement Point |
|---|---|---|
| Individual Customer | INITIATE_P2P_TRANSFER | Session token + role check tại API Gateway |
| System | AUTO_GL_POSTING | Post CBS confirmation |
| Finance Ops | MANUAL_GL_ENTRY | Back-office admin console |

### D.3 Audit & Compliance Logging

| Event                 | Fields to Log                                           | Basis           |
| --------------------- | ------------------------------------------------------- | --------------- |
| Transaction Initiated | TxnID, SenderID, ReceiverID, Amount, Timestamp, Channel | Internal Audit  |
| Authentication        | TxnID, UserID, AuthMethod, Result, Timestamp            | Security Policy |
| CBS Debit/Credit      | TxnID, CBS_ConfirmCode, Amount, ValueDate               | Finance Ops     |
| GL Posting            | TxnID, Dr_GL, Cr_GL, Amount, PostingTimestamp           | Finance Ops     |
| Transaction Completed | TxnID, FinalStatus, Timestamp                           | Compliance      |

---

## E1. AMOUNT LIMIT MATRIX

| Limit Type              | Scope                         | Value           | Config Level | Notes                    |
| ----------------------- | ----------------------------- | --------------- | ------------ | ------------------------ |
| Minimum per Transaction | Per Txn                       | 1.000 VND       | System       | VR-01                    |
| Maximum per Transaction | Per Txn                       | Không giới hạn  | System       | Per bảng hạn mức dịch vụ |
| Daily Limit             | Per User (P2P)                | 50.000.000 VND  | Role         | Tính chung nhóm P2P      |
| Monthly Limit           | Per User (P2P)                | 100.000.000 VND | Role         | Tính chung nhóm P2P      |
| Group Daily Limit       | P2P + Bank Transfer + Payment | 50.000.000 VND  | System       | ERR-LMT-001 khi vượt     |
| Group Monthly Limit     | P2P + Bank Transfer + Payment | 100.000.000 VND | System       | ERR-LMT-002 khi vượt     |

**Error on limit breach:** ERR-LMT-001 (ngày) | ERR-LMT-002 (tháng) | ERR-LMT-003 (nhóm)

---

## E2. FEE, TAX & DISTRIBUTION

### E2.1 Fee Structure

| Fee Type | Trigger | Calculation | Charged To | Timing |
|---|---|---|---|---|
| P2P Transfer Fee | Mọi giao dịch P2P | **Miễn phí** | N/A | N/A |

### E2.2 Tax

Không áp dụng — giao dịch P2P nội bộ không phát sinh phí, không có VAT.

### E2.3 Fee Distribution

Không áp dụng.

---

## E3. GL POSTING RULES

### E3.1 Bút Toán GL — P2P Internal Transfer

> **Nguyên tắc:** Giao dịch nội bộ — tiền không rời hệ thống ví. Chỉ cần 1 bước posting.

| Dr/Cr  | GL Code    | GL Name                                              | Formula / Amount   |
| ------ | ---------- | ---------------------------------------------------- | ------------------ |
| **Dr** | 3388200000 | V-Smart Pay Wallet — Individual Customers (Sender)   | Principal (Amount) |
| **Cr** | 3388200000 | V-Smart Pay Wallet — Individual Customers (Receiver) | Principal (Amount) |

> **Ghi chú:** Cùng GL Code 3388200000 nhưng sub-account khác nhau (phân biệt bởi CustomerID/WalletID tại CBS level).

### E3.2 Reversal / Refund GL Rules

- Reversal đảo ngược toàn bộ bút toán: Dr 3388200000 (Receiver) → Cr 3388200000 (Sender).
- Reversal entry phải mang TxnID gốc làm reference.
- GL posting failure sau CBS confirm → trigger manual intervention queue, alert Finance Ops.

### E3.3 GL Posting Technical Rules

- Posting trigger sau khi CBS xác nhận cả debit lẫn credit thành công.
- Mỗi GL entry mang: TxnID, ValueDate, Amount, Currency (VND), PostingTimestamp, SenderWalletID, ReceiverWalletID.
- Giao dịch nội bộ: không cần Master Account trung gian (TCB/BIDV/Napas).

---

## E4. SETTLEMENT

### E4.1 Settlement Method

| Type | Method | Timing | System |
|---|---|---|---|
| P2P Internal | Book transfer — real-time nội bộ | T+0, immediate | Digital Wallet Ledger (CBS) |

> Giao dịch P2P nội bộ **không qua clearing bên ngoài**. Tiền chuyển tức thì trong cùng CBS ledger.

### E4.2 Settlement Flow

```
Sender xác nhận giao dịch (PIN/Biometric)
    → System tạo TxnID → Trạng thái: PROCESSING
    → CBS Debit Sender Wallet (Dr 3388200000)
    → CBS Credit Receiver Wallet (Cr 3388200000)
    → GL Posting Triggered (Dr/Cr 3388200000)
    → Trạng thái → COMPLETED
    → Push Notification gửi Sender & Receiver
```

---

## E5. RECONCILIATION & EXCEPTION HANDLING

### E5.1 Reconciliation Reports

| Report Name | Purpose | Key Fields |
|---|---|---|
| Transaction Ledger Report | Tất cả giao dịch P2P trong kỳ | TxnID, SenderID, ReceiverID, Amount, Status, ValueDate |
| GL Posting Report | Bút toán Dr/Cr đã hạch toán | TxnID, Dr_GL, Cr_GL, Amount, PostingTimestamp |
| Wallet Balance Snapshot | Số dư ví đầu kỳ / cuối kỳ | WalletID, OpenBalance, CloseBalance, NetMovement |

### E5.2 Exception Scenarios

| Exception | Trigger | System Behaviour | Resolution |
|---|---|---|---|
| CBS Timeout (Debit) | CBS không phản hồi > 30s tại Step 9 | Status PENDING_SETTLEMENT; recon job T+5min | Recon query CBS; auto-update COMPLETED/FAILED |
| CBS Timeout (Credit) | CBS không phản hồi > 30s tại Step 11 | Critical: Sender đã bị debit; Credit Receiver chưa xong | Recon job ưu tiên cao; nếu thất bại → auto-reversal Sender |
| GL Posting Failure | GL Module lỗi sau CBS confirm | Status SETTLED; GL GL_POSTING_PENDING | Retry 3x / 10 phút; escalate Finance Ops |
| Duplicate Transaction | Cùng Sender+Receiver+Amount trong 5 phút | Block giao dịch thứ 2 | Alert Sender ERR-TXN-001 |

### E5.3 Adjustment & Reversal Procedures

**Khi nào Reversal:**
- Giao dịch bị lỗi một nửa (debit Sender thành công, credit Receiver thất bại).
- Sender khiếu nại giao dịch sai (wrong Receiver).

**Reversal Steps:**
1. Finance Ops xác định TxnID cần reversal.
2. Raise Adjustment Request: TxnID, lý do, GL entries gốc.
3. Finance Manager duyệt.
4. Post reversal journal: Dr 3388200000 (Receiver) → Cr 3388200000 (Sender), reference TxnID gốc.
5. Cập nhật status REVERSED tại back-office.
6. Thông báo Sender và Receiver.

### E5.4 Reconciliation SLA

| Break Type | Detection | Auto-Resolution | Manual Escalation |
|---|---|---|---|
| CBS Timeout (Debit) | T+5 min | T+30 min | T+2 giờ |
| CBS Timeout (Credit) | T+5 min | T+30 min (auto-reversal) | T+1 giờ (priority) |
| GL Posting Failure | Real-time alert | T+30 min (3 retries) | T+2 giờ |
| Duplicate Detected | Real-time | Tự động block | N/A |

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
| ERR-TXN-002 | Tự chuyển cho chính mình | "You cannot transfer to your own wallet." | "Không thể chuyển tiền cho chính bạn." |
| ERR-CBS-001 | CBS timeout | "Transaction is being processed. Please check status later." | "Giao dịch đang được xử lý. Vui lòng kiểm tra lại sau." |
| ERR-CBS-002 | CBS debit thất bại | "Unable to process debit. Please try again." | "Không thể thực hiện trừ tiền. Vui lòng thử lại." |
| ERR-GL-001 | GL posting thất bại | "Transaction settled but accounting entry pending. Finance team notified." | "Giao dịch đã thanh toán nhưng hạch toán đang chờ xử lý." |
| ERR-ACC-001 | Ví không hoạt động | "Your wallet account is inactive. Please contact support." | "Tài khoản ví không hoạt động. Vui lòng liên hệ hỗ trợ." |
| ERR-ACC-002 | Ví Receiver không tồn tại | "Recipient wallet not found or inactive." | "Ví người nhận không tồn tại hoặc chưa kích hoạt." |
| ERR-AUTH-001 | Phiên hết hạn | "Your session has expired. Please log in again." | "Phiên làm việc đã hết hạn. Vui lòng đăng nhập lại." |
| ERR-AUTH-003 | Xác thực thất bại nhiều lần | "Too many failed attempts. Account temporarily locked." | "Xác thực thất bại nhiều lần. Tài khoản tạm thời bị khoá." |

---

*End of BRD — UC-TXN-001 v1.0*