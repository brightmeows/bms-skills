# BMSE 原生支持的命令

> 来源：[BMSE Help — 拍子・拡張命令タブ](https://hitkey.nekokan.dyndns.info/bmse_help_full/beat.html)

![BMSE 主面板截图](https://hitkey.nekokan.dyndns.info/bmse_help_full/Capture/bmse_material_beat_mpanel.gif)

以下命令 BMSE 原生支持（出现在主面板或定义列表中）。即使写在 `#IF`-`#ENDIF` 分叉中，BMSE 读取时也会将其**卷起到分叉外**。

- `#PLAYER`
- `#GENRE`
- `#TITLE`
- `#ARTIST`
- `#BPM`
- `#PLAYLEVEL`
- `#RANK`（值 `4`（VERY EASY）会被矫正为 `3`（EASY），可通过 `#DEFEXRANK` 指定同等判定宽度）
- `#TOTAL`
- `#VOLWAV`
- `#STAGEFILE`
- `#WAVzz`（`<Materials>` 记法不会报错但不生效）
- `#BMPzz`（同上）
- `#BGAzz`
- `#BPMzz`（定义编号被自动重排为 `01` 开始的连续编号）
- `#STOPzz`（定义编号被自动重排为 `01` 开始的连续编号）

> **注意**：如果 `#BPMzz` / `#STOPzz` 的**定义**和**配置通道**都写在分叉内，BMSE 读取后：定义被卷起到分叉外，配置通道留在分叉内。卷起后的定义因无配置引用而被视为无意义删除。
