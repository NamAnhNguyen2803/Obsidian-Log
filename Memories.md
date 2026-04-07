---
type: memories
updated: 2026-04-07
---

# Memories — Kiến Thức Dài Hạn

> Claude cập nhật file này khi có quyết định / pattern mới được xác nhận.
> Đây là "RAM" dài hạn — chỉ lưu những gì sẽ còn đúng sau 1 tháng.

---

## Conventions & Quyết định Kỹ Thuật

### BRD Structure
- BRD output lưu tại `BRDs/{UC-ID}/BRD.md` trong vault này
- UC-ID format: `UC-{DOMAIN}-{MARKET}-{SEQ}` (vd: UC-ONB-VN-001, UC-TXN-001)
- Status: `🟡 Draft` / `🟢 Approved` / `🔴 Rejected`

### Pipeline BA
- Với sản phẩm triển khai: Contract → SOW → BRD SOW (chỉ ghi phần change/not-change)
- Mỗi BRD có `Overview.md` riêng: objectives, stakeholders, main channels, use case list, diagrams
- Skills tách theo nhóm chức năng (vd: eKYC, AML, biometric) — không gộp vào 1 skill lớn

### Digital Banking — Rules đã xác nhận
- AML threshold: single transaction hoặc batch item ≥ USD 10,000 → bắt buộc upload AML PDF (max 5 MB)
- Maker-Checker: Maker KHÔNG được approve transaction của chính mình
- 3 failed login → lock 5 phút | 6 total → lock vĩnh viễn
- Session timeout: 5 phút không hoạt động
- Ngôn ngữ app: English, Portuguese, Tetum (thị trường Timor-Leste)

### Onboarding — Notes từ review
- Sau nhập OTP → link device với phone number
- Sau nhập PIN → lưu authen + update user status sang `pending`
- Phải có luồng compliance (đang thiếu trong BRD hiện tại)
- Cần cover: user status, working state, link device, authentication, pocket

---

## Workflow Patterns

### Khi nhận UC mới
1. Đọc SOW/Contract nếu có
2. Xác định: channel nào, market nào, có customization gì so với base
3. Tạo `Overview.md` trước khi viết BRD chi tiết
4. Chạy skill tương ứng trong Skills Documentation

### Khi review BRD
- Dùng 8-lens review từ `brd-reviewer` skill
- Đặc biệt check: user status transitions, authentication flow, error handling paths
- Onboarding BRD: bắt buộc có compliance flow

---

## Cấu trúc Competencies (đang xây)

| Competency | File | Status |
|------------|------|--------|
| BA Digital Banking | `Competencies/BA-Digital-Banking.md` | 🟡 Seeding |
| QA Pipeline | `Competencies/QA-Pipeline.md` | 🟡 Seeding |
| AI Agent Design | `Competencies/AI-Agent-Design.md` | 🟡 Seeding |
| BRD Writing | `Competencies/BRD-Writing.md` | 🟡 Seeding |

---

## Skills Documentation — Map

| Cần làm | Dùng skill |
|---------|-----------|
| Viết BRD mới | `BRD/create-brd.skill` |
| BRD onboarding | `BRD/brd-onboarding.skill` |
| BRD transaction | `BRD/transaction-brd.skill` |
| Review BRD | `BRD/brd-reviewer.skill` |
| Gen test cases | `Test/brd-test-case-writer.md` |
| Toàn bộ QA pipeline | `Test/brd-qa-master.md` |
| Mockup từ BRD | `BRD/mockup-generator.skill` |
