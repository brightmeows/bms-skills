# 制御構文（Control Constructs）与 BMSE 的分支识别

> 来源：[BMSE Help — 拍子・拡張命令タブ](https://hitkey.nekokan.dyndns.info/bmse_help_full/beat.html)

BMSE 对标准 BMS 控制构文的支持有限：

| 命令 | BMSE 识别 |
|------|-----------|
| `#RANDOM` | 识别 |
| `#SETRANDOM` | 不识别 |
| `#IF` | 识别 |
| `#ELSEIF` | 不识别 |
| `#ELSE` | 不识别 |
| `#ENDIF` | 识别 |
| `#ENDRANDOM` | 不识别 |
| `#SWITCH` | 不识别 |
| `#SETSWITCH` | 不识别 |
| `#CASE` | 不识别 |
| `#SKIP` | 不识别 |
| `#DEF` | 不识别 |
| `#ENDSW` | 不识别 |

## BMSE 的分支识别逻辑

- 识别 **`#IF` 到最近的 `#ENDIF` 的区间**，区间内的未知命令和所有通道行收纳入扩展命令选项卡。
- 如果有 `#RANDOM` 命令，还识别 **`#RANDOM` 到最近的 `#IF` 的区间**。

## 嵌套问题

BMSE **不支持分支嵌套**。例如以下代码：

```bms
#RANDOM 1
    #LEVEL 1
    #IF 1
        #00111:11
        #RANDOM 1
            #LEVEL 2
            #IF 1
                #00212:22
            #ENDIF
        #ENDRANDOM
        #00313:33
    #ENDIF
#ENDRANDOM
```

BMSE 将「第一个 `#RANDOM` 到第一个出现的 `#ENDIF`」识别为分支区间。因此：

- `#00313:33` 被视为分支范围外的普通文本，被推出扩展命令选项卡，在主面板渲染。
- `#RANDOM`-`#IF`-`#ENDIF` 以外的控制命令（如 `#SWITCH`）视为未知扩展命令，收纳入扩展命令选项卡。
- 使用单一 `#IF`-`#ENDIF` 区间且不嵌套时可正常工作。
- `#SWITCH` 区间内无 `#RANDOM` 嵌套时，可将整个 `#SWITCH` 区间用[ダミー分岐](06-dummy-branch-technique.md)保护。

![随机分支演示](https://hitkey.nekokan.dyndns.info/bmse_help_full/Capture/bmse_material_expand2.gif)
