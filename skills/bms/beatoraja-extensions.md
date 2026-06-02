# beatoraja BMS 扩展定义

> 来源：[beatoraja Wiki — 楽曲製作者向け資料](https://github.com/exch-bms2/beatoraja/wiki/%E6%A5%BD%E6%9C%8D%E4%BD%9C%E8%80%85%E5%90%91%E3%81%91%E8%B3%87%E6%96%99)
> beatoraja 是目前最活跃的 BMS 播放器之一，实现了一些特有的扩展命令。

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

## #RANK 4（VERY EASY）

- beatoraja 中 `#RANK 4` 的判定宽度为 EASY 的 **1.25 倍**。

## #DEFEXRANK

- beatoraja **0.7.3 起**基线从 EASY 改为 **NORMAL**（即值 `100` 对应 NORMAL）。
- `#DEFEXRANK` 优先级高于 `#RANK`。

## #SCROLL 负值

- `#SCROLL` 的倍率设为负值时，谱面**逆向滚动**（逆走）。
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
