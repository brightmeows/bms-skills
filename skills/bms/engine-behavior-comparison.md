# beatoraja / LR2 运行时行为对比

> 来源：[【発狂PMS】beatoraja/LR2仕様比較まとめ (beatoraja0.8.7対応) - 発Pでハッピー](https://ralba-gear.hateblo.jp/entry/2023/11/06/141139)
> 本文对比 beatoraja 与 LR2 在血量槽、判定、LN 等引擎层面的运行时行为差异。数据主要针对 PMS（9keys）场景。

---

## 一、血量槽（Gauge）规格

### 符号说明

- PGREAT / GREAT / GOOD 为增加量
- BAD / POOR / 空POOR 为减少量
- `a` = 增加率 = TOTAL 值 / Note 总数
- beatoraja HARD/EXHARD 的**增加量 A** 计算方式：

```text
A = min(0.15, max(0, (2 × TOTAL − 320) / ノーツ数))
```

- LR2 HARD/EXHARD 的**减量补正 B** 计算方式：
  - Note 数 > 1000 时，按 TOTAL 值查表：

| TOTAL | ≥240 | ≥230 | ≥210 | ≥200 | ≥180 | ≥160 | ≥150 | ≥130 | ≥120 | <120 |
|-------|------|------|------|------|------|------|------|------|------|------|
| 补正  | 1.0  | 1.11 | 1.25 | 1.5  | 1.666 | 2.0 | 2.5  | 3.333 | 5.0  | 10.0 |

### 1.1 EASY 模式

| 判定 | beatoraja 7keys | beatoraja 9keys | LR2 |
|------|----------------|----------------|-----|
| PGREAT | a | a | 1.2a |
| GREAT | a | a | 1.2a |
| GOOD | 0.5a | 0.5a | 0.6a |
| BAD | −1.5 | −1.0 | −3.2 |
| POOR | −4.5 | −3.0 | −4.8 |
| 空POOR | −1.0 | −3.0 | −1.6 |

### 1.2 NORMAL 模式

| 判定 | beatoraja 7keys | beatoraja 9keys | LR2 |
|------|----------------|----------------|-----|
| PGREAT | a | a | a |
| GREAT | a | a | a |
| GOOD | 0.5a | 0.5a | 0.6a |
| BAD | −3.0 | −2.0 | −4.0 |
| POOR | −6.0 | −6.0 | −6.0 |
| 空POOR | −2.0 | −6.0 | −2.0 |

### 1.3 HARD 模式

| 判定 | beatoraja 7keys | beatoraja 9keys | LR2 |
|------|----------------|----------------|-----|
| PGREAT | A | A | 0.1 |
| GREAT | 0.8A | 0.8A | 0.1 |
| GOOD | 0.2A | 0.2A | 0.05 |
| BAD | −5.0 | −5.0 | −6.0B |
| POOR | −10.0 | −10.0 | −10.0B |
| 空POOR | −5.0 | −10.0 | −2.0B |

**剩余血量对减少量的补正：**

| 剩余血量 | 50 | 40 | 30 | 20 | 10 |
|----------|----|----|----|----|----|
| beatoraja 7keys | 0.8 | 0.7 | 0.6 | 0.5 | 0.4 |
| beatoraja 9keys | 0.8 | 0.7 | 0.6 | 0.5 | 0.4 |
| LR2 | 1.0 | 1.0 | 0.6 | 0.6 | 0.6 |

### 1.4 EXHARD 模式

| 判定 | beatoraja 7keys | beatoraja 9keys | LR2oraja |
|------|----------------|----------------|----------|
| PGREAT | A | A | 0.1 |
| GREAT | 0.4A | 0.4A | 0.1 |
| GOOD | 0 | 0 | 0.05 |
| BAD | −8.0 | −10.0 | −12.0B |
| POOR | −16.0 | −15.0 | −20.0B |
| 空POOR | −8.0 | −15.0 | −2.0B |

> EXHARD 无剩余血量补正。

### 1.5 血量总量·边界·初始值

| 项目 | beatoraja 7keys | beatoraja 9keys | LR2 |
|------|----------------|----------------|-----|
| 最小值 | 2 | 2 | 2 |
| 最大值 | 100 | 120 | 100 |
| 初始值 | 20 | 30 | 20 |
| 清除边界 | 80 | 85 | 80 |

- 9keys 的血量总量为 120，初始值为 30，清除边界为 85（≈70.83%），接近本家 pop'n music 的内部值 724/1024（≈70.70%）。
- 清除所需的净增加量：LR2 为 60（80−20），9keys 为 55（85−30），差距不大。

### 1.6 段位认定血量

| 判定 | PGREAT | GREAT | GOOD | BAD | POOR | 空POOR |
|------|--------|-------|------|-----|------|--------|
| LR2 / beatoraja 段位 | 0.1 | 0.1 | 0.05 | −2.0 | −3.0 | −2.0 |

**剩余血量补正：** 50→1.0, 40→1.0, 30→0.6, 20→0.6, 10→0.6

---

## 二、判定宽度对比

> beatoraja 的判定宽度通过 **JUDGERANK** 控制（EASY 为基准 `100%`），不同模式有不同的补正系数。
> LR2 数值为**垂直同步关闭**时的参考值。

### 2.1 VERY EASY

| 判定 | beatoraja 7keys | beatoraja 9keys | LR2oraja |
|------|----------------|----------------|----------|
| PGREAT | ±25ms | ±20ms | ±18ms（补正为 NORMAL） |
| GREAT | ±75ms | ±66ms | ±40ms |
| GOOD | ±187ms | ±155ms | ±100ms |
| BAD | +275~−350ms | ±183ms | ±200ms |
| 空POOR | +500~−150ms | +500~−175ms | +1000~0ms |

### 2.2 EASY

| 判定 | beatoraja 7keys | beatoraja 9keys | LR2 |
|------|----------------|----------------|-----|
| PGREAT | ±20ms | ±20ms | ±21ms |
| GREAT | ±60ms | ±50ms | ±60ms |
| GOOD | ±150ms | ±117ms | ±120ms |
| BAD | +220~−280ms | ±183ms | ±200ms |
| 空POOR | +500~−150ms | +500~−175ms | +1000~0ms |

### 2.3 NORMAL

| 判定 | beatoraja 7keys | beatoraja 9keys | LR2 |
|------|----------------|----------------|-----|
| PGREAT | ±15ms | ±20ms | ±18ms |
| GREAT | ±45ms | ±35ms | ±40ms |
| GOOD | ±112ms | ±81ms | ±100ms |
| BAD | +165~−210ms | ±183ms | ±200ms |
| 空POOR | +500~−150ms | +500~−175ms | +1000~0ms |

### 2.4 HARD

| 判定 | beatoraja 7keys | beatoraja 9keys | LR2 |
|------|----------------|----------------|-----|
| PGREAT | ±10ms | ±20ms | ±15ms |
| GREAT | ±30ms | ±25ms | ±30ms |
| GOOD | ±75ms | ±58ms | ±60ms |
| BAD | +110~−140ms | ±183ms | ±200ms |
| 空POOR | +500~−150ms | +500~−175ms | +1000~0ms |

### 2.5 VERY HARD

| 判定 | beatoraja 7keys | beatoraja 9keys | LR2 |
|------|----------------|----------------|-----|
| PGREAT | ±5ms | ±20ms | ±8ms |
| GREAT | ±15ms | ±16ms | ±24ms |
| GOOD | ±37ms | ±38ms | ±40ms |
| BAD | +55~−70ms | ±183ms | ±200ms |
| 空POOR | +500~−150ms | +500~−175ms | +1000~0ms |

### 2.6 JUDGERANK 补正系数

**beatoraja 7keys** 各档位的补正系数（以 EASY=100% 为基准）：

| 判定档位 | VERY EASY | EASY | NORMAL | HARD | VERY HARD |
|----------|-----------|------|--------|------|-----------|
| 补正系数 | 125% | 100% | 75% | 50% | 25% |

**beatoraja 9keys** 的补正规则不同——PGREAT、BAD、POOR **不参与 JUDGERANK 补正**，数值固定不变。

| 判定档位 | VERY EASY | EASY | NORMAL | HARD | VERY HARD |
|----------|-----------|------|--------|------|-----------|
| 补正系数 | 133% | 100% | 70% | 50% | 33% |

> 9keys VERY HARD 下，GREAT 判定宽度（±16ms）小于 PGREAT（±20ms），即**不存在 GREAT 判定**（PGREAT 直接跳 GOOD），称为"グドバド判定"。

---

## 三、LN（长音）判定

### 3.1 终点判定宽度

| 实现 | 终点判定宽度 |
|------|-------------|
| beatoraja **9keys** | 全判定 = 对应 Note 判定宽度 **±100ms 扩展** |
| beatoraja **7keys** | PGREAT/GREAT ±100ms 扩展；GOOD ±50ms 扩展；BAD = 同普通 Note |
| **LR2** | 终点宽度 = 普通 Note 的 **GOOD 宽度**。在此范围内离键，获得与**始点相同**的判定 |

### 3.2 判定规则

- beatoraja LN 模式：始点和终点判定中**取较差的一方**
- 过早离键（早すぎ）：beatoraja 判为**早 POOR**（非空 POOR）；LR2 判为**早 BAD**

### 3.3 beatoraja 0.8.7+ 押し直し（Re-press）机制

- LN 按住期间意外离键时，有 **200ms** 的宽容时间（BPM150 下相当于 8 分音符间隔）
- 在此时间内**重新按住**即可恢复按压状态，视为未离键
- 一个 LN 内可以**多次押し直し**
- 7keys 等模式无此功能（宽容时间 = 0ms）
- 注意：即使尚在宽容时间内，若 LN 终点到达且实际已离键，仍以终点判定为准

### 3.4 0.8.6 及之前已修正的问题

- LN 始点不产生空 POOR
- 过早离键后不保证 BAD 以上判定，可能判为见逃し POOR
- LN 终点未按到判定端时终点判定被优先
- CN/HCN 某些条件下不执行终点判定导致无法完奏

---

## 四、`#DEFEXRANK` 与 JUDGERANK 的关系

### 4.1 `#DEFEXRANK` 的基准

- `#DEFEXRANK` 以 **NORMAL 判定**为基准（即值 `100` = NORMAL）
- JUDGERANK（游戏中可选）以 **EASY 判定**为基准
- 两者不同。例如 9keys 下 `#DEFEXRANK 100` 等效于 JUDGERANK `70%`（NORMAL）

### 4.2 9keys 中 BAD 判定不受补正

- beatoraja 9keys 中，BAD 判定宽度**不受 `#DEFEXRANK` 影响**，固定为 ±183ms
- 因此 GOOD 判定宽度不能超过 BAD 宽度（±183ms）
- 当 `#DEFEXRANK ≥ 523` 时，GOOD 判定达到 BAD 边界后消失，GREAT 判定也随之达到 BAD 边界
- PGREAT 也不受 `#DEFEXRANK` 补正，继续增大值不再有任何变化
- 7keys 等模式的 BAD 判定受 `#DEFEXRANK` 补正，可将判定扩展到空 POOR 范围

---

## 五、其他运行时行为差异

### 5.1 空 POOR

- LR2 / 7keys：一个 Note 可在判定范围内**多次**判空 POOR
- beatoraja **9keys**：一个 Note 最多判**一次**空 POOR（本家仕様準拠）

### 5.2 BAD ハマり（BAD 锁定）

- LR2 / 7keys：先出早 BAD，再按时判定已消失，下一个 Note 的 BAD 判定被连累——即 BAD ハマり
- beatoraja **9keys**：BAD 判定后**该 Note 的判定不消失**，可再次击打，因此不会发生 BAD ハマり（本家仕様準拠）

### 5.3 Combo 持続

- LR2 / 7keys：空 POOR 不影响 Combo 计数
- beatoraja **9keys**：空 POOR **中断 Combo**

### 5.4 S 乱（S-random）

- beatoraja **7keys**：S 乱时纵连密度限制为 **40ms**（不允许更密的纵连）
- beatoraja **9keys**：S 乱**无纵连密度限制**
- LR2：也无纵连密度限制

### 5.5 判定算法选择

beatoraja 提供三种判定算法（处理同一轨道上两个 Note 同时靠近判定线时的优先级）：

| 算法 | 行为 | 说明 |
|------|------|------|
| **Combo 优先** | 优先取 GOOD 以上可判定的 Note；均可取时取下方 Note | 默认，接近 LR2 |
| **Score 优先** | 优先取 GREAT 以上可判定的 Note；均可取时取下方 Note | 接近某寺游戏 |
| **最下 Note 优先** | 无条件取下方 Note | 可能发生迟 BAD ハマり |
