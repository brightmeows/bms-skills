# 头部命令：长音与选项

> 来源：<https://hitkey.nekokan.dyndns.info/cmdsJP.htm>

## `#LNTYPE 1`

| 字段 | 值 |
| --- | --- |
| channel: | `#xxx51-69` |
| origin: | RDM |
| support: | RDM (1.3+), WAview, in_BM2, ruvit, Angolmois, Sonorous, o2mania |
| omissible: | RDM (1.7+), DDR (Arrow mode only), nazo, nazoZZ, WAview, in_BM2, bme2wav, Aqua (?), LR2, nanasi, ruvit, fgt++, fgt#, pomu2, BMSE, iBMSC, uBMplay, PMSee-V (1.1.6+), IIDXv, bmx2wav, o2play, HDX, Angolmois, Sonorous, TechnicalGroove, diff |

- `#LNTYPE` 声明谱面所遵循的 LN（长音）记法。
- `#LNTYPE` 值为 `1` 时，LN 通道 `#xxx51-69` 在 RDM 类型和 MGQ 类型中选择 RDM 类型。
- 省略此命令时，默认应用 RDM 类型。
  - MGQ.exe 除外，因为它与 RDM 规范无关。
- 在同时实现了 RDM 类型和 MGQ 类型的应用中，此声明有明确含义。
- 在未实现 MGQ 类型的应用中，此声明无意义。

- MGQ 类型已淘汰。
- 所有支持 LN 的应用都已实现 RDM 类型。
- 因此，已无需显式写入 `#LNTYPE 1`。
- 此声明仅出于向后兼容性保留。

- RDM 类型的 `#xxx51-69` 与 `#LNOBJ` 冲突时的优先级未在规范中定义。
  - pomu2 中，`#xxx51-69` 与 `#LNOBJ` 冲突时，`#xxx51-69` 的所有物体会变成 `#xxx11-29` 的 bug。
  - LR2 中 `#xxx51-69` 与 `#LNOBJ` 冲突时，根据 `#LNTYPE` 的有无，解析结果可能略有不同。
- DDR 中，若在找到终点符号前谱面结束，将返回编译错误。
- BMSE 中，将通道 `#xxx51-69` 的物体移至 BGM 时会导致 BMSE 强制退出。
- iBMSC 会自动写入 `#LNTYPE 01` （而非 `#LNTYPE 1`）。Fixed in Version 3.0 Delta。
- **o2mania 不可省略 `#LNTYPE 1`。** `1` 和 `01` 可作为有效值接收。`001` 为无效值。

### details

RDM 记法可视为 MGQ 记法的语法糖。`#LNTYPE 1` 是 **RDM-type #1 LN**。

LN 从出现非 `00` 音符的位置开始（LN 起点符号）。
LN 在下次出现非 `00` 音符的位置闭合（LN 终点符号）。

| For example | remarks |
| --- | --- |
| <pre>#LNTYPE 1<br>#00151:00220000<br>#06451:000000000033</pre> | O2mania 以外的实现可省略此声明。LN 区间在 `#001` 中开始。若区间内未出现终点以外的物体，则该 LN 在 `#064` 处闭合。 |

此例构成一个长度为 `#001-064` 的 LN 段落。
一个 LN 段落可能算作“1 个物体”或“2 个物体”，取决于实现。多数实现算作 1 个物体。

若文件定义在 `#WAV22`，音会在起始位置播放。
即使文件定义在 `#WAV33`，终点也不会播放音。`#LNTYPE 1` 中，结束位置的音始终被忽略。
起始和结束位置的 `#WAV` 索引不一定相同，但使用 BMS 编辑器时几乎总是相同。

### LN 终端播放不同音

| 字段 | 值 |
| --- | --- |
| channel: | `#xxx51-69` |
| origin: | HDX |
| support: | HDX, IIDXv, TechnicalGroove |

- 在长音始端和终端放置不同索引可分别播放不同音。例如：

| For example | remarks |
| --- | --- |
| <pre>#WAVaa onkeydown.wav<br>#WAVbb onkeyup.wav<br>#00151:00aa00bb</pre> | LN 始端用音。LN 终端用音。LN 始端播放 onkeydown.wav，LN 终端播放 onkeyup.wav。 |

- 结合判定 LN 终端的游戏系统，可模拟再现 beatmania IIDX 的“背转 Scratch”。

---

## `#LNTYPE 2`

| 字段 | 值 |
| --- | --- |
| channel: | `#xxx51-69` |
| origin: | RDM |
| support: | RDM (1.3+), WAview, in_BM2, ruvit, Angolmois, Sonorous |
| caution: | **此命令已过时。不应再使用此命令。** |

- `#LNTYPE` 声明谱面所遵循的 LN（长音）记法。
- `#LNTYPE` 值为 `2` 时，LN 通道 `#xxx51-69` 在 RDM 类型和 MGQ 类型中选择 MGQ 类型。
- 省略此命令时，默认应用 RDM 类型。
  - MGQ.exe 除外，因为它与 RDM 规范无关。
  - 因此 MGQ.exe 会忽略 `#LNTYPE`，始终将 `#xxx51-8C` 解释为 MGQ 记法的 LN。
- MGQ 格式是 BMS 的变体（子集），可模拟 KEYBOARDMANIA 的谱面。准备了 24KEYS *2。
  - 1P-side LN: channel `#xxx51-5C` and `#xxx61-6C`
  - 2P-side LN: channel `#xxx71-7C` and `#xxx81-8C`
- MGQ 格式已过时。不应再使用 MGQ-LN。
  - 对于类似 KEYBOARDMANIA 的游戏，已设计出比 MGQ 更合适的格式。
  - 例如 Synthesia 支持“直接从 MIDI 生成谱面”和 87KEYS。
    <https://www.youtube.com/watch?v=euadl2uWy_g>
- WAview 和 in_bm2 无法正确解析 MGQ-LN，至少不会按照 RDM 规范解释。
- RDM 和 ruvit 中，最后一个音符若为 MGQ-LN，则无法正确解析（大概是 bug）。

### MGQ-LN details

`#LNTYPE 2` 是 **MGQ 记法 LN**。

LN 从出现非 `00` 音符的位置开始。
LN 在非 `00` 音符连续期间保持连接。
LN 在出现 `00` 时闭合。
若“前一小节最后一个音符”非 `00`且“当前小节第一个音符”也非 `00`，MGQ-LN 可跨越小节线连接。

| For example | remarks |
| --- | --- |
| <pre>#LNTYPE 2<br>#00151:0022zzzz<br>#00251:zz<br>#00351:zz<br>...<br>#06351:zz<br>#06451:zzzzzzzz3300<br>#06551:00<br>#06511:0011</pre> | **声明不可省略。** LN 开始 & LN 持续。LN 持续符号不可省略。LN 持续符号不可省略。LN 持续符号不可省略... LN 持续符号不可省略... 最后一个音符不一定必须是 `33`，`33` 也可以是其他索引。显式终点符号 `00`（可省略）。« 用于规避 RDM/ruvit 的 bug ... 不要将各通道最后一个物体设为 MGQ-LN。 |

此例表现与 `#LNTYPE 1` 示例相同的谱面（不含 `#06511:0011`）。
RDM 和 ruvit 中，起始和结束位置的播放方式与 `#LNTYPE 1` 相同。
（WAview 和 in_bm2 的 MGQ-LN 有 bug，无法判断。）
使用 MGQ-LN 时必须非常耐心。

#### 关于版本 798422870970（2013年3月10日）之前 Angolmois 的注记

ruvit 的实现如上所述，但 Guide to understand BMS format 所解释的 MGQ 规范更为复杂。
我没有 MGQ.exe，无法验证这些解释的有效性。
至少，为了在 ruvit 和 2013年3月10日版之前的 Angolmois 中获得相同的解析结果，必须如下编写：

| For example | remarks |
| --- | --- |
| <pre>#LNTYPE 2<br>#00151:00222222<br>#00251:22<br>#00351:22<br>...<br>#06351:22<br>#06451:222222222200<br>#06551:00<br>#06511:0011</pre> | **声明不可省略。** LN 开始 & LN 持续。**所有索引必须相同。** LN 持续符号不可省略。LN 持续符号不可省略。LN 持续符号不可省略... LN 持续符号不可省略... 最后一个索引也必须与第一个索引相同。显式终点符号 `00`（可省略）。« 用于规避 RDM/ruvit 的 bug ... 不要将各通道最后一个物体设为 MGQ-LN。 |

2013年3月10日版之后的 Angolmois 已修改为即使连续不同索引也能获得与 ruvit 相同的解析结果。

---

## `#LNOBJ xx`

| 字段 | 值 |
| --- | --- |
| channel: | `#xxx11-29` |
| origin: | RDM |
| support: | RDM (1.61+), nazo, nazoZZ, ~~WAview~~ (buggy), ~~in_bm2~~ (buggy), bme2wav, LR2, nanasi, ruvit, fgt++, fgt#, pomu2, BMSE, uBMplay, PMSee-V, bmx2wav, iBMSC (3.0+), Angolmois, Sonorous, TechnicalGroove |
| sound or not: | bmx2wav（可从设置更改行为） |
| sound as BGM: | RDM (1.61+), ruvit, ~~Angolmois（仅自动播放时）?~~ |
| sound as KEY: | TechnicalGroove (Keyup sounding) |
| multiple `#LNOBJ`: | TechnicalGroove |

- 写入此命令时，应用 RDM-type #2 的 LN。
- 将 `#WAV` 索引之一指定为 XX。任意 `#WAV` 索引将用作 LN 结束标记。
- 通过消耗一个 `#WAV` 索引，可像编辑普通 `#xxx11-29` 一样编辑 LN。
- 因此，在 BMSC 之外没有编辑器的时代，`#LNOBJ` 被频繁使用。
- 为了规避 BMSE 的 bug（将 `#xxx51-69` 的 LN 移至 BGM 时 BMSE 崩溃），此命令至今仍被频繁使用。
- 在不支持 `#LNOBJ` 的实现中，LN 将成为普通的 `#xxx11-29` 物体。
  - 为避免产生违背谱师意图的谱面，建议将扩展名改为 BML。
  - 严格来说，不支持 `#LNOBJ` 的实现支持扩展名 BML 是错误的。
    - 支持 `#xxx51-69` 但不支持 `#LNOBJ` 因此不支持扩展名 BML 的实现：DDR
    - 不支持 `#LNOBJ` 但支持扩展名 BML 的宽松组：WAview, in_bm2, IIDXv, HDX, ~~iBMSC,~~ O2play
  - BMSE 不显式支持 `#LNOBJ`，但可在“扩展命令”标签页中编辑 `#LNOBJ`。
- 对于 `#LNOBJ`，nanasi 和 fgt++ 存在无法识别小写 `#WAV` 索引的 bug，建议使用大写指定。

### LNOBJ details

`#LNOBJ` 是 RDM-type #1 的语法糖，为 **RDM-type #2 LN**。

`#LNOBJ` 定义的 `#WAV` 索引的音符用作 LN 的结束标记（闭合符号）。
若找到结束标记，其前一个可视物体被解释为 LN 起点。
使用 `#LNOBJ` 的谱面无需 `#LNTYPE` 声明。

当结束标记通过判定线时，**定义为 `#LNOBJ` 的 `#WAV` 索引的文件会作为 BGM 播放。**

- 这是符合 RDM 规范的行为，但大多数实现不会播放结束标记的音。
- 将“空 `#WAV`”的索引定义为 `#LNOBJ` 可保持兼容性。

| For example | remarks |
| --- | --- |
| <pre>#LNOBJ ZZ<br>#00111:00220000<br>#06411:0000000000zz</pre> | 为规避 nanasi 和 fgt++ 的 bug，`#WAV` 索引使用大写。此例表现与 `#LNTYPE 1` 示例相同的谱面。但若 `#WAVzz` 定义了文件，结束标记可能会播放（取决于实现）。 |

### LN 终端播放不同音（`#LNOBJ`）

| 字段 | 值 |
| --- | --- |
| channel: | `#xxx11-29` |
| origin: | TechnicalGroove |
| support: | TechnicalGroove |

- 这是 HDX 扩展在 `#LNOBJ` 上的实现。例如：

| For example | remarks |
| --- | --- |
| <pre>#WAVaa onkeydown1.wav<br>#WAVbb onkeyup1.wav<br>#WAVcc onkeydown2.wav<br>#WAVdd onkeyup2.wav<br>#LNOBJ BB<br>#LNOBJ DD<br>#00111:00aa00bb<br>#00213:00cc00dd</pre> | LN 始端用音 (1)。LN 终端用音 (1)。LN 始端用音 (2)。LN 终端用音 (2)。LN 终端定义 (1)。LN 终端定义 (2)（**multiple LNOBJ**）。LN 始端播放 onkeydown1.wav，LN 终端播放 onkeyup1.wav。LN 始端播放 onkeydown2.wav，LN 终端播放 onkeyup2.wav。 |

---

## `#OCT/FP`

| 字段 | 值 |
| --- | --- |
| origin: | nanasi |
| support: | nanasi (, BMSE, Angolmois, Sonorous), uBMplay (1.5.0+), TechnicalGroove |

- 写入此命令的 BMS 将被解释为 OCTAVE MODE。
- 此命令不带值。

### OCT/FP 说明

<https://web.archive.org/web/*/http://www.diana.dti.ne.jp/~idee/octave.html>

OCT/FP 是八度模式/脚踏板模式。此格式由 idee 于 2002 年（2003 年?）提出。
八度模式使用 13 个按键和 2 个唱盘。可选使用 1 个脚踏板。
这是 14KEYS（“7KEYS + 1 个唱盘”的双人模式）的外观修改模式。
此模式使用的通道与 14KEYS 完全相同。
此格式使用可灵活修改外观的 nazo，将 14KEYS 表现为伪 OCT/FP。
此格式的概念类似于 Project2DX。
BMSE 通过主题文件的方式支持编辑 OCT/FP 谱面。

- nanasi 无法区分 14KEYS 和 OCT/FP，因为它从所用通道推断模式。
- 因此 nanasi 需要此 `#OCT/FP` 命令作为标识符。
- 找到此命令时，nanasi 始终以 OCT/FP 模式渲染 BMS。
- nanasi、uBMplay 1.5.0+ 和 TechnicalGroove 以外的应用会忽略此命令。
  - BMSE 将 `#OCT/FP` 命令分离到“扩展命令”标签页。
  - 需要手动修改 BMSE 的主题文件。步骤如下：
    1. 选择 BMSE 窗口右上角的“基本”标签页。
    2. 选择下拉菜单 “Play Mode”。
    3. 选择菜单项 “13 keys (oct/fp)”。
    4. 选择菜单栏项 “Settings”。
    5. 选择子菜单项 “Select Theme”。
    6. 选择孙菜单项 “Default (for oct/fp)”。
  - Angolmois 可通过命令行参数应用 OCT/FP 渲染。
    （`--key-spec '<Leftside string>' '<Rightside string>'`）
    例如：`$ angolmois.exe --key-spec '21p 16s 11a 12b 13a 14b 15a 18b 19a 22b 23a 24b 25a 28b 29a 26s' ''`
  - Sonorous 可能通过 `#LANES` 独自扩展命令应用 OCT/FP 渲染。
- OCT/FP 谱面近年几乎已不可见。
  （例如 Microcosm from bms同窓会(仮)）

---

## `#OPTION optionID`

| 字段 | 值 |
| --- | --- |
| origin: | nanasi |
| support: | nanasi, HDX (v0.99+), IIDXv (v2.14+), Sonorous（仅解析） |

- BMS 侧定义的播放选项将强制应用于玩家侧。
- 通常命令重复时采用“行号最大的行”，但 `#OPTION` 可部分重复。
- 为应对其他应用可能支持此命令的情况，建议添加厂商前缀。

| 机型 | 前缀 |
| --- | --- |
| nanasi | `774:` |
| HDX, IIDXv | `charatbeatHDX:` |

- 若 BMS 格式的规范标准化，可能也支持无厂商前缀的 `optionID`。
- 可写入多个 `#OPTION`，例如如下指定：

| For example | remarks |
| --- | --- |
| <pre>#OPTION 774:HI-SPEED_x99.75<br>#OPTION 774:HI-SPEED_x0.77<br>#OPTION 774:CHANGE-HS_RANDOM<br>#OPTION 774:CHANGE-HS_COMBO-UP<br>#OPTION 774:HIDDEN_DEEPMIST<br>#OPTION 774:HIDDEN_OFF<br>#OPTION 774:RANDOM_ALL+<br>#OPTION 774:RANDOM_MIRROR<br>#OPTION 774:NOTES_DOUBLE<br>#OPTION 774:NOTES_DBLCOPY<br>#OPTION 774:CRAZY_STAR<br>#OPTION 774:CRAZY_SPIN<br>#OPTION 774:CRAZY_CLONE<br>#OPTION 774:CRAZY_GREAT2BAD<br>#OPTION 774:CRAZY_XYWAVE2<br>#OPTION 774:CRAZY_3DWAVE<br>#OPTION 774:CRAZY_DARK2<br>#OPTION 774:CRAZY_FADEOUT<br>#OPTION 774:CRAZY_RD-SIZE<br>#OPTION 774:CRAZY_ADDMINE<br>#OPTION 774:AUTOPLAY_LINE</pre> | 同系列选项重复时，最接近 EOF 的行优先。<br>应用 `x0.77` 而非 `x99.75`。`x0.77` 是仅可从 BMS 侧设置的值。<br>应用 `COMBO-UP` 而非 `RANDOM`（二者为同系列）。<br>应用 `OFF` 而非 `DEEPMIST`。`OFF` 取消选项的应用。<br>应用 `MIRROR` 而非 `ALL+`（二者为同系列）。<br>应用 `DBLCOPY` 而非 `DOUBLE`。`NOTES` 系列不能在 `#CHANGEOPTION` 中使用。<br>`CRAZY` 系列可同时应用多个选项。通常模式只能应用一个选项。<br>仅在指定 `#OPTION` 时可同时应用多个 `CRAZY` 选项。<br>`CRAZY` 系列中，"影响判定的选项"和 `ADDMINE` 不能在 `#CHANGEOPTION` 中使用。<br>`AUTOPLAY` 系列在 `#OPTION` 和 `#CHANGEOPTION` 中无效（即此行无效）。 |

此例合计强制应用 **14 个选项**。

- nanasi 和 pomu2 中，探索已实现的选项作为元游戏提供。
  - 隐藏选项作为试炼模式的成功奖励解锁。
  - 若接受从 BMS 侧强制选项的游戏性，这可能为节奏游戏的难度线性方向带来新的思路。

---

## `#CHANGEOPTION[01-ZZ] optionID`

| 字段 | 值 |
| --- | --- |
| channel: | `#xxxA6` |
| origin: | nanasi 1.550 |
| support: | nanasi 1.550+, Sonorous（仅解析） |

- 从 BMS 侧动态更改播放选项。
- 动态选项更改通道与 BGM 通道具有相同特性（可多行），以便同时更改多个选项。
- 某些选项无法应用动态更改。
  - `HI-SPEED`、`CHANGE-HS`、`HIDDEN` 和 `CRAZY` 系列大部分可用。
  - `GAMELEVEL`、`BASE-SPEED`、`RANDOM`、`NOTES`、`EXTRA` 和 `AUTOPLAY` 系列不可用。
  - `CRAZY` 系列中，`GOOD2BAD`、`GREAT2BAD`、`HELLJUDGE` 和 `ADDMINE` 不可用。
  - `CRAZY` 系列可同时应用多个选项。

| For example | remarks |
| --- | --- |
| <pre>#changeoption01 774:HIDDEN_STEALTH<br>#changeoption02 774:HIDDEN_OFF<br>#changeoption03 774:HI-SPEED_x0.25<br>#032a6:00000001<br>#034a6:02<br>#034a6:03</pre> | 此例中，在 `#032` 的第 4 拍处，音符将完全不显示。到达 `#034` 时，STEALTH 状态解除，音符将重新显示（以压缩形式）。 |

- 关于 nanasi 的 `optionID`：[详情参见 option.htm](https://hitkey.nekokan.dyndns.info/option.htm)

| category | 1.500 之前 | 1.500 之后 |
| --- | --- | --- |
| `GAMELEVEL` | `774:GTP-EASY` | `774:GAMELEVEL_BEGINNER` |
| `HI-SPEED` | `774:HSP-1.0` | `774:HI-SPEED_x1.00` |
| `CHANGE-HS` | `774:HSP-COMBO` | `774:CHANGE-HS_COMBO-UP` |
| `HIDDEN` | `774:HID-STEALTH` | `774:HIDDEN_STEALTH` |
| `RANDOM` | `774:RND-MIRROR` | `774:RANDOM_MIRROR` |
| `NOTES` | `774:CRA-CPDOUBLE` | `774:NOTES_COPY` |
| `CRAZY` | `774:CRA-NORMAL` | `774:CRAZY_OFF` |
| `EXTRA` | `774:GLV-VERYHARD` | `774:EXTRA_VERYHARD` |

- 解除选项的命令从 `NORMAL` 改为 `OFF`。
- nanasi 1.552 同时支持旧的 `optionID` 和新的 `optionID`。
- 但 **`#CHANGEOPTION` 会忽略旧的 `optionID`。**
