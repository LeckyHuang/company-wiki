# 展厅方案知识库 (Company Wiki)

基于 LLM Wiki Pattern 构建的展厅方案知识管理系统。

## 快速开始

### 环境要求
任意支持文件读写的 LLM 工具均可使用：
- Claude Code（推荐）
- OpenCode
- Cursor / Windsurf
- 其他支持 AGENTS.md 的工具

### 首次使用（公司电脑）
```bash
git clone [仓库地址]
cd company-wiki
# 将方案文件放入 sources/proposals/
# 打开 LLM 工具，加载本项目
# 参考 prompts/ingest.md 执行第一次摄入
```

### 三个核心操作
| 操作 | 用途 | 提示词 |
|---|---|---|
| **Ingest** | 将新材料摄入 wiki | `prompts/ingest.md` |
| **Query** | 为新方案生产查询素材 | `prompts/query.md` |
| **Lint** | 健康检查（建议每月一次） | `prompts/lint.md` |

## 目录结构
```
sources/        # 原始材料（不进 git，本地存放）
wiki/           # LLM 生成的知识库（进 git）
prompts/        # 标准操作提示词
SCHEMA.md       # 核心规则（所有工具共用）
```

## 注意事项
- `sources/` 目录下的原始材料已在 `.gitignore` 中排除，不会上传到 GitHub
- `wiki/` 目录的内容会进入 git，是团队共享的知识资产
- 详细规则见 `SCHEMA.md`
