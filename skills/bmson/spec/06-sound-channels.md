# 音频通道与切片算法

> 来源：[bmson format specification — bmson-spec 1.0.0-beta documentation](https://bmson-spec.readthedocs.io/en/master/doc/#sound-channels)

## 音频通道

**bmson 是基于音频通道的。** 来自同一音频通道的音符被分组在同一个数组中。

### sound_channels :: SoundChannel[]

一个音频通道代表一条音轨。

![音频通道](images/sound_channels.png)

## 切片与重启

音频通道内的音符决定了如何切片以及何时重启。

![切片示意 1](images/slicing_1.png)

不同时间的音符导致声音在不同时间被切片。

![切片示意 2](images/slicing_2.png)

高亮的 `SoundChannel` 代表底鼓声音。与其重复使用底鼓声音多次（导致冗余的音频文件），不如将 `SoundChannel` 重启。

### name :: DOMString

代表音轨的文件名。

- 文件扩展名可以省略。
  - 如果省略文件扩展名，实现应在可兼容的音频文件中搜索该名称。
  - 示例：`{ name: "piano" }` → 尝试 `piano.wav`、`piano.ogg`、`piano.m4a`……
- 如果提供了文件扩展名但找不到文件或无法播放，实现应将文件名视为已去除扩展名。
  - 示例：`{ name: "piano.wav" }` → 未找到 `piano.wav` → 视为 `"piano"` → 尝试 `piano.wav`、`piano.ogg`……
  - 示例：`{ name: "piano.ogg" }` → 不支持 ogg → 视为 `"piano"` → 尝试 `piano.wav`、`piano.ogg`、`piano.m4a`……
- 音频文件可以位于相对于 bmson 文件的子目录中。
  - 路径可以使用反斜杠（`\`）或正斜杠（`/`）分隔，实现应进行规范化。
  - 实现必须防止恶意路径：
    - 绝对路径：`C:\password.txt` 或 `/etc/passwd`
    - 父目录引用：`../../../var/www/html/config.php`
    - 空字符（`\0`）
  - 示例：`{ name: "intro\\drum" }`

### 音频文件格式建议

播放器应支持以下文件格式：

- Microsoft WAV 文件（.wav）。
- OGG Vorbis（.ogg）或 MP4 AAC（.m4a）之一。

OGG Vorbis 是免费的文件格式，可以自由使用，且非常容易创建。不幸的是，并非所有平台都原生支持（通过硬件加速）解码 OGG 文件。

MP4 是手机上最常见的多媒体文件格式，Android 和 iOS 原生支持，但创建 MP4 文件更困难。

> [!WARNING]
> 不鼓励使用 MP3 文件格式。
>
> MP3 编码器和解码器都会在文件开头和结尾添加静音 [^3]。
>
> 这会导致声音偏移，有时多达 20 毫秒。这可能将一个 Perfect GREAT 变成 GREAT。

[^3]: <http://lame.sourceforge.net/tech-FAQ.txt>

### notes :: Note[]

- `x` 是此音符的玩家通道。
  - `0` 或 `null` → 这不是可玩音符（BGM 音符）
  - `1`、`2`、`3`…… → 这是可玩音符
- `y` 是此音符必须被激活的时间（脉冲编号）
- `l` 是音符长度
  - `0` → 这是短音符
  - `> 0` → 这是长按音符，从脉冲 `y` 开始，到 `y + l` 结束。
- `c` 是延续标志
  - `true` → 继续——不重启
  - `false` → 不继续——重启音频

## 切片算法（概要）

切片的高层算法如下。

1. 收集此 `SoundChannel` 中所有音符的脉冲编号。去除重复项。
2. 将这些脉冲编号转换为公制时间（秒）。
3. 每当遇到没有延续标志的音符时，重启音频。
4. 使用步骤 2 中的时间值作为切片点，对生成的音频进行切片。
5. 每个音符被分配一个与其同时开始的切片。

### 切片示例

给定此 `SoundChannel` 对象：

```yaml
sound_channels:
- name: vox.wav
  notes:
  - { x: 1, y: 240, c: false }  # 1
  - { x: 3, y: 360, c: true }   # 2
  - { x: 7, y: 360, c: true }   # 3
  - { x: 2, y: 720, c: false }  # 4
  - { x: 3, y: 720, c: false }  # 5
  - { x: 4, y: 840, c: true }   # 6
  - { x: 6, y: 840, c: true }   # 7
  - { x: 3, y: 1200, c: true }  # 8
  - { x: 0, y: 1680, c: true }  # 9
```

我们得到以下信息（假设 BPM=120）：

| 脉冲编号 | 拍 | 秒 | 重启？ | 音符 | 切片 # |
| --- | --- | --- | --- | --- | --- |
| 240 | 1 | 0.5 | 是 | 1 | 1 |
| 360 | 1.5 | 0.75 | 否 | 2, 3 | 2 |
| 720 | 3 | 1.5 | 是 | 4, 5 | 3 |
| 840 | 3.5 | 1.75 | 否 | 6, 7 | 4 |
| 1200 | 5 | 2.5 | 否 | 8 | 5 |
| 1680 | 7 | 3.5 | 否 | 9 | 6 |

基于这些切片点进行切片，我们得到以下切片：

| 切片 # | 音频开始时间 | 音频结束时间 | 切片持续时间（秒） |
| --- | --- | --- | --- |
| 1 | 0 | 0.25 | 0.25 |
| 2 | 0.25 | 1 | 0.75 |
| 3 | 1 | 1.25 | 0.25 |
| 4 | 1.25 | 2 | 0.75 |
| 5 | 2 | 3 | 1 |
| 6 | 3 | （文件末尾） |  |

切片后的声音如下所示：

![切片示意 3](images/slicing_3.png)

最后，这些切片与音符关联。

从上表可以看出，**多个音符可能被分配同一个切片**。

### 边缘情况

- 如果同一切片同时被分配给可玩音符和 BGM 音符，则必须丢弃 BGM 音符。

  示例：

  ```yaml
  sound_channels:
  - file: kick.wav
    notes:
    - { x: 0, y: 960 } # (x)
    - { x: 1, y: 960 }
    - { x: 3, y: 960 }
  ```

  这里，标记为 (x) 的音符必须被丢弃。

- 如果同一脉冲上有多个音符，其中一些设置了延续标志而另一些没有，实现应视为延续标志未设置。

### 播放行为

- 每个切片只有 1 的复音数。

  这意味着如果一个切片被分配给两个或更多音符（显然在同一脉冲上）并同时触发，该切片不应听起来比正常情况更响亮。

  但是，如果播放的是同一音频通道的不同切片，它们应同时播放。

- 你可以将每个切片视为 BMS 文件中的 #WAV 通道。

- 注意多个音频通道可以引用同一文件。
  - 这是不同的音频通道，因此可以同时播放。这与[多重 WAV 定义](http://hitkey.nekokan.dyndns.info/cmds.htm#WAVXX-MULTIPLEX-DEFINITION)一致。

> **实现建议（本节为非规范性内容）**
>
> 如果连续的切片仅被 BGM 音符使用，可以合并这些切片。这可以减少切片数量，并可能改善声音平滑度和性能。
>
> 粗略算法：
>
> ```text
> for each pair of consecutive slice s1 and s2
>   if either slice is used by non-BGM note
>     don't join
>   else if s2 is not continuing (c: false)
>     don't join
>   else
>     join them together
> ```

## 分层音符

- 如果来自不同音频通道的音符位于相同的 (x, y) 位置：
  - 这些音频通道的音符“融合”为一个音符。播放此音符时，播放来自每个原始音频通道的音频切片。
  - 如果长度不等，则为错误（播放器可以发出警告）。
