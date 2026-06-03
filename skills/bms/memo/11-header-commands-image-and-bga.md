# 头部命令：图像与 BGA

> 来源：[BMS command memo (JP)](https://hitkey.nekokan.dyndns.info/cmdsJP.htm#HEADER)

## `#BMP[00-ZZ] <imagefilename>`

|channel:|remarks|support|
|---|---|---|
|`#xxx04`|BGA BASE|大部分|
|`#xxx06`|BGA POOR|大部分（注：IIDXv 和 HDX 除外）|
|`#xxx07`|BGA LAYER|大部分（注：IIDXv 2.13+, HDX 0.98+）（origin: BM98k）|
|`#xxx0A`|BGA LAYER2|nanasi, Angolmois (2.0a2+), Sonorous, BGAEncAdv, pomu2|
|`#xxx0B`|BGA BASE 的不透明度|nanasi（透明 « `[01-FF]` » 不透明）|
|`#xxx0C`|BGA LAYER 的不透明度|nanasi（透明 « `[01-FF]` » 不透明）|
|`#xxx0D`|BGA LAYER2 的不透明度|nanasi（透明 « `[01-FF]` » 不透明）|
|`#xxx0E`|BGA POOR 的不透明度|nanasi（透明 « `[01-FF]` » 不透明）|

**index range [00-FF] (256):** BM98 — BM98, BM98de, MW, otama, nBMplay, BMSC, BMSV, BMS2AVI, pomu2

**index range [00-FZ] (576):** bemaniaDX — bemaniaDX, DDR, RDM, nazo, BMEV

**index range [00-ZZ] (1296):** nanasi — GALLI, WAview, BGAenc, Mac (?), Aqua (?), LR2, ruvit, nanasi, fgt++,
nazoZZ, GDAC2, BMSE, uBMplay, bmx2wav, IIDXv, HDX, Angolmois, Sonorous, BGAEncAdv, diff

- 图像文件的定义。
- 通常尺寸为 256x256 像素以下。
  - 因为通常实现的图像画布尺寸为 **256x256 pixel**。
  - 规范未定义如何处理超尺寸图像。因此处理方式取决于实现。例如，nazo 不显示超尺寸图像。
- 如果不使用背景动画，则可省略。
- 定义为 `#BMP` 的图像也可以在 `#BGA`、`#@BGA`、`#SWBGA` 的定义中使用（如果它们被支持的话）。
- `#BMP` 索引 `00` 在 miss 时显示。
- `#BMP` 索引 `00` 在通道 `#xxx06` 改变 miss 图像之前一直有效。

**示例 1:** `` `#bmp00 miss-default.bmp` `#bmp11 miss-change.bmp` `#01606:0011` ``
— 从 `#000` 开头到 `#016` 中间区间内，miss 时显示 `#bmp00`。此后 miss 时显示 `#bmp11`。

### 空 `#BMP` 导致的暗转

- 当通道 `#xxx04/06/07` 中发现未定义图像文件的索引时，BM98k 显示 256x256 的黑色（RGB:00:00:00）。
- 这是 BMS 规范未说明的行为，但大多数应用程序沿用了这一行为。

**示例 2:** `` `#BMP11 1.bmp` `#BMP22 2.bmp` `#BMP33 3.bmp` `#01604:112233FF` `` — `#bmpFF` 未定义。当谱面到达 `FF` 时，图像显示被涂黑。

- 但在 RDM 中，如果 `#BMP` 索引包含 `[G-Z]`，且 LAYER 被空的 `#BMP` 索引重置，则可能会随机显示其他无关图像。
- 如果要保持兼容性，我们不应依赖隐式行为。应显式给出定义。
- 简而言之，我们应使用 `black.bmp` 的定义，而非使用空的 `#BMP` 索引。

**更安全的示例：**

- `#BMP11 1.bmp` `#BMP22 2.bmp` `#BMP33 3.bmp` `#BMPFF black.bmp` `#01604:112233FF`
- **black.bmp (256x256)**
- **black.bmp (1x1):** 我们只需要改变图像，所以 1x1 足够了。而且 black.bmp 的调色板只需 2 色（1 位）。减色后尺寸将大幅减小。

### LAYER

- LAYER 通道 `#xxx07` 使黑色透明。只有图像中的黑色部分变为透明，显示 BASE 通道 `#xxx04`。

|示例代码|Image|实际显示|
|---|---|---|
|`#BMP01 1.bmp`|1.bmp|![BASE+LAYER_SEQUENCE](https://hitkey.nekokan.dyndns.info/cmd/Eg_bga+layer.gif)|
|`#BMP02 2.bmp`|2.bmp||
|`#BMP03 3.bmp`|3.bmp||
|`#BMP04 4.bmp`|4.bmp||
|`#00104:01020304`|![BGA_BASE_SEQUENCE](https://hitkey.nekokan.dyndns.info/cmd/Eg_bga.gif)||
|`#BMP05 5.bmp`|![5.bmp](https://hitkey.nekokan.dyndns.info/cmd/Eg_layer1.bmp)||
|`#BMP06 6.bmp`|![6.bmp](https://hitkey.nekokan.dyndns.info/cmd/Eg_layer2.bmp)||
|`#BMP07 7.bmp`|![7.bmp](https://hitkey.nekokan.dyndns.info/cmd/Eg_layer3.bmp)||
|`#BMP08 8.bmp`|![8.bmp](https://hitkey.nekokan.dyndns.info/cmd/Eg_layer4.bmp)||
|`#00107:05060708`|![BGA_LAYER_SEQUENCE](https://hitkey.nekokan.dyndns.info/cmd/Eg_layer.gif)||

在一般实现中，BASE+LAYER 如上显示。

- LAYER 变为透明的黑色阈值取决于桌面属性「画面属性 » 画面颜色」。
  - 如果颜色为 16bit，则 `RGB:00:00:00` - `RGB:0F:0F:0F`（接近黑色）变为透明。
  - 如果颜色为 32bit，则只有 `RGB:00:00:00`（纯黑色）变为透明。
  - 即「DirectX 将值四舍五入后设为 `00` 的颜色」变为透明。
  - （我可能理解有误。这可能是关于应用程序端设置的描述。）
- 一些实现可以操作此阈值。
  - uBMplay 可以修改 `Transparent=0` (ubm.ini)。值可指定 `0` 到 `255`。`0` 为纯黑，`255` 为纯白。
  - nanasi 有可详细操作透明度和透明色的命令。请参考 `#EXBMP` 和 `#ARGB`。
- 透明色仅限于黑色这一事实有时不太方便。
  - 例如，在单色动画中使用 LAYER 时，必须使用「接近黑色」而非纯黑。
  - 透明度也存在同样问题。红色 BASE 加上蓝色 LAYER，显示也不会变成紫色。
    - （如果 BMS 应用程序支持 Alpha 通道且 LAYER 图像指定了 Alpha 值，则可例外地进行混合。）
  - nanasi 拥有丰富的扩展命令可解决这些问题，但熟练掌握非常困难。
  - BGA 创作者不想要复杂的编程。他们更喜欢多媒体创作系统。

### 补正

- 通常情况下，图像的 X 轴中心显示在图像画布的 X 轴中心。
  - 对于 nanasi，除 BMP 外任何文件格式都不进行 X 轴居中。
- 通常情况下，图像的上边缘显示在图像画布的上边缘。
  有关此行为，请参考上述示例及以下 URL：
  <https://hitkey.nekokan.dyndns.info/bmse_help_full/Capture/bmse_bga_easypreview.gif>
- 但作为例外，某些实现会对图像应用缩放。

|自动缩放:|MacBeat, LR2, ruvit|
|---|---|
|- MacBeat 将「小于 256x256 的图像」放大至 256x256 显示。||
|- LR2 将「大于 256x256 的图像」改为「宽高比 1:1，尺寸 256x256」显示。||
|- 也有实现可以更改此显示方式。ruvit 默认基于 256x256 放大显示图像。||

- 一些实现也支持大于 256x256 尺寸的图像。

**扩展画布:** BM98 (origin ?) (仅 5KEYS、5K-Couple ?), DDR, nanasi, fgt++,
uBMplay (1.5.0+), IIDXv, HDX, Sonorous(?), BGAEncAdv, ...（调查中）

- 大于 256x256 的图像会扩展到谱面显示区域。
- 少量 BMS 使用此行为作为演出效果。例如：
  - *relative* — xra, 2001-10-20 (<https://web.archive.org/web/*/http://xra000.hp.infoseek.co.jp/bms.htm>)
  - *FIGHTING OF THE SPIRIT* — shammy, 2001-05-06 (<https://web.archive.org/web/20140819192029/http://shammy.kdn.jp/bm/details.html>)
  - *Chip Disco Paradise* — RED L:TUS, 2002-12-22
  - *せがた三四郎伝説* — デッシュ, 2000-07-22 (<https://www.nicovideo.jp/watch/sm351014>)
  - *loves honey, crazy apple.* — World's endo pisum sativum L., 2007-08-18 (<https://manbow.nothing.sh/event/event.cgi?action=More_def&num=28&event=44>)
- LR2, ruvit 可以使用「大于 256x256 的图像」，但图像不会溢出画布。
- Sonorous 可通过其扩展命令 `#CANVASSIZE` 任意更改画布大小。

### Image file formats

- 支持哪些文件类型取决于实现。位图图像文件几乎一定被支持。
- **BMP** 可能使用 **RLE**（Run Length Encoding）压缩。一些实现无法正确显示 RLE。
  - BMSC 不支持 RLE。某些实现会因不当的 BITMAPINFOHEADER 而崩溃。
  - 似乎过去存在使用 **OS/2 Bitmap** 的 BMS。（例如 *LOVE'S REBIRTH*、*positive dance “2nd RAVE”*）详情请参考 <https://hitkey.nekokan.dyndns.info/bmpfmt.htm>。
- 现代化实现支持 **PNG**。近年来 PNG 非常常用。无损压缩非常方便。
- JPEG 和 GIF 不如 BMP 和 PNG 流行。
- BMP 以外的图像文件格式支持状况：

**PNG:** LR2, nanasi, ruvit, IIDXv, HDX, fgt++, pomu2, nazoZZ, uBMplay, bmx2wav,
PMSee-V, PMChr-V, DTXV, Angolmois, Sonorous, BGAEncAdv

**JPG:** otama, bemaniaDX, MacBeat, LR2, nanasi, ruvit, fgt++, pomu2, nazoZZ, BMSE,
uBMplay, IIDXv, HDX, MyO2, PMSee-V, DTXV, Angolmois, Sonorous, BGAEncAdv

**GIF:** bemaniaDX, BMSE, PMSee-V, pomu2 (Char), LR2, uBMplay (1.5.0+), Angolmois, Sonorous

**PNG (需 ifpng.spi):** BM98de, DDR, RDM, MW, WAview, bms2avi

**JPG (需 ifjpeg.spi):** BM98, BM98de, DDR, RDM, MW, WAview, bms2avi

**GIF (需 ifgif.spi):** BM98de, DDR, RDM, MW, WAview, bms2avi, IIDXv (内置), HDX (内置)

**MAG:** otama

**WMF:** bemaniaDX, BMSE, PMSee-V

**EMF:** bemaniaDX, BMSE, PMSee-V

**CUR:** bemaniaDX

**ICO:** BMSE, PMSee-V, pomu2 (Char)

**TGA:** LR2, nanasi, ruvit, IIDXv, HDX, PMSee-V

**DDS:** LR2, nanasi, ruvit, IIDXv, HDX

**DIB:** IIDXv, HDX

**TIFF:** Angolmois, uBMplay (1.5.0+), Sonorous(?)

**WebP:** Angolmois, Sonorous(?)

**PNM (pbm, pgm, ppm):** Angolmois, Sonorous(?)

**XCF:** Angolmois, Sonorous(?)

**PCX:** Angolmois, Sonorous(?)

**IFF/ILBM:** Angolmois, Sonorous(?)

**PXR:** uBMplay (1.5.0+ / 仅 24-bit / 无保证)

- 一些实现支持带 Alpha 通道的图像。

**Alpha-Channels:** LR2, nanasi, ruvit, IIDXv, HDX, Angolmois (028e49caa6bd 之后), Sonorous, BGAEncAdv, ...（未调查）

- 现代化应用也实现了图像文件的替代搜索。

**替代搜索:** LR2 (origin?), uBMplay, bmx2wav, IIDXv, HDX, Angolmois, Sonorous, BGAEncAdv, ...（调查中）

- 如果定义的 BMP 文件未找到，LR2 会搜索具有相同基名的 PNG 和 JPG。

### `#BMP` 的视频文件定义

**channel:** `#xxx04`

**origin:** LR

**support:** LR2, ruvit, nazoZZ, IIDXv（仅 1 槽）, HDX（仅 1 槽）,
Angolmois（有限尺寸和格式）, uBMplay (1.5.0+ / by DirectShow), Sonorous（有限尺寸和格式?), BGAEncAdv

**loop:** LR2（部分?), ruvit, uBMplay (1.5.0+), Angolmois, Sonorous, BGAEncAdv

**rewind:** LR2, nazoZZ, IIDXv, HDX, uBMplay (1.5.0+), BGAEncAdv

||MPG|AVI|WMA|WMV|OGV|WebM|OGM|MOV|M2P|MP4|MKV|FLV|SWF|3GP|ASF|M4V|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|LR2|Yes|Yes|Yes|No|Yes|Yes|No|No|No|Yes?|No|No|No|No|No|No|
|ruvit|Yes|Yes|No|No|No|Yes|No|No|No|No|No|No|No|No|No|No|
|nazoZZ|Yes|Yes|No|No|No|No|No|No|No|Yes?|No|No|No|No|No|No|
|Angolmois|Yes|No|No|No|No|No|No|No|No|No|No|No|No|No|No|No|
|Sonorous|Yes|No|No|No|No|No|No|No|No|No|No|No|No|No|No|No|
|IIDXv, HDX|Yes|Yes|No|Yes|No|No|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|uBMplay|Yes|Yes|Yes|Yes|No|Yes|No|Yes|No|Yes|No|No|No|No|Yes|No|
|BGAEncAdv|Yes|Yes|?|?|Yes|Yes|?|?|?|Yes|?|?|?|?|?|?|
|MyO2|?|?|?|?|?|?|?|?|?|?|?|Yes|Yes|?|?|?|

- 兼容性最高的格式是 **MPEG1 (256x256)**。
- `#xxx04` 以外通道的视频文件播放不予保证。
  - BGAEncAdv 可在 `#xxx[04|06|07|0A]` 的所有通道上播放多个视频。但请不要认为这是常态。
- 图像 LAYER 可以显示在视频的上层。视频的 LAYER...??
- 如果视频包含音频，视频中的音频应静音。**但 nazoZZ 会播放视频的音频。**
- 如果满足以下条件，一些实现会循环播放：
  1. 视频未被其他图像中断，到达最后一帧。
  2. BMS 演奏时间仍有剩余。
  支持此行为的实现可能更容易使用「通用视频」。
  例如 *Our moments* (Kei + schneider murotani, 2009-09-18) (<https://manbow.nothing.sh/event/event.cgi?action=More_def&num=3&event=60>)
- 一些实现可以倒回视频的播放位置。
  例如 "`#BMP01 mpg.mpg`" 被 "`#00104:01010101`" 触发 4 次「从头播放」。
- 通常视频的宽高比为 1:1。但一些实现支持 4:3 或 16:9 等。
  LR2 和 IIDXv 可以修正视频尺寸。
- O2mania 中，即使没有定义，如果在 BMS 相同位置找到视频文件，视频将从 `#000` 开始播放。

## `#EXBMP[00-ZZ] <a,r,g,b> <imagefilename>`

|channel:|remarks|
|---|---|
|`#xxx04`|BGA BASE|
|`#xxx06`|BGA POOR|
|`#xxx07`|BGA LAYER|
|`#xxx0A`|BGA LAYER2 (nanasi)|

|origin:|nanasi|
|---|---|
|support:|nanasi, Sonorous(parsing-only)|

- 定义具有调整后透明色的图像文件。
- 与 `#BMPxx` 类似，其近似色也会变为透明。
- 可省略。
- `#EXBMPxx` 的索引与 `#BMPxx` 共用。
- 各参数使用 `0` 到 `255` 的十进制整数指定。使用逗号作为参数分隔符。

|param|range|remarks|
|---|---|---|
|`a`|`[0-255]`|alpha|
|`r`|`[0-255]`|red|
|`g`|`[0-255]`|green|
|`b`|`[0-255]`|blue|

**例如:** `` `#exbmp01 0,0,0,0 overlay.png` `#00407:00000001` `` — 所有值均为 `0`，不进行透明化。// ...? 需要测试

- 定义为 `#EXBMP` 的图像也可以在 `#BGA`、`#@BGA`、`#SWBGA` 的定义中使用（如果它们被支持的话）。

## `#BGA[00-ZZ] <[#BMP-index]> <x1> <y1> <x2> <y2> <dx> <dy>`

|channel:|remarks|
|---|---|
|`#xxx04`|BGA BASE|
|`#xxx06`|BGA POOR|
|`#xxx07`|BGA LAYER|
|`#xxx0A`|BGA LAYER2 (nanasi, Angolmois(2.0a2+), Sonorous, BGAEncAdv, pomu2)|

|origin:|BM98de|
|---|---|

**support:** BM98de, DDR, MW, bemaniaDX, RDM, BMEV, WAview, nanasi, ruvit, uBMplay,
BMSE, Angolmois, Sonorous, BGAEncAdv, pomu2

- 裁剪并显示图像文件的一部分。值以像素为单位。

|parameter|summary|remarks|
|---|---|---|
|`x1 y1`|裁剪起始位置|从原始图像裁剪范围的**左上角**坐标|
|`x2 y2`|裁剪结束位置|从原始图像裁剪范围的**右下角**坐标|
|`dx dy`|绘制起始位置|绘制区域的**左上角**坐标|

- 示例：

|原始图像: `#BMP01 320x64.bmp`|裁剪部分:|显示部分:|
|---|---|---|
|![bga_320-64](https://hitkey.nekokan.dyndns.info/cmd/bga_320-64.png)|从 (64, 64) 到 (128, 128) 裁剪。 `#BGA02 01 64 64 128 128 0 0`|在画布的 (96, 96) 处显示。 `#BGA03 01 64 64 128 128 96 96`|

以下 URL 是 BMSE 的操作示例：

1. **部分裁剪:** <https://hitkey.nekokan.dyndns.info/bmse_help_full/Capture/bmse_bga_pre4.png>
2. **部分显示:** <https://hitkey.nekokan.dyndns.info/bmse_help_full/Capture/bmse_bga_pre5.png>
3. **BMSE 中的 `#BGA` 定义:** <https://hitkey.nekokan.dyndns.info/bmse_help_full/Capture/bmse_bga_input_demo.gif>

- BMSE 的 `#BGA`-UI 不太好用，这是因为 `#BGA` 命令的规范本身不太好。
- 通常坐标从原点向右下方向延伸。
- 但如果指定了负坐标，则会向左上方向延伸。
  - **BM98de 的绘制方式:**
    引自 *DTX file format specifications*: <http://dtxmania.net/attach/qa%255Fdtx%255Fspec%255Fe.bga%255Fdesc%255Fe.jpg>
    如果 `x1` 或 `y1` 为负数，则进行以下处理：
    1. 负数值被设为 `0`（舍去）。
    2. 舍去后绘制区域的左上角显示在 (`dx`, `dy`)。
  - BMSE 不支持负坐标。
- 裁剪后的图像片段可以像普通 `#BMP` 一样设置到通道 `#xxx04/06/07` 和 `0A`。
- 定义为 `#BGA` 的图像也可以在 `#SWBGA` 的定义中使用（如果被支持的话）。
- 如果同一索引在 `#BMP` 和 `#BGA` 中都有定义，则 `#BGA` 优先。

**示例 3:**
`` `#BMP01 640x480.bmp` `#BGA01 01 178 99 434 355 0 0` `#BGA02 01 179 98 435 354 0 0` ``
`` `#BGA03 01 179 97 435 353 0 0` `#BGA04 01 180 96 436 352 0 0` `#BGA05 01 181 95 437 351 0 0` `#00104:0102030405` ``
— `#BGA01-05` 在支持 `#BGA` 的实现中显示。`#BMP01` 不显示，仅被 `#BGA` 引用。
（或者「EOF 附近的行」被采用？规范未说明，需要测试。）
在不支持 `#BGA` 的实现中，如果 `#BMP02-05` 未定义，`#BMP01` 会显示一会儿然后立即消失。

- ~~Angolmois 不允许 `#BGA` 引用同一索引的 `#BMP`。（例如上述 `#BGA01`）~~
  [Angolmois e5cea53a2cbd 或之后] 此不兼容性已修复。

### `#BGA` 的兼容性

或者说 `#BGA` 的不兼容性。

|Name|`#BGA`|`#BMP`|`0 0 1 1`|Align|1digit|Overlap|Nega|max-X|max-Y|
|---|---|---|---|---|---|---|---|---|---|
|BM98de|00-ZZ|0-255 [1]|2x2 [2]|Yes|Yes|Yes|Yes|4095|8190|
|DDR|01-ZZ|0-255 [1]|2x2 [2]|Yes|Yes|Yes|Yes|4095|8190|
|pomu2|01-ZZ|1-255 [1]|1x1|Yes|Yes|Yes|Yes|?|?|
|MixWaver|01-ZZ|00-03 [3]|1x1|Yes|Yes|No|Yes|638|478|
|bemaniaDX|00-ZZ [4]|01-FZ [5]|1x1|Yes|No|Yes|Yes|8128|8191|
|Rhythm-it|00-ZZ|00-FZ|1x1|No|Yes|Yes|Yes|8127|8190|
|BMEV|01-ZZ|00-FZ|1x1|No|No|Yes|Yes|70000+|65535|
|WAview|01-ZZ|00-ZZ [6]|1x1|Yes|Yes|Yes|Yes [7]|?|?|
|nanasi|00-ZZ [4]|00-ZZ|1x1|Yes|No|No|Yes|2147|2147|
|ruv-it!|00-ZZ|00-ZZ|1x1|No|Yes|Yes|Yes [7]|2147|2147|
|Angolmois|00-ZZ|00-ZZ|1x1|Yes|No|Yes [10]|Yes|16383|[y1] 32767|
|Sonorous|00-ZZ|00-ZZ|1x1|Yes|Yes [11]|Yes [11]|Yes|16383?|[y1] 32767?|
|BMSE|01-ZZ|01-ZZ [5]|1x1|No|Yes|Yes|No|32767|32767|
|uBMplay -1.4.6|00-ZZ|01-ZZ [5]|1x1|No|Yes|Yes|No|4031|8190|
|uBMplay 1.5.0+|00-ZZ|00-ZZ|1x1|No|Yes|Yes|Yes|4031|8190|
|BGAEncAdv|00-ZZ [4]|00-ZZ [8]|1x1|Yes [9]|Yes|Yes|Yes [7]|70000+|70000+|

- 表中的 `#BGA` 列表示 `#BGA` 命令可用的索引范围。
  - `01-ZZ` 组无法将 `#BGA00` 用作 `#BMP00` 的替代。
  - BMEV 和 WAview 无法显示默认的 miss 图像，暂归为 `#01-ZZ` 组。
  - 注 [4]: 如果至少有一个图像对象存在于通道 `04`、`07` 或 `0A`（如果支持）中，则可以使用 `#BGA00`。
- 表中的 `#BMP` 列表示 `#BGA` 命令可引用的 `#BMP` 索引范围。
  - 注 [1]: 必须指定「十六进制索引 `00-FF` 转换为十进制后的值」。不能使用三十六进制索引 `[G-Z]`。因此可引用的图像最多 256 个文件。
  - 注 [3]: 可引用的 `#BMP` 仅有 `00`、`01`、`02`、`03` 四个槽。引用其他索引也不显示任何内容。
  - 注 [5]: 无法引用 `#BMP00`。
  - 注 [6]: 指定 `100` 以上 `1295` 以下的十进制整数时，会被解释为「三十六进制的 `#BMP` 定义号转换为十进制后的值」。
    "`#BGAxx 099 ...`" 和 "`#BGAxx 009 ...`" 引用 `#BMP09`。
  - 注 [8]: 视频文件的坐标指定也有效。
- "`0 0 1 1`" 列表示 `x1 y1 x2 y2` 参数的处理方式。
  - 大多数应用程序由此裁剪「宽 1 pixel，高 1 pixel」的区域。
  - 注 [2]: BM98de 和 DDR 由此裁剪「宽 2 pixel，高 2 pixel」的区域。
- Align 列表示对 `#BGA` 中「连续空白」的宽容度。可作为分隔符则为 "Yes"。
  - 注 [9]: 也可使用逗号 (U+002C) 代替空白。两者可混用，但如果两个以上逗号连续（例如 "`0,,0`"），BGAEncAdv 会崩溃。
- 1digit 列表示对类似 `#BMP` 索引指定的宽容度。"`#BGAxx 1 ...`" 作为对 `#BMP01` 的引用有效则为 "Yes"。
  - 例如 *風のたどり着く場所 (Extra-Mix)* (猫蟹丸, 2001-12-22) (<https://web.archive.org/web/*/http://www.knm.mydns.jp:32100/download-area/ed_wind_hc.lzh>)
  - 注 [11]: Sonorous 0.1.0-pre (2013-11-18) 已修复此不兼容性。
- Overlap 列表示对 `#BMP` 和 `#BGA` 定义号冲突的宽容度。`#BGA` 优先显示则为 "Yes"。

**示例 4:**
`` `#TITLE BMP vs BGA` `#BPM 120` `#BMP01 1.bmp` `#BMP02 2.bmp` ``
`` `#BGA01 02 0 0 255 255 0 0` `#BGA02 01 0 0 255 255 0 0` `#00104:0102` ``
— 确实存在一些这样模糊的示例。但 BM98de 规范未规定冲突时的处理方式。
由于不保证 `#BGA` 优先显示，我们应避免模糊的描述。
~~此外，直接重叠会导致 Angolmois 崩溃。（例如 "`#BGA01 01 ...`"）~~
注 [10]: Angolmois e5cea53a2cbd (2013-08-15) 已修复此不兼容性。

- Nega 列表示对负坐标的宽容度。我所知的示例只有一个 (<https://web.archive.org/web/*/http://mru.hp.infoseek.co.jp/bm98_beta1.zip> newbgatest.bms)。
  注 [7]:
  - WAview 可能可以显示负坐标。但 WAview 的坐标处理过于独特，不具兼容性。
  - ruvit 对负坐标进行类似平铺的处理。因此与 BM98de 的显示不同。
  - BGAEncAdv 的显示与 BM98de 略有不同。
    - 如果 `x1`、`y1`、`x2` 或 `y2` 为负数，`dx` 和 `dy` 的值会加倍。（bug?）
    - 如果 `dx` 或 `dy` 的值为负数，向左或向上超出的部分不会被裁剪。这与 BM98de 和 uBMplay 1.5.0（或更高版本）的行为相同。详情请参见 BGAEncAdv 的 sample.bms。
- max-X 和 max-Y 列表示可准确显示的坐标上限。单位为像素。
  - 这些值是**在显存 128MB** 环境下的调查结果。高性能计算机可能产生不同结果。
  - 若引用大于这些值的图像，可能导致以下问题：不显示、显示异常、**计算机无响应**等。
  - *FELYS* 的差分 (<https://manbow.nothing.sh/event/event.cgi?action=More_def&num=5&event=16>) 使用 `#BGA` 滚动宽度 3236 pixel 的图像文件（`#066-073`）。
    此差分为 BM98de & DDR & pomu2 专用。虽然可以改写为其他格式，但 MixWaver 无法显示。

## `#@BGA[00-ZZ] <[#BMP-index]> <sx> <sy> <w> <h> <dx> <dy>`

|channel:|remarks|
|---|---|
|`#xxx04`|BGA BASE|
|`#xxx06`|BGA POOR|
|`#xxx07`|BGA LAYER|
|`#xxx0A`|BGA LAYER2 (nanasi, Sonorous, BGAEncAdv)|

|origin:|nanasi|
|---|---|
|support:|nanasi, Sonorous(parsing-only), BGAEncAdv|

- 裁剪并显示图像文件的一部分。
- 此命令是 `#BGA` 的语法糖，内部处理与 `#BGA` 相同。

|parameter|summary|remarks|
|---|---|---|
|`sx sy`|裁剪起始位置|从原始图像裁剪范围的**左上角**坐标|
|`w h`|裁剪距离|从裁剪起始位置开始的**宽度和高度**|
|`dx dy`|绘制起始位置|绘制区域的**左上角**坐标|

- 当裁剪尺寸已知时，`#@BGA` 比 `#BGA` 更便于编写。

## `#POORBGA <[0-2]>`

|origin:|nanasi|
|---|---|
|support:|nanasi, pomu2, Sonorous(parsing-only)|

- 定义 POOR (MISS) 图像的显示方式。

|||
|---|---|
|`0`:|默认。miss 时，将显示切换到通道 `#xxx06` 数秒的传统类型。|
|`1`:|在 BGA LAYER 之上显示 `#xxx06`（miss 时）。|

|summary|channel|remarks|
|---|---|---|
|BGA POOR (MISS)|`#xxx06`|top level|
|BGA LAYER-2|`#xxx0A`|second level|
|BGA LAYER|`#xxx07`|third level|
|BGA BASE|`#xxx04`|lowest level|

例如 *SigSig*（来自 *beatmaniaIIDX*）：<https://www.youtube.com/watch?v=0UOx8sxzYgg>

- miss 时，通道 `#xxx06` 覆盖在通常的通道 `#xxx04/07` 的上层显示。
- 结果，「奔跑跪地的少女」图像序列会显示——就像另一种 LAYER 一样。
- 此示例来自 beatmaniaIIDX 而非 BMS，但可以实现类似效果。

|||
|---|---|
|`2`:|隐藏。miss 时，显示不切换到通道 `#xxx06`。继续显示通常的图像序列。|

- uBMplay 有类似 `#POORBGA` 的功能。uBMplay 可以通过以下菜单更改图像的显示方式：

|label|display channels|
|---|---|
|NORMAL|`#xxx04` + `#xxx07`|
|ALWAYS MISS|`#xxx06`|
|OFF|（隐藏）|
|BASE ONLY|`#xxx04`|
|LAYER ONLY|`#xxx07`|

此功能在测试游玩时非常有用。

- BGAEncAdv 有类似 `#POORBGA` 的功能。（命令行参数 "`-l <[0-2]>`"）

|||
|---|---|
|`-l 0`|Miss-layer only|
|`-l 1`|Show all layer // 等同于 "`#POORBGA 1`"。同时应用 "ALWAYS MISS"。|
|`-l 2`|Standard (default)|

## `#SWBGA[01-ZZ] <fr>:<time>:<line>:<loop>:<a,r,g,b> <pattern>`

|channel:|`#xxxA5`|
|---|---|
|origin:|nanasi|
|support:|nanasi, Sonorous(parsing-only)|
|caution:|**这是试验性实现的命令。今后规格可能发生变化。**|

- 响应按键的图像序列。这是按键绑定 LAYER 动画。

- `fr`: 帧率。每帧所需时间（毫秒）。如果是 60 FPS，则指定 17。（Frames Per Second = 1000 / 60）
- `time`: 动画总持续时间（ms）。指定 0 时，动画在定义的 line 持续按下期间持续显示。
- `line`: 指定应用的目标 line 通道。取值范围为 `[11-19]` `[21-29]`。
- `loop`: 动画完成且显示时间仍有剩余时的处理方式。

|||
|---|---|
|`0`:|保持最后一帧。「不循环」|
|`1`:|倒回动画。「循环」|

- `a,r,g,b`: Alpha channel, Red, Green, Blue。取值范围 `[0-255]`。分隔符为逗号。
- `pattern`: 使用 BMS MAIN DATA 通道消息的记法描述动画模式。（例如 `01020304`）
  可使用 `#BMP`、`#EXBMP`、`#BGA`、`#@BGA` 的各索引。
  （这些索引在 `#BMP` 族之间共享，本质上是同一的。）
  此命令独立于 BMS 的播放进程，因此不存在分解能。
  与普通图像命令不同，`00` 直接显示 `#BMP00`（`/#EXBMP00`/`#BGA00`/`#@BGA00`）的图像。

||BMS code|remarks|
|---|---|---|
|通常记法:|`#00404:00110022`|`00` 是休止符|
|SWBGA 记法:|`#swbga01 100:400:16:0:255,255,255,255 00110022`|`00` 是 `#BMP00`|

因此，如果有不应显示的帧，我们需要使用透明图像或空 `#BMP` 索引代替 `00`。

- 目前，多个 BGA 切换同时应用时的行为未作规定。
- 根据测试，按键绑定动画是排他性的，新输入优先显示。

示例 1:

```text
#SWBGA01 100:400:16:0:255,255,255,255 01020304
#070A5:01
```

- `#070` 之后，通道 `#xxx16`（1P 侧 SCRATCH）被输入时显示。
- 帧率 10 FPS。（100 = 1000/10）
- 持续 400ms。
- 不循环。
- 时间线如下：
  1. 按键时，显示 `#BMP01`。
  2. 100ms 后，显示 `#BMP02`。
  3. 200ms 后，显示 `#BMP03`。
  4. 300ms 后，显示 `#BMP04`。
  5. 400ms 后，按键绑定动画显示结束。
- `#BMP01-04` 图像中，「完全不透明且完全白色部分」变为透明，显示通常的 BMS 图像序列。

示例 2:

```text
#SWBGA02 50:0:11:1:255,255,255,255 01020304
#010A5:02
```

- `#010` 之后，通道 `#xxx11`（1P 侧 KEY1）被输入时显示。
- 帧率 20 FPS。（50 = 1000/20）
- 持续时间为「按住 KEY1 期间」。
- 循环。
- 时间线如下：
  1. 按键时，显示 `#BMP01`。
  2. 50ms 时，如果按键状态仍保持，显示 `#BMP02`。
  3. 100ms 时，如果按键状态仍保持，显示 `#BMP03`。
  4. 150ms 时，如果按键状态仍保持，显示 `#BMP04`。
  5. 200ms 时，动画倒回开头。
- `#BMP01-04` 图像中，「完全不透明且完全白色部分」变为透明，显示通常的 BMS 图像序列。

## `#ARGB[01-ZZ] <a,r,g,b>`

|channel:|summary|
|---|---|
|`#xxxA1`|BGA BASE 绘制时 aRGB 更改|
|`#xxxA2`|BGA LAYER 绘制时 aRGB 更改|
|`#xxxA3`|BGA LAYER2 绘制时 aRGB 更改|
|`#xxxA4`|BGA POOR 绘制时 aRGB 更改|

|origin:|nanasi|
|---|---|
|support:|nanasi, Sonorous(parsing-only), BGAEncAdv|

- 定义应应用于 BGA 各层的 aRGB 值。分隔符为逗号。

|param|range|remarks|
|---|---|---|
|`a`|`[0-255]`|alpha|
|`r`|`[0-255]`|red|
|`g`|`[0-255]`|green|
|`b`|`[0-255]`|blue|

- `#ARGB` 的值与不透明度通道 `#xxx0B-0E` 共享。

|channel|summary|remarks|
|---|---|---|
|`#xxx0B`|BGA BASE's Opacity|透明 « `[01-FF]` » 不透明|
|`#xxx0C`|BGA LAYER's Opacity|透明 « `[01-FF]` » 不透明|
|`#xxx0D`|BGA LAYER2's Opacity|透明 « `[01-FF]` » 不透明|
|`#xxx0E`|BGA POOR's Opacity|透明 « `[01-FF]` » 不透明|

- `#xxx0B-0E` 只能更改透明度，而 `#ARGB` 也能处理透明色的更改。
- `#EXBMP` 是应应用于各文件的 aRGB（单数乘）。
- `#ARGB` 是应应用于各 BGA 通道的 aRGB（整体乘）。
- BGAEncAdv 也可以使用一个或多个空格 (U+0020) 作为分隔符。
- BGAEncAdv 也可以对**视频文件**应用 `#ARGB`。（请参考 sample.bms）
