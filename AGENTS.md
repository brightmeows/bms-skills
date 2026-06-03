# BMS Skills — Agent Guide

## 命令

```bash
# 检查 skills/ 下所有 markdown 文件
markdownlint --config .markdownlint.toml skills/*.md

# 自动修复可修复问题
markdownlint --fix --config .markdownlint.toml skills/*.md

# 编辑 SKILL.md 后同步更新 .well-known/agent-skills/index.json 中的 digest
# 先运行 sha256sum 获取新值，再更新 index.json 中的 "digest" 字段
sha256sum skills/bms/SKILL.md skills/bmson/SKILL.md
```

## 边界

| 层级 | 规则 |
|---|---|
| **Never** | 勿编辑 `origin/` 目录下的任何文件（原始 HTML 源，只读） |
| **Never** | 勿启用 `.markdownlint.toml` 中禁用的规则（注释已说明原因） |
| **Always** | 修改 .md 后运行 `markdownlint` 验证 |
| **Always** | 修改 `skills/bms/SKILL.md` 或 `skills/bmson/SKILL.md` 后，同步更新 `.well-known/agent-skills/index.json` 中对应 `digest` 字段 |
| **Always** | `.claude-plugin/plugin.json` 仅引用目录路径，内容变更无需修改；新增/移除技能目录时同步更新 |
| **Ask** | 需修改 `.markdownlint.toml` 配置时先确认 |

## 提交格式

Conventional Commits。title 英文，body 中文（可选）。

---

## 内容规则

- 只修改格式问题，不修改文字、代码示例、数据内容
- 文件名保持英文，与现有命名风格一致
- 图片引用路径为相对路径 `images/`
- `skills/bms/` 和 `skills/bmson/` 下的文档必须有来源 URL，标记在开头的 `> 来源：[标题](URL)` 行
