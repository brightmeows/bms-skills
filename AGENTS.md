# BMS Skills — Agent Guide

## Commands

```bash
# Check all memo markdown files
markdownlint --config .markdownlint.toml skills/bms/memo/*.md

# Auto-fix fixable issues
markdownlint --fix --config .markdownlint.toml skills/bms/memo/*.md
```

## Boundaries

| 层级 | 规则 |
|---|---|
| **Never** | 勿编辑 `origin/` 目录下的任何文件（原始 HTML 源，只读） |
| **Never** | 勿启用 `.markdownlint.toml` 中禁用的规则（注释已说明原因） |
| **Always** | 修改 .md 后运行 `markdownlint` 验证 |
| **Ask** | 需修改 `.markdownlint.toml` 配置时先确认 |

## Content Rules

- 只修改格式问题，不修改文字、代码示例、数据内容
- 文件名保持英文，与现有命名风格一致
- 图片引用路径为相对路径 `images/`
