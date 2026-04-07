# CLAUDE.md — Obsidian Mind Operating Manual

> Tài liệu này được tự động nạp vào mỗi phiên Claude Code khi làm việc trong vault này.
> Đây là "hệ điều hành" của bộ não ngoài — đọc kỹ trước khi bắt đầu.

---

## Vault này là gì

Đây là Obsidian Mind vault — bộ não ngoài cho Khánh (BA chuyên Digital Banking + AI Agent).
Vault chứa toàn bộ bối cảnh dài hạn: mục tiêu, quyết định, thành quả, domain knowledge.
Skills và công cụ nằm tại `/Users/nguyennamkhanh/Documents/Skills Documentation`.

---

## SESSION START — Làm ngay khi mở phiên mới

1. **Đọc** `North Star.md` — nắm mục tiêu và định hướng hiện tại
2. **Đọc** `Memories.md` — nắm quyết định, convention, pattern đã có
3. **Đọc** `Sessions/` file mới nhất (nếu có) — nắm context phiên trước
4. Sẵn sàng làm việc — không hỏi lại những gì đã có trong 3 file trên

## SESSION END — Làm trước khi kết thúc phiên

1. **Cập nhật** `Memories.md` nếu có quyết định / pattern mới được xác nhận
2. **Tạo** `Sessions/YYYY-MM-DD-{topic}.md` ghi tóm tắt phiên (xem template)
3. Nếu có thành quả đáng ghi nhớ → thêm vào `Brag Doc.md`
4. Nếu có công việc liên quan competency → cập nhật backlink trong `Competencies/`

---

## Cấu trúc Vault

| Thư mục / File | Vai trò |
|----------------|---------|
| `North Star.md` | Mục tiêu, định hướng, OKR cá nhân |
| `Memories.md` | Kiến thức dài hạn Claude ghi nhớ qua các phiên |
| `Brag Doc.md` | Nhật ký thành tựu — backlink tới Work Notes |
| `Work Notes/` | Ghi chú công việc hằng ngày |
| `Competencies/` | Một file/kỹ năng — backlinks = bằng chứng review |
| `Sessions/` | Log mỗi phiên làm việc |
| `BRDs/` | Output BRD (viết bởi Skills) |
| `Domain Knowledge/` | Kiến thức domain (Digital Banking, v.v.) |
| `Index/` | Registry, index tổng |
| `_templates/` | Templates cho note mới |

---

## Output Rules

| Output type | Lưu vào |
|-------------|---------|
| BRD mới | `BRDs/{UC-ID}/BRD.md` |
| BRD Review | `BRDs/{UC-ID}/Review.md` |
| Test Cases | `BRDs/{UC-ID}/TestCases/TC-{N}.md` |
| Work Note | `Work Notes/YYYY-MM-DD-{slug}.md` |
| Session Log | `Sessions/YYYY-MM-DD-{topic}.md` |

---

## Quy tắc ghi nhớ

- **Memories.md** = facts dài hạn (quyết định, convention) — KHÔNG lưu task tạm thời
- **Sessions/** = context ngắn hạn từng phiên
- **Brag Doc.md** = thành quả có thể chứng minh (liên kết work note)
- **Competencies/** = kỹ năng + bằng chứng (backlinks tự xây trong quá trình làm)

---

## Liên kết với Skills Documentation

Skills (BRD writing, QA pipeline, mockup) nằm tại:
`/Users/nguyennamkhanh/Documents/Skills Documentation`

Vault này = **context** | Skills Documentation = **công cụ**
