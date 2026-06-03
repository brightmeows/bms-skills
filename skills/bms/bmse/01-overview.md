# BMSE 兼容性参考——概述

> 来源：[BMSE Help — 拍子・拡張命令タブ](https://hitkey.nekokan.dyndns.info/bmse_help_full/beat.html)
> 涉及 BMSE 打开和保存 BMS 文件时对命令的处理行为。

本文档系列整理 BMSE（Be-Music Source Editor）在打开和保存 BMS 文件时对各类命令的处理行为。BMSE 对部分命令有改写/删除行为，可用ダミー分岐（Dummy Branch）技巧保护。

## 文件索引

| # | 文件 | 内容 |
|---|------|------|
| 01 | 本文 | 概述与来源 |
| 02 | [02-rewritten-commands.md](02-rewritten-commands.md) | BMSE 打开时会改写的命令 |
| 03 | [03-native-commands.md](03-native-commands.md) | BMSE 原生支持的命令 |
| 04 | [04-unsupported-extensions.md](04-unsupported-extensions.md) | BMSE 不支持的扩展命令 |
| 05 | [05-control-flow-branching.md](05-control-flow-branching.md) | 制御構文与 BMSE 的分支识别 |
| 06 | [06-dummy-branch-technique.md](06-dummy-branch-technique.md) | ダミー分岐（Dummy Branch）技巧 |
| 07 | [07-extension-tab.md](07-extension-tab.md) | 扩展命令选项卡的容量限制与注意事项 |
| 08 | [08-beat-tab-operations.md](08-beat-tab-operations.md) | 拍子选项卡的操作说明 |
| 09 | [09-measure-length-limits.md](09-measure-length-limits.md) | BMSE 的小节长度限制 |
| 10 | [10-scroll-speed-extensions.md](10-scroll-speed-extensions.md) | #SCROLL / #SPEED 扩展 |

> **时效性说明**：以下信息基于截至 2024 年的资料。2024 年后新型播放器仍在不断实现新扩展命令，BMSE Help 作者可能未能全部掌握。
