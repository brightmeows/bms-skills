# BMS Scroll ギミック 制作指南

> **来源**：[BMS Scrollギミックの作り方 - numuto (note.com)](https://note.com/numuther/n/n57bf895e7969)
> 本文是 BMS 谱面滚动特效（Gimmick）的实践教程，涵盖 Scroll / BPM / Stop 三类基础命令的组合用法。
>
> **引用约定**：标注 `→ 详见 [xxx.md](xxx.md)` 的内容表示该命令或概念已在现有文档中定义，本文侧重其实战组合用法，不重复格式细节。

---

## 一、三种基础 Gimmick

BMS 的 Gimmick 类命令可分为三大类：

| 类别 | 作用 | 引用 |
|------|------|------|
| **Scroll** | 改变谱面滚动**宽度**（≠ 速度变化） | [scroll-speed-extensions.md](scroll-speed-extensions.md) |
| **BPM** | 改变曲子的**流速** | [memo/09-header-commands-audio-and-bpm.md](memo/09-header-commands-audio-and-bpm.md) |
| **Stop** | 在指定时间内**暂停**谱面滚动 | [memo/09-header-commands-audio-and-bpm.md](memo/09-header-commands-audio-and-bpm.md) |

> **Scroll ≠ 速度**：这是最重要的概念。Scroll 改变的是谱面流过的**宽度**，时间不变，所以看起来像加速/减速。

### 1.1 加速·减速

#### Scroll 方式

Scroll 改变宽度而不改变时间，因此无需移动 Note 位置。

```bms
#SCROLL01 1
#SCROLL02 0.5
#SCROLL03 0.25
#SCROLL04 0.5
#SCROLL05 1
#SCROLL06 2
#SCROLL07 3
#SCROLL08 2
```

→ 详见 [scroll-speed-extensions.md](scroll-speed-extensions.md)

#### BPM 方式

BPM 改变流速，因此加速/减速后需压缩或扩展小节长度来补偿，否则音乐会变快/变慢。

```bms
#BPM01 150
#BPM02 75
#BPM03 37.5
#BPM04 75
#BPM05 150
#BPM06 300
#BPM07 450
#BPM08 300
```

→ 详见 [memo/09-header-commands-audio-and-bpm.md](memo/09-header-commands-audio-and-bpm.md)

#### Stop 方式

Stop 通过超高速连续暂停模拟减速。基准 BPM 下 1 小节 = 192 stop 单位。

- 1/2 倍速：每小节的 stop 值设为 96（即 192/2），剩余 96 用 `#xxx02` 压缩
- 1/4 倍速：stop 值设为 48（192/4），剩余 144 压缩
- 2 倍速：stop 值设为 **-192**（负值），剩余 384 扩展 → 需文本编辑器直接编辑 BMS 文件，因 BMSE 等不支持输入负 stop

```bms
#STOP01 96     ; 1/2 倍
#STOP02 -192    ; 2 倍（需手写）
```

→ 详见 [memo/09-header-commands-audio-and-bpm.md](memo/09-header-commands-audio-and-bpm.md)

---

## 二、ワープ（Warp）

Warp 是指让 Note 瞬间出现在判定线上或消失的特效。

### 2.1 Scroll Warp

让两个 Note 之间的 scroll 宽度极大化，产生瞬间移动效果。

```bms
#SCROLL01 100001
#001SC:01
```

→ 详见 [scroll-speed-extensions.md](scroll-speed-extensions.md)

### 2.2 BPM Warp（100001 倍技术）

使用 **100001 倍** BPM 使 Note 瞬间移动到目标位置。

> 为什么是 100001 而不是 100000？因为在 LR2 上，100001 倍时**显示 BPM 不变**（不显示为极值），这是一个实用技巧。

```bms
#BPM01 100001
#00108:01
```

配合 Stop 补偿：

```bms
#STOP01 30000    ; 100001 倍 BPM 下 3/1920 小节的补偿量
```

### 2.3 Stop Warp

理论上可用巨大负 Stop 实现 Warp，但仅限 LR2 且不推荐。

> 在 BPM 不变的前提下用 Stop Warp：`stop = 192 - 100001 × 192 = -19200000`

---

## 三、アニメーション（Animation）

Animation 的本质是**逐帧动画（帕拉帕拉漫画）**，通过 Warp + 分帧实现。

### 3.1 BPM 逐帧动画

基本步骤：

1. 确定动画 fps
2. 将一小节等分为 N 份
3. 每份内放置一帧的画面（用地雷或不可见 Note）
4. 使用 100001 倍 BPM 在帧间 Warp

**fps 计算示例（BPM=150）：**

| 分割数 | 每帧时间 | fps |
|--------|----------|-----|
| 32 分割 | 240/150/32 = 0.05s | 20fps |
| 64 分割 | 240/150/64 = 0.025s | 40fps |
| 96 分割 | 240/150/96 ≈ 0.0167s | 60fps |

Warp 间隔为 1 小节时，64 分割的 Stop 值：

```text
stop = 192 × 100001 ÷ 64 − 192 = 299811
```

### 3.2 Scroll 动画

原则上不推荐用 Scroll 做动画（LR2 不支持），但 scroll=0.0 可用于冻结画面。

→ 详见 [scroll-speed-extensions.md](scroll-speed-extensions.md) 中 SCROLL=0 的说明

---

## 四、Scroll Gimmick 高阶技巧

以下技巧依赖 beatoraja 对 `#SCROLL` 的支持。

### 4.1 空中停止（Mid-air Stop）

让 Note 悬停在判定线上方，直到敲击瞬间才移动到判定线。

原理：

1. `scroll=0.0` 使谱面**宽度为零**，Note 看似悬空停止
2. 敲击前瞬间插入大 Scroll 值让 Note **瞬间移动**到判定线
3. 移动后**反向补偿**使后续 Note 位置恢复正常

**示例**：在 1/16、1/8、3/16、1/4 小節位置放置空中停止 Note

```bms
; 120/1920 小節份的瞬間移動
#SCROLL01 120
#SCROLL02 -120    ; 复原
#SCROLL03 240     ; 240/1920 小節份
#SCROLL04 -240    ; 复原
; ...
```

> 要点：每个 scroll 变化后必须用负值补偿，否则后续 Note 位置偏移。

### 4.2 逆走（Reverse Scroll）

谱面从下往上滚动（逆向）。

**简单负 scroll 的问题**：`scroll=-1.0` 使 Note 从判定线下方出现，极难游玩。

**解决方案**：

1. 逆走一段距离后（如 5/16 小節）
2. 瞬间移动到判定线位置
3. 再用正 scroll 继续

```bms
; 推荐绿数字 300 时，BPM150 下 5/16 小節逆走
#SCROLL01 -1
#001SC:01
; ...5/16 小節后...
#SCROLL02 10000    ; 瞬间回到判定线
```

> 建议在谱面信息中标注推荐绿数字。

### 4.3 Scroll 出现/消失 Bug（beatoraja 特定）

当 scroll 绝对值超过某个阈值时，Note 在 beatoraja 上变得**不可见**。

- 现象：`#SCROLL` 值设为 1561 时，Note 消失
- 原因：beatoraja 渲染引擎对过大 scroll 值的处理方式
- 影响因素：绿数字、HS（Hi-Speed）、SUD（Sudden）等显示设置
- 应用：可控的渐进消失/出现效果

**渐进出现**：使用 scroll=0.0625 等微小正值替代 scroll=0.0，可获得平滑出现的视觉效果。

> 此行为无固定公式，需根据目标绿数字/SUD 手动调整阈值。

### 4.4 非动画具現化（Non-animation Manifestation）

使地雷瞬间变为实 Note（或反之），**不依赖逐帧动画**。

原理：

1. 实 Note 与地雷 Note 在空间上**分离放置**
2. 通过 scroll 变化使两者在判定线上**重叠**
3. Note 重叠时，**后方的 Note 显示在前方之上**

**地雷→实 Note**：地雷放在前面（后方），实 Note 放在后面（前方）
**实 Note→地雷**：实 Note 放在前面（后方），地雷放在后面（前方）

```bms
; 地雷→实 Note 具現化
; 地雷后于实 Note 240/1920 小節，scroll 补偿 -238
; (因为 2/1920 小節用于执行 scroll 命令本身)
```

> 效果受绿数字和 SUD 影响，建议在谱面信息中推荐设置。

### 4.5 部分ワープ（Partial Warp）

让 LN 或伪 LN 在滚动中**瞬间消失**。

原理：

1. 用 1920 分间隔的 scroll 将 LN 拉伸
2. 瞬间用 100001 倍 BPM 让 LN 到达终点
3. 配合负 scroll 补偿和 Stop 补偿

```bms
; 240/1920 小節 scroll 拉伸 LN
#SCROLL01 240
#SCROLL02 -238    ; 补偿（基准 scroll=1.0 时）
#STOP01 30000     ; 100001BPM × 3/1920 小節的补偿
```

### 4.6 マイナス Scroll アニメーション（Negative Scroll Animation）

将实 Note 区域与动画区域分离：

- **实 Note 区域**：scroll=-1，BPM=100001 倍，快速逆向播放
- **动画区域**：大量地雷/不可见 Note 以正常顺向滚动

效果：实 Note 结束时，大量动画从下方逆向涌入画面，形成复杂的视觉特效。

**关键点**：

- 动画帧顺序**与 BPM 动画相反**：第一帧放在最上方，最后一帧放在最下方
- 因为动画从下方逆向出现，最早出现的帧需最先放置

---

## 五、总结

Scroll Gimmick 的制作本质上是三类基础命令（Scroll / BPM / Stop）的组合与变奏。核心原则：

1. **Scroll 是宽度变化**，不是速度变化——无需移动 Note
2. **BPM 是速度变化**——需压缩/扩展小节补偿音乐时长
3. **Stop 是暂停**——负值可跳跃式前进
4. **Scroll=0.0** 可冻结 Note
5. **负 Scroll** 可逆走
6. **100001 倍 BPM** 是 warp/动画的常用技巧
7. **Scroll 大绝对值** 在 beatoraja 上可隐藏 Note
8. **每次变化后必须补偿**，否则后续 Note 位置偏移
