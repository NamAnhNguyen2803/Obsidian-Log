# Hướng Dẫn Sử Dụng Obsidian Mind

> Đọc một lần, làm theo mãi mãi.

---

## Obsidian Mind là gì?

Một hệ thống biến Obsidian vault thành "bộ não ngoài" cho Claude Code. Mỗi lần chạy `claude` trong vault này, Claude tự động biết:
- Bạn là ai, mục tiêu gì
- Quyết định nào đã được đưa ra
- Đang làm dự án nào, với convention gì

Không cần giải thích lại. Không lãng phí lượt chat.

---

## Cấu trúc cốt lõi

```
Obsidian Vault/
├── CLAUDE.md          ← "Hệ điều hành" — Claude đọc đầu mỗi phiên
├── North Star.md      ← Mục tiêu dài hạn của bạn
├── Memories.md        ← Kiến thức Claude ghi nhớ qua các phiên
├── Brag Doc.md        ← Nhật ký thành tựu
├── Work Notes/        ← Ghi chú công việc hằng ngày
├── Competencies/      ← Bản đồ kỹ năng + bằng chứng tự động
├── Sessions/          ← Log mỗi phiên làm việc
├── BRDs/              ← Output BRD
├── Domain Knowledge/  ← Kiến thức domain
└── _templates/        ← Templates nhanh
```

---

## Cách dùng hằng ngày

### Mở phiên làm việc

```bash
cd "/Users/nguyennamkhanh/Documents/Obsidian Vault"
claude
```

Claude sẽ tự đọc `North Star.md` + `Memories.md` theo protocol trong `CLAUDE.md`.

**Câu đầu tiên bạn nói:**
> "Đọc North Star và Memories, rồi cho tôi biết context hiện tại."

Claude sẽ tóm tắt lại những gì nó đã nắm — bạn confirm hoặc sửa, rồi bắt đầu làm việc.

---

### Tạo Work Note mới

Dùng template `_templates/work-note.md`. Tên file: `Work Notes/YYYY-MM-DD-{slug}.md`.

**Quan trọng**: Cuối mỗi work note, link tới competency liên quan:
```
[[Competencies/BA-Digital-Banking]]
[[Competencies/QA-Pipeline]]
```

Obsidian sẽ tự gom backlinks → bằng chứng tự động cho performance review.

---

### Kết thúc phiên

Yêu cầu Claude:
> "Kết thúc phiên. Tạo session log và cập nhật Memories nếu cần."

Claude sẽ:
1. Tạo `Sessions/YYYY-MM-DD-{topic}.md`
2. Cập nhật `Memories.md` nếu có quyết định mới
3. Gợi ý Brag Doc entry nếu có thành quả

---

### Ghi Brag Doc

Mỗi khi làm xong việc gì đáng kể:
> "Thêm vào Brag Doc: tôi vừa viết xong BRD cho UC-TXN-003, cover 8 business rules AML và 3 market variants."

Claude sẽ format đúng chuẩn và link tới competency.

---

### Dùng Skills (BRD, QA, Mockup)

Skills nằm tại `Skills Documentation`, không phải vault này.

```bash
# Chuyển sang Skills Documentation để dùng skill
cd "/Users/nguyennamkhanh/Documents/Skills Documentation"
claude
```

Hoặc nói với Claude trong vault:
> "Viết BRD cho UC-TXN-004 — dùng transaction BRD skill."

Claude biết tìm skill ở đâu nhờ `CLAUDE.md`.

---

## Workflow tuần hoàn

```
Sáng: Mở vault → Claude đọc context → Bắt đầu làm
  ↓
Làm việc: Tạo Work Notes → Link Competencies
  ↓
Chiều: Kết thúc phiên → Session Log → Update Memories
  ↓
Thành quả: Thêm Brag Doc entry
  ↓
Review hiệu suất: Mở Competency → Xem Backlinks → Bằng chứng có sẵn
```

---

## Cập nhật North Star

Khi mục tiêu thay đổi, sửa `North Star.md` trực tiếp hoặc:
> "Cập nhật North Star: tôi đang focus vào X thay vì Y."

---

## Competencies — Cách đọc khi review

1. Mở `Competencies/BA-Digital-Banking.md` (hoặc bất kỳ competency nào)
2. Trong Obsidian: click **Backlinks** (icon trên thanh sidebar phải)
3. Tất cả Work Notes và BRDs đã link tới file này xuất hiện ở đây
4. Đó chính là bằng chứng — không cần gom tay

---

## Files quan trọng cần sửa ngay

| File | Việc cần làm |
|------|-------------|
| `North Star.md` | Điền mục tiêu thực tế của bạn vào phần OKRs |
| `Memories.md` | Thêm conventions / decisions của dự án thực |
| `Competencies/*.md` | Check [x] những skills đã thực sự có |

---

## Tips

- **Đừng viết note mà không có backlink** — backlink = bằng chứng tự động
- **Session log ngắn thôi** — 5-10 dòng là đủ, quan trọng là làm đều
- **Memories.md chỉ ghi fact dài hạn** — không ghi task "sẽ làm", không ghi deadline
- **North Star.md là la bàn** — sửa ít nhất 1 lần/tháng để phản ánh thực tế
