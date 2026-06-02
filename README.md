# BMS Skills

为 AI 编码助手（OpenCode 等）提供的 BMS（Be-Music Script）及 BMSON 格式知识技能集合。

## 技能

| 技能 | 说明 |
|---|---|
| **bms** | BMS 速查表 — 头部命令、通道映射、控制流、扩展格式、引擎差异（beatoraja vs LR2）、BMSE 兼容性 |
| **bmson** | BMSON 速查表 — JSON 序列化格式、顶层结构、计时与拍号、音频通道与切片、BGA、beatoraja 扩展 |

## 使用方式

### npx skills（推荐）

```bash
npx skills add https://codeberg.org/brightmeows/bms-skills
```

### 手动引用

克隆仓库后，在 AI 助手的配置中引用 `skills/` 下的 `SKILL.md`：

```bash
git clone https://codeberg.org/brightmeows/bms-skills.git
```

## 许可

Apache-2.0
