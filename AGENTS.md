# BMS Skills — Agent Guide

## Commands

```bash
# Check all memo markdown files
markdownlint --config skills/bms/memo/.markdownlint.jsonc skills/bms/memo/*.md

# Auto-fix fixable issues
markdownlint --fix --config skills/bms/memo/.markdownlint.jsonc skills/bms/memo/*.md
```

## Project Structure

```
origin/                          # 原始日文 HTML 源文件 — 只读，勿编辑
skills/bms/memo/                 # 中文翻译后的 markdown 文档 (14 篇)
  .markdownlint.jsonc            # markdownlint 配置（JSONC 格式，含注释）
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

## Disabled Rules

以下规则被禁用，均有技术文档特例原因，**不可重新启用**：

| 规则 | 原因 |
|---|---|
| **MD025** (multiple H1) | `#WAV01` / `#PLAYER` / `#BPM` 等是 **BMS 格式语法**，在代码块/表格中出现时被误判为标题。非 markdown 错误。 |
| **MD046** (code-block-style) | 表格单元格中存在 `<pre>` 包裹的代码片段（BMS 示例），无法统一为 fenced 或 indented 风格。 |
| **MD055** (table-pipe-style) | 表格内嵌入代码块 `<pre>` 时，管道符风格无法保持一致。 |
| **MD056** (table-column-count) | 同上，表格内多行 `<pre>` 内容无法拆分为标准表格列。 |
| **MD060** (table-column-style) | CJK 文本与代码混排的表格中，列宽自动对齐产生大量误报。 |

## Boundaries

| 层级 | 规则 |
|---|---|
| **Never** | 勿编辑 `origin/` 目录下的任何文件（原始 HTML 源，只读） |
| **Never** | 勿启用 `.markdownlint.json` 中禁用的规则（见下方禁用说明） |
| **Always** | 修改 .md 后运行 `markdownlint` 验证 |
| **Ask** | 需修改 `.markdownlint.json` 配置时先确认 |

## Content Rules

- 只修改格式问题，不修改文字、代码示例、数据内容
- 文件名保持英文，与现有命名风格一致
- 图片引用路径为相对路径 `images/`
