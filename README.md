# BMS Skills

> 本仓库既可作为 Agent Skill 供 AI 编码助手加载使用，也可作为参考材料帮助理解 BMS 及 BMSON 谱面格式的实现细节。

为 AI 编码助手（OpenCode 等）提供的 BMS（Be-Music Script）及 BMSON 格式知识技能集合。

## 技能

| 技能 | 说明 |
|---|---|
| [bms](skills/bms/SKILL.md) | 创建/编辑 BMS 谱面、排查格式问题、查通道映射与头部命令、引擎差异（beatoraja vs LR2）、BMSE 兼容性、开发 BMS 工具 |
| [bmson](skills/bmson/SKILL.md) | 处理 .bmson 文件、查 v1/v2 JSON schema、排查 beatoraja 扩展问题、实现 BMSON 解析器、BGA、计时与拍号 |
| [bms-table](skills/bms-table/SKILL.md) | 处理 BMS 难度表（難易度表）、实现解析器/生成器、查询 JSON 字段定义及类型、排查难度表兼容性问题 |

## 使用方式

通过 `npx skills` 直接安装：

```bash
npx skills add brightmeows/bms-skills
```

拉取 `main` 分支，始终最新。

## 贡献

欢迎提交 Issue 或 Pull Request。内容纠错、格式细节补充、引擎差异记录、示例修正等都十分感谢。

## 许可

Apache-2.0
