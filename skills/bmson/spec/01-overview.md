# bmson 格式规范

> 来源：[bmson format specification — bmson-spec 1.0.0-beta documentation](https://bmson-spec.readthedocs.io/en/master/doc/)

- 版本：1.0.0-beta（2015/12/26）

## 链接

| 项目 | 链接 |
| --- | --- |
| 官方网站 | <https://bmson.nekokan.dyndns.info/> |
| how_to_bmson（日语） | <http://www40.atwiki.jp/laser_bm/pages/110.html> |
| #bmson 创建笔记（英语） | <https://docs.google.com/document/d/1gQKPWApeL03aO09-II7slxTeuvm3HO_FmY1D4chRvOQ> |

## 概述

bmson 是一种基于 JSON 的文件格式。

与 BMS 相比，bmson 对于制谱者和开发者而言都是更易处理的格式，因为该格式期望播放器在游戏时对音频音干（sound stems）进行切片，而非要求制谱者事先完成所有工作。这也消除了单个谱面中音效数量的限制，并允许谱面作者使用任何音频的任何部分。

对于开发者而言，由于基于 JSON，实现起来比 BMS 更简单——一个 JSON 解析器就能让格式的实现变得更快。

> [!NOTE]
> 虽然该格式基于 JSON，但本文档中展示的一些 bmson 示例使用了 [YAML 表示法](http://www.yaml.org/spec/1.2/spec.html)书写，以保持简洁和可读性。

## 格式总览

该格式遵循 [Web IDL (Second Edition)](https://heycam.github.io/webidl/)。

```c
// 顶层对象
dictionary Bmson {
    DOMString      version;        // bmson 版本
    BmsonInfo      info;           // 信息，例如标题、艺术家……
    BarLine[]?     lines;          // 小节线位置（以脉冲为单位）
    BpmEvent[]?    bpm_events;     // BPM 变化
    StopEvent[]?   stop_events;    // 停止事件
    SoundChannel[] sound_channels; // 音符数据
    BGA            bga;            // BGA 数据
}

// 头部信息
dictionary BmsonInfo {
    DOMString     title;                 // 不言自明
    DOMString     subtitle = "";         // 不言自明
    DOMString     artist;                // 不言自明
    DOMString[]?  subartists = [];       // ["key:value"]
    DOMString     genre;                 // 不言自明
    DOMString     mode_hint = "beat-7k"; // 布局提示，例如 "beat-7k"、"popn-5k"、"generic-nkeys"
    DOMString     chart_name;            // 例如 "HYPER"、"FOUR DIMENSIONS"
    unsigned long level;                 // 不言自明
    double        init_bpm;              // 不言自明
    double        judge_rank = 100;      // 相对判定窗口
    double        total = 100;           // 相对生命条增益
    DOMString?    back_image;            // 背景图片文件名
    DOMString?    eyecatch_image;        // 过场图片文件名
    DOMString?    banner_image;          // 横幅图片文件名
    DOMString?    preview_music;         // 预览音乐文件名
    unsigned long resolution = 240;      // 每四分音符的脉冲数
}

// 小节线事件
dictionary BarLine {
    unsigned long y; // 脉冲编号
}

// 音频通道
dictionary SoundChannel {
    DOMString name; // 音频文件名
    Note[] notes;   // 使用此音频的音符
}

// 音频音符
dictionary Note {
    any x;           // 轨道
    unsigned long y; // 脉冲编号
    unsigned long l; // 长度（0：普通音符；大于零（以脉冲为单位的长度）：长按音符）
    boolean c;       // 延续标志
}

// BPM 音符
dictionary BpmEvent {
    unsigned long y; // 脉冲编号
    double bpm;      // BPM
}

// 停止音符
dictionary StopEvent {
    unsigned long y;        // 脉冲编号
    unsigned long duration; // 停止持续时间（要停止的脉冲数）
}

// 对于任何自定义的计时类，
// 请遵循 BpmEvent 或 StopEvent 的格式。

// BGA
dictionary BGA {
    BGAHeader[] bga_header;   // 图片 ID 和文件名
    BGAEvent[]  bga_events;   // 图片序列
    BGAEvent[]  layer_events; // 叠加在 bga_notes 上的图片序列
    BGAEvent[]  poor_events;  // 未命中时的图片序列
}

// 图片文件
dictionary BGAHeader {
    unsigned long id; // 不言自明
    DOMString name;   // 图片文件名
}

// BGA 音符
dictionary BGAEvent {
    unsigned long y;  // 脉冲编号
    unsigned long id; // 对应 BGAHeader.id
}
```
