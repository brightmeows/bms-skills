# 头部命令：音频与 BPM

> 来源：[BMS command memo (JP)](https://hitkey.nekokan.dyndns.info/cmdsJP.htm#HEADER)

## #BPM `n`

|          |          |
| -------- | -------- |
| **来源** | BM98     |
| **支持** | 几乎全部 |

- 定义曲子的 BPM（Beats-Per-Minute）。作为谱面滚动速度的初始值。
- 省略命令时应用默认值 `130`。但正经谱面不应省略 `#BPM`。
  - BMS 规范将 `130` 定为默认值，但部分实现未遵守此规范。
  - nanasi 应用 `150`。
  - fgt++ 应用 `30`。
  - nazo、nazoZZ、BMSC、BMSE、iBMSC、BMSV 应用 `120`。
  - fgt#、pomu2、nBMplay、o2play 应用 `0`，即谱面不滚动。
  - LR2 中 BPM 显示栏的槽位会高速旋转，谱面不滚动。
- 多数软件中，值 `n` 可指定小数。
  - 以下软件不反映小数值：BMSV、BMS2WAV、otama、Galli、KbMediaPlayer (bmse.kpi)、fgt。

### BPM 变更（基本形式）

|          |           |
| -------- | --------- |
| **通道** | `#xxx03`  |
| **范围** | `[01-FF]` |
| **来源** | BM98      |
| **支持** | 几乎全部  |

- 可用十六进制指定 `[1-255]` 的整数值。`00` 表示[休止符](https://en.wikipedia.org/wiki/Rest_%28music%29)。

| 例如          | 说明                                     |
| ------------- | ---------------------------------------- |
| `#00103:00FE` | BPM 在 `#001` 中间变为 254。（FE = 254） |

- BMSE 中，如果通道 `#xxx03` 的对象位置叠放了通道 `#xxx08` 的对象，两者均会保存。

| 例如          | 说明                 |
| ------------- | -------------------- |
| `#BPM01 256`  | 如何解释取决于实现。 |
| `#00103:00FF` |                      |
| `#00108:0001` |                      |

- iBMSC 中两者均保存，但会友好地给出警告：![ibmsc-error-mark](https://hitkey.nekokan.dyndns.info/cmd/ibmsc-error-mark.png)

## #BPM`[01-ZZ]` `n`

|          |                                           |
| -------- | ----------------------------------------- |
| **通道** | `#xxx08`                                  |
| **来源** | bemaniaDX version 0.72.0768（2000-07-15） |

**索引范围：**

| 范围      | 说明                                                                                                                                                                                               |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[01-99]` | bemaniaDX（来源），仅十进制索引，但 `#xxx08:[01-2R]`                                                                                                                                               |
| `[01-73]` | BGAEnc（来源），三十六进制索引 255 个，但 `#xxx08:[01-FF]`                                                                                                                                         |
| `[01-FF]` | MGQ（来源）、BM98de（`#BPM01` 有 bug）、DDR、RDM、MW（`#BPMFF` 有 bug）、nazo、Mac?、~~Aqua?~~、GALLI、WAview、~~BGAenc~~、KbMedia、nazoZZ、O2mania                                                |
| `[01-FZ]` | BMEV（来源）                                                                                                                                                                                       |
| `[01-ZZ]` | nanasi（来源）、LR2、fgt++、fgt#、ruvit、pomu2、uBMplay、PMSee-V、BMSE、iBMSC、DTXC、bme2wav、bmx2wav、Aqua (?)、IIDXv、HDX、Angolmois、Sonorous、BGAEncAdv、TechnicalGroove、diff、in_bm2、O2play |

- BPM 可在任意位置变更。（本文中将此“扩展的 BPM 变更命令”简称为 exBPM。）
- 该命令主要定义小数值或超过 255 的 BPM 值，即定义**实数值 BPM**。

| 例如              |
| ----------------- |
| `#BPMII 256`      |
| `#BPMzz 155.5`    |
| `#00108:IIzzIIzz` |

- BMSE 无法复用已定义的滚动对象。如果在 BMSE 中保存，上例会被改写为：

| 例如              |
| ----------------- |
| `#BPM01 256`      |
| `#BPM02 155.5`    |
| `#BPM03 256`      |
| `#BPM04 155.5`    |
| `#00108:01020304` |

- 因此在 BMSE 中最多只能读写 1295 个 exBPM 对象。
- 若需要无限设置 1295 种值，则必须使用 BMSE 以外的手段。
- （值虽限于 255 种，但通道 `#xxx03` 的对象可无限读写。）
- BMSE 会尽可能支持 `[01-FF]`、`[01-FZ]`、`[01-ZZ]` 的所有实现。
  - 若使用的实数 BPM 对象少于 255 个，BMSE 将以 `[01-FF]` 定义 `#BPMxx` 索引。
  - 若使用的实数 BPM 对象多于 256 个，BMSE 将以 `[01-ZZ]` 定义 `#BPMxx` 索引。
- iBMSC 的定义槽限于 ~~255 个~~，可通过设置改为 1295 个。iBMSC 可复用已定义的滚动对象。
- 若定义负数，部分实现会反转滚动方向。（<https://www.youtube.com/watch?v=R4S7BKEB0vs>）
- 值的范围未在规范中定义，负 BPM 的解释取决于实现。

| 行为         | 实现                                                                                                                     |
| ------------ | ------------------------------------------------------------------------------------------------------------------------ |
| **逆流**     | bemaniaDX、BM98de、RDM、nazo、nazoZZ、O2mania、LR2、ruvit、fgt++、fgt#、uBMplay、PMSee-V、~~IIDXv~~、Angolmois、Sonorous |
| **自行解释** | bme2wav、bmx2wav                                                                                                         |
| **忽略**     | KbMedia、GALLI（仅忽略负号）、~~LR2 (partial)~~、nanasi、pomu2                                                           |
| **应用 130** | DDR、WAview、in_bm2                                                                                                      |
| **应用 0**   | iBMSC、DTXC                                                                                                              |
| **可编辑**   | BMSE                                                                                                                     |
| **就地终止** | BGAEncAdv、~~TechnicalGroove~~                                                                                           |

- 负 BPM 源于 ~~nazo~~ bemaniaDX 的 bug，并非要求规范。
- ruvit、fgt++、Angolmois、Sonorous 中即使滚动逆流，也能正常进入结果画面。
- 其他“支持负 BPM 的实现”可能需要按 ESC 键退出谱面。
- ~~LR2 基本不支持负 BPM，但著名的 kusofumen 似乎被例外地硬编码了。~~（例：*Sofa $15 -> $1*、*U9* 等。）
- 虽无义务实现反向滚动，但应考虑可能指定负值的情况。
- 相关文档：[Extended BPM - BMS Command](https://hitkey.nekokan.dyndns.info/exbpm-object.htm)

## #EXBPM`[01-ZZ]` `n`

|          |                                                                   |
| -------- | ----------------------------------------------------------------- |
| **通道** | `#xxx08`                                                          |
| **来源** | nanasi                                                            |
| **支持** | nanasi、pomu2、PMSee-V、Sonorous、BGAEncAdv、TechnicalGroove、HDX |

- 与 `#BPMxx n` 等价。
- 这是用于规避 BMSC bug 的别名。
- BMSC 会混淆 `#BPMxx n` 和 `#BPM n`，导致解析错误。

## #BASEBPM `n`

|          |                                                         |
| -------- | ------------------------------------------------------- |
| **来源** | LR                                                      |
| **支持** | LR（LR2 的 `#BASEBPM` 支持已停止？）~~TechnicalGroove~~ |

- 定义用作滚动速度标准值的 BPM。
- 滚动速度由“BPM 变更对象中的最大值”或 `#BASEBPM` 的值决定。
  - LR2 具有自动设置合适 HI-SPEED 选项的功能。
  - 使用此功能时，通常基于“BPM 变更对象中的最大值”决定 HI-SPEED 选项。
  - 但对于“仅在极短区间内急加速的谱面”，此功能会产生令人失望的结果。
  - 此时 `#BASEBPM` 可定义“方便的替代标准值”。

## #STOP`[01-ZZ]` `n`

|          |                                  |
| -------- | -------------------------------- |
| **通道** | `#xxx09`                         |
| **来源** | DDR Ver.0.50 beta2（2001-12-24） |

**索引范围：**

| 范围      | 说明                                                                                                                                                               |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `[01-99]` | pomu2（来源），仅十进制索引，但 `#xxx09:[01-2R]`                                                                                                                   |
| `[01-73]` | RDM（来源），三十六进制索引 255 个，但 `#xxx09:[01-FF]`                                                                                                            |
| `[01-73]` | fgt（来源），三十六进制索引 255 个，但 `[41, 45-4F, 4H]` 有 bug                                                                                                    |
| `[01-FF]` | DDR（来源）、nazo、nazoZZ、GALLI、WAview、PMSee-V、~~pomu2?、O2mania~~                                                                                             |
| `[01-ZZ]` | nanasi（来源）、LR2、ruvit、fgt++、fgt#、BMSE、uBMplay、in_bm2、bme2wav、BGAenc、bmx2wav、iBMSC、IIDXv、HDX、Angolmois、Sonorous、BGAEncAdv、TechnicalGroove、diff |

- 定义 DDR 类型的停止序列。
- 停止序列是在一定时间内停止谱面滚动的对象。
- 经过定义的时间后谱面滚动将恢复。
- 以 4/4 拍中的“全音符的 1/192 音符”为单位 1。
- 即 `192nd note * 1 === #STOPxx 1`。
- 该值不应受通道 `#xxx02`（小节长度变更）的影响，因为“音符”是与小节长度无关的单位。

| 例如          | 说明                                                           |
| ------------- | -------------------------------------------------------------- |
| `#STOP11 96`  | 值 `96` 即 `192nd note * 96`。                                 |
| `#STOP22 96`  | `#001` 与 `#002` 的停止时间相同。                              |
| `#00109:0011` | 旧版 WAview 和旧版 pomu 存在停止时间依赖通道 `#xxx02` 的 bug。 |
| `#00202:0.5`  |                                                                |
| `#00209:0022` |                                                                |

- 由于以音符为基准，停止时间取决于“停止时的 BPM 值”。

| 停止 1 秒    | 停止 0.5 秒  | 停止 0.25 秒 |
| ------------ | ------------ | ------------ |
| `#BPM 60`    | `#BPM 120`   | `#BPM 240`   |
| `#STOP33 48` | `#STOP44 48` | `#STOP55 48` |
| `#00109:33`  | `#00109:44`  | `#00109:55`  |

- 若多个对象位于同一时间线上，STOP 对象应被最晚评估。
  - STOP 对象与音频对象在同一时机时，先发声，后停止滚动。
  - STOP 对象与 BPM 对象在同一时机时，先变更 BPM，后停止滚动。
- 几乎所有实现都遵循此行为，但 ~~pomu2~~ 部分实现可能产生不同结果。
- 理论上，小 BPM 与大 STOP 组合时，停止时间可无限延长。
  - BPM `1.250001` + STOP `2147483647`
  - 停止时间约 68 年 35 天 2 小时 50 分。（但除 uBMplay 外的应用会强制终止。）
- 若定义负值，部分实现会跳过谱面滚动。

| 行为         | 实现                                                                                          |
| ------------ | --------------------------------------------------------------------------------------------- |
| **跳过**     | ~~RDM~~、nazo、nazoZZ、fgt、fgt++、fgt#、pomu2、uBMplay、IIDXv、HDX、Angolmois (Rust)、BGAEnc |
| **警告**     | PMSee-V                                                                                       |
| **滑动**     | bme2wav、bmx2wav                                                                              |
| **应用 -1**  | LR2（轻微跳过）、RDM                                                                          |
| **应用 0**   | DDR、iBMSC                                                                                    |
| **应用 0.1** | nanasi                                                                                        |
| **忽略**     | ~~LR2、nanasi~~、WAview、in_bm2、ruvit、Angolmois (C)、Sonorous                               |
| **强制终止** | ~~ruvit~~                                                                                     |
| **取绝对值** | BGAEncAdv、TechnicalGroove                                                                    |

- 部分实现解释小数值。

| 行为                     | 实现                                                                                              |
| ------------------------ | ------------------------------------------------------------------------------------------------- |
| **应用小数值**           | ~~RDM~~、ruvit、nanasi、fgt#、iBMSC、IIDXv、HDX、BGAEncAdv、TechnicalGroove                       |
| **取整（小数舍去）**     | DDR、WAview、in_bm2、bme2wav、LR2、fgt++、Angolmois、Sonorous、RDM、BGAEnc、GALLI、pomu2、PMSee-V |
| **取整（小数四舍五入）** | nazo、nazoZZ、uBMplay、BMSE                                                                       |
| **严重偏差**             | ~~GALLI、pomu2、PMSee-V~~                                                                         |
| **中止转换**             | bmx2wav                                                                                           |

- 本质上 `#STOP` 不应接受负数和小数。[注：个人见解]
- 禁止输入非法值的 BMSE & iBMSC 很聪明。
- BMSE 无法复用已定义的停止对象。iBMSC 可以。
  - 因此在 BMSE 中最多只能读写 1295 个 STOP 对象。
  - 若需无限设置 1295 种值，则必须使用 BMSE 以外的手段。
- 若使用的 STOP 对象少于 255 个，BMSE 以 `[01-FF]` 定义 `#STOPxx` 索引。
- 若使用的 STOP 对象多于 256 个，BMSE 以 `[01-ZZ]` 定义 `#STOPxx` 索引。
- 相关文档：[STOP Sequence - BMS Command](https://hitkey.nekokan.dyndns.info/exstop.htm)

## #STP `xxx.yyy` `zzzz`

|          |                                |
| -------- | ------------------------------ |
| **来源** | bemaniaDX                      |
| **支持** | bemaniaDX、Angolmois、Sonorous |

- 定义 bemaniaDX 类型的 STP 序列。

| 参数   | 范围                                             | 说明                   |
| ------ | ------------------------------------------------ | ---------------------- |
| `xxx`  | `[000-399]`（Angolmois & Sonorous: `[000-999]`） | 小节                   |
| `yyy`  | `[000-999]`                                      | 小节内位置（yyy/1000） |
| `zzzz` |                                                  | 停止时间（ms）         |

- 下例总计停止滚动 4500ms：

| 例如                | 说明                                        |
| ------------------- | ------------------------------------------- |
| `#STP 001.500 500`  | `#001` 中间位置停止 500ms。                 |
| `#STP 001.500 4000` | 定义可重复于同一位置，增加停止时间 4000ms。 |

- 若不使用 bemaniaDX 类型的 STP 序列，则无需编写此命令。
- 虽未写入规范，详细说明如下：
  - `xxx` 和 `yyy` 须以 3 位数字书写，不允许省略前导零。
  - `xxx` 和 `yyy` 之间须以句点分隔。
  - `yyy` 和 `zzzz` 之间须以全角/半角空格或制表符分隔，分隔符须为 1 个以上 7 个以下字符。
  - 若省略句点和 `yyy`，则视为 `#STP xxx.000 zzzz`。
  - `zzzz` 后可书写非数字内容，解析时视为注释。但内部处理不明确，不应期待被丢弃。
  - “位置” `yyy` 看似被 1000 等分，但 `960` 以上的值实质上被忽略，此 bug 甚至可能导致冻结。
  - bemaniaDX 不支持 `#400` 以上的小节。若以与其他实现相同的方式编写谱面，bug 会增加。
  - `#STP` 语句大量重复时将被忽略，但规范未写明可重复的上限。
- Angolmois & Sonorous 的 STP 序列比 bemaniaDX 的更符合预期行为。

## #WAV`[00-ZZ]` `soundfilename`

| 通道        | 说明              | 支持                                                                                                                                                                                                                                                  |
| ----------- | ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `#xxx01`    | BGM（多行）       | 几乎全部                                                                                                                                                                                                                                              |
| `#xxx11-19` | 1P Visible Note   | 大部分（`17` 到 `19` 部分支持）                                                                                                                                                                                                                       |
| `#xxx21-29` | 2P Visible Note   | 大部分（`27` 到 `29` 部分支持）                                                                                                                                                                                                                       |
| `#xxx31-39` | 1P Invisible Note | 大部分（`37` 到 `39` 部分支持）                                                                                                                                                                                                                       |
| `#xxx41-49` | 2P Invisible Note | 大部分（`47` 到 `49` 部分支持）                                                                                                                                                                                                                       |
| `#xxx51-59` | 1P Long Note      | RDM、DDR (Arrow mode only)、nazo、nazoZZ、WAview、in_BM2、bme2wav、o2mania、Aqua (?)、LR2、nanasi、ruvit、fgt++、fgt#、pomu2、BMSE、iBMSC、uBMplay、PMSee-V、bmx2wav、o2play、IIDXv、HDX、Angolmois、Sonorous、TechnicalGroove、diff（`57` 部分支持） |
| `#xxx61-69` | 2P Long Note      | RDM、DDR (Arrow mode only)、nazo、nazoZZ、WAview、in_BM2、bme2wav、o2mania、Aqua (?)、LR2、nanasi、ruvit、fgt++、fgt#、pomu2、BMSE、iBMSC、uBMplay、PMSee-V、bmx2wav、o2play、IIDXv、HDX、Angolmois、Sonorous、TechnicalGroove、diff（`67` 部分支持） |
| `#xxx97`    | BGM volume        | forgetalia                                                                                                                                                                                                                                            |
| `#xxx98`    | KEY volume        | forgetalia                                                                                                                                                                                                                                            |
| `#xxxD1-D9` | 1P 地雷           | nanasi、LR2、fgt++、GDAC2+774gsc、PMSee-V (v1.11.0+)、Angolmois、uBMplay (1.5.0+)、Sonorous、TechnicalGroove（`D7` 部分支持）                                                                                                                         |
| `#xxxE1-E9` | 2P 地雷           | nanasi、LR2、fgt++、GDAC2+774gsc、PMSee-V (v1.11.0+)、Angolmois、uBMplay (1.5.0+)、Sonorous、TechnicalGroove（`E7` 部分支持）                                                                                                                         |
| `#xxx10`    | 预留通道          | pomu2、Angolmois（指定 `--key-spec` 时为 Visible Note，否则为 BGM）、Sonorous(?)、TechnicalGroove(?)                                                                                                                                                  |
| `#xxx1A-1Z` | 1P Visible Ex     | pomu2、BMS List Sorter、bme2wav、bmx2wav、Angolmois（`--key-spec`）、Sonorous(?)、TechnicalGroove<br>作为 BGM：BM98、BMSV、nBMplay、PMSee-V（`1A-1F`）、BM98de、DDR、MW、MW2、RDM、ruvit、nazoZZ                                                      |
| `#xxx20`    | 预留通道          | pomu2、Angolmois（指定 `--key-spec` 时为 Visible Note，否则为 BGM）、Sonorous(?)、TechnicalGroove(?)                                                                                                                                                  |
| `#xxx2A-2Z` | 2P Visible Ex     | pomu2、BMS List Sorter、bme2wav、bmx2wav、Angolmois（`--key-spec`）、Sonorous(?)、TechnicalGroove                                                                                                                                                     |
| `#xxx30-6Z` | Inv / LN Ex       | 参照上述扩展支持情况                                                                                                                                                                                                                                  |
| `#xxxD0-EZ` | Landmine Ex       | Angolmois（`--key-spec`）、Sonorous、TechnicalGroove                                                                                                                                                                                                  |

**索引范围：**

| 范围             | 来源      | 支持                                                                                                                                                                                                                                                                       |
| ---------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[01-FF]` (255)  | BM98      | BM98、otama、nBMplay、BMSC、BMSV、bmse.kpi                                                                                                                                                                                                                                 |
| `[01-FZ]` (575)  | BM98de    | BM98de、DDR、RDM、MW、nazo、BMEV、bms2wav、BMSprinter                                                                                                                                                                                                                      |
| `[01-SF]` (1023) | PMSee-V   | ~~PMSee-V~~、SMB                                                                                                                                                                                                                                                           |
| `[01-ZZ]` (1295) | bemaniaDX | bemaniaDX、Mac、Aqua (?)、GALLI、WAview、in_bm2、bme2wav、nazoZZ、DXEmu、o2play、O2mania、LR2、nanasi、ruvit、fgt++、fgt#、pomu2、uBMplay、IIDXv、HDX、PMSee-V (v2.1.0+)、Angolmois、Sonorous、TechnicalGroove、GDAC2、BMSE、iBMSC、bmx2wav、woslicerII、woslicerIII、diff |
| `00`（地雷）     | nanasi    | nanasi、LR2、fgt++、GDAC2+774gsc、PMSee-V (v1.11.0+)、Angolmois、uBMplay (1.5.0+)、Sonorous、TechnicalGroove                                                                                                                                                               |

- 定义音频文件。**WAV** 文件几乎确定被支持。
- 支持的文件类型取决于实现。
- `#WAVxx` 可省略，省略时无声音。
- 可将单个文件定义到多个槽位。**多重定义**是非常重要的技巧。

| 例如             | 说明                               |
| ---------------- | ---------------------------------- |
| `#WAV01 aaa.wav` | 在 4 个槽位中定义 aaa.wav 的示例。 |
| `#WAV02 aaa.wav` |                                    |
| `#WAV03 aaa.wav` |                                    |
| `#WAV04 aaa.wav` |                                    |

### 多重定义

- **通常 `#WAV` 的同时发音数为 1。** 但 **嵌套多个 `#WAV` 索引可增加同时发音数。**

| 图片                                                  | 代码                                                                                                    | 说明                                                                  |
| ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| ![not_multidef_1](https://hitkey.nekokan.dyndns.info/cmd/bmse_material_multidef4.png) | `#00101:01` `#00101:01` `#00101:01` `#00101:01`                                                         | 此例实际播放的 aaa.wav 为 1 个。                                      |
| ![not_multidef_2](https://hitkey.nekokan.dyndns.info/cmd/bmse_material_multidef1.png) | `#00111:0100000000000000` `#00112:0001000000000000` `#00113:0000010000000000` `#00114:0000000100000000` | 此例中播放下一个 aaa.wav 时，前一个发音会被中断。中断时可能产生噪音。 |
| ![multidef_1](https://hitkey.nekokan.dyndns.info/cmd/bmse_material_multidef5.png)     | `#00101:01` `#00101:02` `#00101:03` `#00101:04`                                                         | 此例中实际播放的 aaa.wav 为 4 个。即 aaa.wav 的音量变为 4 倍。        |
| ![multidef_2](https://hitkey.nekokan.dyndns.info/cmd/bmse_material_multidef2.png)     | `#00111:0100000000000000` `#00112:0002000000000000` `#00113:0000030000000000` `#00114:0000000400000000` | aaa.wav 的声音不被中断。                                              |

- 实用示例：

| 1                                                           | 2                                                                | 3                                                           | 4                                                             | 5                                                   |
| ----------------------------------------------------------- | ---------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------- | --------------------------------------------------- |
| ![multidef_for_tremolo](https://hitkey.nekokan.dyndns.info/cmd/bmse_material_multidef8.png) | ![multidef_for_preventNoise](https://hitkey.nekokan.dyndns.info/cmd/bmse_material_multidef9.png) | ![multidef_for_prolong](https://hitkey.nekokan.dyndns.info/cmd/bmse_material_multidefa.png) | ![multidef_for_turnUpVol](https://hitkey.nekokan.dyndns.info/cmd/bmse_material_multidefb.png) | ![not_multidef](https://hitkey.nekokan.dyndns.info/cmd/bmse_material_multidef7.png) |

1. 防止颤音等产生的意外噪音。在安静音乐中这是不可或缺的技术。
1. 防止因时机误判产生噪音。对应用了深度混响的低音鼓等有效。
1. 将短音作为拟似长音播放，可减小归档体积。但可视对象应使用真正的长音，否则中途停止播放会让玩家失望。
1. 增加音量。但需注意削波。
1. 特意使用**非多重定义**的方法。例如：FINAL ATTACK RIDE D-D-D-DECADE // 刺耳。

### WAV 说明

- WAV 文件的量化比特率有 PCM 8/16/24bit 和 IEEE Float 16/32bit 在 BMS 中使用的实例。大部分为 PCM 16 位。
- WAV 文件的采样频率有 8000 到 48000 Hz 在 BMS 中使用的实例。大部分为 11025/22050/44100Hz。
  - *自动化工厂*（automation factory，<http://www.comeup.info/bofoon2007/automation.zip>）使用了
    8000/11025/12000/13275/16000/22050/24000/44100 Hz 的 WAV。
  - Angolmois 无法精确播放非 11025/22050/44100Hz 的采样频率，这是 SDL_mixer 的限制。
- 部分实现限制了可使用的 wav 长度。
  - LR2 禁止“定义了长于 60 秒的音频文件的谱面”注册到互联网排行榜。
  - LR2 还具有将 BMS 转换为 MP3 的功能，但如果 BMS 包含长于 60 秒的 wav，LR2 会禁止此功能。
  - 这些是应对侵犯版权 BMS 的对策（如破解商业游戏数据）。
  - 因此，LR2 用户隐含的习惯是，**长于 60 秒的音频文件已极少使用。**
- uBMplay 似乎不播放文件大小超过约 65500KB 的 wav。
  - （如果是 44100Hz 16bit 立体声，65500KB 相当于约 10 分钟。）
  - 这可能是编程语言的限制。
- BMS 不一定总是使用正常的 WAV。
  - 在 `<fmt >` 块之外附加了过多元信息（如 Loop 标签）的 WAV。
  - 特定波形切片软件（如旧版 woslicerII）有时会输出 **`<data>` 块为 0 字节**的 WAV。
  - 非线性 PCM 的 RIFF WAV（如前述的 *IEEE Float* 类型或后述的 mu-law 等）。
- 宽容的软件可能播放这些文件，但也存在返回错误的软件。

### OGG 说明

- 现代实现支持 **OGG**。（<https://en.wikipedia.org/wiki/Ogg>）

|          |                                                                                                                                                                                |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **来源** | DDR?                                                                                                                                                                           |
| **支持** | DDR、LR2、nanasi、ruvit、fgt++、fgt#、pomu2、nazoZZ、iBMSC、uBMplay、PMSee-V、o2play、bmx2wav、IIDXv（注）、HDX、Angolmois、Sonorous、TechnicalGroove、woslicerII、woslicerIII |

- 注：IIDXv 默认（~~HDX 可通过设置~~）缓存解码后的 WAV。请注意磁盘空间。
- [HDX v1.00 起] 缓存解码后 WAV 的设置已被删除。
- BMSE 可通过 DirectShow 滤镜播放 OGG。（这是非官方行为，官方不支持。）
  - 例如安装以下之一：
    [RadLight Ogg Media DirectShow filters](http://www.free-codecs.com/radlight_ogg_media_directshow_filter_download.htm)
    或 [Open Codecs](http://www.xiph.org/dshow/)
- OGG 是非常方便的格式，能以合适的尺寸提供高质量音频。
- 但播放时需要解码 OGG，与 WAV 相比需要更多内存。
- 因此有不少用户会自行将 OGG 全部转换为 WAV。
- 部分实现似乎无法播放很长的 OGG。（~~PMSee-V 等~~）（PMSee-V 2.1.2+ 已可播放。）
- 可能是我误解——在 64-bit OS 上编码的 OGG 在 32-bit OS 上可能无法播放。

### 小众格式说明

- 部分实现支持 **MP3**。

|          |                                                                                                                                                                                                  |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **来源** | DDR?                                                                                                                                                                                             |
| **支持** | DDR、RDM、MW、bemaniaDX、nazo、nazoZZ、Mac、Aqua、otama、WAview、in_bm2、LR2、ruvit、pomu2、BMSE、iBMSC、uBMplay、MyO2、IIDXv（注 1）、HDX、Angolmois（注 2）、Sonorous（注 2）、TechnicalGroove |

- 注 1：IIDXv 默认（~~HDX 可通过设置~~）缓存解码后的 WAV。请注意磁盘空间。
- 注 2：取决于环境，可播放格式的限制较严格。
- 但播放时发声略有延迟，因此避免使用 MP3 的趋势较明显。
- 部分实现可将 **MIDI** 文件定义到 `#WAVxx` 并播放。

|              |                                                                                                                                  |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| **来源**     | DDR?                                                                                                                             |
| **支持**     | DDR?、LR2、IIDXv、HDX、TechnicalGroove                                                                                           |
| **条件支持** | BMSE、iBMSC（如果以下注册表键中有“Mid”则可播放）`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\MCI Extensions` |

- 但因其过于依赖硬件且发声延迟，MIDI 几乎不被使用。
  - IIDXv 通过软件端模拟 MIDI 演奏来规避这些问题。
  - 当然 IIDXv 也支持硬件音源。
  - IIDXv 还支持蜂鸣音源的 MIDI 演奏，但此功能在 ~~64bit Windows7 上无法使用。~~
  - HDX 0.99 起通过附带 "inpout32.sys" & "inpoutx64.sys" 驱动，可在管理员权限下使用蜂鸣音源 MIDI 播放。
- IIDXv 和 HDX 中，若 MIDI 定义为 `#WAV`，则只能在 BGM 通道 `#xxx01` 中播放。
  - 若有多个 MIDI-BGM 对象，只播放第一个，其余被忽略。
- 此外，还有支持 **ADPCM、AAC、MP2、mu-Law、XA（[WAV to XA](http://dtxmania.net/wiki.cgi?page=qa_xa)、
  [说明](http://dtxmania.net/wiki.cgi?page=qa_xa_e)）、FLAC、MOD（文件格式）** 等的实现。
  - 但考虑到可移植性问题，实际只使用 WAV 和 OGG。

### 替代搜索

- 现代应用程序实现了 **扩展名替代搜索**。

|          |                                                                                                                                                     |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| **来源** | DDR                                                                                                                                                 |
| **支持** | bemaniaDX、DDR、in_bm2、LR2、nanasi、ruvit、fgt++、fgt#、nazoZZ、pomu2、uBMplay、PMSee-V、bmx2wav、IIDXv、HDX、Angolmois、Sonorous、TechnicalGroove |

1. 首先以 BMS 中定义的文件名原样搜索文件。
1. 若未找到，则搜索“具有相同基本名但文件类型不同的文件”。此时搜索的文件类型优先级取决于实现。
1. 若找到则应用该文件。

- 例如：`#WAV01 sample.wav`
  - bemaniaDX 中按以下顺序搜索：`sample.wav` » `sample.mp3`
  - DDR 中按以下顺序搜索：`sample.wav` » `sample.mp3` » `sample.ogg`
  - ~~uBMplay 中若有多个同基本名的文件，只应用最先找到的。~~
    - [uBMplay 1.4.6 及更早] 若存在 `sample.bmp`，会比 `sample.ogg` 更早匹配。
    - [uBMplay 1.5.0 起] 此问题已修复。
  - LR2 中若定义了相对路径且指定目录不存在文件，则搜索其他目录。
  - bmx2wav 中会搜索“定义为 `sample.wav` 的 `sample.ogg`”，但不会搜索“定义为 `sample.ogg` 的 `sample.wav`”。
  - [Angolmois-Rust、Sonorous] 若存在 `sample.rar`，会比 `sample.wav` 更早匹配。
- 此功能非常便利，因为可以在不影响 BMS 的 MD5 的前提下兼顾 WAV 的编辑便利性和 OGG 的发布便利性。
  - 这在互联网排行榜中有利。
  - LR2IR 和 nazoIR 通过 MD5 检查 BMS 的同一性。
  - 假设谱面文件的音频定义如下：`#WAV01 foo.wav` `#WAV02 bar.wav`
  - WAV 版体积巨大，为便于发布可转换为 OGG。于是有“最高音质的 WAV 版 BMS”和“最佳音质的 OGG 版 BMS”。
  - OGG 版谱面文件的音频定义替换为：`#WAV01 foo.ogg` `#WAV02 bar.ogg`
  - 为满足“追求品质的玩家”和“追求最优的玩家”，WAV 版和 OGG 版同时发布。但由于定义部分被修改，谱面文件的 MD5 不再相同。
  - 若 WAV 版谱面文件与 OGG 版不同，则 IR 中会出现麻烦问题（注册分开，页面看起来像玩过的人很少）。
  - 但若使用替代文件搜索，创作者可对 WAV 版和 OGG 版使用同一谱面文件；玩家可自由选择 WAV 版和 OGG 版，并可自由将 OGG 转换为 WAV。
- 但此功能偶尔有误读文件的风险。

### 强调

- 部分应用可区分 BGM 和 KEY 的音量。

|          |                                                                   |
| -------- | ----------------------------------------------------------------- |
| **来源** | LunaticRave (?)                                                   |
| **支持** | LR2、fgt、fgt++、Angolmois、Sonorous、TechnicalGroove……（调查中） |

- 通过减弱 BGM 的播放音量，KEY note 相对得到强调。
- TechnicalGroove 强调 KEY note 波形的开始部分。
- 音量可能可分别控制。

## #WAVCMD `commandID` `#WAV-index` `value`

|          |                             |
| -------- | --------------------------- |
| **通道** | `#xxx11-49`                 |
| **来源** | MacBeat                     |
| **支持** | MacBeat、Sonorous（仅解析） |

- 播放 `#WAV` 读取的文件时，可选地定义音高、音量、音频播放时间。
- 这是 MOD 仿制品。（<https://en.wikipedia.org/wiki/MOD_%28file_format%29>）
- 为避免对不支持 `#WAVCMD` 的软件造成影响，包含此命令的 BMS 需将扩展名改为 **MBM**（MacBeatMod）。

**`commandID`**：指定要更改的参数。

| 值   | 说明                     |
| ---- | ------------------------ |
| `00` | 设置音高（pitch）        |
| `01` | 设置音量（volume）       |
| `02` | 设置音频播放时间（time） |

**`#WAV-index`**：指定要操作的音频。指定事先定义的 `#WAV` 索引。`[01-FF]` (255)。MacBeat 仅支持十六进制的 `#WAV` 索引。

**`value`**：以十进制整数指定要操作的“值”。值的含义取决于 `commandID`。

**音高（pitch）：** `[0-127]`，以十进制整数设置。这是相对音阶，默认值 `60`。

|              | A   | A#  | B   | C      | C#  | D   | D#  | E   | F   | F#  | G   | G#  |
| ------------ | --- | --- | --- | ------ | --- | --- | --- | --- | --- | --- | --- | --- |
| **Octave1**  |     |     |     | 0      | 1   | 2   | 3   | 4   | 5   | 6   | 7   | 8   |
| **Octave2**  | 9   | 10  | 11  | 12     | 13  | 14  | 15  | 16  | 17  | 18  | 19  | 20  |
| **Octave3**  | 21  | 22  | 23  | 24     | 25  | 26  | 27  | 28  | 29  | 30  | 31  | 32  |
| **Octave4**  | 33  | 34  | 35  | 36     | 37  | 38  | 39  | 40  | 41  | 42  | 43  | 44  |
| **Octave5**  | 45  | 46  | 47  | 48     | 49  | 50  | 51  | 52  | 53  | 54  | 55  | 56  |
| **Octave6**  | 57  | 58  | 59  | **60** | 61  | 62  | 63  | 64  | 65  | 66  | 67  | 68  |
| **Octave7**  | 69  | 70  | 71  | 72     | 73  | 74  | 75  | 76  | 77  | 78  | 79  | 80  |
| **Octave8**  | 81  | 82  | 83  | 84     | 85  | 86  | 87  | 88  | 89  | 90  | 91  | 92  |
| **Octave9**  | 93  | 94  | 95  | 96     | 97  | 98  | 99  | 100 | 101 | 102 | 103 | 104 |
| **Octave10** | 105 | 106 | 107 | 108    | 109 | 110 | 111 | 112 | 113 | 114 | 115 | 116 |
| **Octave11** | 117 | 118 | 119 | 120    | 121 | 122 | 123 | 124 | 125 | 126 | 127 |     |

**音量（volume）：** 以十进制整数（百分比）设置。指定大于 `100` 的值会导致削波。

**时间（time）：** 基于“毫秒 × 0.5”指定音频播放时间。以十进制整数指定。小于 `50`ms 的值几乎不可靠。`0` 表示原音长度。

**不使用 `#WAVCMD` 时的默认值：**

| 参数         | 默认值 | 说明               |
| ------------ | ------ | ------------------ |
| 音高         | `60`   | 相对音阶为 C6      |
| 音量         | `100`  | 相对音量为 100%    |
| 音频播放时间 | `0`    | 不改变音频播放时间 |

仅更改需要的参数。

- 典型示例：

| 例如               | 说明                            |
| ------------------ | ------------------------------- |
| `#wav0E aaa.wav`   | 必须为 `#WAVCMD` 定义音频文件。 |
| `#wavcmd 00 0E 61` | `#wav0E` 的原音音高升高半音。   |
| `#WAVCMD 01 0E 50` | `#wav0E` 的原音音量变为 50%。   |

- 可否对某个 `#WAV` 索引同时应用多个效果？
- MacBeatMod 规范书中未提及 `#WAVCMD` 的重复问题。（<http://harinezumi.s14.xrea.com/download/mbm.txt>）
- 我没有 MacBeat，无法测试细节。

## #EXWAV`[00-ZZ]` `[pvf]` `[pan]` `[volume]` `[frequency]` `soundfilename`

|          |                                                       |
| -------- | ----------------------------------------------------- |
| **通道** | `#xxx11-69`、`#xxxD1-E9`                              |
| **来源** | nanasi                                                |
| **支持** | nanasi、Sonorous（仅解析）~~TechnicalGroove（预定）~~ |

- 定义在 BMS 中播放的文件，并应用简单效果。
- `#EXWAVxx` 的索引与 `#WAVxx` 共用。

**`[pvf]`**：指定应操作哪些参数的标志。标志的书写顺序指示后续值对应的参数。不分大小写。

| 例如                                  | 说明                                                    |
| ------------------------------------- | ------------------------------------------------------- |
| `#EXWAV01 vfp -50 100 -10000 aaa.wav` | **v**olume=`-50`，**f**requency=`100`，**p**an=`-10000` |
| `#EXWAV02 P 500 aaa.wav`              | **P**an=`500`                                           |

（nanasi 可使用多个半角空格或制表符作为分隔符。）请按标志定义的顺序书写后续值。

> 注意：未指定任何标志时的行为不保证。

**`[pan]`**：`[-10000 - 10000]`。定义左右波形音量平衡。左端为 `-10000`，右端为 `10000`。默认值设为 `0`。此效果适用于立体声波形，也适用于单声道波形。

> 注意：`#EXWAVxx` 不支持音量增加，因此平衡的更改通过减少 L 或 R 的音量来实现。对于 L-R 平衡为 100%-0% 的波形，应用值 `10000` 后会完全无声。

**`[volume]`**：`[-10000 - 0]`。定义音量。`0` 为原声音量，`-10000` 实质为无声。默认值设为 `0`。这更像是衰减率而非音量。

**`[frequency]`**：`[100 - 100000]`。定义频率，单位为 Hz。实际上此参数操作音高。

**`soundfilename`**：定义要应用效果的音频文件名。与 `#WAVxx` 一样可定义相对路径。
（不能使用“MATERIALS”——nanasi 在 ver.1.00 中移除了对“MATERIALS”的支持。）路径长度限制在 260 字节以内。

## #CDDA `track-number`

|          |     |
| -------- | --- |
| **来源** | DDR |
| **支持** | DDR |

- 可将 CD-DA 用作 BGM。
- DDR 中“SYSTEM OPTION”的“CD-Syncro”设置也会生效。

| 例如      | 说明                                            |
| --------- | ----------------------------------------------- |
| `#CDDA 5` | 此例播放 CD 驱动器中音乐 CD 的第 5 轨作为 BGM。 |

## #MIDIFILE `midifilename`

|          |                                                                         |
| -------- | ----------------------------------------------------------------------- |
| **来源** | BM98                                                                    |
| **支持** | BM98、BMSV、DDR、RDM、Mac (?)、Aqua (?)、IIDXv、HDX、Sonorous（仅解析） |

- 可将 MIDI 文件作为 BGM 播放。
- 非常依赖硬件，或采用软件合成器时发声延迟。
- 由于这些原因，不推荐在 BMS 中使用 MIDI。
- 但热心支持 MIDI 的 IIDXv 是例外。
