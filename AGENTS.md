# BMS Skills — Agent Guide

## 命令

```bash
# pre-commit（6 个任务：markdownlint + well-known list + plugin skills list + digest 检查 + name 一致性 + 格式检查）
pre-commit run --all-files

# 提交时自动触发钩子，也可手动指定单个任务
pre-commit run check-well-known-digest       # index.json digest 与 SKILL.md 匹配
pre-commit run check-skill-name-consistency  # frontmatter name/description 与 index.json 一致
pre-commit run check-plugin-skills-list      # marketplace.json skills 与技能目录一致
pre-commit run check-skill-md-format         # frontmatter 字段格式与 body 行数合规

# 编辑 SKILL.md 后同步更新 .well-known/agent-skills/index.json 中的 digest
sha256sum skills/bms/SKILL.md skills/bmson/SKILL.md skills/bms-table/SKILL.md

# 手动验证
markdownlint --config .markdownlint.toml skills/*.md
markdownlint --fix --config .markdownlint.toml skills/*.md
```

## 边界

| 层级 | 规则 |
|---|---|
| **Never** | 勿编辑 `origin/` 目录下的任何文件（原始 HTML 源，只读） |
| **Never** | 勿启用 `.markdownlint.toml` 中禁用的规则（注释已说明原因） |
| **Always** | 修改 `.md` 后通过 `pre-commit run markdownlint` 验证（pre-commit 中以 `--config .markdownlint.toml` 覆盖默认规则） |
| **Always** | 修改 `skills/*/SKILL.md` 后，同步更新 `.well-known/agent-skills/index.json` 中对应 `digest` 字段（由 `check-well-known-digest` hook 强制） |
| **Always** | 新增/移除技能目录时同步更新 `.well-known/agent-skills/index.json` 与 `.claude-plugin/marketplace.json` 的 `skills` 数组 |
| **Always** | 发布新版本（release/tag）时，更新 `README.md` 中安装命令的版本号引用（`#vX.Y.Z`） |
| **Note** | 通过 raw URL 使用 `npx skills add` 时，仓库根目录必须配置 `.well-known/agent-skills/index.json`，否则无法发现技能 |
| **Ask** | 需修改 `.markdownlint.toml` 配置时先确认 |

## 提交格式

Conventional Commits。title 英文，body 中文（可选）。

---

## 内容规则

- `skills/bms/memo/`、`skills/bms/ext/`、`skills/bms/bmse/`、`skills/bmson/spec/` 等子文档只修改格式问题，不修改文字、代码示例、数据内容；`SKILL.md` 索引文件允许结构编辑和措辞优化
- 文件名保持英文，与现有命名风格一致
- 直接源自特定参考来源的文档（如 memo/*.md、ext/*.md、bmse/*.md、spec/*.md）必须在开头标注 `> 来源：[标题](URL)` 行；间接参考/总览类文档不强制要求
- `skills/bms/memo/` / `skills/bms/ext/` 文档修改后需与 `origin/` 中对应 HTML 对照验证内容准确性；清理未引用的图片前需确认所有 markdown 引用仍能正确解析
