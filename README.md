# BMS Skills

为 AI 编码助手（OpenCode 等）提供的 BMS（Be-Music Script）及 BMSON 格式知识技能集合。

## 技能

| 技能 | 说明 |
|---|---|
| [bms](skills/bms/SKILL.md) | 创建/编辑 BMS 谱面、排查格式问题、查通道映射与头部命令、引擎差异（beatoraja vs LR2）、BMSE 兼容性、开发 BMS 工具 |
| [bmson](skills/bmson/SKILL.md) | 处理 .bmson 文件、查 v1 JSON schema、排查 beatoraja 扩展问题、实现 BMSON 解析器、BGA、计时与拍号 |

## 使用方式

### npx skills（推荐）

```bash
# 注意：Codeberg（Forgejo）需使用 raw URL 路径
npx skills add https://codeberg.org/brightmeows/bms-skills/raw/branch/main
```

### 手动引用

克隆仓库后，在 AI 助手的配置中引用 `skills/` 下的 `SKILL.md`：

```bash
git clone https://codeberg.org/brightmeows/bms-skills.git
```

## 许可

Apache-2.0
