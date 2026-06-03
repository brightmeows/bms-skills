# beatoraja BMS 扩展定义

> 来源：[beatoraja Wiki — 楽曲製作者向け資料](https://github.com/exch-bms2/beatoraja/wiki/%E6%A5%BD%E6%9B%B2%E8%A3%BD%E4%BD%9C%E8%80%85%E5%90%91%E3%81%91%E8%B3%87%E6%96%99)
> beatoraja 是目前最活跃的 BMS 播放器之一，实现了一些特有的扩展命令。
>
> **范围说明**：本文档仅覆盖 beatoraja 对 BMS（.bms/.bme/.bml/.pms）格式的扩展命令。
> BMSON（.bmson）格式的 beatoraja 扩展请参见 [beatoraja BMSON 扩展](../../bmson/ext/beatoraja-bmson-extensions.md)。

## #PREVIEW

| 项目 | 内容 |
|------|------|
| 值 | 音频文件路径 |
| 支持 | beatoraja |

- 指定选曲时自动播放的预览音源。
- 若未指定，beatoraja 自动查找文件夹内的 `preview(*).wav` 或 `preview(*).ogg` 作为预览音源。

```bms
#PREVIEW preview.ogg
```

## #LNMODE

| 项目 | 内容 |
|------|------|
| 值 | `1`（LN）、`2`（CN）、`3`（HCN） |
| 支持 | beatoraja |

- 明确定义谱面中使用的长音种类。
- 指定后，谱面内的长音种类不受选曲时的 LN MODE 选项影响，强制固定。
- 三种模式：

| 值 | 名称 | 行为 |
|----|------|------|
| `1` | LN（Long Note） | 标准长音，起点按键保持至终点松开 |
| `2` | CN（Charge Note） | 蓄力长音，持续按住即可，终点不要求 Keyup |
| `3` | HCN（Hell Charge Note） | 地狱蓄力长音，类似 CN 但判定更严格 |

> 注：上表中 LN/CN/HCN 的行为说明为译者补充——原文（beatoraja Wiki）仅定义了值映射（`1:LN, 2:CN, 3:HCN`），未描述各模式的具体游戏内行为。行为描述基于通用认知整理。

## #RANK 4（VERY EASY）

- beatoraja 中 `#RANK 4` 的判定宽度为 EASY 的 **1.25 倍**。

## #DEFEXRANK

- 值以 **NORMAL 为基准**（即 `100` = NORMAL 判定宽度），可精细指定任意宽度倍率。
- beatoraja **0.7.3 起**基线从 EASY 改为 **NORMAL**。
- `#DEFEXRANK` 优先级高于 `#RANK`。

## #VOLWAV

| 项目 | 内容 |
|------|------|
| 值 | 音量（最大 100） |
| 支持 | beatoraja（0.6.2+） |

- 指定乐曲的音量。
- 值越大音量越高，最大值为 `100`。

```bms
#VOLWAV 80
```

## #SCROLL

| 项目 | 内容 |
|------|------|
| 语法 | `#SCROLLxx`（定义）、`#nnnSC`（通道） |
| 支持 | beatoraja |

- 指定谱面的滚动速度倍率。未定义时默认为 `1.0`。
- 与 BPM 导致的滚动速度补正**重叠生效**。
- 格式与扩展 BPM 定义、STOP 定义相同。
- 倍率设为负值时，谱面**逆向滚动**（逆走）。
- 配合正负交替可实现复杂的滚动演出。

```bms
#SCROLL01 -1
#SCROLL02 1
#001SC:0100000000000000
#002SC:0000000000000002
```

## 音频格式

beatoraja 的音频解码规格：

| 项目 | 说明 |
|------|------|
| WAV 量化 | 8bit / 16bit / 24bit / 32bit |
| WAV 编码 | **仅 PCM 和 MP3**（非 PCM 格式不支持） |
| 采样率 | 任意，解码时统一转为 **16bit 44100Hz** |
| OGG | 支持 |
| MP3 | 支持（含作为 `#WAV` 定义） |

## 视频格式

beatoraja 0.5.4 起检测以下视频扩展名为 BGA（按优先级降序）：

1. `mp4`
2. `wmv`
3. `m4v`
4. `webm`
5. `mpg` / `mpeg`
6. `m1v` / `m2v`
7. `avi`

> 同名文件存在多种格式时，无论 BMS 定义如何，均按上述顺序自动检测。ffmpeg 支持的格式均可使用。
> 建议分辨率不超过 1280x720。beatoraja 可自动维持宽高比（无需手动插入黑边）。
> 视频包含音轨时，解码时自动分离忽略。
