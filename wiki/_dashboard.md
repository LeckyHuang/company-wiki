---
title: 知识库仪表盘
type: dashboard
created: 2025-08-01
updated: 2025-08-01
---

# 展厅方案知识库 · 仪表盘

> 需要 Obsidian **Dataview** 插件（社区插件搜索安装）。
> 所有数据实时从 wiki 页面的 frontmatter 读取，无需手动维护。

---

## 页面总览

```dataview
TABLE rows.file.link AS "页面", rows.status AS "状态", rows.updated AS "最近更新"
FROM "wiki"
WHERE type != "dashboard"
GROUP BY type AS "类型"
SORT type ASC
```

---

## ⚠️ 需要关注

### 已过期 / 待确认页面

```dataview
TABLE file.link AS "页面", type AS "类型", updated AS "最近更新"
FROM "wiki"
WHERE status = "outdated"
SORT updated ASC
```

### 即将过期的证书（有效期字段 `expires`）

```dataview
TABLE file.link AS "证书", expires AS "到期日"
FROM "wiki/credentials/certificates"
WHERE expires != null
SORT expires ASC
```

---

## 行业知识页

```dataview
TABLE file.link AS "页面", tags AS "标签", updated AS "最近更新", status AS "状态"
FROM "wiki/industries"
SORT updated DESC
```

---

## 方案阶段模板

```dataview
TABLE file.link AS "模板", tags AS "覆盖类型", updated AS "最近更新"
FROM "wiki/proposal-stages"
SORT file.name ASC
```

---

## 功能模块库

```dataview
TABLE file.link AS "模块", tags AS "适用场景", updated AS "最近更新"
FROM "wiki/modules"
SORT updated DESC
```

---

## 客户案例

```dataview
TABLE file.link AS "案例（已脱敏）", tags AS "行业/阶段", updated AS "录入日期"
FROM "wiki/clients"
SORT updated DESC
```

---

## 竞品画像

```dataview
TABLE file.link AS "竞品", updated AS "情报更新日"
FROM "wiki/competitors"
SORT updated DESC
```

---

## 政策依据

```dataview
TABLE file.link AS "政策", status AS "状态", tags AS "适用行业", updated AS "最近更新"
FROM "wiki/policies"
SORT status ASC, updated DESC
```

---

## 资质 & 专利

```dataview
TABLE file.link AS "资质/专利", type AS "类型", updated AS "录入日期"
FROM "wiki/credentials"
WHERE type != "dashboard"
SORT type ASC
```

---

## Canvas 验证记录

```dataview
TABLE file.link AS "Canvas 文件", file.ctime AS "生成时间"
FROM "wiki/canvases"
SORT file.ctime DESC
```

---

## 最近操作日志

> 详细日志见 [[wiki/log]]

```dataview
TABLE file.mtime AS "最近修改时间", file.link AS "页面"
FROM "wiki"
WHERE type != "dashboard"
SORT file.mtime DESC
LIMIT 10
```
