---
type: north-star
updated: 2026-04-07
---

# North Star — Định Hướng Dài Hạn

> Đây là la bàn cho mọi quyết định công việc. Claude đọc file này đầu mỗi phiên.
> **Sửa file này bất cứ khi nào hướng đi thay đổi.**

---

## Mục tiêu chính (2026)

**Trở thành BA AI Agent chuyên Digital Banking** — người thiết kế và vận hành pipeline BA tự động, biến yêu cầu nghiệp vụ thành BRD → Test → Spec chỉ với lệnh đơn giản.

---

## Vai trò hiện tại

- **Business Analyst** — chuyên Digital Banking (Mobile Banking, Internet Banking, Backoffice)
- **Nhiệm vụ chính**: Xây dựng BA AI Agent — tự động hóa pipeline từ requirement → BRD → Review → Test Cases → Playwright spec
- **Domain**: Digital Banking (Core T24, Onboarding eKYC, Giao dịch, AML, Maker-Checker, Corporate Banking)

---

## OKRs Hiện Tại

### O1 — Làm chủ pipeline BA tự động
- KR1: Pipeline BRD → Test → Spec chạy end-to-end không cần can thiệp thủ công
- KR2: Mỗi UC mới viết xong BRD trong < 2h với AI
- KR3: Coverage test cases ≥ 150 TC/UC với brd-test-case-writer

### O2 — Xây dựng domain knowledge base hoàn chỉnh
- KR1: Digital Banking knowledge base cover đủ 5 module: Onboarding, Transaction, Corporate, Backoffice, Security
- KR2: Mỗi skill có đủ business rules cho VN market (eKYC, AML, BL, CIC)
- KR3: Skills reusable được cho tối thiểu 2 thị trường

### O3 — Phát triển kỹ năng AI Agent
- KR1: Hiểu và vận hành được Claude Agent SDK
- KR2: Thiết kế được multi-agent workflow (BA Agent → QA Agent → Dev Agent)
- KR3: Có ít nhất 3 case study thực tế documented trong Brag Doc

---

## Nguyên tắc làm việc

1. **Context trước, code sau** — Luôn đọc domain knowledge trước khi viết BRD
2. **Backlink mọi thứ** — Mỗi work note liên kết competency tương ứng
3. **Done = documented** — Thành quả chưa ghi vào Brag Doc là chưa xong
4. **Skills > Tasks** — Ưu tiên xây dựng reusable skill hơn làm một lần

---

## Không làm (Anti-goals)

- Không xây dựng full-stack app — BA là vai trò, không phải developer . Hoặc là MVP cho dự án
- Không nhảy sang domain mới khi Digital Banking chưa complete
- Không ghi note mà không có action item hoặc backlink
