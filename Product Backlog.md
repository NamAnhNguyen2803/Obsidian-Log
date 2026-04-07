---
type: backlog
updated: 2026-04-07
---

# Product Backlog — BA AI Agent Pipeline

> Nguồn: Work Notes 2026-04-07 + đánh giá complexity/priority.
> Cập nhật mỗi khi có item mới hoặc status thay đổi.

---

## Legend

| Priority | Complexity | Status |
|----------|------------|--------|
| 🔥 Cao   | 🔴 Cao    | 📋 Backlog |
| 🟡 TB    | 🟡 TB     | 🔨 In Progress |
| 🟢 Thấp  | 🟢 Thấp   | ✅ Done |
|          |            | ⏸ Blocked |

---

## Nhóm 1 — Nền tảng (làm trước, unblocks mọi thứ)

| # | Item | Priority | Complexity | Status | Notes |
|---|------|----------|------------|--------|-------|
| P-01 | **Audit luồng review skill** — kiểm tra gap giữa 2 phiên bản brd-reviewer, xác nhận luồng file đang đi đúng chưa | 🔥 Cao | 🟢 Thấp | ✅ Done | Xem `Work Notes/2026-04-07-review-audit.md` |
| P-02 | **Skill lập kế hoạch tham vọng** — skill giúp decompose mục tiêu lớn thành sprint, estimate, milestone | 🔥 Cao | 🟡 TB | 📋 Backlog | Cần thiết trước khi kick off O3 KR2 |
| P-03 | **Master Agent end-to-end** — orchestrator điều phối toàn bộ pipeline BRD → Review → Test → Spec | 🔥 Cao | 🔴 Cao | ✅ Done | `Test/ba-master-agent.md` — Phase 0–5, revision loop, self-eval, DoD |

---

## Nhóm 2 — Cải thiện pipeline hiện có

| # | Item | Priority | Complexity | Status | Notes |
|---|------|----------|------------|--------|-------|
| P-04 | **Token logging per session** — log số token đốt theo từng skill để tối ưu chi phí | 🟡 TB | 🟢 Thấp | 📋 Backlog | Có thể làm qua hook hoặc wrapper script |
| P-05 | **User guide + token counting** — bảng giải thích workflow, chi phí ước tính mỗi skill | 🟡 TB | 🟡 TB | 📋 Backlog | Hữu ích khi onboard người mới |
| P-06 | **Agent tự đánh giá trước handoff QA** — create agent chấm điểm output của mình trước khi bàn giao | 🟡 TB | 🟡 TB | ✅ Done | Đã tích hợp vào ba-master-agent Phase 2 (Self-evaluation checklist S1–S8) |
| P-07 | **BRD negotiation trước khi tạo** — create agent và review agent đàm phán tiêu chí thành công trước | 🟡 TB | 🔴 Cao | ✅ Done | Đã tích hợp vào ba-master-agent Phase 0 (Pre-negotiation + DoD.md) |

---

## Nhóm 3 — Research / Deprioritize

| # | Item | Priority | Complexity | Status | Notes |
|---|------|----------|------------|--------|-------|
| P-08 | **Reinforcement learning từ feedback** — AI tự log yêu cầu, update skill liên quan | 🟢 Thấp | 🔴 Cao | 📋 Backlog | Cần nghiên cứu thêm; không native trong Claude Code hiện tại |
| P-09 | **Browser extension ghi chú → sync Obsidian** | 🟢 Thấp | 🔴 Cao | 📋 Backlog | Dùng Readwise tạm thay thế; side project |
| P-10 | **Frontend design agent (Generator + Evaluator loop)** | 🟢 Thấp | 🔴 Cao | 📋 Backlog | Ngoài scope Digital Banking pipeline |

---

## Next Sprint (đề xuất)

1. ~~P-01 Audit review skill~~ ✅
2. ~~P-03 Master Agent end-to-end~~ ✅ (`Test/ba-master-agent.md`)
3. ~~P-06 Agent self-evaluation~~ ✅ (tích hợp vào Master Agent Phase 2)
4. ~~P-07 BRD negotiation~~ ✅ (tích hợp vào Master Agent Phase 0)
5. **P-02** — Viết skill lập kế hoạch (estimate: 1 session)
6. **P-04** — Token logging (estimate: 30 phút, hook-based)

---

## Liên kết

- North Star: [[North Star]]
- Work Note gốc: [[Work Notes/2026-04-07]]
- Review skill audit: [[Work Notes/2026-04-07-review-audit]]
