# 62 进制 BMS 格式规范

> 来源：[62進数BMSフォーマット仕様書](https://docs.google.com/document/u/0/d/e/2PACX-1vTl8zOS3ukl5HpuNsBUlN8rn_ZaNdJSHb8a4se3Z3ap9Y6UJ1nB8LA3HnxWAk9kMTDp0j9orpg43-tl/pub)
> beatoraja 0.8.7 起支持在 BMS 定义中使用 62 进制数。WAV·BMP 的最大定义数从 1295 增加到 3843。

## 概述

传统 BMS 使用 36 进制（`[0-9A-Z]`）作为 `#WAV`、`#BMP` 等定义的索引，上限 1295 个槽位。

62 进制扩展在保留 36 进制 `0-9A-Z` 的基础上，新增小写字母 `a-z`（对应 36–61），
将单索引范围从 `[0-Z]`（36 种）扩展到 `[0-z]`（62 种），
双索引最大编号从 ZZ（1295）扩展到 **zz（3843）**。
通过此扩展，BMS 可实现更丰富的表现。

## 62 进制映射表

| 10 进制 | 62 进制 | 说明 |
|---------|---------|------|
| 0–9 | `0`–`9` | 与 36 进制相同 |
| 10–35 | `A`–`Z` | 与 36 进制相同 |
| 36–61 | `a`–`z` | **新增**。36 进制中未定义的范围 |

> 译者注：以下对照表为便于理解而补充，原文无此表。

### 常见索引对照

| 62 进制 | 10 进制 | 计算过程 | 说明 |
|---------|---------|---------|------|
| `AA` | 630 | 10(`A`) × 62 + 10(`A`) = 630 | |
| `Aa` | 656 | 10(`A`) × 62 + 36(`a`) = 656 | 36 进制中与 `AA` 重复 |
| `aA` | 2242 | 36(`a`) × 62 + 10(`A`) = 2242 | |
| `aa` | 2268 | 36(`a`) × 62 + 36(`a`) = 2268 | |
| `zz` | 3843 | 61(`z`) × 62 + 61(`z`) = 3843 | 最大值 |

> 62 进制映射：`0`=0 … `9`=9，`A`=10 … `Z`=35，`a`=36 … `z`=61。
> 两位 62 进制数 `XY` 的十进制值 = digit_value(`X`) × 62 + digit_value(`Y`)。

## 声明

```bms
#BASE 62
```

- BMS 使用 62 进制时**必须**在头部声明 `#BASE 62`。
- 声明后，以下定义以**区分大小写**的 62 进制读取：
  - `#WAVxx`
  - `#BMPxx`
  - `#BPMxx`
  - `#STOPxx`
  - `#SCROLLxx`
  - `#SPEEDxx`
  - `#LNOBJ`
- `#BASE 62` 声明**必须**的原因是原始 BMS 格式不区分大小写。无此声明时，`aa` 和 `AA` 被视为相同索引。

```bms
// 例如，无 #BASE 62 声明时，以下代码中 aa 会被视为 AA
// 此时 kick.wav 会覆盖 rev-cymbal.wav
#WAVAA kick.wav
#00101:aa
```

### 完整示例

以下为 62 进制 BMS 的完整示例（含头部字段和主数据字段）：

```bms
*---------------------- HEADER FIELD
#PLAYER 1
#GENRE -
#TITLE Base 62 sample data
#ARTIST -
#BPM 127
#PLAYLEVEL 1
#RANK 3
#DIFFICULTY 1
#TOTAL 100

// 62 进制声明
#BASE 62
#LNOBJ zz

// 36 进制下以下 4 行相互重复，只有最后一行（hihat.wav）生效
// 62 进制下 4 个全部独立加载，加载成功即说明 62 进制生效
#WAVAA rev-cymbal.wav
#WAVAa cymbal.wav
#WAVaA kick.wav
#WAVaa hihat.wav
#WAVZZ cymbal.wav

// 同理，62 进制下仅 NotBase62.png 被加载
#BMP0A Base62.png
#BMP0a NotBase62.png

*---------------------- MAIN DATA FIELD

// BPM=127（16 进制，不变）
#00003:7f

#00116:AA           // 36 进制下为 LNOBJ；62 进制下为 cymbal.wav
#00216:ZZ
#00211:aAaAaAaA
#00219:00aa00aa00aa00aa
#00316:Aa

// 伤害 10 的地雷（36 进制，不变）
#004D6:0a
#004D1:0a
#004D2:0a
#004D3:0a
#004D4:0a
#004D5:0a
#004D8:0a
#004D9:0a

// 36 进制下引用 NotBase62.png，62 进制下引用 Base62.png
#00004:0A
```

## 不受影响的语法

| 语法 | 原因 |
|------|------|
| `#xxx03`（旧式 BPM 变更） | 仍为 16 进制 `[01-FF]`，最大值 255 |

旧式 BPM 变更（ch03）定义为 16 进制，最大值 FF=255。本次实现不影响此规格。因此上述写法表示变更为 BPM=127。

| 地雷伤害值 `#xxxD1-E9` | 仍为 36 进制，`ZZ` = 即死 |

地雷定义为 36 进制伤害值，ZZ = 本来是即游戏结束的含义。此规格也不受影响。因此上述各行表示 0a=0A=10% 伤害的地雷。实际实现中可能使用考虑 #RANK 和模式的综合计算公式。

```bms
// BPM=127（16 进制，不变）
#00003:7F

// 伤害 10% 的地雷（36 进制，不变）
// 0a = 0A = 10
#004D6:0A
```

## #LNOBJ 的行为变化

`#LNOBJ` 表示与可见 Note 同一通道的长按终止端。本次实现中 `#LNOBJ` 也以 62 进制处理。

`#LNOBJ` 在 62 进制下也区分大小写：

```bms
#BASE 62
#LNOBJ zz

#00116:AA
// 62 进制下 #LNOBJ zz 与 #00216:ZZ 不匹配
// 因此 ch16 的 #001 小节开头有 AA、#002 小节开头有 ZZ，产生两个独立 Note
//
// 36 进制下 #LNOBJ zz 与 #00216:ZZ 匹配
// #00116:AA 与 #00216:ZZ 作为长按对，ch16 的 #001 小节开头到 #002 小节开头成为 LN
#00216:ZZ
```

## 兼容性

### 62 进制对应软件（2024-04-17 确认）

| 类别 | 软件 | 版本 |
|------|------|------|
| 播放 | [**beatoraja**](https://mocha-repository.info/) | 0.8.7+ |
| 播放 | [**mBMplay**](https://mistyblue.info/mbmplay.html) | v3.24.0414.0 |
| 编辑 | [**BMSE**](https://github.com/Nekokan/BMSE/releases) | 2.2.0a（官方支持） |
| 编辑 | [**μBMSC**](https://nekokan.dyndns.info/file/BOFoonNT/separate/) | 定制构建 |
| 编辑 | [**BMHelper**](https://nekokan.dyndns.info/file/BOFoonNT/separate/) | 定制构建 |
| 编辑 | [**Mid2BMS**](https://nekokan.dyndns.info/file/BOFoonNT/separate/) | 定制构建 |
| 编辑 | [**sayaslicer**](https://github.com/SayakaIsBaka/sayaslicer) | - |

> 译者注：以下注意事项为译者基于规范内容整理，原文无此节。

### 注意事项

- 不支持 62 进制的旧软件会将 `a-z` 解释为 36 进制范围之外的值，可能导致行为异常。
- 使用 62 进制扩展的谱面应确保目标播放器（如 beatoraja 0.8.7+）支持此特性。
- `#BASE 62` 声明位于头部字段，旧软件通常会将其作为未知命令忽略，不影响基本解析。
