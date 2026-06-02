# 头部命令：显示与难度

## `#STAGEFILE imagefilename`

|          |                           |
| -------- | ------------------------- |
| origin:  | BM98k                     |
| support: | 几乎所有                  |

- 定义加载时显示的 640x480 图像文件。即启动画面（splash screen）。
- 可省略。
- 省略时，期望各实现显示其默认的加载画面。

  | in nazobmplay:      |                                                                                                                     |
  | ------------------- | ------------------------------------------------------------------------------------------------------------------- |
  | `#STAGEFILE` 指定时 | ![bmse_header_stagefile1.gif](https://hitkey.nekokan.dyndns.info/bmse_help_full/Capture/bmse_header_stagefile1.gif) |
  | `#STAGEFILE` 未指定 | ![bmse_header_stagefile2.gif](https://hitkey.nekokan.dyndns.info/bmse_help_full/Capture/bmse_header_stagefile2.gif) |

---

## `#BANNER imagefilename`

|          |                                                                                               |
| -------- | --------------------------------------------------------------------------------------------- |
| origin:  | nanasi                                                                                        |
| support: | nanasi, pomu2, LR2, fgt++, iBMSC (3.0+), PMSee-V (2.0.0+), Sonorous, TechnicalGroove (仅解析) |

300x80

- 指定在选曲画面或结果画面等显示的 300x80 图像文件。
- 也可指定相对于「BMS 文件所在目录」的相对路径（仅下级路径）。
- ~~MATERIALS (`<path>`filename) 也可指定。~~ **（注：nanasi 已终止对 MATERIALS 的支持。）**
- 路径全长限制为 260 字节。
- ~~命令省略时，nanasi 会从「BMS 的同级或下级目录」中查找 `banner.bmp`，若存在则应用。~~ **（实际并非如此。该功能似乎已被移除。）**

---

## `#BACKBMP imagefilename`

|          |                                                                                                          |
| -------- | -------------------------------------------------------------------------------------------------------- |
| origin:  | DDR                                                                                                      |
| support: | DDR, nanasi, LR2（有条件）, pomu2（有条件）, iBMSC (3.0+), Sonorous（仅解析）, TechnicalGroove（仅解析） |

- 原始规格：指定作为游戏画面背景显示的 640x480 图像。
  - DDR 和 nanasi 遵循原始规格。
- 若在播放器皮肤的设定中提供了定义，则 LR2 和 pomu2 可使用此命令。
  - 在 LR2 用皮肤 “OVER ACTiVE DX+” 中，多数情况下用作标题图像。
  - 例：[YouTube - OVER ACTiVE DX+ 2.50 DEMO](https://www.youtube.com/watch?v=5Q8pAA9O8Aw)
  - 在 `#STAGEFILE` 之外，游戏前在画面中央显示的曲目信息即为 `#BACKBMP`。
  - 此命令使曲目信息能以任意字体和布局装饰谱面。
  - 但此行为并非规格定义，图像文件大小取决于各皮肤。
  - **（补充）** [“OVER ACTiVE DX+” 的 `#BACKBMP` 规格](http://right-stick.sub.jp/backbmp/index.html)

---

## `#CHARFILE characterfilename.chp`

|          |                                                                                        |
| -------- | -------------------------------------------------------------------------------------- |
| origin:  | pomu                                                                                   |
| support: | pomu2, PMChr-V, Sonorous（仅解析）, TechnicalGroove（仅解析）                          |

- 模仿《pop'n music》的角色文件。
- 抱歉，作者对此规格并不熟悉。
- 目前 `#CHARFILE` 是 pomu2 的独有扩展。但 LunaticRave 的下一代版本可能会支持 `#CHARFILE`。

|                      |                                                                                                                |
| -------------------- | -------------------------------------------------------------------------------------------------------------- |
| For example:         | [Feeling PoMu - YouTube](https://www.youtube.com/watch?v=14skmXXCjOQ)                                          |
| sample download:     | [Colorful Channel official website](https://pmcc.nekokan.dyndns.info/pmcc2/download.html)                      |
| official guidance:   | [続！てきとうなキャラ作成講座](https://web.archive.org/web/*/http://m-nekomi.cool.ne.jp/newpage/mu2/Make2.htm) |
| unofficial guidance: | [シュークリーム分が](https://web.archive.org/web/20140103231405/http://storyof.namidaame.com/yy_pce.htm)       |
| charfile viewer:     | PMChr-V.zip: [ポニラボ](https://sakukoba.ninja-x.jp/ponila/)                                                   |

---

## `#PLAYLEVEL n`

|          |                  |
| -------- | ---------------- |
| origin:  | BM98             |
| support: | 几乎所有         |

- 定义显示在选曲列表中的难度。
- 难度的显示方式依赖具体实现。以下为示例：
  - 部分基于《beatmania》的实现以星号表示难度（★★★★☆☆☆）。
  - 部分基于近作《beatmaniaIIDX》的实现以 12 级柱状图表示难度。
  - 部分基于《DrumMania》的实现以 99 级整数表示难度（注：现为 `xx.yy` 形式的 1000 级）。
- 通常指定整数。
- BM98 中省略时应用默认值 `3`。虽非规格定义行为，但大多数实现沿袭了此做法。
- 各等级应表示的难度标准未在规格中定义，似乎取决于上下文。

### `#PLAYLEVEL 0`

|          |                |
| -------- | -------------- |
| origin:  | BM98           |
| support: | 相当多         |

- 在某些应用中，值 `0` 作为特殊难度显示。
- 例如 BM98 不显示星号而显示问号。
- 存在因 `#RANDOM` 或 `#SWITCH` 导致难度剧烈变化而指定值 `0` 的 BMS 实例。
- 值 `0` 可能作为某种符号被指定。

### `#PLAYLEVEL string`

|          |                                                    |
| -------- | -------------------------------------------------- |
| origin:  | nanasi                                             |
| support: | nanasi, iBMSC (3.0+), TechnicalGroove, （调查中）    |

- `#PLAYLEVEL` 的值可指定字符串。例如：`#PLAYLEVEL 安心`。
- 也可如 `#PLAYLEVEL 12.4` 这样详细描述难度分类。

---

## `#DIFFICULTY [1-5]`

|          |                                                               |
| -------- | ------------------------------------------------------------- |
| origin:  | nanasi                                                        |
| support: | nanasi, LR2, iBMSC (3.0+), Sonorous, TechnicalGroove          |

- 我们可以使用此元信息对谱面进行排序和筛选。
- 支持此命令的实现预计可实现「按谱面难度类别筛选选曲列表」。
- 可省略。
- 若省略此命令，可能无法按谱面难度类别进行筛选。
- 即使筛选功能不可用，仍可按游戏等级或字母顺序进行简单排序。

**示例 1：**

| filename                    | add description        | remarks                                              |
| --------------------------- | ---------------------- | ---------------------------------------------------- |
| himiko_1p_beginner.bms      | `#DIFFICULTY 1`        | 低难度谱面（EASY/BEGINNER/LIGHT 等）                 |
| himiko_1p_normal.bms        | `#DIFFICULTY 2`        | 标准难度谱面（NORMAL/STANDARD 等）                   |
| himiko_1p_hyper.bms         | `#DIFFICULTY 3`        | 高难度谱面（HARD/HYPER 等）                          |
| himiko_1p_another.bms       | `#DIFFICULTY 4`        | 更高难度谱面（EX/ANOTHER 等）                        |
| himiko_1p_blackanother.bms  | `#DIFFICULTY 5`        | 所谓的粪谱面、发狂等                                 |

**示例 2：**

| filename      | add description        | remarks      |
| ------------- | ---------------------- | ------------ |
| astro_EZ.bms  | `#DIFFICULTY 1`        | EASY         |
| astro_NM.bms  | `#DIFFICULTY 2`        | NORMAL       |
| astro_HD.bms  | `#DIFFICULTY 3`        | HARD         |
| astro_MX.bms  | `#DIFFICULTY 4`        | MAXIMUM      |
| astro_SC.bms  | `#DIFFICULTY 5`        | SUPER-CRAZY  |

- 在日本，**kusofumen（粪谱面）** 已形成一种特殊的类型。
  - 提高演奏音符密度的方向。被称为 **INSANE（发狂）**。
    [bms难度表](https://nekokan.dyndns.info/%7Elobsak/genocide/)（BMS difficulties review）是此类谱面的典型案例，非常流行。
    这是由众多用户积极讨论分类而成的巨大谱面数据库。
    各种谱面被提交、讨论并分类。
    传闻该表收录的谱面在 IR（Internet Ranking）中的游玩次数可达通常的 100 倍。
  - 衍生自难度表的网站也很多。例如 [LN 难度表](http://ukita2.web.fc2.com/record.html)、转盘难度表、
    [DP 难度表](http://dpbms.at-ninja.jp/)、[Overjoy](http://achusi.main.jp/overjoy/) 等。
  - 也有玩家偏好停止序列、BPM 变更、类 pop'n 强制选项等技巧。
  - 也有利用 BMS 格式规格漏洞的类型，包含某种幽默或玩笑的倾向。[kusofumen lovers](https://web.archive.org/web/*/http://soflan.hp.infoseek.co.jp/) 是其典型之一。
- `#DIFFICULTY` 经常用于统一收录于包中的 BMS 格式：

  | 系列名                               | URL                                                                                             |
  | ------------------------------------ | ----------------------------------------------------------------------------------------------- |
  | BMS Starter Pack 2009                | <http://www.yamajet.com/bmssp/>                                                                 |
  | Toy Musical 系列                     | <http://toymusical.net/>                                                                        |
  | Colorful Canvas & Colorful Channel   | <https://pmcc.nekokan.dyndns.info/pmcc2/download.html>                                          |

- **（补充）** 据[作者的调查](https://hitkey.nekokan.dyndns.info/diary1405.php#D140529)，在 BMS 活动「戦[sen-goku]國 〜甲午の乱〜」中，超过半数的注册作品使用了此命令。

### 按文件名分组

|          |           |
| -------- | --------- |
| origin:  | pomu2     |
| support: | pomu2     |

- 通过按照既定规则指定文件名，可执行文件可识别难度分组。

  [连字符减号与既定**小写**关键词构成的、针对基名的后缀]

  | filename                     | 对应难度             | group      |
  | ---------------------------- | -------------------- | ---------- |
  | `pmsname**-n**.pms`          | `#DIFFICULTY 2`      | **N**ORMAL |
  | `pmsname**-h**.pms`          | `#DIFFICULTY 3`      | **H**YPER  |
  | `pmsname**-ex**.pms`         | `#DIFFICULTY 4`      | **EX**TRA  |
  | `pmsname**-p**.pms`          | `#DIFFICULTY 5`      | **P**LUS   |

- 据作者回忆，除 pomu2 外还有其他应用具有类似功能。~~但现已无法想起。~~
  **（补充）** 可能是指 Lunatic Rave 的 `*.lr` 文件。作者对此并不了解，但找到了[转载详细信息的页面](https://web.archive.org/web/20140819141616/http://forums.outphase.com/showthread.php?t=514&page=12)。

### 按副标题分组

|          |                                  |
| -------- | -------------------------------- |
| origin:  | TechnicalGroove                  |
| support: | ~~TechnicalGroove~~              |

- 满足以下所有条件时，会自动设置虚拟的 `#DIFFICULTY` 值：
  - 不存在有效的 `#DIFFICULTY` 命令。
  - 存在 `#SUBTITLE` 或隐式副标题。
  - 副标题包含以下任一关键词（不区分大小写）。

  | Grouping            | Keywords                                                       |
  | ------------------- | -------------------------------------------------------------- |
  | `#DIFFICULTY 1`     | "Easy", "Beginner", "Light", "Simple", "5Button", "[B]", "(B)" |
  | `#DIFFICULTY 2`     | "Normal", "Standard", "[N]", "(N)"                             |
  | `#DIFFICULTY 3`     | "Hyper", "Hard", "Extend", "[H]", "(H)"                        |
  | `#DIFFICULTY 4`     | "Maniac", "Extra", "EX"                                        |
  | `#DIFFICULTY 5`     | "Insane", "Another", "Plus", "[A]", "(A)"                      |

- TechnicalGroove 也支持 `#DIFFICULTY 6` 及以上的正整数（但不推荐）。
- TechnicalGroove 也支持 `#DIFFICULTY 0`，但未明确 `0` 的含义。
