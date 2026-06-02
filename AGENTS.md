# BMS Skills — Agent Guide

## Commands

```bash
# Check all memo markdown files
markdownlint --config skills/bms/memo/.markdownlint.json skills/bms/memo/*.md

# Auto-fix fixable issues
markdownlint --fix --config skills/bms/memo/.markdownlint.json skills/bms/memo/*.md
```

## Project Structure

```
origin/                          # 原始日文 HTML 源文件 — 只读，勿编辑
skills/bms/memo/                 # 中文翻译后的 markdown 文档 (14 篇)
  .markdownlint.json             # markdownlint 配置
  01-overview.md ... 14-channel-mapping.md
  images/                        # 图片资源
```

## Markdown Formatting

遵照 `skills/bms/memo/.markdownlint.json` 中配置的规则：

- 行宽 120 字符（表格行除外）
- 代码块使用 fenced 风格 ` ``` `
- 表格内必要的 `<br>`/`<code>`/`<pre>` 允许使用
- 裸 URL 用 `<>` 包裹

修改 .md 文件后运行 lint 检查确保无新增错误。

## Boundaries

| 层级 | 规则 |
|---|---|
| **Never** | 勿编辑 `origin/` 目录下的任何文件（原始 HTML 源，只读） |
| **Never** | 勿修改 `.markdownlint.json` 中禁用的规则（MD025/055/056/060 为技术文档特例） |
| **Always** | 修改 .md 后运行 `markdownlint` 验证 |
| **Ask** | 需修改 `.markdownlint.json` 配置时先确认 |

## Content Rules

- 只修改格式问题，不修改文字、代码示例、数据内容
- 文件名保持英文，与现有命名风格一致
- 图片引用路径为相对路径 `images/`
