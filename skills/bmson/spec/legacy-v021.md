# bmson v0.21 格式规范（旧版）

> 来源：[bmson format specification — bmson-spec 1.0.0-beta documentation](https://bmson-spec.readthedocs.io/en/master/doc/)
> 版本历史由 changelog 逆向重构

- 此文档描述 **bmson v0.21**（又称"legacy bmson"），即标准化之前的版本。
- v0.21 无正式的独立规范文档——标准化规范将其标记为"超出本规范范围"（out of scope）。本文档基于 changelog、早期实现代码、社区资料及官方示例文件逆向整理，供兼容性实现参考。
- 格式差异对照参见[更新日志](./02-changelog.md)，当前标准格式参见 [01-overview.md](./01-overview.md)。

---

## 背景

bmson 格式由 **wosderge** 于 **2015 年 8 月 13 日**首次公开发布。
最初在 [Bemuse](https://bemuse.ninja/)（dtinth/flicknote）和
[raindrop](https://github.com/zardoru/raindrop)（zardoru）中实现，
随后 [beatoraja](https://mocha-repository.info/download/)（両替士）加入支持。

v0.21 是标准化前广泛使用的版本。标准化规范将不带 `version` 字段的文件视为 legacy v0.21，
并留给实现者两种处理选择：

1. 拒绝处理（要求将旧格式转换为新格式）
2. 作为 v0.21 处理

---

## 格式定义

### 顶层对象

以下为根据 changelog 及 wosderge 官方示例 `bmson_sample.zip` 逆向重构的 v0.21 结构：

```c
dictionary Bmson {
    BmsonInfo      info;           // 信息，例如标题、艺术家……
    BarLine[]?     lines;          // 小节线位置
    EventNote[]?   bpmNotes;       // BPM 变化（camelCase 命名）
    EventNote[]?   stopNotes;      // 停止事件（camelCase 命名）
    SoundChannel[] soundChannel;   // 音符数据（camelCase 命名）
    BGA            bga;            // BGA 数据
}

// 顶层不含 version 字段
```

| 顶层字段 | 命名 |
|---------|------|
| `soundChannel` | camelCase |
| `bpmNotes` | camelCase |
| `stopNotes` | camelCase（部分文档记为 `stopEvents`，存在不一致） |

### 信息对象

```c
dictionary BmsonInfo {
    DOMString     title;            // 曲名
    DOMString     artist;           // 作者
    DOMString     genre;            // 曲风
    unsigned long level;            // 难度等级
    double        initBPM;          // 初始 BPM（camelCase）
    double        judgeRank;        // 判定窗口（camelCase）
    double        total;            // 生命条增益（绝对数值，camelCase）
}
```

info 中存在的字段（均为 camelCase）：

| 字段 | 类型 | 说明 |
|------|------|------|
| `title` | 字符串 | 曲名 |
| `artist` | 字符串 | 作者 |
| `genre` | 字符串 | 曲风 |
| `level` | 整数 | 难度等级 |
| `initBPM` | 浮点数 | 初始 BPM |
| `judgeRank` | 浮点数 | 判定窗口 |
| `total` | 浮点数 | 生命条增益（**绝对数值**） |

info 中不存在以下字段：`subtitle`、`subartists`、`mode_hint`、`chart_name`、
`back_image`、`eyecatch_image`、`banner_image`、`preview_music`、`resolution`。

无 `mode_hint` 时默认假定布局为 beat-7k。无 `resolution` 时固定为 240 ppqn。

### 小节线事件

```c
dictionary BarLine {
    unsigned long y; // 时间位置
    any?           k; // 可选字段，用途未明确定义
}
```

`k` 字段的用途在原始规范中未明确定义，wosderge 官方示例中所有 `k` 值均为 0。
可能用于表示节拍键签名或其它元数据，该字段在标准化时被移除。

### 音频通道

```c
dictionary SoundChannel {
    DOMString name;  // 音频文件名
    Note[] notes;    // 使用此音频的音符
}
```

### 音频音符

```c
dictionary Note {
    any           x; // 轨道（0=BGM, 1+ = 可演奏轨道）
    unsigned long y; // 时间位置
    unsigned long l; // 长度（0=普通音符, >0=长按音符）
    boolean       c; // 延续标志（false=重开音频, true=延续）
}
```

### 计时事件（BPM / STOP）

v0.21 使用**统一类型** `EventNote` 表示 BPM 变化和 STOP 事件：

```c
// BPM 和 STOP 的通用事件类型
dictionary EventNote {
    unsigned long y; // 时间位置
    double        v; // 值
}
```

- **BPM 变化**：位于 `bpmNotes` 数组，`v` 表示新的 BPM 值
  - 示例：`{ "y": 240, "v": 180 }` → 在 240 处 BPM 变为 180

- **停止事件**：位于 `stopNotes` 数组（部分文档记为 `stopEvents`），`v` 表示停止持续时间
  - 示例：`{ "y": 240, "v": 240 }` → 在 240 处停止 240 个时间单位

> **关于 `stopNotes` 与 `stopEvents` 的不一致：**
> 标准化规范 changelog 称 v0.21 的字段名为 `stopEvents`，但 wosderge
> 官方示例文件（bmson_sample.zip）中实际使用的字段名是 `stopNotes`。
> 参照 `bpmNotes` 的命名模式（"Notes" 后缀），`stopNotes` 更可能是 v0.21
> 的原始命名。实现在解析时应同时兼容这两种命名。

### BGA

```c
dictionary BGA {
    BGAHeader[]  bgaHeader;  // 图片 ID 和文件名（camelCase）
    BGAEvent[]   bgaNotes;   // 图片序列（camelCase）
    BGAEvent[]   layerNotes; // 叠加图层（camelCase）
    BGAEvent[]   poorNotes;  // 未命中时图片序列（camelCase）
}

dictionary BGAHeader {
    unsigned long ID;   // 图片标识符（大写）
    DOMString     name; // 图片文件名
}

dictionary BGAEvent {
    unsigned long y;  // 时间位置
    unsigned long id; // 对应 BGAHeader.ID
}
```

BGA 字段命名均为 camelCase：

| BGA 字段 | 说明 |
|----------|------|
| `bgaHeader` | — |
| `bgaNotes` | — |
| `layerNotes` | — |
| `poorNotes` | — |
| `BGAHeader.ID` | 大写 I |

---

## 时间位置值（y 的含义）

`y` 是 bmson 中所有时间索引的核心字段，用于小节线、BPM 事件、停止事件、音符和 BGA 事件。
它是一个非负整数，表示从乐曲开始处的时间位置。

### 数值规律

基于 wosderge 官方示例 `pz_menu.bmson` 的实际数据分析：

| 属性 | 值 |
|------|-----|
| BPM | 170 |
| 小节线间隔 | 960（全部 99 个小节线间隔一致）|
| 音符最小间隔 | 120 |
| 音符间隔类型 | 120, 240, 360, 480 |
| 总音符数 | 384 |
| 所有 y 值对齐到 | 10 单位粒度 |

### 与音乐时间的对应关系

v0.21 使用固定的时间分辨率：**240 个单位 = 1 拍（四分音符）**。

基于此分辨率的时间换算：

| y 值 | 拍数 | 示例位置 |
|------|------|----------|
| 240 | 1 拍 | — |
| 480 | 2 拍 | — |
| 960 | 4 拍 = 1 小节（4/4） | 小节线位置 |
| 120 | ½ 拍 = 8 分音符 | 音符最小间隔 |
| 60 | ¼ 拍 = 16 分音符 | — |
| 40 | ⅙ 拍 = 16 分三连音 | — |
| 30 | ⅛ 拍 = 32 分音符 | — |
| 10 | ¹⁄₂₄ 拍 = 64 分音符 | 最小可表示粒度 |

### 转换为秒

将 `y` 值转换为实际时间（秒）的公式：

```text
秒数 = y × 60 / (240 × initBPM)
     = y × 60 / (240 × BPM)
```

以 `pz_menu.bmson`（BPM=170）为例：

```text
y=0    → 0.000 秒
y=240  → 0.353 秒 (1拍)
y=960  → 1.412 秒 (1小节)
y=3840 → 5.647 秒 (4小节)
```

### 分辨率与对齐

- v0.21 无显式的 `resolution` 字段，固定为 **240 单位/拍**。
- 所有时间位置值均应为此分辨率的整数倍。官方示例中所有 y 值均可被 10 整除，
  即最小可表达 64 分音符。
- 小节线标准间隔为 `960`（= 240 × 4），对应 4/4 拍号的 1 个小节。
  变拍号可通过在 `lines` 数组中插入非 960 间隔的小节线实现。

---

## 完整示例

以下 v0.21 bmson 文件结构已通过 wosderge 官方发布的 `bmson_sample.zip` 验证。

```json
{
  "info": {
    "title": "Example Song",
    "artist": "Example Artist",
    "genre": "Example",
    "level": 5,
    "initBPM": 140,
    "judgeRank": 100,
    "total": 200
  },
  "lines": [
    { "y": 0, "k": 4 },
    { "y": 960 },
    { "y": 1920 }
  ],
  "bpmNotes": [
    { "y": 0, "v": 140 },
    { "y": 1920, "v": 180 }
  ],
  "stopNotes": [
    { "y": 960, "v": 48 }
  ],
  "soundChannel": [
    {
      "name": "kick.wav",
      "notes": [
        { "x": 1, "y": 0, "l": 0, "c": false },
        { "x": 0, "y": 480, "l": 0, "c": false },
        { "x": 1, "y": 960, "l": 0, "c": false },
        { "x": 0, "y": 1440, "l": 0, "c": false }
      ]
    },
    {
      "name": "snare.wav",
      "notes": [
        { "x": 3, "y": 480, "l": 0, "c": false },
        { "x": 3, "y": 1440, "l": 0, "c": false }
      ]
    }
  ],
  "bga": {
    "bgaHeader": [
      { "ID": 0, "name": "bg.png" },
      { "ID": 1, "name": "layer.png" }
    ],
    "bgaNotes": [
      { "y": 0, "id": 0 }
    ],
    "layerNotes": [],
    "poorNotes": []
  }
}
```

---

## 实现兼容性说明

### 文件检测

判断一个文件是否为 v0.21 格式（伪代码）：

```text
if 文件解析失败 → 报错
if 顶层有 "version" 字段:
    按 version 值处理
else if 顶层有 "soundChannel"（camelCase）:
    按 v0.21 处理
else if "bpmNotes" 存在 或 "stopNotes" 存在:
    可能为 v0.21，尝试按 v0.21 解析
else:
    按默认格式处理
```

### 字段命名兼容

- v0.21 全为 camelCase。部分编辑器可能写出 snake_case 和 camelCase 混用的文件，
  实现应对两种命名风格都做容错处理。
- `stopNotes` 与 `stopEvents`：标准化文档称原名 `stopEvents`，
  但 wosderge 官方示例中使用 `stopNotes`。实现应同时兼容这两种命名。

### 已知实现

| 实现 | v0.21 支持 | 备注 |
|------|-----------|------|
| Bemuse | ✅ 支持 | 最早实现之一，自 2015-08 起 |
| raindrop | ✅ 支持 | 初步支持（2015-10） |
| beatoraja | ✅ 支持 0.2.1 | README 明确标注 |
| BmsONE | ✅ 支持 | 可读写 v0.21 格式 |
| fbmsonplay | ✅ 支持 | bmson 模拟器 |
| bms2bmson | ✅ 支持 | BMS→bmson 转换工具 |

---

## 相关文档

以下为本研究过程中发现的相关文档和资料，按类别列出。

### 历史规范草案

| 文档 | 描述 |
|------|------|
| [bmson specs (Google Doc, 已停维)](https://docs.google.com/document/d/1ZDjfjWud8UG3RPjyhN-dd1rVjPaactcMT3PIODTap9s/) | 标准化起草阶段的 Google Doc，含大量讨论记录和投票 |
| [#bmson Creation Notes (English)](https://docs.google.com/document/d/1gQKPWApeL03aO09-II7slxTeuvm3HO_FmY1D4chRvOQ) | 英文创建笔记 |
| [how_to_bmson (日本語, atwiki)](http://www40.atwiki.jp/laser_bm/pages/110.html) | 日文创建指南 |

### 社区文档

| 文档 | 描述 |
|------|------|
| [bmson 開発部 (atwiki)](https://w.atwiki.jp/laser_bm/pages/104.html) | 开发者聚合页，2015-08 由 wosderge 创建 |
| [bmson 概述 (atwiki)](https://w.atwiki.jp/laser_bm/pages/97.html) | 日文概述，含首次发布日期和原始 Google Doc 链接 |
| [Be-Music Source (Wikipedia)](https://en.wikipedia.org/wiki/Be-Music_Source) | Wikipedia 条目，提及早期版本信息 |
| [bmson_sample.zip（官方示例）](http://cerebralmuddystream.nekokan.dyndns.info/bmson/bmson_sample.zip) | wosderge 官方发布的示例 bmson 文件（v0.21 格式，含 `stopNotes` 命名的实际数据） |
| [bmson / Bemuse 文档集](https://bmson.nekokan.dyndns.info/documents/) | 官方资料汇总页面 |

### 实现参考

| 项目 | 描述 |
|------|------|
| [Bemuse 的 bmson 支持 (Qiita)](https://qiita.com/dtinth/items/a2d644c608b3d1cf41ff) | dtinth 发布的 bmson v0.21 支持公告（2015-08） |
| [bemusic/bmson (npm)](https://github.com/bemusic/bmson) | JavaScript 库，含 v0.21 格式解析实现 |
| [beatoraja](https://github.com/exch-bms2/beatoraja) | 明确标注支持 bmson 0.2.1 |
| [raindrop BMSON 支持提交](https://github.com/zardoru/raindrop/commit/2d72a7d3360ecbe061e73294901464aa022fa39d) | 2015-10 首次 BMSON 支持（+270 行实现） |
| [bms2bmson（WSH 版）](https://hitkey.nekokan.dyndns.info/bmson/_wsh_bms2bmson/) | BMS→bmson 转换工具 |
| [BmsONE 编辑器](https://excln.github.io/bmsone_en.html) | 跨平台 bmson 编辑器，支持 v0.21 格式 |
