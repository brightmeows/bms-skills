# bmson 格式规范 v2.0.0-rc1 差异说明

> 来源：[bmson format specification — bmson-spec-fork 2.0.0-rc1 documentation](https://bmson-spec-fork.readthedocs.io/en/latest/doc/)

- 版本：2.0.0（2023/10/24）
- 本文档为 bmson-spec-fork 分支版本，仅记录相对于 v1.0.0 的差异。
- 未在此处说明的内容与 v1.0.0 一致，参见 [v1 规范](./01-overview.md)。

---

## 更新日志（2.0.0，从 1.0.0 起）

### 破坏性变更

- 将 `Bmson` 拆分为 `Bmson` 和 `ChartData`

  - `Bmson.lines` → `ChartData.lines`
  - `Bmson.bpm_events` → `ChartData.bpm_events`
  - `Bmson.stop_events` → `ChartData.stop_events`
  - `Bmson.sound_channels` → `ChartData.sound_channels`
  - `Bmson.bga` → `ChartInfo.bga`

- 将 `BmsonInfo` 拆分为 `SongInfo`、`ChartInfo` 和 `ChartData`

  | v1 字段 | v2 字段 |
  | --- | --- |
  | `BmsonInfo.subtitle` | `ChartInfo.subtitle` |
  | `BmsonInfo.subartists` | `ChartInfo.subartists` |
  | `BmsonInfo.mode_hint` | `ChartData.mode_hint` |
  | `BmsonInfo.chart_name` | `ChartInfo.chart_name` |
  | `BmsonInfo.level` | `ChartInfo.level` |
  | `BmsonInfo.back_image` | `ChartInfo.back_image` |
  | `BmsonInfo.eyecatch_image` | `ChartInfo.eyecatch_image` |
  | `BmsonInfo.banner_image` | `ChartInfo.banner_image` |
  | `BmsonInfo.preview_music` | `ChartInfo.preview_music` |
  | `BmsonInfo.judge_rank` | `ChartData.judge_multiplier` |
  | `BmsonInfo.total` | `ChartData.life_multiplier` |

- 对象重命名：`Note` → `NoteEvent`
- 字段重命名：`Bmson.info` → `Bmson.song_info`；`SoundChannel.notes` → `SoundChannel.note_events`
- `judge_multiplier` 和 `life_multiplier` 改为乘数制（v1 为百分比制）

### 非破坏性变更

- 新增字段

  - `Bmson.chart_info`
  - `Bmson.chart_data`
  - `ChartData.ln_type_hint`
  - `ChartData.ln_judge_hint`
  - `ChartData.ln_life_hint`

- 新增可选字段

  - `NoteEvent.up`
  - `NoteEvent.ln_type_hint`
  - `NoteEvent.ln_judge_hint`
  - `NoteEvent.ln_life_hint`

- 新增对象（DJ.NEXT 扩展）

  - `JudgementDeltas`
  - `LifeDeltas`

- 新增字段（DJ.NEXT 扩展）

  - `ChartData.judge_deltas`
  - `ChartData.life_deltas`
  - `NoteEvent.vol`
  - `NoteEvent.pan`

## 格式总览

该格式遵循 [Web IDL (Second Edition)](https://heycam.github.io/webidl/)。
以下仅列出 v2 新增或变更的结构，
未列出的（BarLine、BpmEvent、StopEvent、BGA、BGAHeader、BGAEvent）与 v1 一致。

```c
// 顶层对象
dictionary Bmson {
    DOMString version;                       // bmson 版本
    SongInfo song_info;                      // bmson 信息（标题、艺术家……）
    ChartInfo chart_info;                    // 谱面信息（难度、谱面名称……）
    ChartData chart_data;                    // 谱面数据
}

// 歌曲信息（原 BmsonInfo 的 title/artist/genre 提取为此对象）
dictionary SongInfo {
    DOMString    title;                      // 不言自明
    DOMString    artist;                     // 不言自明
    DOMString    genre;                      // 不言自明
}

// 谱面信息（原 BmsonInfo 的谱面相关字段 + BGA）
dictionary ChartInfo {
    DOMString     subtitle = "";             // 不言自明
    DOMString[]?  subartists = [];           // ["key:value"]
    DOMString     chart_name;                // 例如 "HYPER"、"FOUR DIMENSIONS"
    unsigned long level;                     // 不言自明
    DOMString?    eyecatch_image;            // 过场图片文件名
    DOMString?    banner_image;              // 横幅图片文件名
    DOMString?    back_image;                // 背景图片文件名
    DOMString?    preview_music;             // 预览音乐文件名
    BGA           bga;                       // BGA 数据
}

// 谱面数据（原 Bmson 的谱面数据字段 + 新增长按提示）
dictionary ChartData {
    DOMString      mode_hint = "beat-7k";    // 布局提示
    DOMString      ln_type_hint = "ln";      // [新增] 长按类型提示
    DOMString      ln_judge_hint = "normal"; // [新增] 长按判定提示
    DOMString      ln_life_hint = "normal";  // [新增] 长按生命提示
    double         init_bpm;                 // 不言自明
    double         judge_multiplier = 1.00;  // [变更] 判定窗口乘数
    double         life_multiplier = 1.00;   // [变更] 生命条增益乘数
    unsigned long  resolution = 240;         // 每四分音符的脉冲数
    BarLine[]?     lines;                    // 小节线位置
    BpmEvent[]?    bpm_events;               // BPM 变化
    StopEvent[]?   stop_events;              // 停止事件
    SoundChannel[] sound_channels;           // 音符数据

    // DJ.NEXT 扩展：
    JudgementDeltas judge_deltas;            // 自定义判定窗口增量（毫秒）
    LifeDeltas      life_deltas;             // 自定义生命增减（百分比）
}

// 音频通道（notes → note_events）
dictionary SoundChannel {
    DOMString   name;                        // 音频文件名
    NoteEvent[] note_events;                 // [重命名] 使用此音频的音符
}

// 音频音符（原 Note → NoteEvent，新增可选字段）
dictionary NoteEvent {
    any           x;                         // 轨道
    unsigned long y;                         // 脉冲编号
    unsigned long l;                         // 长度
    boolean       c;                         // 延续标志

    // 可选变量：
    boolean   up;                            // [新增] 上键音标志
    DOMString ln_type_hint;                  // [新增] 长按类型提示覆盖
    DOMString ln_judge_hint;                 // [新增] 长按判定提示覆盖
    DOMString ln_life_hint;                  // [新增] 长按生命提示覆盖

    // DJ.NEXT 扩展：
    signed byte vol;                         // [新增] 音量（百分比）
    signed byte pan;                         // [新增] 声像
}

// DJ.NEXT 扩展：判定增量值
dictionary JudgementDeltas {
    unsigned long perfect;                   // perfect 窗口增量（毫秒）
    unsigned long great;                     // great 窗口增量（毫秒）
    unsigned long good;                      // good 窗口增量（毫秒）
    unsigned long miss;                      // miss 窗口增量（毫秒）
}

// DJ.NEXT 扩展：生命增量值
dictionary LifeDeltas {
    signed float perfect;                    // perfect 生命增量（百分比）
    signed float great;                      // great 生命增量（百分比）
    signed float good;                       // good 生命增量（百分比）
    signed float miss;                       // miss 生命增量（百分比）
}
```

## 顶层对象（Bmson）

### version :: DOMString

当前可能值为 `2.0.0`。其余规则与 v1 一致。

## 谱面数据对象（ChartData）

ChartData 合并了原 v1 中分布在 `Bmson` 和 `BmsonInfo` 中的谱面相关字段。以下仅说明 v2 新增或变更的字段，其余字段语义与 v1 对应字段一致。

### ln_type_hint :: DOMString

指定长按音符类型。默认值为 `ln`。

- 实现者应查看 `ln_type_hint` 来检查**默认**如何判定长按音符。
- 实现者应注意，**这可以在每个 `NoteEvent` 中可选地覆盖**。

可能的值

- `ln`：仅判定初始按下。
- `cn`：初始按下和释放分别判定。

### ln_judge_hint :: DOMString

指定长按音符判定类型。默认值为 `normal`。

- 实现者应查看 `ln_judge_hint` 来检查**默认**如何处理长按音符判定。
- 实现者应注意，**这可以在每个 `NoteEvent` 中可选地覆盖**。

可能的值

- `normal`：仅判定对应的音符，取决于 `ln_type_hint`。
- `ticks`：在长按音符期间，额外会有“tick”被判定。

### ln_life_hint :: DOMString

指定长按音符生命类型。默认值为 `normal`。

- 实现者应查看 `ln_life_hint` 来检查**默认**如何处理长按音符生命。
- 实现者应注意，**这可以在每个 `NoteEvent` 中可选地覆盖**。

可能的值

- `normal`：仅对应的音符恢复生命，取决于 `ln_type_hint`。
- `ticks`：在长按音符期间，额外会有“tick”被判定。

### judge_multiplier :: double

指定判定窗口的宽度。默认值为 `1.00`。

- 语义与 v1 的 `judge_rank` 相同，但基准从百分比（`100`）改为乘数（`1.00`）。
- `1.00` = 正常，`2.50` = 2.5 倍宽，`0.50` = 一半宽。

判定窗口参考表与 v1 一致。

### life_multiplier :: double

定义生命条相对于默认速率的增长量。默认值为 `1.00`。

- 语义与 v1 的 `total` 相同，但基准从百分比（`100`）改为乘数（`1.00`）。

IIDX 默认速率近似值与 v1 一致。

## NoteEvent 可选变量

以下为 `NoteEvent` 新增的可选字段。

### up :: boolean

上键音标志，用于 CN（Charge Note）、BSS（BackSpin Scratch）和 MSS（MultiSpin Scratch）。

- 对于释放键音或 BSS，必须在长按音符末端叠加一个 `up: true` 的普通音符。
- 对于 MSS，必须在长按音符末端叠加一个 `up: true` 的长按音符。
  - 此音符可以使用长按音符提示覆盖来改变长按音符行为。

### ln_type_hint、ln_judge_hint、ln_life_hint

长按音符提示的单音符级别覆盖。参考 ChartData 中对应字段的说明。

## DJ.NEXT 扩展

以下为 DJ.NEXT 播放器引入的扩展字段，不属于 bmson 核心规范。

### ChartData.judge_deltas :: JudgementDeltas

自定义判定窗口增量。每个字段对应一个判定等级的窗口偏移量（毫秒）。

### ChartData.life_deltas :: LifeDeltas

自定义生命增减。每个字段对应一个判定等级的生命变化量（百分比），支持负值。

### NoteEvent.vol :: signed byte

音符音量（百分比）。

### NoteEvent.pan :: signed byte

音符声像。

## 附录：DJ.NEXT 模式提示

v2 新增了 DJ.NEXT 系列模式提示，Beat 和 Popn 系列与 v1 一致。

### DJ.NEXT

Player 1 侧：

| x | 1 | 11 | 12 | 13 | 14 | 15 | 10 | 31 | 32 | 33 | 34 | 20 | 21 | 22 | 23 | 24 | 25 | 2 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| mode_hint | Buttons side 1 | Effectors | Buttons side 2 |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| dj-5k-only |  | 1 | 2 | 3 | 4 | 5 |  |  |  |  |  |  |  |  |  |  |  |  |
| dj-ruby | TT | 1 | 2 | 3 | 4 | 5 | PD |  |  |  |  |  |  |  |  |  |  |  |
| dj-5k | TT | 1 | 2 | 3 | 4 | 5 | PD |  |  |  |  |  |  |  |  |  |  |  |
| dj-7k | TT | 1 | 2 | 3 | 4 | 5 | PD | E1 | E2 |  |  |  |  |  |  |  |  |  |
| dj-10k | TT | 1 | 2 | 3 | 4 | 5 | PD |  |  |  |  |  |  | 6 | 7 | 8 | 9 | 10 | TT |
| dj-14k | TT | 1 | 2 | 3 | 4 | 5 |  | E1 | E2 | E3 | E4 |  | 6 | 7 | 8 | 9 | 10 | TT |
| dj-andromeda | TT | 1 | 2 | 3 | 4 | 5 | PD | E1 | E2 | E3 | E4 | PD | 6 | 7 | 8 | 9 | 10 | TT |

Player 2 侧：

| x | 2 | 25 | 24 | 23 | 22 | 21 | 20 | 34 | 33 | 32 | 31 | 10 | 15 | 14 | 13 | 12 | 11 | 1 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| mode_hint | Buttons side 1 | Effectors | Buttons side 2 |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| dj-5k-only |  |  |  |  |  |  |  |  |  |  |  |  | 6 | 7 | 8 | 9 | 10 |  |
| dj-ruby |  |  |  |  |  |  |  |  |  |  |  | PD | 6 | 7 | 8 | 9 | 10 | TT |
| dj-5k |  |  |  |  |  |  |  |  |  |  |  | PD | 6 | 7 | 8 | 9 | 10 | TT |
| dj-7k |  |  |  |  |  |  |  |  |  | E3 | E4 | PD | 6 | 7 | 8 | 9 | 10 | TT |
| dj-10k | TT | 1 | 2 | 3 | 4 | 5 |  |  |  |  |  | PD | 6 | 7 | 8 | 9 | 10 | TT |
| dj-14k | TT | 1 | 2 | 3 | 4 | 5 |  | E1 | E2 | E3 | E4 |  | 6 | 7 | 8 | 9 | 10 | TT |
| dj-andromeda | TT | 1 | 2 | 3 | 4 | 5 | PD | E1 | E2 | E3 | E4 | PD | 6 | 7 | 8 | 9 | 10 | TT |

TT：Turntable（转盘）

PD：Pedal（踏板）
