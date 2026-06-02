# 头部命令（HEADER）——元数据

## `#TITLE <string>` / `#SUBTITLE <string>`

### `#TITLE <string>`

| | |
|---|---|
| origin: | BM98 |
| support: | 几乎所有 |

- 定义曲目标题。
- 如果省略此命令，规范未规定实现应如何行为。
  - 例如，若将省略此命令的 BMS 拖入 nanasi，程序将强制退出。
- 规范未设定字符串长度上限。
  - 过长标题的示例：
    <http://dee.manbow.org/event/event.cgi?action=More_def&num=127&event=60>
  - BMSE 可输入至少 10000 字符。但其他应用程序不一定能显示。
    <https://hitkey.nekokan.dyndns.info/bmse_help_full/Capture/textlength43679.txt>
  - DDR 若超过 500 字节将强制退出（文档中已说明）。
- 部分实现会对文本进行 trim（nanasi, LR2 等）。
  - 例如 `#TITLE    ABC   ` 将显示为 "ABC"。
  - BMSE 不会 trim 文本。不进行无意识改动的编辑器是可取的。
- 若文本包含多字节字符，可能产生字符编码问题。
  - ruvit, iBMSC, IIDXv, HDX, Sonorous, BGAEncAdv, TechnicalGroove 支持部分 ASCII 以外的字符集。
  - 此外，LR2 的次世代版本采用带 BOM 的 UTF-16LE 作为默认字符集，而非 ASCII。
  - 此类应用可指定如 `#TITLE LOVE♡SHINE` 的值。
  - 我对其他应用的多语言支持情况了解不多。
  - HTML5 规范草案已移除对 UTF-7 和 UTF-32 字符集的支持。
    (<https://www.w3.org/TR/2008/WD-html5-20080122/>)
    > Authors should not use JIS_X0212-1990, x-JIS0208, and encodings based on EBCDIC.
    > Authors should not use UTF-32. Authors must not use the CESU-8, UTF-7, BOCU-1 and SCSU encodings. [CESU8] [UTF7] [BOCU1] [SCSU]
  - 当 BMS 文件在 Web 应用程序中使用时，UTF-7 和 UTF-32 可能成为障碍。

#### 隐式子字符串（`#TITLE`）

| | |
|---|---|
| origin: | DDR |
| support: | DDR |

- 若在 `#TITLE` 或 `#GENRE` 的值中发现特定字符串，选曲列表和游戏画面上将显示相应标签。
- 仅在 Arrow-mode 下才会在游戏画面中显示标签。
- 值不区分大小写。

| 被搜索的字符串: | label: |
|---|---|
| Basic, Light | Basic |
| Another, Trick, Standard | Another |
| Maniac, SSR, Heavy | SSR |

- **显然，当曲名包含这些词时，此功能会失效。**
- 关键词重复时，将应用标签 "Basic"。

#### 隐式副标题

| | |
|---|---|
| origin: | bemaniaDX |
| support: | bemaniaDX, nazo, nazoZZ, LR2, TechnicalGroove |

- 若 `#TITLE` 的值包含分隔符字符，该部分将被解释为副标题。
- 在选曲列表、加载画面等界面中，副标题预期将以与主标题不同的形式显示。

| example | remarks |
|---|---|
| `#TITLE main-sub-` | 连字符减号 (<https://www.fileformat.info/info/unicode/char/002d/index.htm>) |
| `#TITLE main～sub～` | 全角波浪线 (<https://en.wikipedia.org/wiki/Tilde#Japanese>) |
| `#TITLE main(sub)` | 括号（bemaniaDX 也支持全角括号？？） |
| `#TITLE main[sub]` | 方括号 |
| `#TITLE main<sub>` | 尖括号 |
| `#TITLE main  sub` | 两个以上半角空格（仅 bemaniaDX？） |
| `#TITLE main"sub"` | 双引号（仅 LR2 和 TechnicalGroove） |

- 问题在于规范未规定分隔符的优先级。
- 当混合多种分隔符时，解释取决于实现。
  例如：`#TITLE while(1) { LOVE -- }`

| implementation | maintitle | subtitle |
|---|---|---|
| bemaniaDX | `while` | `(1) { LOVE -- }` |
| nazo / nazoZZ | `while(1) { LOVE ` | `-- }` |
| LR2 | `while(1) { LOVE -- }` | (empty) |

- 对于难以解释的文本，规范未提供任何提示。
- 此外，不存在防止这种麻烦行为的方法。
- `#SUBTITLE` 命令很可能是为解决这些问题而提出的。

### `#SUBTITLE <string>`

| | |
|---|---|
| origin: | nanasi |
| support: | nanasi, LR2, iBMSC (3.0+), Sonorous, TechnicalGroove, diff |

- `#TITLE` 的隐式副标题在很多情况下无法正常工作。
- 此时，显式定义 `#SUBTITLE` 会更好。
- 若不使用 `#SUBTITLE`，可以省略。
- LR2 和 TechnicalGroove 在省略 `#SUBTITLE` 时，会采用 `#TITLE` 的隐式副标题。
- LR2 中若 `#SUBTITLE` 与隐式副标题冲突，隐式副标题字符串将不显示。

#### `#SUBTITLE` 的多重定义

| | |
|---|---|
| origin: | Sonorous |
| support: | Sonorous |

- 可定义多个副标题。语法解析结果对比如下：

| BMS code | Sonorous | LR2, nanasi, iBMSC |
|---|---|---|
| ```
#TITLE title
#SUBTITLE (5keys)
#SUBTITLE [Beginner]
#ARTIST artist
``` | ```
title
(5keys)
[Beginner]
artist
``` | ```
title

[Beginner]
artist
``` |

---

## `#ARTIST <string>` / `#SUBARTIST <string>`

### `#ARTIST <string>`

| | |
|---|---|
| origin: | BM98 |
| support: | 几乎所有 |

- 定义艺术家。
- "艺术家"是什么？规范未予说明。

### `#SUBARTIST <string>`

| | |
|---|---|
| origin: | LR2 |
| support: | LR2, nanasi, iBMSC (3.0+), Sonorous, TechnicalGroove, diff |

- 我们可以使用此命令来显示 BGA 创作者、铺面制作者及其他协作者。
- 在支持此命令的实现中，`#SUBARTIST` 预期将以与 `#ARTIST` 不同的形式显示。
- 若省略此命令，则直接采用 `#ARTIST`。

#### `#SUBARTIST` 的多重定义

| | |
|---|---|
| origin: | TechnicalGroove |
| support: | TechnicalGroove, Sonorous |

- 可定义多个协作者名称。语法解析结果对比如下：

| BMS code | TechnicalGroove | LR2, nanasi, iBMSC |
|---|---|---|
| ```
#TITLE title
#ARTIST artist
#SUBARTIST MOVIE: m
#SUBARTIST NOTER: n
#SUBARTIST TESTR: t
``` | ```
title
artist
MOVIE: m
NOTER: n
TESTR: t
``` | ```
title
artist


TESTR: t
``` |

---

## `#MAKER <string>`

| | |
|---|---|
| origin: | bemaniaDX |
| support: | bemaniaDX, Sonorous(parsing-only), TechnicalGroove(parsing-only) |

- 当音乐作曲家与 BMS 制作者不同时，我们可以定义 BMS 制作者名称。此值为元信息。
- 此值在游戏全程中不显示。

---

## `#GENRE <string>` / `#GENLE <string>`

### `#GENRE <string>`

| | |
|---|---|
| origin: | BM98 |
| support: | 几乎所有 |

- 定义音乐流派。
- 省略时设置为空值。

### `#GENLE <string>`

| | |
|---|---|
| origin: | uBMplay |
| support: | uBMplay, Sonorous, TechnicalGroove |

- 用于处理错别字。

#### 隐式子字符串（`#GENRE`）

| | |
|---|---|
| origin: | DDR |
| support: | DDR |

- 若在 `#TITLE` 或 `#GENRE` 的值中发现特定字符串，选曲列表和游戏画面上将显示相应标签。
- 仅在 Arrow-mode 下才会在游戏画面中显示标签。
- 值不区分大小写。

| 被搜索的字符串: | label: |
|---|---|
| Basic, Light | Basic |
| Another, Trick, Standard | Another |
| Maniac, SSR, Heavy | SSR |

- 关键词重复时，将应用标签 "Basic"。

---

## `#COMMENT <string>`

| | |
|---|---|
| origin: | pomu |
| support: | pomu2, nanasi, PMSee-V, BM-A4, beat arranger, iBMSC (3.0+), Sonorous, TechnicalGroove(parsing-only) |

- 此文本显示在选曲列表中。
- 双引号用于表示"空字符串"。
- 但部分旧 BMS 中存在不带双引号的谱面。（例如 `はーとのしっぽ`：<http://necoco.2-d.jp/bms/nm28_nm07.zip>）
- 因此，开发者不应以双引号为前提进行解析。
- 可省略。

### `#COMMENT` 的多重定义

| | |
|---|---|
| origin: | Sonorous |
| support: | Sonorous |

- 可定义多个注释。语法解析结果对比如下：

| BMS code | Sonorous | nanasi, iBMSC |
|---|---|---|
| ```
#TITLE title
#COMMENT Legacy comment
#COMMENT "Current comment"
``` | ```
title
Legacy comment
Current comment
``` | ```
title

Current comment
``` |

---

## `#TEXT[00-ZZ] "<string>"`

| | |
|---|---|
| channel: | `#xxx99` |
| origin: | pomu |
| support: | pomu2, nanasi, Sonorous(parsing-only), ~~TechnicalGroove(skin-dependent)~~ |

- 定义在任意时机显示的任意文本。
- nanasi 中，miss 时会显示 `#TEXT00` 的内容（pomu2 不支持 `#TEXT00`）。

| For example | remarks |
|---|---|
| ```
#TEXT00 "MISS!!"
#TEXTaa "SIROMARU eats only the bread crust"
#TEXTbb ""
#10099:aabbaabb
``` | `#100` 使文本闪烁。 |

- 实际上，即使删除 `#TEXTbb ""` 这一行，结果也相同。

---

## `#SONG[01-ZZ] "<string>"`

| | |
|---|---|
| channel: | `#xxx99` |
| origin: | pomu |
| support: | pomu2, Sonorous(parsing-only), ~~TechnicalGroove(skin-dependent)~~ |
| caution: | **此命令已过时。我们不应再使用此命令。** |

- 与 `#TEXT[01-ZZ]` 等价的命令，但已过时。
- **`#SONG[01-ZZ]` 已过时。** 现在推荐使用 `#TEXT[01-ZZ]`。
- 若 `#TEXT[01-ZZ]` 与 `#SONG[01-ZZ]` 冲突，优先使用 `#TEXT[01-ZZ]` 的内容。
- Sonorous 不区分 `#TEXT` 和 `#SONG`。索引重复时，优先使用更接近 EOF 的行。

---

## 地雷（Landmine）

| | |
|---|---|
| header: | `#WAV00 <soundfilename>` |
| channel: | `#xxxD1-E9` |
| origin: | nanasi |
| support: | nanasi, LR2, fgt++, GDAC2+774gsc, PMSee-V (v1.11.0+), Angolmois, uBMplay (1.5.0+), Sonorous, TechnicalGroove |

- 触碰后会减少血量的物件。
  - 例如 `ガバディ` (GABBAR-D)：<https://www.youtube.com/watch?v=fnkWsyvvOrw>
  - 虽然不太容易听清，但触碰到红色尖刺物件时会播放 "dosukoi" 音效。
  - Dosukoi 是相扑的呐喊声。【doθkói】
- 地雷爆炸时，播放 `#WAV00` 中定义的文件。
  - PMSee-V 中地雷不会爆炸。
- 当 `#xxxD1-E9` 被使用但未给 `#WAV00` 分配文件时：
  - nanasi、LR2 和 uBMplay 会播放各实现自带的默认爆炸音。
  - fgt++ 和 Angolmois 没有默认爆炸音。因此，触碰地雷时只减少血量，不播放爆炸音。
- 血量减少量由 `[01-ZZ]` 定义。
  - 减少 值/2 的血量。
  - `ZZ` 为强制游戏结束。

| For example | remarks |
|---|---|
| ```
#WAV00 explode.wav
#001D3:0000001E
``` | 放置一个夺取 25% 血量的地雷。// 1E (36 进制) = 50 (10 进制) |

- 由于日本 BMS 领域的事实标准 LR2 支持了地雷，它正在悄然流行。
- 地雷的特点是能够强制玩家 keyup。与 LN 或特殊模式组合时，能发挥极其凶恶的威力。
- **通常，地雷的爆炸音无法更改。** 但：

| For example | remarks |
|---|---|
| ```
#wav00 bomb.wav
#wav11 visible.wav
#wav33 invisible.wav

#00111:00000011
#00131:33
#001D1:1E
``` | - 叠加不可见物件可以模拟播放其他音效。<br>- 但此行为取决于实现，不具备兼容性和确定性。<br>- Angolmois 和 LR2 在时机合适时可能可行。<br>- nanasi 同时播放不可见物件的音效和爆炸音。 |

---

## `#PATH_WAV <path>`

| | |
|---|---|
| origin: | BMEV (?) |
| support: | BMEV, WAview, in_bm2, uBMplay, IIDXv, Angolmois, Sonorous, BGAEncAdv, TechnicalGroove |

- 可定义 WAV 文件所在目录的路径。
- （可能无法指定相对路径。）
- 预期用于测试游玩。

| For example | remarks |
|---|---|
| ```
#PATH_WAV C:\WINDOWS\Media
#WAV01 onestop.mid
#WAV02 chimes.wav
#WAV03 chord.wav
#BMP01 ..\..\img\koala.bmp
``` | 指定路径前缀。<br>`#WAV01` 引用的文件："C:\WINDOWS\Media\onestop.mid"<br>`#WAV02` 引用的文件："C:\WINDOWS\Media\chimes.wav"<br>`#WAV03` 引用的文件："C:\WINDOWS\Media\chord.wav"<br>`#BMP01` 引用的文件："C:\img\koala.bmp" |

- 发布 BMS 时，应将 `#PATH_WAV` 注释掉。
- WAview, in_bm2, uBMplay (1.5.1 及更早版本) 中，值的后缀需要分隔符。例如：
  `#PATH_WAV C:\WINDOWS\Media\`

---

## `#CHARSET <encode>`

| | |
|---|---|
| origin: | ruvit |
| support: | ruvit (2.0b5p2 以下) |
| caution: | **此命令已过时。我们不应再使用此命令。** |

- 以 IANA 注册名指定谱面所采用的字符集。
  (<http://www.iana.org/assignments/character-sets>)
- 可指定 `[EUC-KR|SHIFT-JIS|UTF-8]` 作为值。例如：`#CHARSET EUC-KR`
- 日语的 IANA 库名是 "Shift_JIS"（下划线而非连字符），但此命令需指定 `SHIFT-JIS`。
- 若文本文件（BMS 文件）包含 BOM，`#CHARSET` 将基于 BOM 内容处理。
- 以 EUC-KR/Shift_JIS 以外编码创建的 BMS，建议以"带 BOM 的 UTF-8"保存后分发。
- ruvit 从版本 2.0b5p2 开始全面支持 Unicode，因此 `#CHARSET` 已被移除。
- 目前，ruvit 会自动检测字符集。
- 引用自 <https://nvyu.net/rdm/rby_ex.php>：
  > **Unicode 支持：**
  > 若包含 BOM，则按 BOM 设置文本格式进行读取。（支持 UTF-8/UTF-16LE/UTF-16BE）
  > 若不包含 BOM，则根据 DBCS LeadByte 的频率在 EUC-KR/Shift-JIS 中判别并读取。
  > 当然，两种语言代码的代码区域存在重叠，可能导致错误结果，但这也是无奈之举。
  > 这种处理方式导致已创建的中文、越南语等其他语言 BMS 的 CHARSET 被忽略的问题。
  > 对于这类语言，我们认为引导使用 Unicode 格式保存比添加 `#CHARSET` 更为合理。
  > 归根结底，尽可能将新创建的 BMS 保存为 UTF-8 格式是理想的，但能否被接受还未知。

### Unicode 支持

| | |
|---|---|
| origin: | ruvit |
| support: | ruvit, iBMSC, IIDXv (2.13+), HDX (0.98+), Sonorous, BGAEncAdv, TechnicalGroove |

- ruvit 和 iBMSC 支持部分 ASCII 以外的字符集。
- 此外，LR2 的次世代版本采用带 BOM 的 UTF-16LE 作为默认字符集，而非 ASCII。
- HTML5 规范草案已移除对 UTF-7 和 UTF-32 字符集的支持。
  (<https://www.w3.org/TR/2008/WD-html5-20080122/>)
  > Authors should not use JIS_X0212-1990, x-JIS0208, and encodings based on EBCDIC.
  > Authors should not use UTF-32. Authors must not use the CESU-8, UTF-7, BOCU-1 and SCSU encodings. [CESU8] [UTF7] [BOCU1] [SCSU]
  - 当 BMS 文件在 Web 应用程序中使用时，UTF-7 和 UTF-32 可能成为障碍。
- IIDXv 和 HDX 支持 UTF-8, UTF-16LE, UTF-16BE（**必须带 BOM**）。
- Sonorous 支持 UTF-8（**BOM 可选**）。
- BGAEncAdv 和 TechnicalGroove 支持 **UTF-8 (无 BOM)**、UTF-8 (+BOM)、UTF-16LE (+BOM)、UTF-16BE (+BOM)。

---

## `%URL <string>`

| | |
|---|---|
| origin: | BMS Manager |
| support: | BMS Manager, BMSC |

- 作为元信息，表示与 BMS 创建者相关的网页 URL。
- 这与 BMS 的语法解析无关。
- 个人认为，这出乎意料地有用。
- 因为在分发差分 BMS 时，可以标明获取原始 BMS 的地点等信息。
- 若谱面被 BMSE 或 iBMSC 覆写，此命令将被删除。

---

## `%EMAIL <string>`

| | |
|---|---|
| origin: | BMS Manager |
| support: | BMS Manager, BMSC |

- 作为元信息，表示 BMS 创建者的电子邮件地址。
- 这与 BMS 的语法解析无关。
- 若谱面被 BMSE 或 iBMSC 覆写，此命令将被删除。
