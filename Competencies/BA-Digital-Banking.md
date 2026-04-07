---
type: competency
name: BA Digital Banking
framework: Digital Banking Domain
updated: 2026-04-07
---

# BA Digital Banking

> Năng lực phân tích nghiệp vụ trong lĩnh vực Digital Banking.
> **Cách dùng cho review**: Xem phần Backlinks (Obsidian) để thấy toàn bộ bằng chứng tự động gom lại.

---

## Định nghĩa năng lực

Khả năng phân tích, mô tả và tài liệu hóa nghiệp vụ ngân hàng số — bao gồm Mobile Banking, Internet Banking (Corporate/Individual), và Backoffice — với đủ độ sâu để Dev + QA có thể implement mà không cần hỏi lại.

---

## Các sub-skills

### Core Banking Integration (T24)
- [ ] Hiểu CIF (Customer Information File) structure
- [ ] Nắm account types và GL posting flow
- [ ] Biết khi nào cần call T24 API vs xử lý local

### Onboarding & eKYC
- [x] eKYC flow: OCR → face match → liveness check
- [x] User status machine: pending → active → blocked
- [x] Device linking: OTP → link phone → link device
- [ ] Compliance flow (đang build)
- [ ] Market-specific: VN (phone, NID, biometric, số đẹp)

### Transaction & Payments
- [x] Internal transfer (P2P, ví nội bộ)
- [x] External transfer (ra ngân hàng ngoài — BIDV case)
- [x] AML rule: ≥ USD 10,000 → upload PDF
- [ ] Batch payment
- [ ] Scheduled payment

### Maker-Checker (Four-Eyes)
- [x] Maker creates, Checker approves — Maker không approve own transaction
- [x] Corporate sizes: Micro/Small vs SME vs Enterprise
- [x] Approval policy types: Simple, Sequential AND, Parallel AND, OR Logic
- [ ] Approval matrix configuration (Backoffice)

### Security & Authentication
- [x] FIDO biometrics, OTP for new device
- [x] Lock rules: 3 failed → 5 min | 6 total → permanent
- [x] Session timeout: 5 phút
- [ ] FIDO device management (add/remove)

---

## Bằng chứng (Backlinks tự động trong Obsidian)

> Mọi Work Note và BRD có liên kết tới file này sẽ xuất hiện ở đây trong Obsidian Backlinks panel.
> Không cần gom thủ công — cứ làm việc và link là xong.

### BRDs đã contribute
- [[BRDs/UC-ONB-VN-001/BRD|UC-ONB-VN-001]] — Individual eKYC Onboarding (VN)
- [[BRDs/UC-TXN-001/BRD|UC-TXN-001]] — P2P Internal Transfer
- [[BRDs/UC-TXN-002/BRD|UC-TXN-002]] — Transfer ra BIDV
