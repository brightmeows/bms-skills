---
name: bmson-quick-reference
description: Use when working with .bmson files, creating or editing BMSON charts, converting between BMS and BMSON, implementing BMSON parsers/generators, looking up v1 JSON schema fields, or troubleshooting beatoraja BMSON extensions.
---

# BMSON 速查表

BMSON 是 BMS 的 JSON 序列化格式，以结构化方式描述谱面。核心设计理念：播放器运行时对音频切片，无需制谱者预切音效。

本速查表以 **v1** 为主，覆盖 **顶层结构**、**信息对象字段**、**计时与拍号**、**音频通道与切片**、**BGA**、**beatoraja 扩展**、**模式提示**。

> 详细参考：参见 `spec/` 下各文档和同级文档。

---

## 一、基础信息

| 项目 | 内容 |
|------|------|
| 文件扩展名 | `.bmson` |
| 格式 | JSON（本文档示例用 YAML 表示） |
| v1 | 1.0.0-beta（2015/12/26） |
| v2 | 2.0.0-rc1（2023/10/24，fork 分支） |
| 主要实现 | beatoraja |

> → 详见 [spec/01-overview.md](spec/01-overview.md)、[spec/v2-specification.md](spec/v2-specification.md)

---

## 二、顶层结构（v1）

```c
dictionary Bmson {
    DOMString      version;        // 版本号（"1.0.0"）
    BmsonInfo      info;           // 头部信息
    BarLine[]?     lines;          // 小节线位置（脉冲）
    BpmEvent[]?    bpm_events;     // BPM 变化
    StopEvent[]?   stop_events;    // 停止事件
    SoundChannel[] sound_channels; // 音频通道与音符
    BGA            bga;            // BGA 数据
}
```

> → 详见 [spec/04-top-level-and-info.md](spec/04-top-level-and-info.md)

---

## 三、信息对象（BmsonInfo）

### 元数据

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `title` | DOMString | — | 曲名 |
| `subtitle` | DOMString | `""` | 副标题，可含 `\n` |
| `artist` | DOMString | — | 主要艺术家 |
| `subartists` | DOMString[] | `[]` | `["key:value"]` 形式 |
| `genre` | DOMString | — | 曲风 |
| `mode_hint` | DOMString | `"beat-7k"` | 布局提示 |
| `chart_name` | DOMString | — | 谱面名称（HYPER / ANOTHER…） |
| `level` | unsigned long | — | 难度等级（≥ 0） |

### 计时与判定

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `init_bpm` | double | — | 初始 BPM（**必需**） |
| `judge_rank` | double | `100` | 判定窗口宽度（%），>100 更宽 |
| `total` | double | `100` | 生命条增长量（%），>100 更多 |
| `resolution` | unsigned long | `240` | 每四分音符脉冲数 |

### 资源

| 字段 | 类型 | 说明 |
|------|------|------|
| `back_image` | DOMString? | 游戏背景图片 |
| `eyecatch_image` | DOMString? | 加载过场图片 |
| `banner_image` | DOMString? | 选曲横幅（15:4 比例） |
| `preview_music` | DOMString? | 预览音乐 |

> → 详见 [spec/04-top-level-and-info.md](spec/04-top-level-and-info.md)

---

## 四、计时与拍号

BMSON **没有原生小节/拍号概念**，一切基于**脉冲编号（y）**。

### 小节线

```c
dictionary BarLine {
    unsigned long y; // 脉冲编号
}
```

- 未指定时默认 4/4 拍，每 `4 × resolution` 脉冲一条小节线
- 空数组 → 无小节线
- `y: 0` 可省略

### BPM 事件

```c
dictionary BpmEvent {
    unsigned long y;   // 脉冲编号
    double        bpm; // BPM
}
```

- 多个 BPM 事件在同一脉冲 → 取最后一个

### STOP 事件

```c
dictionary StopEvent {
    unsigned long y;        // 脉冲编号
    unsigned long duration; // 停止持续时间（脉冲数）
}
```

- 多个 STOP 在同一脉冲 → 累加

### 事件处理顺序（同脉冲时）

1. `Note` / `BGAEvent`
2. `BpmEvent`
3. `StopEvent`

> → 详见 [spec/05-time-signatures-and-timing.md](spec/05-time-signatures-and-timing.md)

---

## 五、音频通道与切片

### SoundChannel

```c
dictionary SoundChannel {
    DOMString name;  // 音频文件名（可省略扩展名）
    Note[]    notes; // 使用此音频的音符
}
```

- 文件扩展名可省略，实现自动搜索 WAV/OGG/M4A
- 路径可用 `\` 或 `/`，实现应规范化
- **禁止**绝对路径、父目录引用、空字符

### Note

```c
dictionary Note {
    any           x; // 轨道（0=BGM，>0=可玩）
    unsigned long y; // 脉冲编号
    unsigned long l; // 长度（0=普通，>0=长按结束于 y+l）
    boolean       c; // 延续标志（false=重启音频）
}
```

### 切片算法概要

1. 收集通道内所有音符的脉冲编号，去重
2. 转换为公制时间（秒）
3. 遇到 `c: false` 的音符时重启音频
4. 按时间值切片
5. 每个音符分配与其同时开始的切片

**边缘情况：**

- 每个切片复音数为 1（同时触发不叠加）
- 同一脉冲上 `c` 值不一致 → 视为未延续
- 不同音频通道引用同一文件 → 可同时播放

**分层音符：** 不同通道音符位于相同 `(x, y)` → 融合为一个音符，同时播放各通道切片。

> → 详见 [spec/06-sound-channels.md](spec/06-sound-channels.md)

---

## 六、BGA

```c
dictionary BGA {
    BGAHeader[] bga_header;   // 图片 ID 与文件名
    BGAEvent[]  bga_events;   // 背景动画序列
    BGAEvent[]  layer_events; // 叠加层序列
    BGAEvent[]  poor_events;  // miss 时序列
}

dictionary BGAHeader { unsigned long id; DOMString name; }
dictionary BGAEvent  { unsigned long y; unsigned long id; }
```

> 与 BMS 不同：LAYER 通道黑色像素**不**自动透明。
> → 详见 [spec/07-bga.md](spec/07-bga.md)

---

## 七、beatoraja 扩展

| 字段 | 位置 | 类型 | 说明 |
|------|------|------|------|
| `ln_type` | `info` | `1`/`2`/`3` | 全局长音类型：LN/CN/HCN |
| `t` | Note | `1`/`2`/`3` | 单音符长音类型覆盖（优先级高于 `ln_type`） |
| `up` | Note | boolean | 长音终点播放终点音（须 `l>0`）beatoraja 0.6.2+ |
| `scroll_events` | Root | `{y, rate}[]` | 滚动速度事件数组（`rate` 负值=逆走）beatoraja 0.7.6+ |
| `mine_channels` | Root | `{name, notes[{x,y,damage}]}[]` | 地雷通道（`damage` 支持小数） |
| `key_channels` | Root | `{name, notes[{x,y}]}[]` | 不可见音符通道（不显示/不判定，空打播音频） |

> → 详见 [beatoraja-bmson-extensions.md](beatoraja-bmson-extensions.md)

---

> v2.0.0-rc1 是 fork 分支（2023/10/24），结构变动较大
> （`Bmson` → `SongInfo`+`ChartInfo`+`ChartData`，`judge_rank`→`judge_multiplier` 等）。
> 详见 [spec/v2-specification.md](spec/v2-specification.md)。

---

## 九、模式提示速查

### Beat 系列

| mode_hint | x=1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|-----------|-----|---|---|---|---|---|---|---|
| beat-5k | 1 | 2 | 3 | 4 | 5 | | | SC |
| beat-7k | 1 | 2 | 3 | 4 | 5 | 6 | 7 | SC |
| beat-10k | 1 | 2 | 3 | 4 | 5 | | | SC | *P2: 1-5 + SC* |
| beat-14k | 1 | 2 | 3 | 4 | 5 | 6 | 7 | SC | *P2: 1-7 + SC* |

SC = Scratch（转盘）

### Popn 系列

| mode_hint | x=1-9 |
|-----------|--------|
| popn-5k | 1-5 |
| popn-9k | 1-9 |

> → 详见 [spec/08-appendices.md](spec/08-appendices.md)

---

## 十、术语速查

| 术语 | 单位 | 说明 |
|------|------|------|
| 公制时间（t） | 秒 | SI 时间单位 |
| 音乐时间（b） | 拍 | 取决于 BPM 和 STOP |
| 时钟时间（y） | 脉冲 | MIDI 时钟，一拍被分割为等距离散脉冲 |
| 分辨率 | 脉冲/四分音符 | 默认 `240`，一拍 = 240 脉冲 |
| x | 轨道号 | 代表玩家通道（0=BGM，1+=可玩） |

> → 详见 [spec/03-terminologies.md](spec/03-terminologies.md)

---

## 快速索引

| 主题 | 章节 |
|------|------|
| v1 顶层结构 | 二、顶层结构（v1） |
| BmsonInfo 字段 | 三、信息对象 |
| 小节线 / BPM / STOP | 四、计时与拍号 |
| SoundChannel / Note / 切片 | 五、音频通道与切片 |
| BGA 事件 | 六、BGA |
| beatoraja 扩展 | 七、beatoraja 扩展 |
| 模式提示（beat/popn） | 九、模式提示速查 |
| 术语定义 | 十、术语速查 |
| 切片算法细节 | [spec/06-sound-channels.md](spec/06-sound-channels.md) |
| BGA 完整规范 | [spec/07-bga.md](spec/07-bga.md) |
| v2 规范（fork 分支） | [spec/v2-specification.md](spec/v2-specification.md) |
| 更新历史（v0.21→v1.0.0） | [spec/02-changelog.md](spec/02-changelog.md) |
