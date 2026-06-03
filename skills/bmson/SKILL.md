---
name: bmson
description: 处理 .bmson JSON 格式谱面、实现解析器/生成器、查询 v1/v2 schema 字段定义及类型、或排查 beatoraja BMSON 扩展兼容性问题时使用。
license: Apache-2.0
---

# BMSON 参考索引

BMSON 是 BMS 的 JSON 序列化格式，以结构化方式描述谱面。核心设计理念：播放器运行时对音频切片，无需制谱者预切音效。

**本文件为索引，不作为事实来源。** 具体实现或谱面制作时，须阅读对应参考文档以获取完整规范。

所有引用路径均相对于本文件所在目录。

---

## 使用方式

本文档是 BMSON 技能的总索引。使用规则如下：

### 目录导航

- **spec/**：BMSON 格式规范。实现解析器/生成器时按序号顺序阅读。
- **ext/**：beatoraja BMSON 扩展。涉及 beatoraja 特有功能时查阅。

### 建议阅读路径

- **解析器/播放器开发者：** 从 spec/01 → spec/04 → spec/05 → spec/06 顺序阅读核心规范，再按需查阅 ext/ 了解引擎扩展。v2 格式差异见 spec/v2-specification。
- **谱面作者：** 先读 spec/01 了解格式概览，再按需查阅 spec/ 中对应字段的详解。
- **工具/转换器开发者：** spec/ 全部通读后，按需查阅 ext/。

### 字段联动规则

处理某个字段时，必须阅读**所有**「相关字段」列包含该字段的文档，以全面了解其定义和跨文档影响。例如 `init_bpm` 同时出现在 spec/01、spec/04、spec/05 等多篇文档中——只读其中一篇不足以掌握完整规范。

---

## spec/ —— BMSON 格式规范

| 文件 | 说明 | 使用方式 | 相关字段 |
|------|------|---------|----------|
| [01-overview.md](./spec/01-overview.md) | bmson 格式概述：顶层结构、基本类型定义、核心链表 | 了解 BMSON 格式的整体构成和设计思路时用 | `version` `info` `lines` `bpm_events` `stop_events` `sound_channels` `bga` |
| [02-changelog.md](./spec/02-changelog.md) | 版本更新日志（0.21→1.0.0）：破坏性变更（snake_case、字段拆分）、非破坏性变更（新增字段） | 从旧版本迁移或理解版本差异时用 | — |
| [03-terminologies.md](./spec/03-terminologies.md) | 术语定义：公制时间/音乐时间/时钟时间、节拍分辨率（240 脉冲/四分音符）、x/y 维度含义 | 理解 BMSON 时间模型和坐标系统时必读 | `y` `x` `resolution` |
| [04-top-level-and-info.md](./spec/04-top-level-and-info.md) | 顶层对象 `Bmson` 与信息对象 `BmsonInfo` 各字段详解：版本、元数据（title/artist/genre 等）、计时参数（init_bpm/judge_rank/total）、资源路径（图片/预览音频）、分辨率 | 实现 BMSON 解析器和头部信息处理时必读 | `version` `title` `subtitle` `artist` `subartists` `genre` `mode_hint` `chart_name` `level` `init_bpm` `judge_rank` `total` `back_image` `eyecatch_image` `banner_image` `preview_music` `resolution` |
| [05-time-signatures-and-timing.md](./spec/05-time-signatures-and-timing.md) | 拍号与计时：小节线机制（BarLine）、BPM 事件（BpmEvent）、STOP 事件（StopEvent）、同脉冲事件处理顺序 | 理解 BMSON 脉冲时间系统和事件时序时必读 | `lines` `BarLine` `bpm_events` `BpmEvent` `stop_events` `StopEvent` |
| [06-sound-channels.md](./spec/06-sound-channels.md) | 音频通道与切片算法：SoundChannel 结构、Note 字段（x/y/l/c）、切片算法步骤、边缘情况、分层音符 | 实现 BMSON 音频系统和切片逻辑时必读 | `sound_channels` `SoundChannel` `Note` (`x` `y` `l` `c`) |
| [07-bga.md](./spec/07-bga.md) | BGA 数据结构：`bga_header`（图片 ID 与文件名）、`bga_events`/`layer_events`/`poor_events`；与 BMS 的差异（LAYER 黑色不自动透明） | 实现 BGA 系统时查阅 | `bga` `BGAHeader` `BGAEvent` `bga_header` `bga_events` `layer_events` `poor_events` |
| [08-appendices.md](./spec/08-appendices.md) | 模式提示规范列表：beat 系列（beat-5k/7k/10k/14k）、popn 系列（popn-5k/9k）的 x 值与键位映射 | 查询模式提示和键位映射时用 | `mode_hint` |
| [v2-specification.md](./spec/v2-specification.md) | v2.0.0-rc1（fork 分支）差异说明：`Bmson` 拆分为 `SongInfo`+`ChartInfo`+`ChartData`、`judge_rank`→`judge_multiplier`/`total`→`life_multiplier`、`Note`→`NoteEvent`、新增 LN 提示/DJ.NEXT 扩展 | 开发或适配 v2 格式时必读 | `SongInfo` `ChartInfo` `ChartData` `NoteEvent` `judge_multiplier` `life_multiplier` `ln_type_hint` `ln_judge_hint` `ln_life_hint` `up` `vol` `pan` `JudgementDeltas` `LifeDeltas` |

---

## ext/ —— beatoraja 扩展

| 文件 | 说明 | 使用方式 | 相关字段 |
|------|------|---------|----------|
| [beatoraja-bmson-extensions.md](./ext/beatoraja-bmson-extensions.md) | beatoraja 针对 BMSON 格式的特有扩展：全局长音类型（`ln_type`）、单音符长音类型覆盖（`t`）、终点音标志（`up`）、滚动速度事件（`scroll_events`）、地雷通道（`mine_channels`）、不可见音符通道（`key_channels`） | 面向 beatoraja 做谱或实现 beatoraja BMSON 兼容功能时必读 | `info.ln_type` `Note.t` `Note.up` `scroll_events` `mine_channels` `key_channels` |
