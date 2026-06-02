# 顶层对象与信息对象

> 来源：[bmson format specification — bmson-spec 1.0.0-beta documentation](https://bmson-spec.readthedocs.io/en/master/doc/#top-level-object-bmson)

## 顶层对象（Bmson）

### version :: DOMString

指定此 bmson 的版本。

当前可能值为 `1.0.0`。

- 版本号应使用 [语义化版本 2.0.0](http://semver.org/spec/v2.0.0.html) 算法进行比较。
- 没有 version 字段的 bmson 文件是旧版 bmson 文件。实现者应该：
  - 拒绝处理此文件（旧格式必须转换为新格式），或
  - 将此文件作为 bmson v0.21 处理（超出本规范范围）。
- 如果 `version` 为 `null`，播放器应显示错误信息。

## 信息对象（BmsonInfo）

### title :: DOMString

将显示的歌曲标题。

- 实现者*无需*按分隔符（如 `()`、`--`）拆分标题字符串。

### subtitle :: DOMString

将显示的歌曲副标题。

默认值为空字符串。

- 通常以比 `title` 更小的字体显示。
- 可通过包含换行符 `\n` 来实现多行副标题。

### artist :: DOMString

将显示的主要艺术家。

- 通常是音乐作者。
- 此字符串中可能包含多个名字，例如：
  - `Artist1 vs Artist2`
  - `Artist1 feat. Vocalist`

### subartists :: DOMString[]

协助编写此 bmson 文件的其他艺术家。

默认值为空数组。

- 这对于索引和搜索很有用。例如 [BMserver](http://bms.main.jp/)。
- 这是一个字符串数组，每个字符串采用 `key:value` 形式。
  - `key` 可以是 `music`、`vocal`、`chart`、`image`、`movie`、`other`
  - 如果省略 `key`，默认为 `other`
  - 每个元素应仅包含一个名字。
  - 实现者应去除 `key` 和 `value` 前后的空格。
- 示例：`"subartists": ["music:5argon", "music:encX", "chart:flicknote", "movie:5argon", "image:5argon"]`

### genre :: DOMString

歌曲流派。

### mode_hint :: DOMString

指定游戏模式。

默认值为 `beat-7k`。

- 实现者应查看 `mode_hint` 来检查音符是否是为该特定游戏模式设计的。例如，8 键游戏与 IIDX 风格游戏不同，即使它们使用完全相同的通道编号。
- 对于通用对称键盘布局，应使用 `generic-nkeys`，其中 `n` 为按键数。应从左到右排列。

> **扩展提示：添加本文档中未列出的模式时**
>
> 播放器可以通过 `version` 和 `mode_hint` 判断格式是否受支持。
> 因此如果你创建了扩展格式，应使用不同的 `mode_hint`，
> 以便播放器判断如何处理谱面。
> 你不应修改 `version`，因为它代表底层 bmson 格式版本。

### chart_name :: DOMString

谱面名称。

默认值为空字符串。

- 示例：`BEGINNER`、`NORMAL`、`HYPER`、`ANOTHER`、`INSANE`、`7keys Beginner`

### level :: unsigned long

表示谱面难度的数值。

- 通常由作者主观评估决定。建议难度编号基于基础游戏的难度等级。
- 例如在 `beat` 模式下，难度应基于 1~12 的等级考虑。
- `level` 必须 ≥ 0。负值可能被播放器视为无效。

### init_bpm :: double

表示歌曲开始时的速度。

- 如果 `init_bpm` 未指定，则为致命错误。

### judge_rank :: double

指定判定窗口的宽度。

默认值为 `100`。

- 如果 `judge_rank` 大于 `100`，判定窗口比播放器默认值更宽。
- 如果 `judge_rank` 小于 `100`，判定窗口比播放器默认值更窄。
- 具体实现取决于各个播放器。

一种可能的解释（本节仅供参考，非规范性内容）

- `judge_rank` 可被解释为判定窗口的百分比。
- 例如，正常情况下要获得 PERFECT 判定，必须在 20 毫秒窗口内按键。
- 如果 `judge_rank` 为 250，则此判定窗口为正常大小的 2.5 倍，即 50 毫秒。这使谱面更容易。
- 如果 `judge_rank` 为 50，则判定窗口为正常大小的一半（缩小 2 倍）。必须在 10 毫秒窗口内按键。

以下是几个流行播放器的默认判定窗口：

| LunaticRave2 [^2] | Bemuse |  |  |
| --- | --- | --- | --- |
| Perfect GREAT | ≤ 18 ms | METICULOUS | ≤ 20 ms |
| GREAT | ≤ 40 ms | PRECISE | ≤ 50 ms |
| GOOD | ≤ 100 ms | GOOD | ≤ 100 ms |
| BAD | ≤ 200 ms | OFFBEAT | ≤ 200 ms |
| POOR | > 200 ms | MISSED | > 200 ms |

[^2]: #RANK 2（NORMAL）

### total :: double

默认值为 `100`。

- `total` 必须 ≥ 0。
  - 如果为 0，生命条不会增长。
  - 如果为负值，取绝对值。
- 定义生命条（也称为 *groove gauge*）相对于默认速率的增长量。
  - 默认速率取决于各个播放器。
  - 如果 `total` 大于 `100`，当音符以高精度播放时，生命条增长更多。
  - 如果 `total` 小于 `100`，当音符以高精度播放时，生命条增长更少。
  - 也可以参考当玩家未命中音符时生命条减少多少。
    - 此行为也可能因播放器而异。

## 参考

IIDX 的默认速率近似值：

如果玩家完美演奏了所有音符，groove gauge 增长 `7.605 * n / (0.01 * n + 6.5)` 百分比。

### back_image :: DOMString

可能在游戏过程中显示的静态背景图片路径。

- 如果 `back_image` 未定义、为 null 或为空，播放器使用默认背景图片。
- 示例：[Toy Musical 2](https://www.youtube.com/watch?v=8mDNzrQBlBY)

### eyecatch_image :: DOMString

可能在歌曲加载期间显示的图片路径。

- 如果 eyecatch_image 未定义、为 null 或为空，播放器使用默认过场图片。

### title_image :: DOMString

将在歌曲开始前显示的图片路径。

- 这等同于 [OADX+ 皮肤中的 #BACKBMP](http://www.geocities.jp/red_without_right_stick/backbmp/index.html)。
- 如果 `title_image` 未定义、为 null 或为空，播放器将以默认字体显示标题。

### banner_image :: DOMString

可能在歌曲选择界面或结果界面显示的图片路径。

- 图片尺寸应为 15:4，通常为 600x160。遵循此比例的其他尺寸（如 900x240）可用于某些高分辨率显示器。

### preview_music :: DOMString

用于预览音乐的短音频文件路径。

- 如果 `preview_music` 未指定，播放器可以从 `sound_channels` 创建预览。

### resolution :: unsigned long

4/4 拍号下每个四分音符的脉冲数。

默认值为 `240`。

- `resolution` 必须 > 0。
  - 如果为 0、`null` 或 `undefined`，使用默认值。
  - 如果为负值，取绝对值。
- 详细信息参见 [术语/节拍分辨率](03-terminologies.md)。
