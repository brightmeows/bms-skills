# BMS Format Specification 相关笔记

## 关于规定的最低要求规格

- 规格摘要：
  - 此文件格式于 1998 年由 Urao Yane 与 NBK 共同设计。
  - 任何人都可以自由使用此格式。
  - 以 `#` 开头的行为命令行，其余内容均被忽略（用于注释）。
  - 命令行不区分大小写。
  - 命令行分为两种：头语句（header）和通道语句（channel）。
  - BMS 文件在运行时会进行编译，因此我们可以自由排列任何行。
    - 引用者注：即头语句可以写在 BMS 的任何位置。
    - 引用者注：头语句不一定写在通道语句之前。

- 但是，规格的详细内容依赖于具体实现。
  - 例如某些实现在特殊情况下会区分大小写，例如 `#LNOBJ XX`。
  - 也存在完全无法识别小写 `<HEADER>` 的实现（O2play）。

## 关于规范未指明的详细实现

- 头语句使用一个半角空格作为分隔命令和值的分隔符。
  - `#header value` = `#` + `<header>` + `␣` + `<value>`
  - 某些实现也支持使用制表符（tab）作为分隔符。
  - 某些实现允许连续空白作为分隔符。
  - 某些实现允许使用空白进行缩进。
  - 不支持这些特性的某些实现可能会错误解析 BMS。

- 通道语句使用一个半角冒号作为分隔命令和值的分隔符。
  - `#xxxCH:00112233` = `#` + `xxx` + `<CH>` + `:` + `<00>` + `<11>` + `<22>` + `<33>`
  - `xxx`：小节编号 [000-999]
  - `<CH>`：通道编号
  - `<00>`, `<11>`, `<22>`, `<33>`：由 2 字符字母数字对组成，对应各对象的索引。

    | 时期 | 进制 | 范围 |
    |---|---|---|
    | 初期：十六进制 | 16\*16 (256) | [0-9A-Fa-f][0-9A-Fa-f] |
    | 中期：有限三十六进制 | 16\*36 (576) | [0-9A-Fa-f][0-9A-Za-z] |
    | 现在：三十六进制 | 36\*36 (1296) | [0-9A-Za-z][0-9A-Za-z] |

  - 由若干「索引」组成的值将等分小节。
  - 若写入 4 个索引，则小节被 4 等分，即指定了「4 个四分音符」的节奏。
  - `00` 表示休止符。
  - `00` 以外的索引所指代的内容取决于各通道，可能是 `#WAVxx`、`#BMPxx` 或其他。

- 谱面文件本质上是纯文本。将扩展名 `TXT` 改为 `BMS` 后，BMS 应用程序便能识别谱面。
  - BMS 文件的编码未在规范中指定。这一事实导致了双字节字符集（DBCS）被篡改的问题（乱码）。
  - 大多数日文 BMS 文件使用 Shift_JIS 编码。<https://en.wikipedia.org/wiki/Shift_JIS>
  - 许多日文 BMS 应用程序不支持多语言编码。
    - 若 BMS 命令的值包含 DBCS，值可能被篡改（乱码）。然而，若 OS 为日文版 Windows，使用 Shift_JIS 编写的 DBCS 恰好能正确显示。
    - 若文件名或目录名包含 DBCS，BMS 应用程序可能发生故障。但在日文版 Windows 上，使用 Shift_JIS 编写的 DBCS 路径恰好能正常工作。
    - 已知在日文版 Windows 上，使用 JIS (ISO-2022-JP) 或 EUC-KR 编写的 BMS 会导致 LR2 和 BMX2WAV 出现问题。
  - 对所有实现而言安全的字符集只有 ASCII (ANSI)。我们不应在文件名和目录名中使用 DBCS。
  - ruvit、iBMSC、IIDXv (2.13+)、HDX (0.98+)、Sonorous、BGAEncAdv、TechnicalGroove 支持多语言编码。
    - 但这也并不能保证字符串能准确显示。
    - ~~在 UTF-16 成为 BMS 主流之前，字符被篡改的问题不可避免。~~
    - ~~即使 UTF-16 普及，此问题也无法完全解决。~~
    - YEN-SIGN problem、WAVE-DASH problem、WON-SIGN problem、GB_18030 等。
  - 图片：ruvit 标题栏 Unicode 字符串示例 / Unicode 文件名示例

## 一般实现的行为

- 同一头语句重复时，采用更靠近文件末尾（EOF）的一侧：

  | remarks | sample BMS code |
  |---|---|
  | (line number 100) | `#TITLE ABC` |
  | (line number 200) | `#TITLE DEF` |

  此 BMS 的 `#TITLE` 值为 `DEF`。
  - 此规则适用于除 `#ExtChr`、`#STP`、`#WAVCMD`、`#OPTION` 以及控制语法之外的所有头语句。
  - 此规则可能不适用于 `#SUBTITLE`、`#SUBARTIST`、`#COMMENT` 和 `#LNOBJ`。
  - 此规则也适用于通道 `#xxx02`（小节长度）。
  - 但若 `#xxx02` 重复，ruvit（b5p3 及之前）采用第一行（b5p4 中已修复）。

- 但以下命令为例外，需要相对复杂的处理：
  - 以下命令可以书写多行：
    - `#ExtChr`：[BM98] 扩展字符
    - `#STP`：[bemaniaDX] 停止序列
    - `#WAVCMD`：[MacBeat] 仿 MOD（MOD 格式说明：<https://en.wikipedia.org/wiki/MOD_%28file_format%29>）
    - `#OPTION`：[nanasigroove] 强制选项
    - `#SUBTITLE`：[nanasigroove] 显式副标题 / [仅 Sonorous 可多重定义]
    - `#SUBARTIST`：[LunaticRave] 协助者信息 / [仅 TechnicalGroove 与 Sonorous 可多重定义]
    - `#COMMENT`：[feelingPomu] 选曲画面字幕 / [仅 Sonorous 可多重定义]
    - `#LNOBJ`：[Rhythm-it] 长按音符终止定义 / [仅 TechnicalGroove 可定义多个 LN 结束音]
  - 控制语法更为特殊。在 BMS 记法中，仅以下命令群构成块结构：
    - `#RANDOM` 或 `#SETRANDOM`
      - `#IF`
      - `#ELSEIF`
      - `#ELSE`
      - `#ENDIF`
      `#ENDRANDOM`
    - `#SWITCH` 或 `#SETSWITCH`
      - `#CASE`
      - `#SKIP`
      - `#DEF`
      `#ENDSW`

- 同一小节的同一通道重复时，两者将被合并。
  - 但此行为不适用于 `#xxx01`（BGM 通道）、`#xxx02`（小节长度）、`#xxxA6`（`#CHANGEOPTION`）。
  - 多行 BGM 必须作为多行 BGM 解释，不得合并。
  - 多条 `#xxx02` 通道行冲突时，采用最靠近 EOF 的行。
  - 动态选项变更通道与 BGM 通道具有相同性质，可同时变更多项选项。

- 行号较大者优先，但 `00` 不会覆盖旧位置：

  | remarks | sample BMS code | comment |
  |---|---|---|
  | (行号 100) | `#00113:11111111` | // `1100110011001100` |
  | (行号 200) | `#00113:0022332255224400` | // `#001` 的通道 `13` 重复 |
  | (行号 300) | `#00113:0066` | // `#001` 的通道 `13` 重复 |
  | 语法解析结果： | `#00113:1122332266224400` | // 3 行全部合并 |

  这是 BM98k 为便于编写复杂节奏而准备的行为。
  - **分行书写的技巧**（假定最终合并）能够实现非常有趣的表现。
  - 将此技术发挥到极致的作品是《オートメーション工場》（<http://www.comeup.info/bofoon2007/automation.zip>）。
  - 将此技术用得最为克制的作品是《Netzwerkgenen》（<https://manbow.nothing.sh/event/event.cgi?action=More_def&num=39&event=76>）。

- 但遗憾的是，约半数的实现并未满足此规范。（……这本身算是「要求规格」吗？）
  - 不考虑通道行重复的实现可能无法准确统计物件数量。
  - pomu2 具有自动变更物件数组的功能，但通道行重复时此功能无法正常工作。（原谱面重复 5 次的示例：<https://hitkey.nekokan.dyndns.info/100418_duplicate_channel_pomu2.png>）
  - LR2 具有类似 pomu2 的功能，存在相同 bug，无法正确处理重复的地雷通道。

- BMS 解析器可能需要读取混合了三种换行符的文件（CRLF、CR、LF）。
  - 例如：<https://web.archive.org/web/*/http://soflan.hp.infoseek.co.jp/mrym.zip>

- BMS 解析器可能需要读取不包含换行符的 EOF。
  - 例如：`#08401:ZZ[EOF]`
  - 我们会发现比预期更多的内联 EOF。

- 规范未规定应如何解释给出了非法值的通道行。

  | sample BMS code | remarks |
  |---|---|
  | `#00111:0011文字2233` | // 混入 DBCS |
  | `#00112:0011223` | // 数据长度非 2 的倍数 |
  | `#00113:+-;$%&'()/` | // 无法用作 `#WAV` 索引的特殊字符 |
  | `#00114:1100...(over 500000 characters)...011` | // 数据过长 |
  | `#00115:11  ;comment` | // DTX 的内联注释（GDAC2 无法正确解析） |
  | `#00102:12.375f` | // IIDXv 与 HDX 的 float 型显式选项 |
   | <code>#00121:  FFFFFF  OOOOOO  OOOOOO  NNNNNN<br>#00122:  FF      OO  OO  OO  OO  NN  NN<br>#00123:  FFFFFF  OO  OO  OO  OO  NN  NN<br>#00124:  FF      OO  OO  OO  OO  NN  NN<br>#00125:  FF      OOOOOO  OOOOOO  NN  NN</code> | // ASCII art（[Shift_JIS art](https://en.wikipedia.org/wiki/Shift_JIS_art)） |

  某些实现（nazo、uBMplay、LR2 等）会尝试解释这些内容，但方式不明。「适当解释」的定义亦不明。

- BMS 解析器可能需要读取如下形式的头语句：

  | sample BMS code | remarks |
  |---|---|
  | `#stopA 192` | // 定义编号省略前导零 |
  | `#stop11 -192` | // 负数值 |
  | `#stop22␣` | // 空定义槽（带分隔符） |
  | `#stop33` | // 空定义槽（无分隔符） |
  | `#if⇥   4` | // 制表符分隔符 |
  | `#bpm 2.147484e+0.9` | // 科学记数法表示的值 |
  | `#bpm99 12.375f` | // IIDXv 与 HDX 的 float 型显式选项（BMSE 崩溃） |
  | `#WAV60 ura_63.wav` | // 实际文件名为 `ura_63..wav`（<http://www.comeup.info/bofoon2007/automation.zip>） |
  | `#bga01⇥ fz␣512␣␣256␣␣768␣␣384␣␣0␣␣␣64␣␣big.bmp` | // 多个制表符和空格排列的值 |
  | `#bmp字 big.bmp` | // 定义编号为 DBCS |
   | <code>#random 10<br>⇥   #if 1<br>⇥   ⇥   #wavZZ foo.wav<br>⇥   #else<br>⇥   ⇥   #wavZZ bar.wav<br>⇥   #endif<br>#endrandom</code> | // 多个制表符和空格的缩进 |

  某些实现允许上述内容作为有效值，而另一些实现则将其视为无效值。

- 现代实现支持 1295 (1296) 种槽位。各头语句的值定义在 [01-ZZ]（或 [00-ZZ]）的索引中。
- 小节用 3 位数字表示，一般实现支持 [`#000`-`#999`]。
- 若干例外与注意事项：

  | apps | remarks |
  |---|---|
  | BMSV | [000-511] |
  | DDR | [000-998] |
  | bemaniaDX | [000-399] |
  | GALLI | [000-399] |
  | BGAenc | [000-249] |
  | BMEV | BMEV 打开只有 `#000` 内容的谱面时会崩溃。 |
  | RDM | RDM 无法滚动只有 `#000` 内容的谱面。 |
  | GDAC2 | GDAC2 在 `#170` 之后进行「粘贴」时会崩溃（Ver.0.20 中已修复）。 |
  | LR2 | 若可见物件或长按音符物件存在于 `#000` 开头，LR2 会在 `#000` 之前插入与 `#000` 等长的空小节。（这是避免游戏启动时困扰用户的方法之一）。此功能虽方便，但若物件同时存在于 `#000` 和 `#999`，谱面将无法正常结束。 |
  | pomu2 | 键音播放期间 pomu2 不会结束谱面。但若谱面超过 `#999` 后仍有键音在播放，pomu2 会崩溃。 |
  | otama | 根据 otama 的文档（<https://www.asahi-net.or.jp/~VG5M-OBT/otamadoc.html>）：「小节最大数为 1024」。但此描述颇为奇怪，`#1024xx` 会引发 bug。 |
  | MyO2 | 382.《[ニコニコ動画] 組曲》在谱面跑完前会崩溃，这与小节数无关吗？以下为错误信息引用（乱码部分已省略）：<br>`TypeError: Error #1010: A term is undefined and has no properties.`<br>`  at ::scorenum/()[E:\popstage_5key\com\scorenum.as:44]`<br>`  at ::scorenum/setNum()`<br>`  at ::o2jam/()[E:\popstage_5key\com\o2jam.as:2951]`<br>`  at ::o2jam/()[E:\popstage_5key\com\o2jam.as:2630]`<br>`  at ::o2jam/()[E:\popstage_5key\com\o2jam.as:3056]` |
