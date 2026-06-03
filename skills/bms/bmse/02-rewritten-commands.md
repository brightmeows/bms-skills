# BMSE 打开时会改写的命令

> 来源：[BMSE Help — 拍子・拡張命令タブ](https://hitkey.nekokan.dyndns.info/bmse_help_full/beat.html)

下列命令在 BMSE 中打开时会被篡改或删除：

| 命令 | 问题 |
|------|------|
| `#PLAYER 4` | Battle Play → 变为 PMS（9 Keys） |
| `#PLAYLEVEL string` | 字符串值难度 → 变为值 `0` |
| `#RANK 4` | VERY EASY 判定 → 变为 EASY（值 `3`） |
| `#WAV00` | 地雷爆炸音定义 → **被删除** |
| `#WAVCMD` | → 变为 `#WAVMD` |
| `%URL` | → **被删除** |
| `%EMAIL` | → **被删除** |
| `#RANDOM` 嵌套 | 嵌套范围识别错误 |
| `#RANDOM` 区间内写有 BMSE 原生支持的命令 | 这些命令被“卷起”到分叉外 |
| 扩展命令选项卡装不下的分叉或通道数据 | → **被丢弃** |
