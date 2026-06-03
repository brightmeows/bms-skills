# BMS Skills

> 本仓库既可作为 Agent Skill 供 AI 编码助手加载使用，也可作为参考材料帮助理解 BMS 及 BMSON 谱面格式的实现细节。

为 AI 编码助手（OpenCode 等）提供的 BMS（Be-Music Script）及 BMSON 格式知识技能集合。

## 技能

| 技能 | 说明 |
|---|---|
| [bms](skills/bms/SKILL.md) | 创建/编辑 BMS 谱面、排查格式问题、查通道映射与头部命令、引擎差异（beatoraja vs LR2）、BMSE 兼容性、开发 BMS 工具 |
| [bmson](skills/bmson/SKILL.md) | 处理 .bmson 文件、查 v1 JSON schema、排查 beatoraja 扩展问题、实现 BMSON 解析器、BGA、计时与拍号 |

## 使用方式

### npx skills（推荐）

> 注意：Codeberg（Forgejo）需使用 raw URL 路径

```bash
npx skills add https://codeberg.org/brightmeows/bms-skills/raw/branch/main
```

### 手动引用

克隆仓库后，在 AI 助手的配置中引用 `skills/` 下的 `SKILL.md`：

```bash
git clone https://codeberg.org/brightmeows/bms-skills.git
```

## 贡献

欢迎提交 Issue 或 Pull Request。内容纠错、格式细节补充、引擎差异记录、示例修正等都十分感谢。

## 许可

Apache-2.0
