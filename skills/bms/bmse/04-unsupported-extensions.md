# BMSE 不支持的扩展命令

> 来源：[BMSE Help — 拍子・拡張命令タブ](https://hitkey.nekokan.dyndns.info/bmse_help_full/beat.html)

扩展命令选项卡是用于记录 BMSE 不直接支持的命令的文本框。
关于文本输入操作参见
[BMSE Help — テキスト入力フォームの操作](https://hitkey.nekokan.dyndns.info/bmse_help_full/direct.html#textbox)，
扩展命令的详细说明参见
[BMS command memo](https://hitkey.nekokan.dyndns.info/cmdsJP.htm#TOC)
等文档。

BMSE 不支持的命令几乎全部被隔离到此选项卡。但有例外：自由区域和地雷的配置通道文在 BMSE 打开时会被**删除**。因此仅通过 BMSE 打开谱面无法判断是否存在 BMSE 不支持的命令——这一点请注意。

以下命令 BMSE 不原生支持，打开后会被隔离到“扩展命令”选项卡。**但并非所有都能安全保留——部分会被直接删除**。

## 可保留到扩展命令选项卡的

| 命令 | 补充说明 |
|------|----------|
| `#DEFEXRANK` | |
| `#EXRANKzz` | 对应通道 `#xxxA0` 保留 |
| `#BANNER` | |
| `#BACKBMP` | |
| `#CHARFILE` | |
| `#PREVIEW` / `#PREVIEWPOINT` / `#PREVIEWTIME` | |
| `#OFFSET` | |
| `#DIFFICULTY` | |
| `#SUBTITLE` | |
| `#SUBARTIST` | |
| `#MAKER` | |
| `#COMMENT` | |
| `#EXT` | |
| `#TEXTzz` / `#SONGzz` | 通道 `#xxx99` 被删除，但 `#TEXTxx` 定义可保留 |
| `#PATH_WAV` | 只是字符串，无害 |
| `#EXBPMzz` | |
| `#BASEBPM` | |
| `#STP` | |
| `#LNTYPE` 1/2 | |
| `#LNOBJ ZZ` | |
| `#LNMODE` | |
| `#SCROLLzz` | 通道 `#xxxSC` 保留 |
| `#SPEEDzz` | 通道 `#xxxSP` 保留 |
| `#4K` / `#6K` | |
| `#OCT/FP` | |
| `#OPTION` | |
| `#CHANGEOPTIONzz` | 通道 `#xxxA6` 保留 |
| `#EXWAVzz` | |
| `#CDDA` | |
| `#MIDIFILE` | |
| `#EXBMPzz` | |
| `#@BGAzz` | |
| `#POORBGA` | |
| `#SWBGAzz` | 通道 `#xxxA5` 保留 |
| `#ARGBzz` | |
| `#VIDEOFILE` / `#VIDEOF/S` / `#VIDEOCOLORS` / `#VIDEODLY` | |
| `#MOVIE` | |
| `#SEEKzz` | 通道 `#xxx05` 被删除 |
| `#EXTCHR` | 通道 `#xxx05` 被删除 |
| `#MATERIALSWAV` / `#MATERIALSBMP` | |
| `#DIVIDEPROP` | |
| `#CHARSET` | |

## 会被 BMSE 直接删除的

| 命令或通道 | 原因 |
|-----------|------|
| `#WAV00` | 被当作 `#WAV` 处理，00 被视为非法编号被删 |
| `#WAVCMD` | 变为 `#WAVMD`（多个 `#WAVCMD` 时，仅最后一个幸存为 `#WAVMD`） |
| 地雷通道 `#xxxD1-E9` | **从 BMS 文件中删除** |
| 自由区域通道 `#xxx17` / `#xxx27` | 从通道列表中删除 |
| 通道 `#xxx[1-6](0\|7)` / `#xxx[7-9][0-9]` | 读取时删除（含 `xxx17` 等自由区域通道） |
| 通道 `#xxx[D-E][0-9]` | **删除**（含地雷通道） |
| `%URL` / `%EMAIL` | 不以 `#` 开头，被忽略删除 |
| **信息选项卡**（创作者注释等） | BMSE 对象面板 → 信息选项卡内容被删除 |

> 如果不想让 `%URL` / `%EMAIL` / 信息选项卡内容被删除，可以在行首添加 `#`，将其伪装为 BMSE 扩展命令选项卡可保留的格式。但无法保证其他应用程序能正常播放。
> **时效性说明**：以下信息基于截至 2024 年的资料。2024 年后新型播放器仍在不断实现新扩展命令，BMSE Help 作者可能未能全部掌握。

## BMSE 读取时保留到扩展命令选项卡的

以下通道 BMSE 无法原生解析，但**不会删除**——读取后自动收入扩展命令选项卡：

| 通道范围 | 行为 |
|---------|------|
| `#xxx[0-6][A-Z]` | 保留到扩展选项卡 |
| `#xxx[7-9][A-Z]` | 保留到扩展选项卡 |
| `#xxx[A-C][0-9A-Z]` | 保留到扩展选项卡 |
| `#xxx[D-E][A-Z]` | 保留到扩展选项卡 |
| `#xxx[F-Z][0-9A-Z]` | 保留到扩展选项卡 |

![拡張命令タブ预览](https://hitkey.nekokan.dyndns.info/bmse_help_full/Capture/bmse_material_expand.gif)

> **注意**：若将上述会被删除的通道写在 `#IF`-`#ENDIF` 分叉中（用 [ダミー分岐技巧](06-dummy-branch-technique.md) 保护），则 BMSE 不会删除它们。
