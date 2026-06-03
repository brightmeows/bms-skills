# 扩展命令选项卡的容量限制与注意事项

> 来源：[BMSE Help — 拍子・拡張命令タブ](https://hitkey.nekokan.dyndns.info/bmse_help_full/beat.html)

## 容量限制

| 操作系统 / 条件 | 上限 | 超出时的行为 |
|-----------------|------|-------------|
| Windows 95 | 63356 字节（ANSI 换算） | 强制退出 |
| Windows 98/ME，仅 ASCII 字符 | 63348 字节（ANSI 换算） | — |
| Windows 98/ME，非 ASCII 字符多 | 16383 字符 | 16384 字符起截断 |
| Windows 2000/XP+，仅 ASCII 字符 | 65535 字符 | 65536 字符起截断丢弃 |
| Windows 2000/XP+，非 ASCII 字符多 | 32767 字符 | 32768 字符起截断丢弃 |
| Windows 2000/XP+，非 ASCII 字符少量混入 | **9 字符**（!?） | Visual Basic 6.0 的固有限制 |

> **注意**：最后一项「9 字符限制」源于 Visual Basic 6.0 和 Windows 的底层行为，非 BMSE 自身问题。但在东亚语言环境中，BMS 代码中混入非 ASCII 字符是常见情况，因此扩展命令选项卡中编辑大量内容时应格外谨慎。

- 文件大小超过 64 KiB（65535 bytes）的谱面可能包含巨大的分叉，打开前建议先检查。

## 与主面板的功能隔离

BMSE 的**所有功能均不适用于扩展命令选项卡**的内容：

| BMSE 操作 | 对扩展命令选项卡的影响 |
|-----------|----------------------|
| 定义列表整列 | 选项卡内的配置行继续引用整列前的编号 |
| 未使用定义删除 | 仅在 `#IF`-`#ENDIF` 区间内被引用的定义视为未使用并被删除 |
| 未使用文件删除 | 仅在 `#BANNER` 定义中被引用的图像文件视为未使用并被删除 |
| 小节插入/删除 | 分支内容可能适用于非预期的小节 |
| 拍子变更 | 选项卡内“等分配置”的分割长度变更（节奏变化） |
| 组合执行 | 可能无法修复 |

**建议**：在所有其他编辑工作完成后，最后写入扩展命令选项卡的内容。

## 撤销限制

扩展命令选项卡文本框内的 Ctrl+Z/Ctrl+Y 是 **Windows 自身的撤销功能**，不是 BMSE 的撤销/重做。通常只能追溯 **1 步**。

![9 字符限制图示](https://hitkey.nekokan.dyndns.info/bmse_help_full/Capture/why-expand-textbox.gif)

## 保存位置

BMSE 保存时，扩展命令选项卡的记述插入到 BMS 代码中 **MAIN DATA FIELD 的紧前方**。

![BMSE 标题栏星号标记](https://hitkey.nekokan.dyndns.info/bmse_help_full/Capture/bmse00_titlebar.gif)

## 星号标记

打开含有扩展命令选项卡内容的谱面时，BMSE 标题栏末尾显示 `*`（星号）。

> 参见：[ダミー分岐技巧](06-dummy-branch-technique.md)——用ダミー分岐保护内容免于被删除；[BMSE 不支持的扩展命令](04-unsupported-extensions.md)——哪些命令被隔离到扩展命令选项卡。
