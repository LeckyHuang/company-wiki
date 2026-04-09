# Wiki Schema — 展厅方案知识库

本文件是整个 wiki 的核心规则，所有 LLM 工具均以此为准。

---

## 一、目录结构

```
sources/                    # 原始材料（只读，不修改）
  proposals/                # 己方历史方案（按序号_项目名命名）
  competitors/              # 友商方案（竞品情报）
  patents/                  # 发明专利文件
  certificates/             # 资质证书文件
  policies/                 # 行业政策文件

wiki/                       # LLM 生成和维护的知识库
  industries/               # 行业知识页
  solution-types/           # 方案类型知识页
  hall-types/               # 展厅类型知识页
  proposal-stages/          # 各阶段方案框架模板
  modules/                  # 功能模块库
  competitors/              # 竞品画像页
  credentials/              # 证书与专利
    patents/
    certificates/
    credentials-index.md    # 按适用场景聚合的索引
  clients/                  # 客户案例页（已脱敏）
  policies/                 # 政策依据页（含有效期）
  index.md                  # 内容目录（分类索引）
  log.md                    # 操作日志（只追加，不修改历史）

prompts/                    # 标准操作提示词模板
```

---

## 二、Wiki 页面格式规范

每个 wiki 页面必须包含以下元数据头：

```markdown
---
title: 页面标题
type: industry | solution-type | hall-type | proposal-stage | module | competitor | credential | client | policy
tags: [标签1, 标签2]
created: YYYY-MM-DD
updated: YYYY-MM-DD
status: active | outdated | archived
sources: [关联的 sources 文件名]
---
```

- `status: outdated` — 内容可能过时，需人工确认后再引用
- `status: archived` — 已确认过时，移入 `wiki/archive/`，不再用于生产
- 每次修改必须更新 `updated` 字段

---

## 三、核心业务分类体系

### 行业类型
- 通信/运营商（电信、移动、联通等）
- 工业/制造
- 金融
- 政府/政务
- 能源
- 其他

### 项目类型
- 新建
- 升级
- 运营
- 复合型（升级+运营、新建+运营等）

### 展厅类型
- 企业营销
- 品牌形象
- 企业文化

### 方案阶段
- 可研方案 — 论证期，偏"为什么做"
- 规划方案 — 方向性，偏宏观蓝图
- 投标方案 — 竞争性，格式标准化
- 应标方案 — 针对客户 RFP 精准对标
- 落地方案 — 执行向，含技术实施细节
- 友商方案 — 竞品情报，单独管理

### 方案内容组合
- 内容、运营、系统（可单独或组合）

### 客户层级
- 集团级
- 省级
- 地市级

---

## 四、三大操作规则

### 4.1 Ingest（摄入）

**触发时机**：有新文件放入 sources/ 任意子目录时

**执行步骤**：
1. 读取新文件，识别其类型（方案/友商方案/专利/证书/政策）
2. 提取以下核心信息：
   - 行业、项目类型、展厅类型、方案阶段、客户层级
   - 核心问题与解决思路
   - 方案整体框架（章节大纲）
   - 涉及的功能模块
   - 政策依据（标注名称和时效）
   - 结果（中标/未中标/未知）
3. 按提取内容，更新或新建以下 wiki 页：
   - 对应行业页（追加该案例的特征）
   - 对应方案阶段模板页（对比已有框架，提炼通用结构）
   - 涉及的功能模块页（补充该方案中的描述话术）
   - 客户案例页（脱敏处理后新建）
   - 若有政策引用 → 检查 wiki/policies/ 是否已有，有则更新，无则新建
4. 更新 wiki/index.md
5. 在 wiki/log.md 追加一条记录：
   ```
   [YYYY-MM-DD HH:MM] INGEST | sources/proposals/xxx.pdf | 更新页面: [列表]
   ```

**友商方案特殊处理**：
- 提取内容写入 `wiki/competitors/` 对应竞品页
- 重点提取：技术方案特点、价格信号、话术风格、薄弱点
- 不写入通用模板页，保持竞品情报独立

**证书/专利特殊处理**：
- 新建 `wiki/credentials/` 对应页面
- 必须记录有效期（证书）或授权日期（专利）
- 更新 `wiki/credentials/credentials-index.md`

### 4.2 Query（查询）

**触发时机**：需要为新方案生产提供素材时

**执行步骤**：
1. 明确查询意图（方案类型+行业+阶段+客户层级）
2. 检索相关 wiki 页：
   - 行业页 → 痛点和话术
   - 方案阶段模板页 → 框架参考
   - 功能模块页 → 模块描述
   - 客户案例页 → 类似案例
   - 竞品页 → 差异化定位
   - 政策页 → 政策依据（确认 status: active）
   - 证书索引 → 该类项目所需资质
3. 合成输出，每条内容注明来源 wiki 页
4. 若本次 query 产生新洞察或修正了已有内容，回写对应 wiki 页并更新 log.md

### 4.3 Lint（健康检查）

**触发时机**：定期（建议每月一次）或手动触发

**检查项**：
1. **过期政策**：wiki/policies/ 中有效期已过 → 标记 `status: outdated`
2. **过期证书**：wiki/credentials/certificates/ 中有效期 < 3 个月 → 在页面顶部加警告
3. **孤立页面**：wiki 页中无任何其他页面交叉引用 → 列出待处理
4. **矛盾内容**：同一模块在不同页面的描述存在明显矛盾 → 列出待人工确认
5. **空页面**：存在但内容为空或极少的页面 → 列出
6. **外链失效**：sources/links.md 中的外部链接 → 尝试访问，标记失效链接

**输出格式**：
```markdown
# Lint Report — YYYY-MM-DD
## 过期政策 (N条)
## 临期证书 (N条)
## 孤立页面 (N条)
## 内容矛盾 (N条)
## 空页面 (N条)
## 失效外链 (N条)
```

---

## 五、知识沉淀规则

- **中标方案**的框架优先级高于未中标方案，提取时标注
- **友商方案**的知识只流向 `wiki/competitors/`，不污染己方模板
- **客户信息**写入 wiki 前必须脱敏（用"某地市电信"代替具体公司名）
- **过时内容**不直接删除，先标记 `status: archived`，移入 `wiki/archive/`
- 每次 query 生成的有价值洞察必须回写 wiki（不允许只在对话中消耗）

---

## 六、方案生产工作流（供商务使用）

1. 告知 LLM：客户行业、项目类型、展厅类型、方案阶段、客户层级
2. LLM 执行 query → 输出：推荐框架 + 核心话术 + 适用模块 + 所需资质
3. 人工确认框架后，LLM 协助填充各章节内容
4. 方案完成后，将定稿文件放入 sources/proposals/
5. 执行 ingest，将本次方案的新知识回写 wiki
