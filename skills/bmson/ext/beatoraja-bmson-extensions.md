# BMSON 格式

> 来源：[beatoraja Wiki — 楽曲製作者向け資料](https://github.com/exch-bms2/beatoraja/wiki/%E6%A5%BD%E6%9B%B2%E8%A3%BD%E4%BD%9C%E8%80%85%E5%90%91%E3%81%91%E8%B3%87%E6%96%99)
> BMSON 是 BMS 的 JSON 序列化格式，以结构化方式描述谱面。

## 概述

| 项目 | 内容 |
|------|------|
| 文件扩展名 | `.bmson` |
| 版本 | 0.2.1 / 1.0.0 |
| 支持实现 | beatoraja |
| 格式 | JSON |

BMSON 将 BMS 的头部信息、通道数据、定义等以 JSON 对象表示，便于程序生成和解析。

## beatoraja 支持的扩展定义

### ln_type（BMSInfo）

- 在根级 BMSInfo 对象中指定全局长音类型。
- 值：`1`（LN）、`2`（CN）、`3`（HCN）。
- 类型定义同 BMS 侧 [`#LNMODE`](./../bms/beatoraja-extensions.md)。

```json
{
  "info": {
    "title": "example",
    "ln_type": 1
  }
}
```

### t（Note）

- 在单个 Note 对象中指定该音符的长音类型。
- 优先级高于 BMSInfo 中的 `ln_type`。
- 同一谱面中允许不同类型的长音混用。

```json
{
  "notes": [
    { "x": 0, "y": 0, "l": 240, "t": 1 },
    { "x": 1, "y": 120, "l": 240, "t": 2 }
  ]
}
```

### up（Note）

- 布尔值，`true` 时在该 Note 的长音终点位置播放终点音。
- 须与 `l > 0` 结合使用。
- beatoraja 0.6.2+ 支持。

```json
{ "x": 0, "y": 0, "l": 240, "up": true }
```

### scroll_events（BMSON Root）

| 项目 | 内容 |
|------|------|
| 值 | `ScrollEvent[]` |
| 支持 | beatoraja 0.7.6+ |

- BMSON 形式的 `#SCROLL` / `#SPEED`，详见 BMS 侧 [`beatoraja-extensions.md`](./../bms/ext/beatoraja-extensions.md#SCROLL)。
- 以事件数组形式定义谱面各位置的滚动速度倍率。
- 每个事件包含 `y`（位置）和 `rate`（倍率）。
- 倍率为负值时谱面**逆走**。

```json
{
  "scroll_events": [
    { "y": 0, "rate": 1.0 },
    { "y": 240, "rate": 2.0 },
    { "y": 480, "rate": 0.5 }
  ]
}
```

### mine_channels（BMSON Root）

| 项目 | 内容 |
|------|------|
| 值 | `MineChannel[]` |
| 每个 MineChannel | `{ "name", "notes" }` |

- 地雷通道定义。
- `name`：通道名称（字符串）。
- `notes`：MineNote 数组。
- MineNote 包含 `x`（轨道）、`y`（位置）、`damage`（伤害值，**支持小数**）。
- 判定时若按住按键则扣减对应伤害值的血量。
- 地雷爆炸时播放指定音源。

```json
{
  "mine_channels": [
    {
      "name": "mine1",
      "notes": [
        { "x": 0, "y": 120, "damage": 10.5 },
        { "x": 1, "y": 240, "damage": 25.0 }
      ]
    }
  ]
}
```

### key_channels（BMSON Root）

| 项目 | 内容 |
|------|------|
| 值 | `KeyChannel[]` |
| 每个 KeyChannel | `{ "name", "notes" }` |

- 不可见音符（Invisible Note）通道定义。
- `name`：通道名称（字符串）。
- `notes`：KeyNote 数组。
- KeyNote 包含 `x`（轨道）、`y`（位置）。
- 不可见音符不显示、不判定，但在该位置空打按键可播放指定音源。

```json
{
  "key_channels": [
    {
      "name": "ghost1",
      "notes": [
        { "x": 0, "y": 0 },
        { "x": 2, "y": 120 }
      ]
    }
  ]
}
```
