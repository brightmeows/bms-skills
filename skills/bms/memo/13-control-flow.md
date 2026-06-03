# 控制流

> 来源：[BMS command memo (JP)](https://hitkey.nekokan.dyndns.info/cmdsJP.htm#CONTROL-FLOW)

## `#RANDOM n` / `#IF n` / `#ENDIF`

| 项目 | 内容 |
| ------ | ------ |
| 来源 | BM98 (<http://bm98.yaneu.com/bm98/bmsformat.html>) |
| 支持 | BM98, BM98de, DDR, RDM, MW, bemaniaDX, nazo, Mac, Aqua, WAview, in_BM2, bms2wav, bms2avi, LR2, ruvit, nanasi, fgt++, fgt#, pomu2, nazoZZ, uBMplay, PMSee-V, IIDXv, HDX, bmx2wav, outliner, Angolmois, BMSE (partial), iBMSC (3.0+ / partial), Sonorous, BGAEncAdv, TechnicalGroove |
| 解除嵌套 | RDM, bemaniaDX, nazo, nazoZZ, WAview, in_BM2, LR2, ruvit, fgt++, fgt#, uBMplay (1.4.6 及更早), PMSee-V, bmx2wav |
| 允许嵌套 | BM98, BM98de, DDR, MW, nanasi, Aqua, pomu2, BMSE (partial), IIDXv, HDX, outliner, Angolmois, uBMplay (1.5.0 及更新), Sonorous, BGAEncAdv, TechnicalGroove |

- `#RANDOM` 会生成从 1 到 `*n*` 之间的一个自然数。不言而喻，`*n*` 应指定为自然数。
- 从 `#IF` 到 `#ENDIF` 之间的区间是块语句。
- 与 `#RANDOM` 生成值相同标签的块，其内容会被应用。
- 与生成值不匹配的块，其内容会被全部忽略。
- 也就是说，这种语法使得制作 **“每次游玩都会变化的 BMS”** 成为可能。
  - 1998-08-09: BM98 实现了 `#RANDOM`。（早于 `#PLAYER 3` (DP), `#TOTAL`, `#xxx07` (BGA LAYER) 等）
  - 这一特性是为了模拟 beatmania 的 DJ BATTLE 而提出的。
  - 据我所知，最早的随机化 BMS 是 FANKS "Anthology" COLLECTION (cranky, 1998-11-06)
  - 至今，随机化 BMS 仍在不断发布。例如 DJ BATTLE 2011 (2106, 2011-06-12)
  - 详见另项的作品列表：[#RANDOM BMS list](https://hitkey.nekokan.dyndns.info/randombms.htm)
- 重要的一点是，**谱面的所有内容都可以被随机化**。
  - 游玩选项中的 RANDOM 无法随机化音符的节奏。
  - 而 `#RANDOM` 命令则更加动态、更加强大。

### 基本示例

|BMS code|生成值 1 时|生成值 2 时|
|----------|-------------|-------------|
|<pre><code>#00111:11000000<br><br>#RANDOM 2<br><br>#IF 1<br>#00112:00220000<br>#ENDIF<br><br>#IF 2<br>#00113:00003300<br>#ENDIF<br><br>#00114:00000044</code></pre>|![基本示例 生成值1时的流程图](https://hitkey.nekokan.dyndns.info/cmd/flow1.png)<br><pre><code>#00111:11000000<br>#00112:00220000<br>#00114:00000044</code></pre>|![示例 生成值2时的流程图](https://hitkey.nekokan.dyndns.info/cmd/flow2.png)<br><pre><code>#00111:11000000<br>#00113:00003300<br>#00114:00000044</code></pre>|

- 此例的顶层有 2 行和一个 `#RANDOM` 块。
- `#00111:11000000` 和 `#00114:00000044` 写在 `#RANDOM` 外部 (top-level)。因此，无论 `#RANDOM` 的生成值如何，这两行始终会被解析。
- 此 `#RANDOM` 语句写在顶层。因此，随机数始终会生成。
  - 如果生成值为 1，则 `#00112:00220000` 被应用，`#00113:00003300` 被忽略。
  - 如果生成值为 2，则 `#00112:00220000` 被忽略，`#00113:00003300` 被应用。
- 不支持 `#RANDOM` 的实现会显示全部 4 个音符。

### 不推荐的示例：公共部分的孤儿化

|BMS code|生成值 1 时|生成值 2 时|
|----------|-------------|-------------|
|<pre><code>#RANDOM 2<br>#00111:11000000<br>#00114:00000044<br><br>#IF 1<br>#00112:00220000<br>#ENDIF<br><br>#IF 2<br>#00113:00003300<br>#ENDIF</code></pre>|![不推荐示例 生成值1时的孤儿流程图](https://hitkey.nekokan.dyndns.info/cmd/flow3.png)<br><pre><code>#00112:00220000</code></pre>|![不推荐示例 生成值2时的孤儿流程图](https://hitkey.nekokan.dyndns.info/cmd/flow4.png)<br><pre><code>#00113:00003300</code></pre>|

- 谱师可能会期望这个示例与第一个示例产生相同的结果。但是，这是**不推荐的**。
- 因为 `#RANDOM` 块包含了不属于 `#IF` 部分的行。
- 音符 `11` 和音符 `44` 没有写在顶层。因此，这些孤儿的显示不被保证。
- 在 BM98、ruvit、uBMplay 中，结果与第一个示例相同。但在 nazo、LR2、BMSE 等中，这些孤儿会被忽略。
- 如果它们是“公共部分”，我们应该将其写在 `#RANDOM` 块的外部。

### 嵌套 `#RANDOM` 示例

|`#RANDOM 2` 的双重嵌套|生成值 1 » 进一步生成值 1 时|生成值 1 » 进一步生成值 2 时|生成值 2 时|
|------------------------|----------------------------|----------------------------|------------|
|<pre><code>#00111:11000000<br><br>#RANDOM 2<br><br>  #IF 1<br>    #00112:00220000<br><br>    #RANDOM 2<br><br>      #IF 1<br>        #00115:00550000<br>      #ENDIF<br><br>      #IF 2<br>        #00116:00006600<br>      #ENDIF<br><br>    #ENDRANDOM<br><br>  #ENDIF<br><br>  #IF 2<br>    #00113:00003300<br>  #ENDIF<br><br>#ENDRANDOM<br><br>#00114:00000044</code></pre>|![嵌套示例 生成值1进一步1时的流程图](https://hitkey.nekokan.dyndns.info/cmd/flow5.png)<br>(pattern A)<br><pre><code>#00111:11000000<br>#00112:00220000<br>#00115:00550000<br>#00114:00000044</code></pre>|![嵌套示例 生成值1进一步2时的流程图](https://hitkey.nekokan.dyndns.info/cmd/flow6.png)<br>(pattern B)<br><pre><code>#00111:11000000<br>#00112:00220000<br>#00116:00006600<br>#00114:00000044</code></pre>|![示例 生成值2时的流程图](https://hitkey.nekokan.dyndns.info/cmd/flow2.png)<br>(pattern C)<br><pre><code>#00111:11000000<br>#00113:00003300<br>#00114:00000044</code></pre>|

- **请注意，能正确解释嵌套 `#RANDOM` 的实现很少。**
- 为了使代码更易读，我在示例中使用了缩进和 `#ENDRANDOM`。实际使用嵌套 `#RANDOM` 时，应去掉缩进，并删除行尾的空格。
- **`#ENDRANDOM` 不可省略。** 因为 nanasi 在嵌套 `#RANDOM` 中要求使用它。
- 顶层的行始终被解释：`#00111:11000000`, `#RANDOM 2`, `#00114:00000044`
  - 如果顶层 `#RANDOM` 生成值为 `1`，则 `#00112:00220000` 被解释。同时，第二层的 `#RANDOM` 语句也会被解释。
    - 如果第二层 `#RANDOM` 生成值为 `1`，则 `#00115:00550000` 被解释。
    - 如果第二层 `#RANDOM` 生成值为 `2`，则 `#00116:00006600` 被解释。
  - 如果顶层 `#RANDOM` 生成值为 `2`，则 `#00113:00003300` 被解释。
- 此例分支出 3 种模式，但每种模式被选中的概率并非各 1/3。
  - `#00115:00550000` 被选中的概率为 1/4。
  - `#00116:00006600` 被选中的概率为 1/4。
  - `#00113:00003300` 被选中的概率为 1/2。
- 例如，如果将 `#RANDOM 2` 嵌套 6 层，最深层的模式被选中的概率为 1/64。（64 是 2 的 6 次方）为了实现同样的效果，我们也可以写 64 个 `#IF` 块。虽然这样很浪费，但大多数实现都能解释。

### 不支持 `#RANDOM` 的实现

不支持 `#RANDOM` 的实现会将所有行解释为顶层的语句，从而导致解析错误。

例如：Declinin' (sta, 2008-01-13) (下载: [低级语言关怀会](http://bit192.info/))

| 实现 | 截图 |
| ------ | ------ |
| BMSE（部分支持） | <https://hitkey.nekokan.dyndns.info/090102/090102_01.png> |
| BMSC（不支持） | <https://hitkey.nekokan.dyndns.info/090102/090102_02.png> |
| iBMSC 3.0（部分支持） | <https://hitkey.nekokan.dyndns.info/img/20120123_ibmsc30_random.png><br>“扩展代码”选项卡的隔离是准确的。该选项卡至少可存储 700,000 字符以上。<br>但目前的隔离还不完善，“编辑面板”中仍会显示流控表达式。 |

- 该 BMS 在 `#026-033` 中使用 40 个 `#RANDOM` 语句来随机化音符的节奏。
- 概念图：<https://hitkey.nekokan.dyndns.info/090102/090102_03.gif>
- BMSE 能解析 `#RANDOM` 部分并将其分离到“扩展命令”选项卡。然而，该 BMS 中过多的 `#RANDOM` 块会超出 BMSE “扩展命令”选项卡的容量。通过此 BMS，我发现“扩展命令”选项卡最多只能容纳 65535
  字符。

### 通用注意事项

- 控制流需要在所有其他命令之前进行解析。
- 控制流有能力更改包括头部在内的任何命令行。
  - e.g. オートメーション工場 (automation factory) (John "De Bello" Cage, 2007-03-14)
    (<http://www.comeup.info/bofoon2007/automation.zip>)
  - 该 BMS 会随机自动生成音乐。
  `#TITLE`, `#RANK`, `#BPM[01-ZZ]` 都受到该语法控制。
  - 第 347781-409539 行的部分是 `#IF 14` (347780-409542) 的子级。
  - 该谱面使用了 6177 个 `#RANDOM` 语句，其中 1260 个是第 347780 行 `#IF 14` 的子级。
  - 该 BMS 是“**无限兄弟**”的绝佳样本。这是被称为 benchmark BMS 的 kusofumen 的典型示例。
  - 该 BMS 不包含“**无限子孙**”元素，对许多实现来说算是幸运。
- 几乎所有的实现都无法解析“无限控制流”。
- 几乎在所有情况下，可随机化的头部都有限制，或者语句的数量有限制。
- 例如，在允许嵌套的实现中，理论上应该可以实现半永久的嵌套。
  - 然而，无论嵌套深度如何，我们都必须始终写上块结束命令 `#ENDRANDOM`。
  - 因为如果没有 `#ENDRANDOM` 的 `#RANDOM` 语句出现 47 次以上，nanasi 会强制终止。
  - （这看起来像是 nanasi 的 bug，但也可能是我的误解。）
- IIDXv 和 HDX（以及 BGAEncAdv 和 TechnicalGroove）在未找到 `#RANDOM *n*` 时，会应用默认值 `1`。
  - 这是处理手写导致的简单语法违规的一种方法。
  - （例如，`#random5`（无分隔符）或 `#rondam 5`（拼写错误）等）

---

## `#RONDAM n`

| 项目 | 内容 |
| ------ | ------ |
| 来源 | uBMplay |
| 支持 | uBMplay, outliner, Sonorous, BGAEncAdv, TechnicalGroove |

- 为了处理拼写错误。
- e.g. 20,november "club edit" [random ver.] (cranky, 1999-04-10)
  - 第 366 行写着 `#rondam 5`。如果这一行被忽略，就会产生语法难题。
  - 这个 `#RONDAM` 有 5 个 `#IF` 块，那应该如何处理它们呢？
    - 几乎所有的实现都会引用前一个 `#RANDOM` 的生成值。
    - 然而，此行为未被规范定义，因此依赖于实现。
  - `#RANDOM` 的范围是如何确定的呢？是否根本不存在范围，只有 `#IF`-`#ENDIF` 的部分？

  |可疑的代码示例|说明|
  |---------------|------|
  |<pre><code>#00111:11<br><br>#RANDOM 2<br>  #IF 1<br>    #00212:22<br>  #ENDIF<br>  #IF 2<br>    #00313:33<br>  #ENDIF<br><br>#00414:44<br>#00515:55<br><br>#RONDAM 3<br>  #IF 1<br>    #00616:66<br>  #ENDIF<br>  #IF 2<br>    #00717:77<br>  #ENDIF<br>  #IF 3<br>    #00818:88<br>  #ENDIF<br><br>#00919:99</code></pre>|此行为顶层，始终被解释。<br><br>┐<br>│<br>│<br>│<br>│<br>│<br>┆ 无法确定 #RANDOM 是否在此处关闭。<br><br>┐ 通常情况下，这些行属于顶层。<br>┘ 但在 #RONDAM 中，这可能不正确。此处可能成为孤儿块。<br><br>] 此行被注释掉。<br>┐<br>│ 如果前一个 #RANDOM 生成值为 1，则此块也被应用。<br>┘<br>┐<br>│ 如果前一个 #RANDOM 生成值为 2，则此块也被应用。<br>┘<br>┐<br>│ 如果前一个 #RANDOM 生成值为 3，则此块也被应用。<br>┘ 但这不可能实现，因此此块无论如何都不会被应用。<br><br>] 此行属于顶层吗？在 #RANDOM 外部吗？真的吗？|

  - 如果第一个 `#RANDOM 2` 不存在，上述示例会发生什么？
    - nanasi 不会将 `#IF` 块识别为 `#IF` 块。也就是说，`#IF` 块的内容会以与“写在 `#RANDOM` 外部的命令行”相同的优先级处理。
    - LR2 和 uBMplay 会忽略所有孤立的 `#IF` 内容。（丢弃: `#00616:66`, `#00717:77`, `#00818:88`）
    - PMSee-V 会发出如下警告：`在第一个 #RANDOM 或 #SETRANDOM 之前出现了 #IF！！`
- 控制流的问题在于语法错误很容易被写出来。
- uBMplay 的方法可能是 ad-hoc 的，但既然规范没有定义细节，那也没办法。

---

## `#END IF`

| 项目 | 内容 |
| ------ | ------ |
| 来源 | uBMplay (1.5.0) |
| ad-hoc 支持 | Angolmois (`#END`), outliner (`#END`), Sonorous (`#END`), BGAEncAdv, TechnicalGroove |
| 偶然被解释 | nazo, rdm, ruvit, fgt++, fgt#, LR2, uBMplay (1.4.6 及更早), PMSee-V |
| 仅当第一个 `#IF` 匹配时被解释 | BM98k, BM98de, DDR, MW（死机）, pomu2, IIDXv, HDX |
| 仅当最后一个 `#IF` 匹配时被解释 | nanasi |

- 为了处理拼写错误。
- e.g. Velocity Magic 3 [Aren't you lucky?] (Speed Magician / Transfero, 2010-09-11)
  (<http://uploader.bms.ms/data/PW/vm3_ts3.zip>)（目前无法下载）
  - 请将 lovetricks.**ogg** 的扩展名改为 bms、bme 或 bml。
  - `#END IF` 是基于误解的拼写错误。或者这可能是故意的技巧，但我对此不感兴趣。
  - 通常的实现会忽略这一行。也就是说，这是“没有 `#ENDIF` 的 `#IF`”的问题。
  - 不支持嵌套 `#RANDOM` 的实现可以轻松处理此示例。因为在 `#IF` 块中，如果在找到 `#ENDIF` 之前发现了另一个 `#IF`，可以断定“它不是嵌套，而是相邻的兄弟块”。
  - 对于支持嵌套的实现来说，这个示例很麻烦，因为它无法忽视它是嵌套的可能性。
    - 我想起了没有结束标签的 HTML。HTML 解析器必须完成比 XML 解析器更困难的任务。
    - BMS 解析器是否应该获得类似 HTML 解析器的能力？我认为 BMS 最好还是 XML 化。

---

## 常见的错误示例

- `#RONDAM *n*`
- `#END IF`
- 没有 `#ENDIF` 的 `#IF *n*`
- `＃ENDIF`（全角 `＃`）
- `#RANDOM*n*`
- `#IF*n*`
- `#IFEND`

参考：[#RANDOM BMS list](https://hitkey.nekokan.dyndns.info/randombms.htm)

### 对应情况

uBMplay 1.5.2 及更新, Sonorous (UTF-8), BGAEncAdv, TechnicalGroove

---

## `#SETRANDOM n`

| 项目 | 内容 |
| ------ | ------ |
| 来源 | pomu2 |
| 支持 | pomu2, WAview, in_bm2, nanasi, Aqua, PMSee-V, IIDXv, HDX, outliner, iBMSC (3.0+ / partial), Angolmois, Sonorous, BGAEncAdv, TechnicalGroove |
| 类似功能 | bms2avi（“SEED”菜单）, bmx2wav (bmx2wav.ini 的 RandomConstantValue) |

- 如果用 `#SETRANDOM *n*` 代替 `#RANDOM *n*`，则会生成常量 `*n*`。

|示例|说明|
|------|------|
|<pre><code>// #RANDOM 3<br>#SETRANDOM 2<br>  #IF 1<br>    #WAV01 a.wav<br>  #ENDIF<br>  #IF 2<br>    #WAV01 b.wav<br>  #ENDIF<br>  #IF 3<br>    #WAV01 c.wav<br>  #ENDIF<br>#ENDRANDOM</code></pre>|- 此示例注释掉了原来的 `#RANDOM` 语句。<br>- 目前几乎还没有实现支持缩进，因此使用缩进还为时过早。本文件为使示例代码更易读而使用了缩进。<br>- 此 `#SETRANDOM` 始终生成 `2`，因此标签为“2”的块始终被选中。<br>- 结果，`#WAV01 b.wav` 始终被应用。<br>- 只要指定了 `#SETRANDOM 2`，`#IF 1` 和 `#IF 3` 就永远不会被选中。<br>- 未被选中的语句块会被简单地忽略。|

- 看起来 `#SETRANDOM` 很可能是为了测试嵌套控制语法而准备的命令。

---

## `#ELSEIF n`

| 项目 | 内容 |
| ------ | ------ |
| 来源 | IIDXv |
| 支持 | IIDXv, HDX, outliner, Angolmois, Sonorous, BGAEncAdv, TechnicalGroove |

- `#ELSEIF *n*` 语句可以在 `#IF` 语句和 `#ENDIF` 语句之间的任意位置多次书写。
- 如果存在 `#ELSE`，`#ELSEIF *n*` 语句应写在 `#ELSE` 的前面。
- 如果不需要，我们不必写 `#ELSEIF`。
- “标签与生成值匹配的块中的第一个块”的内容会被应用。示例：

|代码|说明|
|------|------|
|<pre><code>#RANDOM 5<br><br>  #IF 1<br>            #00111:UU<br>  #ELSEIF 2<br>            #00112:VV<br>  #ELSEIF 3<br>            #00113:WW<br>  #ELSE<br>            #00115:ZZ<br>  #ENDIF<br><br>  #IF 2<br>            #00121:AA<br>  #ELSEIF 2<br>            #00123:BB<br>  #ELSEIF 5<br>            #00125:CC<br>  #ELSE<br>            #00126:DD<br>  #ENDIF<br><br>#ENDRANDOM</code></pre>|- 生成值为 `1` 时，应用：`#00111:UU` + `#00126:DD`<br>- 生成值为 `2` 时，应用：`#00112:VV` + `#00121:AA`<br>- 生成值为 `3` 时，应用：`#00113:WW` + `#00126:DD`<br>- 生成值为 `4` 时，应用：`#00115:ZZ` + `#00126:DD`<br>- 生成值为 `5` 时，应用：`#00115:ZZ` + `#00125:CC`<br><br>在 IIDXv 和 HDX 中，`#IF` - `#ELSEIF` - `#ELSE` 的各块是互斥的选项。一旦找到与生成值匹配的选项，后续选项将被跳过。<br>- 因此，此示例中的 `#00123:BB` **无论生成值如何都绝不会被应用**。<br>- outliner 尚未能模拟此互斥行为。|

---

## `#ELSE`

| 项目 | 内容 |
| ------ | ------ |
| 来源 | nanasi |
| 支持 | nanasi, IIDXv, HDX, outliner, iBMSC (3.0+ / partial), Angolmois, Sonorous, BGAEncAdv, TechnicalGroove |

- `#ELSE` 语句可以在 `#IF` 语句和 `#ENDIF` 语句之间的任意位置书写一次。
- 如果存在 `#ELSEIF`，`#ELSE` 语句应写在 `#ELSEIF` 的后面。
- 如果不需要，我们不必写 `#ELSE`。
- 当存在 `#ELSE` 时，`#IF`-`#ENDIF` 的语句块被分割为 `#IF`-`#ELSE` 和 `#ELSE`-`#ENDIF` 两个区间。
- 当 `#ELSE` 存在于语句块中时，进行以下处理：
  - 如果块的标签与生成值一致，则应用 `#IF`-`#ELSE` 的内容。
  - 如果块的标签与生成值不一致，则应用 `#ELSE`-`#ENDIF` 的内容。
- 当 `#ELSE` 不存在于语句块中时，进行常规处理：
  - 如果块的标签与生成值一致，则应用 `#IF`-`#ENDIF` 的内容。
  - 如果块的标签与生成值不一致，则忽略 `#IF`-`#ENDIF` 的内容。

### RANDOM 示例

|代码|// 1|// 2|// 3|// 4|图示|
|------|------|------|------|------|------|
|<pre><code>#RANDOM 4<br>  #IF 1<br>        #00111:UU<br>  #ELSE<br>        #00112:VV<br>  #ENDIF<br>  #IF 2<br>        #00113:WW<br>  #ELSE<br>        #00114:XX<br>  #ENDIF<br>  #IF 3<br>        #00115:YY<br>  #ELSE<br>        #00116:ZZ<br>  #ENDIF<br>#ENDRANDOM</code></pre>|<pre><code> UU <br><br>    <br><br>    <br><br> XX <br><br>    <br><br> ZZ <br></code></pre>|<pre><code>    <br><br> VV <br><br> WW <br><br>    <br><br>    <br><br> ZZ <br></code></pre>|<pre><code>    <br><br> VV <br><br>    <br><br> XX <br><br> YY <br><br>    <br></code></pre>|<pre><code>    <br><br> VV <br><br>    <br><br> XX <br><br>    <br><br> ZZ <br></code></pre>|<pre><code>┐<br>│<br>│ IF-1 block<br>│<br>┘<br>┐<br>│<br>│ IF-2 block<br>│<br>┘<br>┐<br>│<br>│ IF-3 block<br>│<br>┘</code></pre>|

- 生成值为 `1` 时，应用：`#00111:UU` + `#00114:XX` + `#00116:ZZ`
- 生成值为 `2` 时，应用：`#00112:VV` + `#00113:WW` + `#00116:ZZ`
- 生成值为 `3` 时，应用：`#00112:VV` + `#00114:XX` + `#00115:YY`
- 生成值为 `4` 时，应用：`#00112:VV` + `#00114:XX` + `#00116:ZZ`

### 另一个示例（特殊模式以 1/64 概率应用的谱面）

|`#RANDOM` 方式|使用 `#ELSE` 的方式|说明|
|----------------|-------------------|------|
|<pre><code>#RANDOM 64<br>  #IF 1<br>    // special pattern<br>  #ENDIF<br>  #IF 2<br>    // default pattern<br>  #ENDIF<br>    ...<br>  #IF 64<br>    // default pattern<br>  #ENDIF<br>#ENDRANDOM</code></pre>|<pre><code>#RANDOM 64<br>  #IF 1<br>    // special<br>  #ELSE<br>    // default<br>  #ENDIF<br>#ENDRANDOM</code></pre><br>注：违反 nanasi 规范|- 生成值为 `1` 时，应用特殊模式。<br>- 生成值非 `1` 时，应用默认模式。<br>- 如果不使用 `#ELSE`，则必须写 1 个特殊模式和 63 个默认模式。|

- nanasi 规范写道：“需要覆盖 1-*x* 的所有模式。”
  - 因此，上述两个示例违反了 nanasi 规范。
  - 但是，nanasi 能够准确解析上述两个示例。
  - IIDXv 和 outliner 也能够准确解析上述两个示例。
  - 然而，进行这种流控制时，我们应该使用 `#SWITCH` 而不是 `#ELSE`。

---

## `#ENDRANDOM`

| 项目 | 内容 |
| ------ | ------ |
| 来源 | nanasi |
| 支持 | nanasi, IIDXv, HDX, outliner, iBMSC (3.0+ / partial), Angolmois, Sonorous |

- 用于明确表示 `#RANDOM` 块结束的命令。
- 一个 `#RANDOM` 块由一个 `#RANDOM` 语句开始，由一个 `#ENDRANDOM` 语句关闭。
- 不支持 `#ENDRANDOM` 的实现会直接忽略 `#ENDRANDOM`。
  - 如果 `#ENDRANDOM` 从一开始就包含在 BMS 规范中，程序员的工作会比现在稍微轻松一些。
  - 但事实并非如此，因此解析 `#RANDOM` 成了一件麻烦的工作。
- nanasi 规范写道：“嵌套分支时必须使用 `#ENDRANDOM`。”
  - 但实际上，**即使不嵌套 `#RANDOM`，我们也应该始终写上 `#ENDRANDOM`**。
  - 因为如果没有 `#ENDRANDOM` 的 `#RANDOM` 语句出现 47 次以上，nanasi 会强制终止。
- 在除 nanasi 之外的所有“支持（嵌套）`#RANDOM` 的实现”中，`#ENDRANDOM` 是可省略的。
  - 因此，目前来说，`#ENDRANDOM` 仅用于让 nanasi 准确解释 `#RANDOM`。
  - 但我建议始终写上 `#ENDRANDOM`，因为它能使 BMS 代码更易读。
- IIDXv 和 HDX 在找到 `#ENDRANDOM` 时，会将该层级的 `#RANDOM` 生成值重置为 `1`。
  - 反过来说，只要不进行 `#ENDRANDOM` 的后处理，之前的 `#RANDOM` 值就会被继承。

---

## `#SWITCH n` / `#SETSWITCH n` / `#CASE n` / `#SKIP` / `#DEF` / `#ENDSW`

| 项目 | 内容 |
| ------ | ------ |
| 来源 | nanasi |
| 支持 | nanasi, IIDXv, HDX, outliner, iBMSC (3.0+ / partial), Sonorous (parsing-only), BGAEncAdv, TechnicalGroove |

- 这些与编程语言中的 SWITCH 语法几乎相同。
- `#SWITCH` 会生成从 1 到 `*n*` 之间的一个自然数。不言而喻，`*n*` 应指定为自然数。
- 如果用 `#SETSWITCH *n*` 代替 `#SWITCH *n*`，则会生成常量 `*n*`。这在测试游玩时非常有用。
- 检查 `#CASE` 的标签 `*n*` 是否与 `#SWITCH` 的生成值一致。不言而喻，BMS 解析器会按行号顺序评估 `#SWITCH` 之后存在的每一行。从标签匹配的 `#CASE` 到 `#ENDSW`
  之间的区间内容会被应用。
- 如果在标签匹配的 `#CASE` 与下一个 `#CASE` 之间存在 `#SKIP`，则解析会从 `#SKIP` 跳到 `#ENDSW`。
- 即使下一个 `#CASE` 的标签与目标值不匹配，如果当前 `#CASE` 与下一个 `#CASE` 之间没有 `#SKIP`，则下一个 `#CASE` 的内容也会被应用。也就是说，**nanasi 规范允许
  *fall-through***。
- `#ENDSW` 结束 `#SWITCH` 块。与 `#ENDRANDOM` 的情况不同，谱师不允许省略 `#ENDSW`。
- 也可以使用 `#DEF` 代替 `#CASE`。
  - nanasi 规范写道：“如果之前的 #CASE 都不匹配，则执行。”
  - 但是，即使 `#CASE` 已经与目标值匹配，如果省略了 `#SKIP`，`#DEF` 也会通过 *fall-through* 被应用。
  - `#DEF` 是作为“当所有 `#CASE` 都不匹配时应应用的 **def**ault 选项”而准备的命令（至少在 nanasi 中是这样）。
  - 因此，谱师不应为一个 `#SWITCH` 写多个 `#DEF`。
  - 此外，如果需要 `#DEF`，谱师应将 `#DEF` 写在所有其他 `#CASE` 之后。
    - 这条规则并未写在 nanasi 规范中，但我们必须遵守这条规则。
    - 因为当 nanasi 找到 `#DEF` 后，`#DEF`-`#ENDSW` 之间的所有 `#CASE` / `#SKIP` / `#DEF` 都会失效。也就是说，nanasi 会将 `#DEF`-`#ENDSW`
      视为一个整体，并应用其间所有的内容。
    - IIDXv、HDX 和 outliner 能够正确解释写在 `#CASE` 之前的 `#DEF`。从 `#DEF` 的 *fall-through* 也是有效的。如果 `#DEF` 与下一个 `#CASE` 之间没有
      `#SKIP`，解析会流向下一个 `#CASE`。
  - 我认为“CASE 之前的 DEF”或 *fall-through* 这样的风格是不雅的。但我不懂编程，所以无法下定论。

### SWITCH 示例

|BMS code|说明|
|----------|------|
|<pre><code>#SWITCH 5<br>  #CASE 1<br>    #00111:XX<br>  #CASE 2<br>    #00112:YY<br>    #SKIP<br>  #CASE 3<br>    #00113:ZZ<br>  #DEF<br>    #00114:AA<br>    #00115:BB<br>#ENDSW</code></pre>|1. 生成值为 `1` 时，应用：`#00111:XX` + `*#00112:YY*`<br>2. 生成值为 `2` 时，应用：`#00112:YY`<br>3. 生成值为 `3` 时，应用：`#00113:ZZ` + `*#00114:AA*` + `*#00115:BB*`<br>4. 生成值为 `4` 时，应用：`#00114:AA` + `#00115:BB`<br>5. 生成值为 `5` 时，应用：`#00114:AA` + `#00115:BB`<br><br>斜体表示因省略 `#SKIP` 而被应用的行（即 *fall-through*）。<br>实际使用 `#SWITCH` 时，**我们应该去掉缩进**。（否则 nanasi 会忽略整个 `#SWITCH` 块。）|

### `#SWITCH` 与 `#RANDOM` 的对比

`#SWITCH` 可能比 `#RANDOM` 更巧妙地表达相同的内容。

|`#RANDOM` 方式|使用 `#ELSE` 的方式|`#SWITCH` 方式|1/65535 方式|
|----------------|-------------------|---------------|--------------|
|<pre><code>#RANDOM 64<br>  #IF 1<br>    // special pattern<br>  #ENDIF<br>  #IF 2<br>    // default pattern<br>  #ENDIF<br>    ...<br>  #IF 64<br>    // default pattern<br>  #ENDIF<br>#ENDRANDOM</code></pre>|<pre><code>#RANDOM 64<br>  #IF 1<br>    // special<br>  #ELSE<br>    // default<br>  #ENDIF<br>#ENDRANDOM</code></pre><br>注：违反 nanasi 规范|<pre><code>#SWITCH 64<br>  #CASE 1<br>    // special<br>    #SKIP<br>  #DEF<br>    // default<br>    #SKIP<br>#ENDSW</code></pre>|<pre><code>#SWITCH 65535<br>  #CASE 1<br>    // special<br>    #SKIP<br>  #DEF<br>    // default<br>    #SKIP<br>#ENDSW</code></pre>|

- 使用 `#RANDOM` 时，谱师必须写出所有选项。此示例需要写 63 个默认模式。
  - 所有支持 `#RANDOM` 的实现都能解释此方法。此方法的可移植性最高。
  - 但此方法最为繁琐，谱面文件体积也最大。
- `#ELSE` 是最聪明的方法。虽然没有实际危害，但它违反规范，不够妥当。
- `#SWITCH` 是次优的方法。写法非常简洁，概率也容易调整。
- `#SWITCH` 能表达的内容，`#RANDOM` 也能表达。但我不想写 65534 个 `#IF`。

### SWITCH 其他注意事项

- 以下规则未写入规范，这让我感到不安：
  - 谱师必须按升序书写每个标签。
  - 每个标签只能书写一次。
  - `#RANDOM` 也是如此。一些实现能解释不规范的流控语句，但并非所有实现都能。
- BMSE 不支持的命令行会被分离到 BMSE 的“扩展命令”选项卡。
  - BMSE 能解析 `#IF`-`#ENDIF` 的区间并将其分离到“扩展命令”选项卡，这是非常出色的特性。
  - 但 BMSE 不支持 `#SWITCH`，因此 `#SWITCH` 的区分内容将作为顶层代码被解释。
  - 也就是说，结果与“在 BMSC 中打开 RANDOMized BMS 时”相同。(<https://hitkey.nekokan.dyndns.info/090102/090102_02.png>)
- 目前仍然不存在能够编辑控制流本身的 BMS 编辑器。据我推测，如果存在，它可能会以大纲处理器的形式呈现。
- 根据 nanasi 规范：
  - **`#SWITCH` 块可以写在 `#RANDOM` 块中。**
  - **`#RANDOM` 块可以写在 `#SWITCH` 块中。**
  - **`#RANDOM` 和 `#SWITCH` 可以无限嵌套。**
- 然而，nanasi 实现的控制流令人费解。
  - 如果 `#RANDOM` 嵌套超过 90 层，nanasi 会强制终止。（仅使用 `#RANDOM` 时）
  - 如果 `#SETSWITCH` 嵌套超过 47 层，nanasi 会强制终止。（仅使用 `#SWITCH` 时）
  - nanasi 通过**交替**嵌套 `#RANDOM` 和 `#SWITCH`，可以实现约 100 层以上的嵌套。
- outliner 中可嵌套的流控语句层数取决于[浏览器侧的渲染限制](https://hitkey.nekokan.dyndns.info/nest1000.htm)。

---

## 缩进样式 (Indent style)

| 项目 | 内容 |
| ------ | ------ |
| 来源 | IIDXv |
| 支持 | 见下表 |

| 应用 | 制表符 (U+0009) | 半角空格 (U+0020) | 全角空格 (U+3000) |
| ------ | :---: | :---: | :---: |
| fgt, fgt++, fgt# | Yes | Yes | Yes |
| IIDXv, HDX | Yes | Yes | Yes |
| iBMSC | Yes | Yes | Yes |
| outliner | Yes | Yes | Yes |
| PMSee-V (v1.10.5+) | Yes | Yes | Yes |
| BGAEncoder (仅 HEADER) | Yes | Yes | Yes |
| BGAEncAdv, TechnicalGroove | Yes | Yes | Yes |
| Sonorous (UTF-8) | Yes | Yes | Yes |
| uBMplay | No | Yes | Yes |
| bemaniaDX | No | Yes | Yes |
| LR2 | Yes | Yes | No |
| BMS List Sorter, BME2WAV, BMX2WAV | Yes | Yes | No |
| Angolmois, Sonorous (non-UTF8) | Yes | Yes | No |

- 使用制表符或半角空格进行缩进。（在 IIDXv 支持页面中称为“段落化”）
- 我认为这对谱师非常有用。我已经无法在不使用缩进的情况下编写控制流了。
- 在 fgt, fgt++, fgt#, BGAEncoder（仅限头行）, BGAEncAdv, TechnicalGroove 中，“`#`”以外的任何字符都会成为缩进。
  - forgetalia 系列**无法注释掉命令行**。
  - BGAEncoder **无法注释掉头行**。
  - BGAEncAdv 和 TechnicalGroove 除了“`//`”外，**无法注释掉命令行**。
  - 一些 BMS 因此规范而失去兼容性。（例如：FANKS "RANDOM" COLLECTION 用“`/`”注释掉了 `#random`）

---

## 注释语法 (Comment syntax)

| 项目 | 内容 |
| ------ | ------ |
| 来源 | IIDXv |
| 支持 | IIDXv, HDX, outliner（不包括转义）, BGAEncAdv（仅“`//`”）, TechnicalGroove（仅“`//`”） |

- 用于谱面调试的显式注释语法。
- 注释语法优先于控制语法被解释。
- `;`：单行注释起始符。从 `;` 到换行符的区间作为注释被忽略。
  - 这与 DTX 格式的单行注释语法相同。
- `//`：单行注释起始符。从 `//` 到换行符的区间作为注释被忽略。
  - 这与 C 语言风格编程语言的单行注释语法相同。
- `/*` - `*/`：多行注释的起始符和结束符。从 `/*` 到 `*/` 的区间作为注释被忽略。
  - 这与 C 语言风格编程语言的多行注释语法类似，但**请注意注释块中的换行符会被解释**。
  - 块注释不能嵌套。

### 注释语法示例

|解析前|解析后|说明|
|--------|--------|------|
|<pre><code>#TITLE foo-/*bar-*/baz; :)<br>#RANDOM 23<br>  #IF 1<br>    #00111:XX<br>  #ELSEIF 2/*<br>    #00112:YY<br>  #ELSEIF ; */3<br>    #00113:ZZ<br>  #ELSE//IF 4<br>    #00114:AA<br>  #ENDIF<br>//*<br>#ARTIST foon<br>/*/<br>#ARTIST asso<br>//*/<br>#BPM 130</code></pre>|<pre><code>#TITLE foo-baz<br>#RANDOM 23<br>  #IF 1<br>    #00111:XX<br>  #ELSEIF 2<br><br>3<br>    #00113:ZZ<br>  #ELSE<br>    #00114:AA<br>  #ENDIF<br><br>#ARTIST foon<br><br><br><br>#BPM 130</code></pre>|- 与编程语言不同，不会变成 `#ELSEIF 23` 的结果。<br>- 这是因为遵循了 BMS “一行一个命令”的规范。<br>- 注释的结果是，行中只留下字符 `3`。此行根据 BMS 规范被解释为隐式注释行，被简单地忽略。<br><br>- 如果删除 `//*` 开头的一个斜杠：<br>  - 此处会按如下方式被注释掉：<br>    <pre><code>/*<br>#ARTIST foon<br>/*/<br>#ARTIST asso<br>//*/</code></pre> 或 <pre><code>/* (another solution)<br>#ARTIST foon<br>/*/<br>#ARTIST asso<br>/**/</code></pre><br>  - 这样就可以轻松切换块注释的开关。|

### 转义

- 在 IIDXv 和 HDX 中，**不存在禁用注释语法的手段**。
  - IIDXv 的文档建议将字符串值中想用的注释符号改为全角字符。
  - 例如，将 `#ARTIST audio: foo // video: bar` 改为 `#ARTIST audio: foo ／／ video: bar`。
  - 对于过去的 BMS 作品等无法修改的情况，IIDXv 和 HDX 只能将其解释为 `#ARTIST audio: foo`。
- 在 IIDXv 2.13+ 和 HDX 0.98+ 中，提供了**字符串转义**。（**但这并不能禁用注释语法**）
  - 所有接受字符串值的命令都可以用引号 (U+0022) 将值括起来。
  - 引号括起来的字符串中的注释标记仅作为普通字符串解释。
  - 此外，前置转义字符 “`\`” (U+005C) 的字符仅作为普通字符解释。

| 示例 | IIDXv/HDX 显示 | 其他软件显示 |
| ------ | --------------- | ------------- |
| `#ARTIST C:\usr the "DPer" (http://hitkey.nekokan.dyndns.info/)` | `C:usr the DPer (http:` | `C:\usr the "DPer" (https://hitkey.nekokan.dyndns.info/)` |
| `#ARTIST C:\\usr the \"DPer\" (http:\//hitkey.nekokan.dyndns.info/)` | `C:\usr the "DPer" (https://hitkey.nekokan.dyndns.info/)` | `C:\\usr the \"DPer\" (http:\//hitkey.nekokan.dyndns.info/)` |
| `#ARTIST "C:\usr the "DPer" (http://hitkey.nekokan.dyndns.info/)"` | `C:usr the DPer (https://hitkey.nekokan.dyndns.info/)` | `"C:\usr the "DPer" (https://hitkey.nekokan.dyndns.info/)"` |
| `#ARTIST "C:\\usr the \"DPer\" (http://hitkey.nekokan.dyndns.info/)"` | `C:\usr the "DPer" (https://hitkey.nekokan.dyndns.info/)` | `"C:\\usr the \"DPer\" (https://hitkey.nekokan.dyndns.info/)"` |

- HDX 0.99 及更新版本中，未用 U+0022 括起来的 “`\`” (U+005C) 会直接输出，不再进行转义。

---

## 测试用例

### 1. 来自 BMIIDXView2010.txt

|BMS code|说明|
|----------|------|
|<pre><code>#SWITCH 5<br>    #DEF<br>        #00013:0055<br>        #SKIP<br>    #CASE 1<br>        #00013:0100000000000000<br>        #RANDOM 2<br>            #IF 1<br>                #00014:04<br>            #ELSE<br>                #00014:05<br>            #ENDIF<br>    #CASE 2<br>        #00013:0200000000000000<br>        #SKIP<br>    #CASE 3<br>        #00013:0300000000000000<br>        #SWITCH 2<br>            #CASE 1<br>                #00016:1111<br>                #SKIP<br>            #CASE 2<br>                #00016:2222<br>                #SKIP<br>        #ENDSW<br>        #SKIP<br>#ENDSW</code></pre>|- 缩进<br>- `#CASE` 前的 `#DEF`<br>- 无 `#ENDRANDOM` 的嵌套 `#RANDOM`<br>- 目标值大于已准备的 `#IF` 数量（注：违反 nanasi 规范）<br>- 因省略 `#SKIP` 导致的 fall-through<br>- `#RANDOM` 和 `#SWITCH` 非交替的嵌套<br><br>**通过测试：** IIDXv, HDX, outliner<br>**部分通过（但隔离失败）：** iBMSC (3.0+)|

### 2. オートメーション工場 (automation factory)

<http://www.comeup.info/bofoon2007/automation.zip>

- 6177 个 `#RANDOM` 语句 / 147723 个 `#IF` 语句（无限兄弟）
- 包含头部的语句块
- 超过 60,000 行的巨大嵌套 `#RANDOM`（347781-409539）
- 目标值远远超过已准备的 `#IF` 语句数
- 随机化的 `#TITLE`（对于使用标题的选择画面或 IR 等实现来说，大概是大问题）

**通过测试：** pomu2, IIDXv, HDX, outliner, Angolmois, uBMplay (1.5.0 及更新), Sonorous, BGAEncAdv, TechnicalGroove

**部分通过（但所有嵌套被解除）：** uBMplay (1.4.6 及更早), LR2, fgt++, ruvit, bmx2wav

如果补充了 `#ENDRANDOM`，nanasi 也能通过。(<https://hitkey.nekokan.dyndns.info/a_lot_of_random_for774.zip>)

### 3. 20,november "club edit" [random ver.]

(cranky, 1999-04-10)

- `#RONDAM` 是拼写错误。
- **通过测试：** uBMplay, BMSE, outliner, Sonorous, BGAEncAdv, TechnicalGroove

### 4. Velocity Magic 3 [Aren't you lucky?]

(Speed Magician / Transfero, 2010-09-11) (<http://uploader.bms.ms/data/PW/vm3_ts3.zip>)（目前无法下载）

- lovetricks.ogg » lovetricks.bms
- `#END IF` 是拼写错误。
- **通过测试：** nazoZZ, RDM, ruvit, fgt++, fgt#, LR2, uBMplay, PMSee-V, outliner, Angolmois, Sonorous, BGAEncAdv,
  TechnicalGroove

### 5. kitchen twies

(Orange Strophe, 2009-07-22) (<https://onedrive.live.com/?id=A1F351E1932E6FF7!282&cid=A1F351E1932E6FF7>)

|修正前|修正后|说明|
|--------|--------|------|
|<pre><code>#RANDOM 2<br><br>#00002:0.5<br>#00003:4F<br>#IF 1<br>#00004:01<br>#00006:03<br>#ENDIF<br>#IF 2<br>#00004:02<br>#00006:04<br>#ENDIF</code></pre>|<pre><code>#00002:0.5<br>#00003:4F<br>#RANDOM 2<br>#IF 1<br>#00004:01<br>#00006:03<br>#ENDIF<br>#IF 2<br>#00004:02<br>#00006:04<br>#ENDIF</code></pre>|- 非法的分支包含了不属于 `#IF` 的行。<br>- 2009-10-05：此 bug 已通过补丁修正。<br>- 当然，通过测试是理想的。BM98 就是这样实现 `#RANDOM` 的。<br><br>**通过测试：** RDM, ruvit, nanasi, uBMplay, PMSee-V, bmx2wav, IIDXv, HDX, Angolmois, Sonorous, BGAEncAdv, TechnicalGroove, ...（未调查）|

---
