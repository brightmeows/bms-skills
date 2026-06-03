---
name: bms
description: 创建或编辑 BMS 谱面文件、排查格式问题、查询通道映射和头部命令、或开发 BMS 解析/生成工具时使用。
license: Apache-2.0
---

# BMS 速查表

BMS（Be-Music Script）是 1998 年由 Urao Yane 与 NBK 设计的音乐游戏谱面格式。非标准扩展由各播放器/编辑器社区推动。

本速查表覆盖**头部命令**、**通道映射**、**控制流**、**扩展格式**、**引擎差异**和**工具兼容性**。

> 详细参考：参见 `memo/` 下各文档和同级独立文档。
>
> **注意：** 本技能文档仅用于速查。如需实现特定部分的功能，必须阅读对应的参考文档以理解完整实现。

---

## 一、基础语法

### 文件扩展名

| 扩展名 | 键数 | 说明 |
|--------|------|------|
| `.bms` | 5K | 标准 BMS（1P+2P） |
| `.bme` | 7K | BMS 扩展格式（IIDX 7KEYS） |
| `.bml` | — | 长音谱面（RDM `#LNOBJ`） |
| `.pms` | 9K | pop'n music 风格谱面 |
| `.mbm` | — | MacBeat MOD 格式 |

> → 详见 [memo/04-file-extensions.md](memo/04-file-extensions.md)

### 行格式

| 类型 | 格式 | 示例 |
|------|------|------|
| 头部命令 | `#<命令> <值>` | `#TITLE My Song` |
| 通道命令 | `#<小节xxx><通道CH>:<数据>` | `#00111:11000000` |

- 分隔符：头部用半角空格，通道用半角冒号
- 不区分大小写（除非 `#BASE 62` 声明）
- `//` 或 `;` 起为行注释（IIDXv/HDX）
- `/* */` 为块注释
- 同一命令重复时，靠近文件末尾者优先

> → 详见 [memo/03-format-notes.md](memo/03-format-notes.md)

### 数值进制

| 进制 | 范围 | 索引数 | 字符集 |
|------|------|--------|--------|
| 16 进制 | `[0-9A-F]` | 256 | 初期 |
| 36 进制 | `[0-9A-Z]` | 1296 | 当前标准 |
| **62 进制** | `[0-9A-Za-z]` | 3844 | beatoraja 0.8.7+ |

> → 详见 [base62-format.md](ext/base62-format.md)

---

## 二、模式与判定

| 命令 | 值 | 说明 |
|------|----|------|
| `#PLAYER 1` | 1 | Single Play（1P） |
| `#PLAYER 2` | 2 | Couple Play（1P+2P） |
| `#PLAYER 3` | 3 | Double Play（DP） |
| `#RANK 0` | 0 | VERY HARD ±8ms |
| `#RANK 1` | 1 | HARD ±15ms |
| `#RANK 2` | 2 | NORMAL ±18ms（默认值） |
| `#RANK 3` | 3 | EASY ±21ms |
| `#RANK 4` | 4 | VERY EASY（nanasi/beatoraja） |
| `#DEFEXRANK n` | 百分比 | 以 NORMAL=100 为基准的判定值 |
| `#EXRANKxx n` | 通道 `#xxxA0` | 动态判定变更 |
| `#TOTAL n` | 数值 | 最佳判定时血量总增加量（默认因实现而异） |

> → 详见 [memo/06](memo/06-header-commands-mode-and-judgment.md). RANK 4 见 [beatoraja-extensions](ext/beatoraja-extensions.md).
> LR2 默认 `#TOTAL` ≈160；nanasi 默认 350；**强烈不建议省略**。

---

## 三、元数据

| 命令 | 说明 |
|------|------|
| `#TITLE <string>` | 曲名 |
| `#SUBTITLE <string>` | 副标题 |
| `#ARTIST <string>` | 艺术家 |
| `#SUBARTIST <string>` | 协作者 |
| `#GENRE <string>` | 曲风 |
| `#MAKER <string>` | BMS 制作者 |
| `#COMMENT <string>` | 选曲列表附加文本 |
| `#TEXTxx "<string>"` | 通道 `#xxx99`，游戏中文本显示 |

> → 详见 [memo/08](memo/08-header-commands-metadata.md)
> 字符串编码：Shift_JIS 为日文主流，ruvit/iBMSC 支持多编码。现代工具建议 UTF-8 + BOM。

---

## 四、显示与难度

| 命令 | 值/格式 | 说明 |
|------|---------|------|
| `#STAGEFILE` | 图像路径 | 640×480 启动画面 |
| `#BANNER` | 图像路径 | 300×80 选曲横幅 |
| `#BACKBMP` | 图像路径 | 640×480 游戏背景 |
| `#CHARFILE` | `.chp` 路径 | pop'n 风格角色文件 |
| `#PLAYLEVEL n` | 整数/字符串 | 难度等级显示 |
| `#DIFFICULTY [1-5]` | 1–5 | 难度分类筛选 |

> → 详见 [memo/07](memo/07-header-commands-display-and-difficulty.md)
> `#PLAYLEVEL` 可指定字符串（nanasi）。`#DIFFICULTY` 的映射：1=Easy, 2=Normal, 3=Hyper, 4=Another, 5=Insane。

---

## 五、音频与 BPM

### BPM 相关

| 命令/通道 | 格式 | 说明 |
|-----------|------|------|
| `#BPM n` | 数值 | 基准 BPM（默认值取决于实现） |
| `#BPMxx n` | 通道 `#xxx08` | 扩展 BPM（小数/255+） |
| `#EXBPMxx n` | 同 `#BPMxx` | 规避 BMSC bug 的别名 |
| `#BASEBPM n` | 数值 | HS 选项的基准 BPM |
| `#xxx03` | `[01-FF]` | BPM 变更（1-255，整数） |
| `#xxx08` | 索引序列 | 扩展 BPM 对象放置 |

> → 详见 [memo/09](memo/09-header-commands-audio-and-bpm.md)
> 负 BPM：部分实现（beatoraja/LR2/ruvit/angolmois）支持逆向滚动。
> 详见 [scroll-gimmick-guide.md](ext/scroll-gimmick-guide.md)

### STOP

| 命令 | 通道 | 单位 | 说明 |
|------|------|------|------|
| `#STOPxx n` | `#xxx09` | 1 = 4/4 拍的 1/192 | 谱面暂停 |

> → 详见 [memo/09](memo/09-header-commands-audio-and-bpm.md)
> 负 STOP：nazo/fgt/pomu2 等支持跳过。LR2 轻微跳过。

### 音频定义

| 命令 | 索引范围 | 说明 |
|------|----------|------|
| `#WAVxx <file>` | `[01-ZZ]`（1295） | 音频文件定义 |
| `#WAV00 <file>` | 固定 | 地雷爆炸音定义 |

> → 详见 [memo/09](memo/09-header-commands-audio-and-bpm.md)
> 支持格式：WAV（PCM 8/16/24/32bit）、OGG、MP3（部分实现）。LR2 限制 >60s 音频不得 IR 注册。
> 替代搜索：未找到定义文件时自动搜索同基本名的 `.wav`/`.ogg`/`.mp3`。

**多重定义技巧**：同一 `#WAV` 文件定义到多个槽位可增加同时发音数。

### 地雷（Landmine）

| 项 | 值 |
|----|----|
| 通道 | `#xxxD1-D9`（1P）、`#xxxE1-E9`（2P） |
| 索引范围 | `[01-ZZ]`，伤害 = 十进制值/2，`ZZ`=即死 |
| 爆炸音 | `#WAV00` |

> BMSE 会删除地雷通道。用 ダミー分岐 保护。

---

## 六、长音与选项

### 长音（LN）

| 命令 | 值 | 说明 | 通道 |
|------|----|------|------|
| `#LNTYPE 1` | 1 | RDM 记法 LN（当前标准） | `#xxx51-69` |
| `#LNOBJ xx` | `#WAV` 索引 | LN 终点符号（RDM type #2） | `#xxx11-29` |

- `#LNTYPE 1` 可省略（但 o2mania 例外——它不可省略）
- `#LNOBJ` 建议用大写索引（规避 nanasi/fgt++ 小写 bug）
- 建议将空 `#WAV` 索引设为 `#LNOBJ`，避免终点播放不期望的音效
- 62 进制下 `#LNOBJ` 区分大小写

> → 详见 [memo/10](memo/10-header-commands-long-notes-and-options.md)、[base62-format.md](ext/base62-format.md)

---

## 七、图像与 BGA

| 命令 | 通道 | 说明 |
|------|------|------|
| `#BMPxx <file>` | `#xxx04/06/07` | 图像定义（通常 ≤256×256） |

> → 详见 [memo/11](memo/11-header-commands-image-and-bga.md)

**BGA 层叠顺序**（从上到下）：

| 顺序 | 通道 | 名称 |
|------|------|------|
| 顶层 | `#xxx06` | POOR（miss 时） |
| | `#xxx0A` | LAYER2 |
| | `#xxx07` | LAYER（黑色透明） |
| 底层 | `#xxx04` | BASE |

> LAYER 通道（`#xxx07`）中仅黑色（RGB:00:00:00）变为透明。
> 支持格式：BMP、PNG、JPEG、GIF 等（取决于实现）。Alpha 通道支持：LR2/nanasi/ruvit 等。

---

## 八、视频与扩展

| 命令 | 说明 |
|------|------|
| `#VIDEOFILE <file>` | BGA 视频（MPEG/AVI） |
| `#MOVIE <file>` | 视频，不循环，`#xxx04` 冲突时优先 |
| `#SEEKxx <ms>` | 通道 `#xxx05`，视频跳转 |

> 详见 [memo/12](memo/12-header-commands-video-and-extensions.md)。

---

## 九、滚动与速度扩展

| 命令 | 通道 | 值 | 说明 | 支持 |
|------|------|----|------|------|
| `#SCROLLxx n` | `#xxxSC` | 实数（可负） | 滚动速度倍率（非 BPM） | beatoraja/Bemuse/Qwilight/raindrop |
| `#SPEEDxx n` | `#xxxSP` | 实数 | 关键帧插值渐变滚动速度 | 同上（部分实现） |

- `#SCROLL` 改变的是谱面流动**宽度**（不是速度），因此无需移动 Note 位置
- 值 `2` = 2 倍滚动速度，`0.5` = 半速
- `#SCROLL 0` = 停止滚动（但音效/BGA 继续播放）
- 负值 = 逆向滚动（beatoraja/ruvit/fgt++/angolmois 等）
- `#SPEED` 在两个关键帧之间自动线性补间，无需逐小节计算

> → 详见 [scroll-speed-extensions.md](ext/scroll-speed-extensions.md)
> → Gimmick 实战组合技：详见 [scroll-gimmick-guide.md](ext/scroll-gimmick-guide.md)

### Gimmick 核心原则

1. Scroll = 宽度变化，不移动 Note；BPM = 速度变化，需压缩/扩展小节
2. Scroll=0.0 冻结 Note；负 Scroll 可逆走
3. 每次变化后**必须补偿**，否则后续 Note 位置偏移
4. 100001 倍 BPM 是实现 Warp/动画的常用技巧
5. beatoraja 上 Scroll 大绝对值可隐藏 Note

---

## 十、通道映射速查

### 系统通道（00-0E）

| 通道 | 名称 | 说明 |
|------|------|------|
| `01` | BGM | `#WAVxx`（多行，可合并） |
| `02` | 小节长度 | 1 = 4/4，小数可 |
| `03` | BPM 变更 | `[01-FF]`（1-255 整数） |
| `04` | BGA BASE | `#BMPxx` 基础层 |
| `05` | SEEK | LR 视频跳转 |
| `06` | BGA POOR | miss 时显示 |
| `07` | BGA LAYER | 黑色透明叠加层 |
| `08` | exBPM | `#BPMxx` 实数 BPM 对象 |
| `09` | STOP | `#STOPxx` 暂停 |
| `0A` | BGA LAYER2 | 第二叠加层 |
| `0B-0E` | 不透明度 | BASE/LAYER/LAYER2/POOR |

### 演奏通道（11-6Z）

| 通道 | 名称 | 说明 |
|------|------|------|
| `11-15` | 1P Visible KEY1-5 | BMS 5K 主体 |
| `16` | 1P SCRATCH | 转盘 |
| `17` | 1P FREE ZONE / 脚踏板 | 取决于实现 |
| `18-19` | 1P Visible KEY6-7 | BME 7K 扩展（FlashTerminal） |
| `1A-1Z` | 1P Visible Ex | MGQ/pomu 扩展 |
| `21-25` | 2P Visible KEY1-5 | 2P 侧 |
| `26` | 2P SCRATCH | |
| `27` | 2P FREE ZONE / 脚踏板 | |
| `28-29` | 2P Visible KEY6-7 | |
| `2A-2Z` | 2P Visible Ex | |
| `31-3Z` | 1P Invisible | 不可见对象（对应 `11-1Z`） |
| `41-4Z` | 2P Invisible | 不可见对象（对应 `21-2Z`） |
| `51-5Z` | 1P LN | RDM 记法长音 |
| `61-6Z` | 2P LN | RDM 记法长音 |

### 特殊通道（70-E9）

| 通道 | 名称 | 说明 |
|------|------|------|
| `97` | BGM 音量 | `[01-FF]`（255 级） |
| `98` | KEY 音量 | `[01-FF]`（255 级） |
| `99` | TEXT 显示 | `#TEXTxx` 运行时文本 |
| `A0` | JUDGE | `#EXRANKxx` 判定动态变更 |
| `A1-A4` | aRGB | BGA 各层颜色混合 |
| `A5` | SWBGA | 按键绑定动画 |
| `A6` | OPTION | `#CHANGEOPTIONxx` 动态选项（多行） |
| `D1-D9` | 1P 地雷 | `#WAV00`，伤害 = 十进制/2 |
| `E1-E9` | 2P 地雷 | 同上 |

> → 完整按键分配表详见 [memo/14-channel-mapping.md](memo/14-channel-mapping.md)

### 键位分配（常见格式）

| 格式 | 键位→通道映射 |
|------|-------------|
| BMS 5K | 11-15（键）, 16（转盘）, 17（FreeZone） |
| BME 7K | 16（转盘）, 11-15（键1-5）, 18-19（键6-7） |
| PMS 9K | 11-15（键1-5）, 22-25（键6-9） |
| 14K DP | 1P: 16,11-15,18-19 + 2P: 26,21-25,28-29 |

---

## 十一、控制流

### #RANDOM / #SWITCH 系列

| 命令 | 说明 |
|------|------|
| `#RANDOM n` | 生成 `[1, n]` 的随机整数 |
| `#SETRANDOM n` | 生成常量 `n`（测试用） |
| `#IF n` | 标签匹配块 |
| `#ELSEIF n` | 条件分支 |
| `#ELSE` | 不匹配时应用 |
| `#ENDIF` | 块结束 |
| `#ENDRANDOM` | 随机块显式结束（建议始终写上） |
| `#SWITCH n` | 类 C switch 生成随机数 |
| `#SETSWITCH n` | 生成常量 `n` |
| `#CASE n` | switch 分支 |
| `#SKIP` | 跳出 switch（类似 break） |
| `#DEF` | default 分支 |
| `#ENDSW` | switch 块结束 |

> → 详见 [memo/13-control-flow.md](memo/13-control-flow.md)

**要点：**

- 公共部分始终写在 `#RANDOM` 块**外部**（顶层），否则可能成为“孤儿”
- `#ELSE` + `#RANDOM` 可显著减少重复代码量
- 嵌套 `#RANDOM` 大多数实现支持有限，nanasi 限制约 90 层
- nanasi `#SWITCH` 支持 fall-through（无 `#SKIP` 时）

---

## 十二、62 进制格式

| 项 | 内容 |
|----|------|
| 声明 | `#BASE 62`（**必需**，否则视为 36 进制） |
| 字符集 | `0-9A-Za-z`（追加 a-z 对应 36-61） |
| 最大索引 | `zz` = 3843（36 进制 `ZZ` = 1295） |
| 受影响命令 | `#WAVxx`、`#BMPxx`、`#BPMxx`、`#STOPxx`、`#SCROLLxx`、`#SPEEDxx`、`#LNOBJ` |
| 不受影响 | `#xxx03`（16 进制 BPM）、`#xxxD1-E9`（36 进制地雷伤害） |
| 行为变化 | `#LNOBJ` 在 62 进制下区分大小写 |
| 支持 | beatoraja 0.8.7+, mBMplay, BMSE 2.2.0a, μBMSC 等 |

> → 详见 [base62-format.md](ext/base62-format.md)

---

## 十三、beatoraja 扩展

| 命令/特性 | 值 | 说明 |
|-----------|----|------|
| `#PREVIEW <file>` | 音频路径 | 选曲预览音源（自动搜索 `preview.*`） |
| `#LNMODE [1-3]` | 1=LN, 2=CN, 3=HCN | 固定长音种类，不受选曲选项影响 |
| `#RANK 4` | VERY EASY | 判定宽度为 EASY 的 1.25 倍 |
| `#DEFEXRANK` | 0.7.3+ 基准改为 NORMAL | 值 `100` = NORMAL |
| `#SCROLL` 负值 | — | 逆向滚动 |
| 音频格式 | WAV(PCM/MP3)/OGG | WAV 仅支持 PCM 和 MP3 编码 |
| 视频格式 | mp4/wmv/m4v/webm/mpg/avi | 按优先级自动检测 |

> → 详见 [beatoraja-extensions.md](ext/beatoraja-extensions.md)
> → Scroll Gimmick 详见 [scroll-gimmick-guide.md](ext/scroll-gimmick-guide.md)

---

## 十四、兼容性与引擎差异

### BMSE 兼容性

BMSE 对部分命令有改写/删除行为，可用ダミー分岐（Dummy Branch）技巧保护。

> → 详见 [bmse-compatibility.md](ext/bmse-compatibility.md)

### 引擎行为差异（beatoraja vs LR2）

beatoraja 与 LR2 在血量槽规格、判定宽度、LN 行为、空 POOR 机制等方面存在显著差异。

> → 详见 [engine-behavior-comparison.md](ext/engine-behavior-comparison.md)

---

## 快速索引（按使用趋势分类）

### 主流扩展

| 命令 | 参见章节 |
|------|----------|
| `#BANNER` | 四、显示与难度 |
| `#BASE 62` | 十二、62 进制格式 |
| `#BASEBPM` | 五、音频与 BPM |
| `#BMP` | 七、图像与 BGA |
| `#BPM` / `#BPMxx` / `#EXBPM` | 五、音频与 BPM |
| `#DEFEXRANK` | 二、模式与判定 |
| `#DIFFICULTY` | 四、显示与难度 |
| `#LNOBJ` | 六、长音与选项 |
| `#PLAYER 1–3` | 二、模式与判定 |
| `#PLAYLEVEL` | 四、显示与难度 |
| `#RANK` | 二、模式与判定 |
| `#SCROLL` / `#SPEED` | 九、滚动与速度扩展 |
| `#STAGEFILE` | 四、显示与难度 |
| `#STOPxx` | 五、音频与 BPM |
| `#SUBARTIST` | 三、元数据 |
| `#SUBTITLE` | 三、元数据 |
| `#TITLE` / `#ARTIST` / `#GENRE` / `#MAKER` | 三、元数据 |
| `#TOTAL` | 二、模式与判定 |
| `#WAVxx` / `#WAV00` | 五、音频与 BPM |
| `#xxx03`（BPM 变更） | 五、音频与 BPM |
| `#xxx04/06/07/0A`（BGA 通道） | 七、图像与 BGA |
| `#xxx08`（exBPM 对象） | 五、音频与 BPM |
| `#xxx09`（STOP 对象） | 五、音频与 BPM |
| `#xxx51–69`（LN 通道） | 六、长音与选项 |

### 偶尔使用的扩展

| 命令 | 参见章节 |
|------|----------|
| `#BACKBMP` | 四、显示与难度 |
| `#CASE` / `#DEF` / `#SKIP` | 十一、控制流 |
| `#CHARFILE` | 四、显示与难度 |
| `#COMMENT` | 三、元数据 |
| `#ELSE` / `#ELSEIF` | 十一、控制流 |
| `#ENDIF` / `#ENDRANDOM` / `#ENDSW` | 十一、控制流 |
| `#EXRANK` | 二、模式与判定 |
| `#IF` / `#RANDOM` / `#SWITCH` | 十一、控制流 |
| `#LNMODE` | 十三、beatoraja 扩展 |
| `#LNTYPE 1` | 六、长音与选项 |
| `#MOVIE` / `#VIDEOFILE` / `#SEEK` | 八、视频与扩展 |
| `#PREVIEW` | 十三、beatoraja 扩展 |
| `#TEXT` / `#SONG` | 三、元数据 |
| `#xxx99`（TEXT 通道） | 三、元数据 |
| `#xxxA0`（判定通道） | 二、模式与判定 |
| `#xxxD1–E9`（地雷通道） | 五、音频与 BPM |

### 测试游玩时有用

| 命令 | 参见章节 |
|------|----------|
| `#PATH_WAV` | 附录 |
| `#SETRANDOM` / `#SETSWITCH` | 十一、控制流 |

---

## 附录：遗留与低频命令

以下命令已过时、极少使用、或仅特定场景需要。

### 已废弃命令

| 命令 | 说明 |
|------|------|
| `#PLAYER 4` | Battle Play（极少支持） |
| `#LNTYPE 2` | MGQ 记法 LN（已被 `#LNTYPE 1` 取代） |
| `#MATERIALSWAV` | 路径寻址（nanasi <1.00） |
| `#MATERIALSBMP` | 路径寻址（nanasi <1.00） |
| `#DIVIDEPROP` | 小节分辨率指定（nanasi <0.98） |
| `#CHARSET` | 字符集声明（ruvit <2.0b5p2） |
| `#WAVCMD` | MacBeat MOD 音高/音量/时间控制 |
| `#ExtChr ...` | BM98 界面自定义（`#xxx05`） |

### 低频命令

| 命令 | 说明 |
|------|------|
| `#ARGBxx <a,r,g,b>` | BGA 各层 aRGB 叠加 |
| `#BGAxx` / `#@BGAxx` | 图像裁剪显示 |
| `#CHANGEOPTIONxx <prefix:option>` | 动态播放选项变更 |
| `#EXBMPxx <a,r,g,b> <file>` | 带透明色修正的图像 |
| `#EXWAVxx <pvf> <值> <file>` | 带效果（pan/vol/freq）的音频 |
| `#OCT/FP` | Octave 模式/脚踏板模式声明 |
| `#OPTION <prefix:option>` | BMS 侧强制播放选项 |
| `#POORBGA [0-2]` | POOR 图像显示模式 |
| `#STP xxx.yyy zzzz` | 毫秒级 STOP 序列 |
| `#SWBGAxx <params> <pattern>` | 按键绑定 LAYER 动画 |
| `#VIDEOf/s <n>` | 视频帧率 |
| `#VIDEOCOLORS <n>` | 视频调色板位数 |
| `#VIDEODLY <n>` | 视频延迟（帧数） |
| `#VOLWAV` | WAV 音量调整（与 `#RANK` 交互） |
| `#CDDA` | CD 音轨定义 |
| `#MIDIFILE` | MIDI 文件定义 |
| `#PATH_WAV` | 定义文件引用目录路径 |
| `%URL` | 制作者 URL（BMSC 自动生成） |
| `%EMAIL` | 制作者邮箱（BMSC 自动生成） |
