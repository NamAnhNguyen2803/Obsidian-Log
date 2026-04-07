---
type: competency
name: QA Pipeline
framework: Test Automation
updated: 2026-04-07
---

# QA Pipeline

> Năng lực thiết kế và vận hành pipeline QA tự động: BRD → Test Cases → Playwright Spec → Auto Test.

---

## Định nghĩa năng lực

Khả năng chuyển BRD thành test coverage hoàn chỉnh — từ review nghiệp vụ, sinh test cases, viết Playwright spec, đến chạy auto test và báo cáo kết quả.

---

## Pipeline hiện tại

```
BRD → brd-reviewer → brd-test-case-writer → brd-spec-writer → brd-auto-tester → TEST_MATRIX
```

## Sub-skills

### BRD Review (8 lenses)
- [ ] Completeness — đủ flows chưa
- [ ] Business rules — rules rõ ràng, không mơ hồ
- [ ] Edge cases — có cover not-happy-path
- [ ] Security — authentication, authorization
- [ ] Integration — T24, external APIs
- [ ] UI/UX — screens, labels, actions
- [ ] Error handling — mỗi error có message rõ
- [ ] Compliance — AML, regulatory

### Test Case Writing
- [x] Cấu trúc TC: ID, title, precondition, steps, expected result
- [x] Coverage target: ≥ 150 TC/UC
- [ ] Equivalence partitioning cho input fields
- [ ] Boundary value analysis

### Playwright Spec
- [ ] Page Object Model setup
- [ ] Test environment config
- [ ] CI/CD integration

### Test Matrix
- [x] Schema: Module | File | Tổng TC | Có code | Chạy | Pass/Fail stats
- [ ] Automated status update

---

## Bằng chứng (Backlinks tự động trong Obsidian)
