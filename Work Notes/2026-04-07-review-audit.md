---
type: work-note
date: 2026-04-07
topic: Audit luồng brd-reviewer skill
---

# Audit — Luồng Review Skill (2026-04-07)

> Câu hỏi gốc từ work note: "review skill có tạo ra file feedback không hay sửa trực tiếp luôn? luồng review đang đi như nào? đọc ở obsidian như nào?"

---

## Kết quả audit

### Có 2 phiên bản brd-reviewer đang tồn tại song song

| File | Vị trí | Format | Canonical? |
|------|--------|--------|-----------|
| `brd-reviewer.md` | `Test/` | Plain markdown, 8 lens, Pass/Fail gate | ✅ **Dùng cái này** |
| `SKILL.md` | `BRD/brd-reviewer/` | Zip-style, AC audit, compliance checklist, E2E coverage | 🟡 Phiên bản cũ hơn, chi tiết hơn nhưng output format khác |

**Vấn đề**: Hai phiên bản có output format khác nhau — một bên dùng scorecard 8-lens, bên kia dùng CRITICAL/MAJOR/MINOR issues table. Cần thống nhất hoặc merge.

---

### Luồng đang hoạt động (Test/brd-reviewer.md)

```
User: "Review BRD UC-XXX"
         ↓
Đọc BRD từ:
/Obsidian Vault/BRDs/{UC-ID}/BRD.md
         ↓
Chạy 8 lens phân tích
         ↓
Ghi Review.md vào:
/Obsidian Vault/BRDs/{UC-ID}/Review.md  ← đây là "feedback file"
         ↓
      PASS?
     /     \
   YES      NO
    ↓        ↓
Xác nhận   Liệt kê điểm cần sửa
BRD đã     → Chờ user sửa
lưu vault  → User nói "Sửa xong, review lại"
                ↓
           Đọc BRD mới + Review.md cũ
           Patch điểm fail + re-review
           Ghi đè Review.md (giữ history cuối)
```

### Trả lời câu hỏi gốc

| Câu hỏi | Trả lời |
|---------|---------|
| Có tạo file feedback không? | **Có** — `Review.md` trong cùng thư mục BRD |
| Hay sửa trực tiếp BRD? | **Không** — reviewer chỉ ghi Review.md, không chạm BRD |
| Đọc ở Obsidian như nào? | Mở `BRDs/{UC-ID}/Review.md` — file markdown chuẩn |
| Re-review đi qua đâu? | Ghi đè Review.md, giữ history ở cuối file (bảng Review History) |

---

## Gaps phát hiện

### Gap 1 — Hai phiên bản reviewer chưa thống nhất
- `Test/brd-reviewer.md`: canonical theo INDEX.md, dùng 8-lens + Pass/Fail gate
- `BRD/brd-reviewer/SKILL.md`: có thêm AC Audit, Compliance Checklist, E2E Flow Coverage, Integration Gap Analysis
- **Đề xuất**: Merge các lens từ BRD version vào Test version (bổ sung Lens 9: AC Testability, Lens 10: E2E Coverage)

### Gap 2 — Không có Master Agent orchestrate
- Hiện tại user phải tự trigger từng bước (create BRD → review → test case writer)
- Không có agent nào tự động pass result sang bước tiếp theo
- **Đề xuất**: Xem P-03 trong Product Backlog

### Gap 3 — Review.md không có backlink ngược lại BRD
- Review.md không tự động tạo link `[[BRD]]` → không hiển thị trong Obsidian graph
- **Đề xuất**: Thêm frontmatter `related: [[BRDs/{UC-ID}/BRD]]` vào output template

---

## Action items

- [ ] Merge 2 phiên bản reviewer → 1 canonical skill (estimate: 1 session)
- [ ] Thêm backlink vào Review.md template
- [ ] Cập nhật INDEX.md nếu merge xong
- [ ] Track trong Product Backlog (P-03 area)

---

*Backlink: [[Product Backlog]] | [[Work Notes/2026-04-07]]*
