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

### Never

- 勿编辑 `origin/` 目录下的任何文件（原始 HTML 源，只读）
- 勿启用 `.markdownlint.toml` 中禁用的规则（注释已说明原因）

### Always

- 修改 `.md` 后通过 `pre-commit run markdownlint` 验证（pre-commit 中用 `--config .markdownlint.toml`）
- 修改 `skills/*/SKILL.md` 后，同步更新 `.well-known/agent-skills/index.json` 的 digest（`check-well-known-digest` hook 强制）
- 新增/移除技能目录时同步更新 `.well-known/agent-skills/index.json` 与 `.claude-plugin/marketplace.json` 的 `skills`
- 发布新版本（release/tag）时，更新 `README.md` 中安装命令的版本号引用（`#vX.Y.Z`）

### Ask

- 修改 `.markdownlint.toml` 配置前先确认

### Note

- 通过 raw URL 使用 `npx skills add` 时，仓库根目录必须配置 `.well-known/agent-skills/index.json`，否则无法发现技能

## 提交格式

Conventional Commits。title 英文，body 中文（可选）。

---

## 维护指南

按「起步→观察→补充→精简→重复」的增量迭代维护本文件：

- 代理反复忽略某条规则 → 补充到对应边界节
- 代理已能稳定遵循某条规则 → 从边界节移除（已内化）
- 规则可被 hook 或 lint 强制 → 迁移到 `.pre-commit-config.yaml`，指向工具配置
- 边界节膨胀超过 10 条 → 审计精简，工具可强制的移出
- 发布新版本后 → 确认 `README.md` 安装命令 tag 已更新
- 内容规则引用的目录/路径发生变化 → 同步更新

## 内容规则

| 文档类型 | 编辑限制 | 来源标注 | 验证要求 |
|---------|---------|---------|---------|
| `memo/`、`ext/`、`bmse/`、`spec/` 子文档 | 仅格式，不改文字/代码/数据 | 直接来源需 `> 来源：[标题](URL)` | memo/ext 修改后对照 `origin/` HTML 验证 |
| `SKILL.md` 索引文件 | 允许结构编辑和措辞优化 | — | — |
| 新文件名 | 英文，与现有风格一致 | — | — |
| 引用图片 | — | — | 清理前确认 markdown 引用仍可解析 |
