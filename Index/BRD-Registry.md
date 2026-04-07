---
type: index
title: BRD Registry
---

# BRD Registry — Tất Cả Use Cases

> **Hướng dẫn:** Mở file này trong Obsidian → dùng **Bases view** (icon database trên toolbar) để query toàn bộ BRDs theo frontmatter.
> Hoặc cài plugin **Dataview** từ Community Plugins để dùng các query bên dưới.

---

## Bases Query (Native — Không cần plugin)

Tạo một Base mới với source = folder **BRDs/** để xem dạng bảng/lọc theo:
-  — 🟡 Draft / 🟢 Approved / 🔴 Rejected
-  — onboarding / transaction / ...
-  — VN / General / ...
-  — sắp xếp theo ngày

---

## Danh Sách BRDs (Manual Index)

| UC ID | Tên | Domain | Market | Status | Updated |
|-------|-----|--------|--------|--------|---------|
| [[BRDs/UC-ONB-VN-001/BRD\|UC-ONB-VN-001]] | Individual Customer eKYC Onboarding (Vietnam) | onboarding | VN | 🟡 Draft | 2026-04-06 |
| [[BRDs/UC-TXN-001/BRD\|UC-TXN-001]] | Chuyển Tiền Nội Bộ Ví — P2P | transaction | General | 🟡 Draft | 2026-04-06 |
| [[BRDs/UC-TXN-002/BRD\|UC-TXN-002]] | Chuyển Tiền Ví Ra Ngân Hàng BIDV | transaction | VN | 🟡 Draft | 2026-04-07 |

---

## Dataview Queries (cần cài Dataview plugin)

### Tất Cả BRDs

```dataview
TABLE uc_id, name, status, domain, market, updated
FROM "BRDs"
WHERE file.name = "BRD"
SORT updated DESC
```

---

### 🟡 Draft — Chưa Review

```dataview
LIST file.link + " — " + name
FROM "BRDs"
WHERE file.name = "BRD" AND status = "🟡 Draft"
```

---

### 🟢 Approved

```dataview
LIST file.link + " — " + name
FROM "BRDs"
WHERE file.name = "BRD" AND status = "🟢 Approved"
```

---

### Theo Domain

```dataview
TABLE rows.file.link as BRDs, count(rows) as "Số lượng"
FROM "BRDs"
WHERE file.name = "BRD"
GROUP BY domain
```

---

### Theo Market

```dataview
TABLE rows.file.link as BRDs, count(rows) as "Số lượng"
FROM "BRDs"
WHERE file.name = "BRD"
GROUP BY market
```
