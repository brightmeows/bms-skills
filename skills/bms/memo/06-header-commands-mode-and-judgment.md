# 头部命令：模式与判定

> 来源：<https://hitkey.nekokan.dyndns.info/cmdsJP.htm#HEADER>

## #PLAYER [1-4]

| origin | support |
| -------- | --------- |
| BM98 | 几乎全部 |

- 定义玩家数。
- 命令省略时，默认值为 `1`。

- 实际上，该值并非玩家数，而是游戏模式的选择。对应关系如下：

| command | summary | gauges | abbreviated name | remarks |
| --------- | --------- | -------- | ------------------ | --------- |
| `#PLAYER 1` | 1P | 1 条 groove 值 | Single Play, 1P, SP | default |
| `#PLAYER 2` | 1P + 2P | 2 条 groove 值 | Couple Play, 2P | 近年来此模式几乎不被支持。 |
| `#PLAYER 3` | 1P + 2P for 1P | 1 条 groove 值 | Double Play, DP | 现代应用程序实现了中心双打（CENTER DOUBLE）。 |
| `#PLAYER 4` | 1P vs 2P | 2 条 groove 值 | Battle Play | 仅 BM98 (BM98k) 支持。 |

- `#PLAYER 2` 是双人同时演奏双人用谱面的模式。类似合奏或二重奏。
  - “Couple Play” 在 *beatmaniaIIDX 19 Lincle* (2011-09-15) 中被移除。
  - 取而代之的是 **“FREE PLUS”** 模式，即双人同时演奏“两份单人用乐谱”。
  - 例如，左侧玩家演奏 “DIAVOLO (SP-HYPER)”，右侧玩家演奏 “DIAVOLO (SP-ANOTHER)”。

- “Battle Play” 同时进行两个 “Single Play”。
  - 这是两名玩家同时演奏同一 SP 谱面、比拼分数的模式。
  - 因此，如果有 `#PLAYER 1` 的乐谱，客户端可通过游戏选项覆盖。
  - 无需为 “Battle Play” 特意准备特殊乐谱。
  - 因规范无意义，BM98 之后的所有软件都停止了对 `#PLAYER 4` 的支持。
  - 同样地，`#PLAYER 2` 是可由客户端覆盖的模式。

- beatmania 的 “Single Play” 画面为 3 列静态布局：

  ```text
  +--------+ +--------+ +--------+
  |1P side | |movie   | |2P side |
  |notes   | |display | |notes   |
  +--------+ +--------+ +--------+
  ```

  - 左列：显示 1P 侧谱面的屏幕
  - 中列：影片屏幕
  - 右列：显示 2P 侧谱面的屏幕
  - 对于街机 beatmania，此布局源于设备的物理设计。

- 然而在 BMS 中，并非必须采用此布局。
  - 因为 PC 键盘并非为两人同时操作而设计。
  - 两人共用一台 PC 键盘的游玩方式已不再被接受。
  - 即 “Couple Play” 和 “Battle Play” 已不再受欢迎，取而代之的是在线对战。

- BM98, BM98de, DDR, bemaniaDX, DDR 及 fgt++ 沿用了这种静态 3 列布局。LR2 的部分皮肤也是如此。
  - 但这种布局在其他实现中几乎见不到了。

- 基于这些原因，`#PLAYER 2` 和 `#PLAYER 4` 取决于具体实现。
  - 例如，bemaniaDX 将 `#PLAYER 2` 和 `#PLAYER 4` 解释为 `#PLAYER 3`。

- beatmania 系应用程序确定实现了 “Double Play”，即 `#PLAYER 3`。
  - 因为在 beatmania 中，DP 是非常流行的游戏模式。
  - 1P 侧谱面与 2P 侧谱面不相邻的 DP 布局，俗称为 “TWIN MIX PLAY (TMP)”。
    - 早期 beatmania 系列不支持 “CENTER DOUBLE”，因此 DP 非常困难。
  - “CENTER DOUBLE” 的画面布局为 1P 侧与 2P 侧相邻：

    ```text
    +--------+ +--------+ +--------+        ┌┐+--------+ +--------+┌┐
    |1P side | |2P side | |movie   |        └┘|1P side | |2P side |└┘
    |notes   | |notes   | |display |   or   ┌┐|notes   | |notes   |┌┐
    +--------+ +--------+ +--------+        └┘+--------+ +--------+└┘
    ```

    - 影片屏幕显示在谱面以外的空间。
    - 一些实现可以分屏显示影片。 (<https://www.youtube.com/watch?v=FtQ8woowZiM>)
  - “CENTER DOUBLE” 首次出现在 *beatmania complete MIX 2* (2000-01-27)。
    - 因此一些较旧的实现不支持 “CENTER DOUBLE”。

- `#PLAYER` 不约束频道，因此 `#PLAYER 1` 和 `#xxx21-29` 可以同时写入。

**for example:**

```text
# PLAYER 1
# 00111:1100000000000000
# 00121:2100000000000000
# 00122:0022000000000000
# 00123:0000230000000000
# 00124:0000002400000000
# 00125:0000000025000000
# 00128:0000000000260000
# 00129:0000000000002700
# 00126:000000000000002S
```

**in nazobmplay:**
![nazo-2p-bug](https://hitkey.nekokan.dyndns.info/cmd/nazo-2p-bug.png)

**remarks:**

依赖 `#PLAYER` 命令进行渲染的实现会出现问题。
`#PLAYER 1` 应应用 “Single Play” 模式，但朴素的实现也会显示 2P 侧物件。
此 bug 被一些幽默谱面故意利用。
此时玩家击打物件的计数方式取决于实现。

- 现代实现不再信任 `#PLAYER`。
  - LR2, nanasi, ruvit 和 pomu2 忽略 `#PLAYER`，从解析出的频道推断实际游玩模式。
  - 这种方法不会犯低级错误，比依赖 `#PLAYER` 要好。
  - 但这种方法并非始终有效。
    - 仅使用 `#xxx16` 频道的谱面可能是以下任意一种：5KEYS, 7KEYS, 10KEYS, 14KEYS, 9KEYS（若扩展名为 PMS）, 18KEYS... 应用程序无法判断谱师希望的是哪种模式。
    - 未使用 `#xxx21-29` 但使用了 `#xxx41-49` 或 `#xxx61-69` 时：谱面的 2P 侧不存在可视物件，但谱师希望该谱面以 DP 方式渲染。然而旧版 ruvit 将其解释为 SP（已在 b5p6 中修复）。
  - 目前，BMS 格式在理论上没有解决这些问题的方法。
    - 因此 LR2 和 ruvit 通过硬编码单独处理了例外谱面。
    - 下一代格式将采用能详细指定游玩模式的记法，从理论上解决此问题。
    - 它将使用类似 `#OCT/FP` 命令的标识符作为标志。

- 近年来，`#PLAYER` 仅为了向后兼容而存在。

## #RANK [0-3]

| origin | support |
| -------- | --------- |
| BM98 | 几乎全部 |

- 判定等级的定义。
- 省略时，默认值 ~~为 `3`~~。

| 不适用: | BMSC, HDX（不支持 `#RANK`）, |
| `0` 适用: | nBMplay, GDAC2, |
| `2` 适用: | BM98, BM98de, MW, nazo, DDR, nanasi, LR2, ruvit, Angolmois, uBMplay, TechnicalGroove, |
| `3` 适用: | BMSE, iBMSC, |

- 值与标签的对应关系由规范定义。

| name & value | label | range in LR2 | remarks |
| -------------- | ------- | -------------- | --------- |
| `#RANK 0` | VERY HARD | ± 8ms | |
| `#RANK 1` | HARD | ±15ms | |
| `#RANK 2` | NORMAL | ±18ms | |
| `#RANK 3` | EASY | ±21ms | |

- 判定宽度取决于各实现。判定方式也取决于实现。

| judgment system | support | remarks |
| ----------------- | --------- | --------- |
| 像素判定 | 早期实现 | 判定宽度取决于谱面滚动速度。<br>BPM 越高判定越严格，BPM 越低判定越宽松。 |
| 时间判定 | DDR, uBMplay, pomu2, LR2, ... | 无论 BPM 如何，判定宽度始终不变。 |
| 动态像素判定 | nanasi, ruvit, fgt++, ... | BPM 变更时，重新计算适当的判定宽度。 |

- 部分实现忽略 `#RANK`。
  - D3beat 忽略谱面的 `#RANK` 指定，强制应用各用户设定的判定难度。这大概是 `#RANK` 直接应用于触屏设备不太方便。
  - O2mania ~~忽略谱面的 `#RANK` 指定，强制应用各用户设定的判定难度。~~ 判定等级似乎只有三种（Easy, Normal, Hard），未见 “Very Hard”。

### #RANK 4

| origin | support |
| -------- | --------- |
| nanasi | nanasi, ~~ruvit~~, fgt++, iBMSC (3.0+), Angolmois, Sonorous, TechnicalGroove |

- ナナシグルーヴ 规范将值 `4` 赋予名为 VERY EASY 的标签。
- `#RANK 4` 是比 `#RANK 3` 更宽松的判定。
- ruvit 支持 `#DEFEXRANK` 和 `#EXRANK`，但**不支持 `#RANK 4`**。
  - 在选曲列表中比较 `#RANK 3` 或 `#DEFEXRANK` 与 `#RANK 4` 的情况即可明确。
  - ruvit 在读取无效的 `#RANK` 值时，似乎会以 NORMAL 判定（`#RANK 2`）为默认值。

### 相对 #RANK

| origin | support |
| -------- | --------- |
| fgt | fgt, Angolmois, TechnicalGroove |

- 根据 forgetalia 的帮助文件，判定等级的初始值为 “`#RANK` 值加 2”（？）
- 另外，文件指出可以指定 `#RANK 6` 或 `#RANK -1` 之类的值（？？）
- 但我无法理解此规范。引用 forgetalia 帮助文件：
  > `#EXRANK` 与 `#RANK` 不作区分。由判定值来决定是哪一种。
  > 通常时的判定宽度为 (`#RANK+2`)。也就是说，`#RANK 6` 和 `#RANK -1` 都是可以的……
- 我很难将这晦涩的日文翻译成英文。
- Angolmois 2.0 alpha 2 的相对 `#RANK` 与 forgetalia 的实现不同。引用 INTERNALS.md：
  `±{BAD:144, GOOD:84, GREAT:48, COOL:14.4} / (1.5 - #RANK-value * 0.25)` milliseconds
  但 “Angolmois #RANK system is certainly ill-designed however, so it may change without a notice.”
- TechnicalGroove 的 `#RANK` 是 `#EXRANK` 的别名，仅用于向后兼容。
  - 应用 “`25 * #RANK-value + 50`” 作为 `#EXRANK` 的值。可带小数，无上限。
  - JUST 范围以 `#DEFEXRANK 100` 为 `±16ms`（每增加 1 放宽 `0.16ms`）。

## #DEFEXRANK n

| origin | support |
| -------- | --------- |
| nanasi | nanasi, ruvit (2.0b3+), fgt++, iBMSC (3.0 / 实际是 `#EXRANK` 而非 `#DEFEXRANK`), Sonorous, TechnicalGroove |

- 可以比 `#RANK [0-3]` 更详细地指定判定难度。
- 以百分比指定判定初始值。值 `100` 相当于 `#RANK 2`（标签：NORMAL）。
- 可指定小数。
- 找到多个 `#RANK` 和 `#DEFEXRANK` 时，采用最接近 EOF 的行。
- 省略时应用 `#RANK`。
- `#DEFEXRANK` 和 `#RANK` 都省略时，应用 `#RANK 2`。

- nanasi 中，值的范围与标签关联如下，选曲列表显示对应图标：

| label | 标准 | nanasi 的情况 | fgt++ 的情况 | TechnicalGroove 的情况 | remarks |
| ------- | ------ | ------------- | ------------- | ------------------------ | --------- |
| OVER HARD | - | `#DEFEXRANK ↑` | `#DEFEXRANK ↑` | `#DEFEXRANK ↑` | nanasi 独有的标签 |
| VERY HARD | `#RANK 0` | `#DEFEXRANK 48` | `#DEFEXRANK 60` | `#DEFEXRANK 50` | |
| MORE HARD | - | `#DEFEXRANK ↕` | `#DEFEXRANK ↕` | `#DEFEXRANK ↕` | nanasi 独有的标签 |
| HARD | `#RANK 1` | `#DEFEXRANK 64` | `#DEFEXRANK 80` | `#DEFEXRANK 75` | |
| LITTLE HARD | - | `#DEFEXRANK ↕` | `#DEFEXRANK ↕` | `#DEFEXRANK ↕` | nanasi 独有的标签 |
| NORMAL | `#RANK 2` | `#DEFEXRANK 100` | `#DEFEXRANK 100` | `#DEFEXRANK 100` | **基准值** (= 100%) |
| LITTLE EASY | - | `#DEFEXRANK ↕` | `#DEFEXRANK ↕` | `#DEFEXRANK ↕` | nanasi 独有的标签 |
| EASY | `#RANK 3` | `#DEFEXRANK 120` | `#DEFEXRANK 120` | `#DEFEXRANK 125` | ~~省略判定时的默认值~~ |
| MORE EASY | - | `#DEFEXRANK ↕` | `#DEFEXRANK ↕` | `#DEFEXRANK ↕` | nanasi 独有的标签 |
| VERY EASY | `#RANK 4` | `#DEFEXRANK 160` | `#DEFEXRANK 140` | `#DEFEXRANK 150` | nanasi ~~和 ruvit~~ 与 fgt++ 支持的标签 |
| OVER EASY | - | `#DEFEXRANK ↓` | `#DEFEXRANK ↓` | `#DEFEXRANK ↓` | nanasi 独有的标签 |
| RANDOM | - | `#EXRANKxx n` | `#EXRANKxx n` | `#EXRANKxx n` | 存在动态判定变更时 |

- fgt++ 没有 HI-SPEED 选项，因此推测其判定设定得较宽松。
- ruvit 支持页面中，虽然未找到具体细节，但有如下描述：“与 nanasi-groove 略有差异。”
- 例如，指定 `#DefExRank 199.97` 时，谱面将以 NORMAL 判定（`#RANK 2`）的 199.97% 判定宽度开始。
- iBMSC 3.0 有 `#EXRANK` 的文本框，但实际上那是 `#DEFEXRANK`。
  我们使用它时，必须用文本编辑器打开输出的 BMS 文件，将 `#EXRANK` 改写为 `#DEFEXRANK`。

### #DEFEXRANK 0

| origin | support |
| -------- | --------- |
| TechnicalGroove | ~~TechnicalGroove~~ |

- 使除 Scratch 外的演奏通道判定变为 “All or Nothing”。
- 存在 [Perfect GREAT, GREAT, GOOD, BAD, POOR] 五种判定等级的系统，只会出现 [Perfect GREAT, BAD, POOR]。
- 此外，键盘通道的 Perfect GREAT 判定仅**1 帧宽度**。
- 这是与 beatmaniaIIDX 的 *GAMBOL* (Another) 相同的判定模式。
- `#EXRANK[01-ZZ] 0` 同理。

## #EXRANK[01-ZZ] n

| channel | origin | support |
| --------- | -------- | --------- |
| `#xxxA0` | nanasi | nanasi, ruvit (2.0b3+), fgt++, Sonorous, TechnicalGroove |

- 判定宽度的动态变更。
- 命令省略或定义为空时，不影响判定。

**for example:**

```text
# RANK 2
# EXRANKaa 48
# EXRANKcc 100
# 114a0:aa0000cc
```

**remarks:**

在 `#114` 中，判定变为与 nanasi 基准的 “VERY HARD” 相同宽度。
在 `#114` 的第 4 拍，判定变为与 “NORMAL” 相同宽度。

- nanasi 中，对于描述了 `#EXRANK` 的谱面，判定难度标签为 “RANDOM”。
- 选曲列表中会显示专用图标。

## #TOTAL n

| origin | support |
| -------- | --------- |
| BM98 | mostly |

- 所有物件均以最佳判定演奏时，groove 值总量最多增加 `n`%。
- groove 值增加率由此值计算。
- 大部分实现应支持小数值，但我未确认详情。
- 此命令未写入英文版 BMS 规范，但出现在 BM98 帮助文件中。
  - 这是隐含的要求规范吗？还是不是？我无法判断，但日本的 BMS 可播放软件几乎全部实现了 `#TOTAL`（除 HDX 外）。
- 例如指定 `#TOTAL 200` 时，groove 值最大增加至 200%。
  - 若 groove 值初始值为 20%，则 groove 值将增至 220%。
  - 演奏物件总数为 400 时，每个最佳判定 groove 值增加 0.5%（200 / 400 = 0.5）。
- 这仅仅是 groove 值的上升率，实际 groove 值当然不会超过 100%。

- 省略 `#TOTAL` 时，规范未规定其应如何作用。
  - BM98 中，默认值为可视物件数加 200。
  - 此行为是 “BMS 格式的规范” 还是 “BM98 的独有解释”，尚不明确。
  - 大多数实现基于 BM98 的解释，但并非所有实现都如此。例如：

| body | default |
| ------ | --------- |
| bm98 | `200+NOTES` |
| bemaniaDX | `200` |
| LR2 | `160`?（注：NORMAL Gauge） |
| ruvit | `160`?（注：NORMAL Gauge） |
| nanasi | `350` |
| fgt++ | `100+NOTES/8` (min: `#TOTAL 125`) |
| nazo | `100+NOTES`? (min: `#TOTAL 130`?) |
| TechnicalGroove | `0` |

- **强烈不建议省略 `#TOTAL`**，因适用的默认值因软件而异。

- 关于适当计算公式的讨论：
  - *beatmaniaIIDX* 的 groove 值增加率据称为 “`7.605*NOTES/(0.01*NOTES+6.5)`”。
  - （但 IIDX 在 `#TOTAL` 低于 `260` 时，似乎会应用 `260`。）
  - 但也有不同意见，DJMAX 和 O2Jam 则又另当别论。

- 部分实现中，指定负值为 `#TOTAL` 时，精确击打音符反而会减少 groove 值。
  - nazo, nazoZZ, PMSee-V, ...（可能还有同类实现，但我未作调查。）

- 本部分描述了 “beatmania / beatmaniaIIDX 中的 NORMAL groove 值”。但：

| name | the first appearance | remarks |
| ------ | --------------------- | --------- |
| NORMAL gauge | 1997-12-10: beatmania | 1 阶段（乐曲）结束时，若 groove 值达一定量则阶段 Clear。 |
| EXPERT gauge | 1998-09-28: beatmania 3rdMIX | 从 groove 值 100% 开始，阶段结束时若刻度剩余则阶段 Clear。即所谓的生命值条（EXPERT 模式从初代即存在，但 EXPERT groove 值从 3rdMIX 开始引入）。 |
| HARD gauge | 2001-09-28: beatmaniaIIDX 6th style | 玩家可选的选项之一。减少率较大的生命值条。EXPERT 模式下，groove 值比通常更难增加、更容易减少。 |
| EX-HARD gauge | 2011-09-15: beatmaniaIIDX 19 Lincle | 同上，但减少更多。存活玩家可获得勋章。 |
| HAZARD gauge | 2008-12-19: beatmaniaIIDX 16 EMPRESS | 连击中断即 STAGE FAILED。 |
| Grade gauge | 2002-03-27: beatmaniaIIDX 7th style | 专为段位认定调整的生命值条。 |
| EX-Grade gauge | 2011-09-15: beatmaniaIIDX 19 Lincle | 同上，但减少更多。此选项通过特定命令输入适用。 |

- 这些分别属于不同语境。
- `#TOTAL` 是 1998-08-10 实现的命令。生命值条型 groove 值在当时尚不存在。

## #VOLWAV n

| origin | support |
| -------- | --------- |
| BM98 | BM98k, DDR, WAview, bemaniaDX, Mac, Aqua, KbMedia, PMSee-V, BMSC, BMSE, TechnicalGroove |

- 谱面使用的所有音源以原始值的 `n`% 音量播放。
- 原始音量等价于 `#VOLWAV 100`。
- 省略时默认值为 `100`。
- `#VOLWAV` 非常依赖于各实现和硬件，因此不应信任此命令。
- 目前此命令已被弃用。

- `#VOLWAV 200`：谱面使用的音源以原始值的 200% 音量播放。

### BGM 音量变更

| channel | range | origin | support |
| --------- | ------- | -------- | --------- |
| `#xxx97` | `[01-FF]` | fgt | fgt（fgt++/fgt# 已删除，**已过时？**） |

- 以 255 级动态变更 BGM 音量。
- 以十六进制指定 [1-255] 的整数值。`00` 为休止符。

**For example:**

```text
# 00197:003C
# 00497:FF
```

**remarks:**

BGM 音量在 `#001` 中途变为 60（3C = 60）。
BGM 音量在 `#004` 开头恢复原音（FF = 255）。

### KEY 音量变更

| channel | range | origin | support |
| --------- | ------- | -------- | --------- |
| `#xxx98` | `[01-FF]` | fgt | fgt（fgt++/fgt# 已删除，**已过时？**） |

- 以 255 级动态变更玩家演奏的 KEY SOUND 音量。
- 以十六进制指定 [1-255] 的整数值。`00` 为休止符。

**For example:**

```text
# 00198:003C
# 00498:FF
```

**remarks:**

KEY 音量在 `#001` 中途变为 60（3C = 60）。
KEY 音量在 `#004` 开头恢复原音（FF = 255）。
