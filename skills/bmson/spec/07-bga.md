# BGA

> 来源：[bmson format specification — bmson-spec 1.0.0-beta documentation](https://bmson-spec.readthedocs.io/en/master/doc/#bga-bga)

目前，BGA 规范仅与 BMS 兼容。

## bga_header :: BGAHeader[]

- `id` 是图片文件标识符。
  - 如果同一文件中存在相同的值，播放器可以发出警告，采用后者。
- `name` 是图片文件的路径。
- 建议图片尺寸为 1280x720。1920x1080 也可接受。
  - 在宽高比不同的游戏中，背景图片可能在中心处被裁剪。因此，请确保关键元素靠近图片中心。
- 播放器应支持以下文件格式：
  - 图片：PNG
  - 视频：WebM
    - 音频通道可以被忽略。

## bga_events、layer_events、poor_events :: BGAEvent[]

`bga_events` 表示将作为歌曲背景动画显示的图片/视频文件 [^4]。

`layer_events` 表示将*叠加*在 BGA 之上的图片/视频文件。

- `id` 指定在 `bga_header` 中声明的图片。
- `y` 是显示图片时的脉冲编号。
- 与 [BMS Layer Channel #xxx07](http://hitkey.nekokan.dyndns.info/cmds.htm#BMPXX-LAYER) 不同，
  黑色像素不会被设为透明。如果需要透明效果，
  请使用支持透明度的文件格式，如 PNG [^5]。

[^4]: 某些游戏可能选择将 BGA 作为背景显示，并在其上叠加音符。使用此方法的商业游戏示例有 DJ MAX 系列、DDR 和 Pump It Up。其他游戏可能在专用空间中显示 BGA。示例有 beatmaniaIIDX 和 LunaticRave2。

[^5]: 由此推论，转换器在将 BMP 文件转换为 PNG 时，应将纯黑色像素转换为透明像素。注意同一图片文件可能同时用作 BGA 和 LAYER，因此单个 BMP 文件可能需要转换为两个不同的 PNG 文件。
