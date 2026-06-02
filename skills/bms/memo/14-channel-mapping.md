# 通道映射

## 通道分配表

| 通道号 | 名称 | 说明 | 来源 |
|--------|------|------|------|
| `00` | 永久空缺通道 | 某些实现中: 在 `#xxx` 开头变更 BPM (已删除) | - |
| `01` | BGM | `#WAVxx` (多行) | BM98 |
| `02` | `#xxx` 的长度 | (1 相当于 4/4 拍) // 指定整数或小数 | BM98 |
| `03` | BPM 变更 | BPM 1 « `[01-FF]` » BPM 255 | BM98 |
| `04` | BGA BASE | `#BMPxx` (BASE 显示在 LAYER 下方) | BM98 |
| `05` | Extended Object | `#ExtChr` (仅 BM98) | BM98 |
| `05` | SEEK Object | `#SEEKxx` `*n*` (仅 LR) | LR |
| `06` | BGA POOR | `#BMPxx` (出现 MISS 时显示 POOR) | BM98 |
| `07` | BGA LAYER | `#BMPxx` (LAYER 显示在 BASE 上方) | BM98k |
| `08` | exBPM | `#BPMxx` `*n*` \| `#EXBPMxx` `*n*` (实数) | bemaniaDX |
| `09` | STOP | `#STOPxx` `*n*` (1 相当于 192 分音符) | DDR |
| `0A` | BGA LAYER2 | `#BMPxx` (LAYER2 显示在 LAYER 上方) | nanasi |
| `0B` | BGA BASE 不透明度 | 透明 « `[01-FF]` » 不透明 | nanasi |
| `0C` | BGA LAYER 不透明度 | 透明 « `[01-FF]` » 不透明 | nanasi |
| `0D` | BGA LAYER2 不透明度 | 透明 « `[01-FF]` » 不透明 | nanasi |
| `0E` | BGA POOR 不透明度 | 透明 « `[01-FF]` » 不透明 | nanasi |
| `11-1Z` | 1P Visible | { BM98: 11-17, FlashTerminal: 18-19, MGQ: 1A-1F, pomu: 1G-1Z } | BM98/FT/MGQ/pomu |
| `21-2Z` | 2P Visible | { BM98: 21-27, FlashTerminal: 28-29, MGQ: 2A-2F, pomu: 2G-2Z } | BM98/FT/MGQ/pomu |
| `31-3Z` | 1P Invisible | { BM98: 31-36, FlashTerminal: 38-39, MGQ: 3A-3F, pomu: 3G-3Z } | BM98/FT/MGQ/pomu |
| `41-4Z` | 2P Invisible | { BM98: 41-46, FlashTerminal: 48-49, MGQ: 4A-4F, pomu: 4G-4Z } | BM98/FT/MGQ/pomu |
| `51-5Z` | 1P Longnote | { MGQ: 51-5F, pomu: 5G-5Z } | MGQ/pomu |
| `61-6Z` | 2P Longnote | { MGQ: 61-6F, pomu: 6G-6Z } | MGQ/pomu |
| `70-96` | 预留通道 | [ `10`, `20`, `30`, `40`, `50`, `60` ] 也是预留通道 | - |
| `97` | BGM volume | 最小 1 « `[01-FF]` » 最大 255 (最大相当于原声音量) // 仅 fgt | forgetalia |
| `98` | KEY volume | 最小 1 « `[01-FF]` » 最大 255 (最大相当于原声音量) // 仅 fgt | forgetalia |
| `99` | TEXT | `#TEXTxx` `"*string*"` | pomu |
| `A0` | JUDGE | `#EXRANKxx` `*n*` (100 相当于 NORMAL 判定 // 指定整数或小数) | nanasi |
| `A1` | BGA BASE aRGB | `#ARGBxx` `*a,r,g,b*` (各 `[0-255]`) | nanasi |
| `A2` | BGA LAYER aRGB | `#ARGBxx` | nanasi |
| `A3` | BGA LAYER2 aRGB | `#ARGBxx` | nanasi |
| `A4` | BGA POOR aRGB | `#ARGBxx` | nanasi |
| `A5` | BGA KEYBOUND | `#SWBGAxx` | nanasi |
| `A6` | OPTION | `#CHANGEOPTIONxx` (多行) | nanasi |
| `D1-D9` | 1P Landmine (地雷) | `#WAV00` // [01-ZZ] 指定伤害 (decimalize/2) // ZZ 即死 | nanasi |
| `E1-E9` | 2P Landmine (地雷) | `#WAV00` // [01-ZZ] 指定伤害 (decimalize/2) // ZZ 即死 | nanasi |

## 按键分配表

| 格式 | 可视对象通道映射 |
|------|-----------------|
| **BMS** (5K) | 1P: KEY1=11, KEY2=12, KEY3=13, KEY4=14, KEY5=15, SCRATCH=16, (FREE ZONE)=17 |
| | 2P: KEY1=21, KEY2=22, KEY3=23, KEY4=24, KEY5=25, SCRATCH=26, (FREE ZONE)=27 |
| **BMS (nanasi 踏板)** | 1P: KEY1=11, KEY2=12, KEY3=13, KEY4=14, KEY5=15, SCRATCH=16, FOOT PEDAL=`#xxx17` |
| | 2P: KEY1=21, KEY2=22, KEY3=23, KEY4=24, KEY5=25, SCRATCH=26, FOOT PEDAL=`#xxx27` |
| **BMS (Angolmois 踏板)** | 1P: SCRATCH=16, KEY1=11, KEY2=12, KEY3=13, KEY4=14, KEY5=15, FOOT PEDAL=`#xxx17` |
| | 2P: KEY1=21, KEY2=22, KEY3=23, KEY4=24, KEY5=25, SCRATCH=26, FOOT PEDAL=`#xxx27` |
| **纵连打测定器** (Taterenda) | SCRATCH=?, KEY1=?, KEY2=? |
| **DDR Arrow 6** | ←=11, ＼=12, ↓=13, ↑=14, ／=15, →=16 |
| **DDR Arrow 4/8** | 1P: ←=11, ↓=13, ↑=15, →=16 |
| | 2P: ←=21, ↓=23, ↑=25, →=26 |
| **Project2DX** | SCRATCH=16, KEY1=11, KEY2=12, KEY3=13, KEY4=14, KEY5=15, KEY6=21, KEY7=22 |
| **BME** (7K) | 1P: SCRATCH=16, KEY1=11, KEY2=12, KEY3=13, KEY4=14, KEY5=15, KEY6=18, KEY7=19 |
| | 2P: KEY1=21, KEY2=22, KEY3=23, KEY4=24, KEY5=25, KEY6=28, KEY7=29, SCRATCH=26 |
| **BME (nanasi 踏板)** | SCRATCH=16, KEY1=11, KEY2=12, KEY3=13, KEY4=14, KEY5=15, KEY6=18, KEY7=19, FOOT PEDAL=`#xxx17` |
| **BME (Angolmois 踏板)** | 1P: SCRATCH=16, KEY1=11, KEY2=12, KEY3=13, KEY4=14, KEY5=15, KEY6=18, KEY7=19, FOOT PEDAL=`#xxx17` |
| | 2P: KEY1=21, KEY2=22, KEY3=23, KEY4=24, KEY5=25, KEY8=28, KEY9=29, SCRATCH=26, FOOT PEDAL=`#xxx27` |
| **DSC/FPP** (双 Scratch) | SCRATCH=16, KEY1=11, KEY2=12, KEY3=13, KEY4=14, KEY5=15, KEY6=18, KEY7=19, SCRATCH=26 |
| **OCT/FP** (Octave 模式/踏板) | SCRATCH=16, KEY1=11, KEY2=12, KEY3=13, KEY4=14, KEY5=15, KEY6=18, KEY7=19, KEY8=22, KEY9=23, KEY10=24, KEY11=25, KEY12=28, KEY13=29, SCRATCH=26, FOOT PEDAL=`#xxx21` |
| **PMS** (9K) | KEY1=11, KEY2=12, KEY3=13, KEY4=14, KEY5=15, KEY6=22, KEY7=23, KEY8=24, KEY9=25 |
| **PMS (BME 型)** (9K) | 1P: KEY1=11, KEY2=12, KEY3=13, KEY4=14, KEY5=15, KEY6=18, KEY7=19, KEY8=16, KEY9=17 |
| | 2P: KEY1=21, KEY2=22, KEY3=23, KEY4=24, KEY5=25, KEY6=28, KEY7=29, KEY8=26, KEY9=27 |
| **PMS (5 键)** | KEY3=13, KEY4=14, KEY5=15, KEY6=22, KEY7=23 (KEY1, KEY2, KEY8, KEY9 未使用) |
| **PMS (pomu-battle)** (3K) | KEY1=11, KEY2=12, KEY3=13 |
| **O2mania (?)** | KEY1=16, KEY2=11, KEY3=12, KEY4=13, KEY5=14, KEY6=15, KEY7=18, (KEY8)=19 |
| **MyO2 (?)** | KEY1=16, KEY2=11, KEY3=12, KEY4=13, (KEY5)=14, (KEY6)=15, (KEY7)=18, (KEY8)=19 |

### 未调查

- GDA 9btn (feeling Pomu second)
- 16panel (D3beat)
