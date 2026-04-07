---
# Đây là frontmatter của file Index — Dataview query dashboard
type: index
title: BRD Registry
---

# BRD Registry — Tất Cả Use Cases

> Dashboard tự động — Dataview plugin đọc frontmatter từ tất cả files trong thư mục BRDs/

---

## 📋 Tất Cả BRDs

```dataview
TABLE uc_id, name, status, domain, market, updated
FROM "BRDs"
WHERE file.name = "BRD"
SORT updated DESC
```

---

## 🟡 Draft — Chưa Review

```dataview
LIST file.link + " — " + name
FROM "BRDs"
WHERE file.name = "BRD" AND status = "🟡 Draft"
```

---

## 🟢 Approved

```dataview
LIST file.link + " — " + name
FROM "BRDs"
WHERE file.name = "BRD" AND status = "🟢 Approved"
```

---

## 📊 Theo Domain

```dataview
TABLE rows.file.link as BRDs, count(rows) as "Số lượng"
FROM "BRDs"
WHERE file.name = "BRD"
GROUP BY domain
```

---

## 🌏 Theo Market

```dataview
TABLE rows.file.link as BRDs, count(rows) as "Số lượng"
FROM "BRDs"
WHERE file.name = "BRD"
GROUP BY market
```
