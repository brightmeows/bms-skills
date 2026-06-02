# 更新日志

> 来源：[bmson format specification — bmson-spec 1.0.0-beta documentation](https://bmson-spec.readthedocs.io/en/master/doc/#changelog)

## 1.0.0（从 0.21 起）

### 破坏性变更

- 将所有 *camelCase* 字段改为 *snake_case*。

  - `soundChannel`
  - `judgeRank`
  - `initBPM`
  - `bgaHeader`
  - `bgaNotes`
  - `layerNotes`
  - `poorNotes`
  - `ID`

- 字段重命名

  - `bpmNotes` → `bpm_events`
  - `stopEvents` → `stop_events`

- 移除字段

  - `BarLine.k`

    - 在通用 bmson 格式中不必要

- 将 `EventNote` 拆分为 `BpmEvent` 和 `StopEvent`

  - `bpmNotes.v` 现为 `BpmEvent.bpm`
  - `stopNotes.v` 现为 `StopEvent.duration`

- 时间单位已改为 *pulse*（脉冲）
- `BMSInfo.total` 改为相对值

### 非破坏性变更

- 新增字段

  - `version`
  - `BMSInfo.subtitle`
  - `BMSInfo.subartists`
  - `BMSInfo.mode_hint`
  - `BMSInfo.chart_name`
  - `BMSInfo.back_image`
  - `BMSInfo.eyecatch_image`
  - `BMSInfo.banner_image`
  - `BMSInfo.preview_music`
  - `BMSInfo.resolution`
