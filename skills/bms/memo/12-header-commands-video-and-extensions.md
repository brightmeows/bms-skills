# 头部命令：视频与扩展

> 来源：<https://hitkey.nekokan.dyndns.info/cmdsJP.htm#HEADER>

## #VIDEOFILE

| origin | support |
|--------|---------|
| bemaniaDX | bemaniaDX, TypeAce, Be-Pachi Music, RDM, ruvit, Sonorous(parsing-only) |

- 指定作为 BGA 播放的视频文件名。

| | | |
|--|---|---|
| filetype | MPEG 支持 | TypeAce, RDM, ruvit |
| | AVI 支持 | bemaniaDX, TypeAce, Be-Pachi Music, RDM, ruvit |
| filename | | bemaniaDX 中可指定完整路径和相对路径。 |
| loopable | | 若演奏时间剩余，视频将循环播放。视频从 `#000` 开始播放。 |
| sounding | | 视频包含的音频不会播放，视频的音频被忽略。 |

- AVI 取决于 PC 上安装的编解码器。
- bemaniaDX 的情况：
  - 若颜色褪色，`#VIDEOCOLORS` 的调色板定义可能低于视频的调色板。
    请正确指定 `#VIDEOCOLORS`。
  - 通道 `#xxx04`、`07` 和 `06` 可与视频同时使用。
    - 通道 `#xxx04` 堆叠在 VIDEOFILE 之上。
    - 通道 `#xxx07` 堆叠在通道 `#xxx04` 之上。

| summary | channel/header | remarks |
|---------|----------------|---------|
| BGA LAYER | `#xxx07` | top level |
| BGA BASE | `#xxx04` | middle level |
| VIDEOFILE | `#VIDEOFILE` | bottom level |

- 通道 `#xxx04` 和 `#xxx07` 中的黑色部分 (RGB:00:00:00) 变为透明。
- 打出 Miss 时，通道 `#xxx04`+`#xxx07` 的显示切换为通道 `#xxx06` 的显示。
    经过一定时间后，恢复为通道 `#xxx04`+`#xxx07` 的显示。

| summary | channel/header | remarks |
|---------|----------------|---------|
| BGA POOR | `#xxx06` | top level |
| VIDEOFILE | `#VIDEOFILE` | bottom level |

同样，通道 `#xxx06` 的黑色部分 (RGB:00:00:00) 变为透明。

- RDM 和 ruvit 中，VIDEOFILE 和 BGA 通道仅显示其中一方（排他性显示）。

## #VIDEOf/s

| origin | support |
|--------|---------|
| bemaniaDX | bemaniaDX |

- 定义视频的帧率（每秒显示帧数）。
- 可指定包含小数部分的十进制数。
- 视频的帧率可从 AVI 文件的属性"帧率"中查看（"*n* 帧/秒"）。
- 省略此命令时，AVI 文件以自身帧率播放。
- 更改播放速度的示例：

| BMS code | remarks |
| -------- | ------- |
| `<pre>#VIDEOFILE MOVIE.avi<br>#VIDEOf/s 30</pre>` | MOVIE.avi 文件本身的帧率为 15 帧/秒。视频播放速度设为 30 FPS。 |

MOVIE.avi 将以 2 倍速播放。

## #VIDEOCOLORS

| origin | support |
|--------|---------|
| bemaniaDX | bemaniaDX |

- 定义视频的调色板。
- 视频的调色板可从 AVI 文件的属性"视频采样大小"中查看（"*n* 位"）。
- 省略此命令时，默认值为 `16`（16bit color）。

## #VIDEODLY

| origin | support |
|--------|---------|
| bemaniaDX | bemaniaDX |

- 定义视频从哪一帧开始播放，即设置视频延迟。
- 省略此命令时，从 `#000` 起始位置的 0 帧开始播放（无延迟）。

## #MOVIE

| origin | support |
|--------|---------|
| DXEmu | DXEmu, IIDXv, HDX, Sonorous(parsing-only) |

- 视频从 `#000` 的起始位置开始播放。
- `#MOVIE` 和通道 `#xxx04` 冲突时：
  - 若通道 `#xxx04` 为图像文件（BMP、PNG 等），`#MOVIE` 优先。
  - 若通道 `#xxx04` 和 `#MOVIE` 均为视频文件，则通道 `#xxx04` 优先。
- 不循环播放。若演奏时间剩余，视频将停留在最后一帧并停止。
- 若视频包含音频，不播放视频的音频。

## #SEEK[00-ZZ]

| origin | support |
|--------|---------|
| LR | LR, (LR2 中已删除 ?) |

| channel | `#xxx05` |
|---------|----------|

- 以毫秒为单位更改当前视频的播放位置。
- 此命令的详细信息不详。

## #ExtChr

| origin | support |
|--------|---------|
| BM98 | BM98k, DDR (部分支持) |

| channel | `#xxx05` |
|---------|----------|
| detail | *BM98FinalSecret* (<http://bm98.yaneu.com/bm98/bm98secret.html>) |

- 将 BMS 侧准备的 Extended-Character 文件应用于 BM98。
- 播放画面上显示的所有元素均可替换为 BMS 侧准备的图像文件的一部分。
- 此命令用于自定义播放 screen 的用户界面。
- 详细说明：
  - `*SpriteNum*`: `*[0-1023]*` (?)
    - 从内部准备的字符映射中选择并指定要更改的字符索引。
  - `*BMPNum*`: `*[00-FF]*`
    - 需要预先将位图文件定义为 `#BMP`，以供 `#ExtChr` 引用。
    - 此文件将被裁剪，因此也可定义大于 256x256 的图像。
    - ~~实际上，与规格不同，`#BMP` 索引只能指定 `[00-09]` 或 `[0-9]`。~~
    - 将 `*[#BMP-index]*` 的十六进制数转换为十进制数后进行指定。
    - 推测 `-1` 和 `257` 被分配为 BM98 的默认主题，这些也可操作。
  - `*startX startY*`:
    - 裁剪起始位置，从原始图像裁剪区域的左上角坐标。
  - `*endX endY*`:
    - 裁剪结束位置，从原始图像裁剪区域的右下角坐标。
  - `*offsetX offsetY*`:
    - 为裁剪后的坐标添加偏移量。
    - 例如，假设图像裁剪的偏移量为 (-5, -10)。
    - 当此图像裁剪被指示显示在坐标 (100, 120) 时，
    - 实际显示在坐标 (95, 110)。
    - 若不需要偏移量，此"offsetX offsetY"参数可省略。
  - `*x y*`:
    - 指定绝对坐标，适用于静态元素。
    - 例如修改判定线正下方的键盘图像等情况。
    - 若不需要，此"x y"参数可省略。
    - 指定此参数时，即使不需要"偏移量"，也**不能**省略"偏移量"参数。
- 简单示例：

```bms
#bmp00 custom_skin1.bmp
#bmp09 custom_skin2.bmp
#bmpFF custom_skin3.bmp
#extchr 512  09 30  0 99  9
#extchr 514 255 38 11 62 19
#extchr 516   0 38  1 62  9 -2 -2
#extchr 513   0 38  1 62  9 -2 -2 0 0
#extchr 512   9 30  0 49 19
```

> // 覆盖 characterID-`512` 的命令

- DDR 部分支持 *Project2DX* 格式（仅 1P 侧数据）。
  - 该格式是一种通过皮肤实现的 7KEYS 模拟，将通道 `#xxx21` 和 `#xxx22` 分配给 1P 侧的 KEY6 和 KEY7。
  - 这是在尚未存在支持 7KEYS 的实现的时期的发明。
  - 当 BMS 中存在以下任一描述时，DDR 执行 *Project2DX* 专用处理：

```text
#ExtChr 730 -1 245  1 257 18 201 140
#ExtChr 774 -1 259 64 271 81 201 140
#ExtChr 780 -1 335 64 347 81 201 140
#ExtChr 779 -1 325 64 333 81 201 140
#ExtChr 745 -1 275 20 287 37 201 140
#ExtChr 771 -1 400 41 404 58 201 140
#ExtChr 760 -1 259 41 271 58 201 140
#ExtChr 784 -1 399 64 411 87 201 140
```

    - `#PLAYER` 的值设为 `1`。
    - 通道 `#xxx21` 和 `#xxx22` 替换为 `#xxx18` 和 `#xxx19`。
    - 通道 `#xxx26`（2P 侧 SCRATCH）全部被忽略。

- BM98de 不一定支持 `#ExtChr`。
- 然而，错误解释的结果可能恰好改变显示。
- 此命令过于复杂，未能完全理解。请参考实际示例：
  - *九十九里* (TEM, 2000-11-01) (<http://childs.squares.net/bms/index.html>) (umi_99ri.rar 中的 **umi_99ri_3.bms**)

## MATERIALS

| origin | support |
|--------|---------|
| BM98de | BM98de, RDM, bemaniaDX, nanasi (低于 1.00) |

- 这是"以可执行文件为起点的相对路径定义"的别名。
  1. 在与 bm98body.exe 同级的"Materials"目录下创建新目录。
  2. 在此新目录中放置音频文件或图像文件。
  3. 在 BMS 侧使用 "`<`*foldername*`>`*filename*" 格式定义（目录名需用半角尖括号括起）。
  4. 这样，"Materials"下目录中的文件也可被 BMS 引用。
- 目录层级示例：

```text
bm98_beta1
  ├ bm98body.exe
  ├ ...
  └ Materials
           └ samples
                  ├ samplesound.wav
                  └ sampleimage.bmp
```

```text
// 位于其他位置的 BMS
#WAV01 <samples>samplesound.wav
#BMP01 <samples>sampleimage.bmp
```

  该 BMS 将从"`bm98_beta1\Materials\samples\`"加载 `samplesound.wav` 和 `sampleimage.bmp`。

- 此功能可便于管理音色集。
- 不过，现代实现支持路径定义而非 MATERIALS。

## #MATERIALSWAV

| origin | support |
|--------|---------|
| nanasi | nanasi (低于 1.00) |

| caution | **此命令已过时。不应再使用此命令。** |
|---------|----------------------------------------------------------------|

- 指定以 Materials 文件夹为起点的相对路径（260 字节限制）。
- 包含此命令的 BMS 将全部从 Materials 路径加载音频文件。
- 优先级如下：

| 优先级 | 路径 | 备注 |
|--------|------|------|
| 1 | 通常的"MATERIALS"定义 | `#WAVxx <*path*>*filename*` |
| 2 | 与可执行文件同级的"Materials"文件夹 | 可与其他实现共享。 |
| 3 | "`_datafiles\materials`"（nanasigroove 文件夹内） | nanasigroove 专用的"materials"文件夹 |

- "MATERIALS"、`#MATERIALSWAV` 和 `#MATERIALSBMP` 的支持已在 nanasi 1.00 中移除。

## #MATERIALSBMP

| origin | support |
|--------|---------|
| nanasi | nanasi (低于 1.00) |

| caution | **此命令已过时。不应再使用此命令。** |
|---------|----------------------------------------------------------------|

- 指定以 Materials 文件夹为起点的相对路径（260 字节限制）。
- 包含此命令的 BMS 将全部从 Materials 路径加载图像文件。
- 优先级如下：

| 优先级 | 路径 | 备注 |
|--------|------|------|
| 1 | 通常的"MATERIALS"定义 | `#BMPxx <*path*>*filename*` |
| 2 | 与可执行文件同级的"Materials"文件夹 | 可与其他实现共享。 |
| 3 | "`_datafiles\materials`"（nanasigroove 文件夹内） | nanasigroove 专用的"materials"文件夹 |

- "MATERIALS"、`#MATERIALSWAV` 和 `#MATERIALSBMP` 的支持已在 nanasi 1.00 中移除。

## #DIVIDEPROP

| origin | support |
|--------|---------|
| nanasi | nanasi (低于 0.98) |

| caution | **此命令已过时。不应再使用此命令。** |
|---------|----------------------------------------------------------------|

- 指定 BMS 的小节分辨率。此命令因不再需要而被移除。
- 目前，nanasi 的分辨率自动设为 960。
- 分辨率 960 是使用浮点数的模拟值。
