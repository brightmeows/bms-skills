---
name: bms
description: 创建或编辑 BMS 谱面文件、排查格式问题、查询通道映射和头部命令、或开发 BMS 解析/生成工具时使用。
license: Apache-2.0
---

# BMS 参考索引

BMS（Be-Music Script）是 1998 年由 Urao Yane 与 NBK 设计的音乐游戏谱面格式。非标准扩展由各播放器/编辑器社区推动。

**本文件为索引，不作为事实来源。** 具体实现或谱面制作时，须阅读对应参考文档以获取完整规范。

所有引用路径均相对于本文件所在目录。

---

## memo/ —— BMS 格式参考

| 文件 | 内容 | 相关命令 |
|------|------|----------|
| [01-overview.md](./memo/01-overview.md) | BMS command memo 元信息：来源 URL、作者、更新履历、外部参考链接 | — |
| [02-bms-application-list.md](./memo/02-bms-application-list.md) | BMS 相关应用程序完整列表：播放器、编辑器、查看器、转换工具等，含下载链接。查找参考实现或工具时用 | — |
| [03-format-notes.md](./memo/03-format-notes.md) | BMS 格式规格详解：行格式、数值进制演变、字符编码问题、解析行为、一般实现共通规则。**BMS 解析器/生成器实现必读** | `#TITLE` `#xxx01` `#xxx02` |
| [04-file-extensions.md](./memo/04-file-extensions.md) | 各扩展名（BMS/BME/BML/PMS）的由来、历史沿革、通道映射差异、严格/宽松定义区别。理解格式演变和兼容性时用 | `#PLAYER` `#LNTYPE` `#LNOBJ` `#xxx11-29` `#xxx51-69` |
| [05-command-usage-trends.md](./memo/05-command-usage-trends.md) | 命令使用趋势分类，快速区分命令普及度和适用场景：主流扩展、偶尔使用、测试有用 | — |
| [06-header-commands-mode-and-judgment.md](./memo/06-header-commands-mode-and-judgment.md) | `#PLAYER`、`#RANK`/`#DEFEXRANK`/`#EXRANK`、`#TOTAL`、`#VOLWAV` 详解，含各实现默认值差异矩阵。**实现判定系统和血量系统时必读** | `#PLAYER` `#RANK` `#DEFEXRANK` `#EXRANK` `#TOTAL` `#VOLWAV` `#xxx97` `#xxx98` `#xxxA0` |
| [07-header-commands-display-and-difficulty.md](./memo/07-header-commands-display-and-difficulty.md) | `#STAGEFILE`、`#BANNER`、`#BACKBMP`、`#CHARFILE`、`#PLAYLEVEL`、`#DIFFICULTY` 详解。处理选曲画面显示和难度分类时用 | `#STAGEFILE` `#BANNER` `#BACKBMP` `#CHARFILE` `#PLAYLEVEL` `#DIFFICULTY` |
| [08-header-commands-metadata.md](./memo/08-header-commands-metadata.md) | `#TITLE`/`#SUBTITLE`、`#ARTIST`/`#SUBARTIST`、`#GENRE`、`#MAKER`、`#COMMENT`、`#TEXT`/`#SONG`、地雷、`#PATH_WAV`、`#CHARSET`、`%URL`/`%EMAIL` 详解。**解析元数据字段时必读** | `#TITLE` `#SUBTITLE` `#ARTIST` `#SUBARTIST` `#GENRE` `#MAKER` `#COMMENT` `#TEXT` `#SONG` `#WAV00` `#PATH_WAV` `#CHARSET` `%URL` `%EMAIL` `#xxx99` `#xxxD1-E9` |
| [09-header-commands-audio-and-bpm.md](./memo/09-header-commands-audio-and-bpm.md) | `#BPM`/扩展 BPM、`#BASEBPM`、`#STOP`/`#STP`、`#WAV`（含多重定义技巧）、`#WAVCMD`、`#EXWAV`、`#CDDA`、`#MIDIFILE` 详解及各实现兼容性矩阵。**实现音频和 BPM 系统时必读** | `#BPM` `#BPMxx` `#EXBPM` `#BASEBPM` `#STOPxx` `#STP` `#WAVxx` `#WAV00` `#WAVCMD` `#EXWAV` `#CDDA` `#MIDIFILE` `#xxx03` `#xxx08` `#xxx09` `#xxx01` `#xxxD1-E9` |
| [10-header-commands-long-notes-and-options.md](./memo/10-header-commands-long-notes-and-options.md) | `#LNTYPE 1/2`（RDM/MGQ 记法）、`#LNOBJ`、`#OCT/FP`、`#OPTION`、`#CHANGEOPTION` 详解。**实现长音系统和强制选项时必读** | `#LNTYPE` `#LNOBJ` `#OCT/FP` `#OPTION` `#CHANGEOPTION` `#xxx51-69` `#xxxA6` |
| [11-header-commands-image-and-bga.md](./memo/11-header-commands-image-and-bga.md) | `#BMP`、`#EXBMP`、`#BGA`/`#@BGA`、`#POORBGA`、`#SWBGA`、`#ARGB` 详解及图像格式兼容性矩阵。**实现 BGA 系统时必读** | `#BMPxx` `#EXBMPxx` `#BGAxx` `#@BGAxx` `#POORBGA` `#SWBGAxx` `#ARGBxx` `#xxx04` `#xxx06` `#xxx07` `#xxx0A` `#xxx0B-0E` `#xxxA1-A5` |
| [12-header-commands-video-and-extensions.md](./memo/12-header-commands-video-and-extensions.md) | `#VIDEOFILE`、`#MOVIE`、`#SEEK`、`#ExtChr`、MATERIALS 系列已废弃命令详解。实现视频播放和自定义皮肤时用 | `#VIDEOFILE` `#VIDEOf/s` `#VIDEOCOLORS` `#VIDEODLY` `#MOVIE` `#SEEKxx` `#ExtChr` `#MATERIALSWAV` `#MATERIALSBMP` `#DIVIDEPROP` `#xxx05` |
| [13-control-flow.md](./memo/13-control-flow.md) | `#RANDOM`/`#SWITCH` 控制流语法详解：嵌套、`#ELSE`/`#ELSEIF`、`#SETRANDOM`/`#SETSWITCH`、缩进样式、注释语法、各实现兼容性矩阵及测试用例。**实现控制流解析器时必读** | `#RANDOM` `#SETRANDOM` `#IF` `#ELSEIF` `#ELSE` `#ENDIF` `#ENDRANDOM` `#SWITCH` `#SETSWITCH` `#CASE` `#SKIP` `#DEF` `#ENDSW` |
| [14-channel-mapping.md](./memo/14-channel-mapping.md) | 完整通道分配表和按键分配表：BMS/BME/BML/PMS/DDR/OCT/FP 等全部格式的键位映射。**通道查询的唯一权威来源** | 所有通道 `#xxx01`–`#xxxE9` |

---

## ext/ —— 扩展格式与引擎规格

| 文件 | 内容 | 相关命令 |
|------|------|----------|
| [base62-format.md](./ext/base62-format.md) | 62 进制声明方式、映射表、索引对照、`#LNOBJ` 行为变化、不受影响的语法、兼容软件列表。**实现或支持 62 进制时必读** | `#BASE 62` `#WAVxx` `#BMPxx` `#BPMxx` `#STOPxx` `#SCROLLxx` `#SPEEDxx` `#LNOBJ` |
| [beatoraja-extensions.md](./ext/beatoraja-extensions.md) | beatoraja 特有扩展命令：`#PREVIEW`、`#LNMODE`（LN/CN/HCN）、`#RANK 4`、`#SCROLL` 负值、音频/视频格式规格。面向 beatoraja 做谱或实现兼容功能时用 | `#PREVIEW` `#LNMODE` `#RANK 4` `#DEFEXRANK` `#VOLWAV` `#SCROLL` |
| [engine-behavior-comparison.md](./ext/engine-behavior-comparison.md) | beatoraja 与 LR2 运行时行为对比：血量槽增减量（EASY–EXHARD）、判定宽度（VERY EASY–VERY HARD）、LN 判定（押し直し/repress）、`#DEFEXRANK`/JUDGERANK 关系、空 POOR/BAD ハマり/Combo/S 乱差异。**跨引擎兼容性分析和实现判定系统时必读** | `#PLAYER` `#RANK` `#DEFEXRANK` `#EXRANK` `#TOTAL` `#LNTYPE` `#LNOBJ` |
| [scroll-gimmick-guide.md](./ext/scroll-gimmick-guide.md) | Scroll/BPM/Stop 三类 Gimmick 实战教程：加速/减速、Warp、逐帧 Animation、空中停止、逆走、Scroll 出现/消失 Bug、非动画具現化、部分 Warp、负 Scroll 动画。**制作 Gimmick 谱面时必读** | `#SCROLLxx` `#SPEEDxx` `#BPMxx` `#STOPxx` `#xxxSC` `#xxxSP` |

---

## bmse/ —— BMSE 编辑器兼容性

| 文件 | 内容 | 相关命令 |
|------|------|----------|
| [01-overview.md](./bmse/01-overview.md) | 系列概述与来源说明，含完整文件索引。处理 BMSE 兼容性问题的起点 | — |
| [02-rewritten-commands.md](./bmse/02-rewritten-commands.md) | BMSE 打开时会篡改或删除的命令列表。排查谱面被 BMSE 保存后行为异常时用 | `#PLAYER 4` `#PLAYLEVEL` `#RANK 4` `#WAV00` `#WAVCMD` `%URL` `%EMAIL` `#RANDOM` |
| [03-native-commands.md](./bmse/03-native-commands.md) | BMSE 原生支持的命令及"卷起到分叉外"行为。理解 BMSE 如何解析标准命令时用 | `#PLAYER` `#GENRE` `#TITLE` `#ARTIST` `#BPM` `#PLAYLEVEL` `#RANK` `#TOTAL` `#VOLWAV` `#STAGEFILE` `#WAVzz` `#BMPzz` `#BGAzz` `#BPMzz` `#STOPzz` |
| [04-unsupported-extensions.md](./bmse/04-unsupported-extensions.md) | BMSE 不支持的扩展命令分类：可保留到扩展命令选项卡 vs 会被直接删除，含通道保留规则。保护谱面内容不被 BMSE 破坏时用 | `#DEFEXRANK` `#BANNER` `#PREVIEW` `#DIFFICULTY` `#SUBTITLE` `#SCROLLzz` `#SPEEDzz` 等全部扩展命令及 `#WAV00` `#WAVCMD` `#xxxD1-E9` `#xxx17`/`#xxx27` |
| [05-control-flow-branching.md](./bmse/05-control-flow-branching.md) | BMSE 的控制流解析行为：识别/不识别哪些控制命令、分支识别逻辑、嵌套限制。在 BMSE 中使用 `#RANDOM`/`#IF` 时必读 | `#RANDOM` `#SETRANDOM` `#IF` `#ELSEIF` `#ELSE` `#ENDIF` `#ENDRANDOM` `#SWITCH` `#CASE` `#SKIP` `#DEF` `#ENDSW` |
| [06-dummy-branch-technique.md](./bmse/06-dummy-branch-technique.md) | ダミー分岐（Dummy Branch）技巧：基本写法、适用场景（保护地雷/SCROLL/自由区域等）、注意事项。**保护扩展命令不被 BMSE 删除时必读** | `#RANDOM` `#IF` `#ENDIF` `#ENDRANDOM` |
| [07-extension-tab.md](./bmse/07-extension-tab.md) | 扩展命令选项卡容量限制（各 Windows 版本上限）、与主面板的功能隔离、撤销限制、保存位置。大谱面使用 BMSE 时的限制参考 | — |
| [08-beat-tab-operations.md](./bmse/08-beat-tab-operations.md) | 拍子选项卡操作说明：基本操作、小节长度定义、快捷键、全选注意事项、对象重叠处理。使用 BMSE 编辑变拍子谱面时用 | `#xxx02` |
| [09-measure-length-limits.md](./bmse/09-measure-length-limits.md) | BMSE 小节长度限制：精度（0.015625 倍数）、最小值/最大值、链式保存退化问题、比率型ソフランの弱点、特殊拍子崩溃条件。**理解 BMSE 如何处理小节长度时必读** | `#xxx02` `#BPM` |
| [10-scroll-speed-extensions.md](./bmse/10-scroll-speed-extensions.md) | `#SCROLL`/`#SPEED` 扩展详解：语法、通道号、负值逆向滚动、BMSE 保存兼容性。实现或使用 SCROLL/SPEED 扩展时必读 | `#SCROLLxx` `#SPEEDxx` `#xxxSC` `#xxxSP` |

---

## test/ —— 可执行规范（BMSpec）测试用例

Gherkin 可执行规范，按功能分组。实现 BMS 解析器/生成器时，以此验证格式兼容性。

| 文件 | 覆盖主题 | 测试场景数 |
|------|----------|-----------|
| [bmspec-1-basic.md](./test/bmspec-1-basic.md) | 句子解析、头部命令、对象定位与合并、拍号、BPM、STOP、基本信息、WAV 引用 | 22 |
| [bmspec-2-bml.md](./test/bmspec-2-bml.md) | 长音 LNOBJ 与 LNTYPE1 两种模式 | 2 |
| [bmspec-3-positioning.md](./test/bmspec-3-positioning.md) | SCROLL 滚动定位（含扩展行） | 3 |
| [bmspec-4-random.md](./test/bmspec-4-random.md) | RANDOM 随机分支（含多随机值序列） | 3 |
| [bmspec-5-song-info.md](./test/bmspec-5-song-info.md) | 隐式/显式副标题、多行副标题、子艺术家 | 4 |
| [bmspec-6-spacing.md](./test/bmspec-6-spacing.md) | SPEED 间距（含多值插值） | 3 |
