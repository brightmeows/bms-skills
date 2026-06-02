# 命令使用趋势

> 来源：<https://hitkey.nekokan.dyndns.info/cmdsJP.htm#TRENDS-IN-USE-OF-COMMANDS>

## 主流扩展

| command line | remarks | origin |
| --- | --- | --- |
| **channel** | | |
| `#xxx07` | BGA-LAYER | BM98k |
| `#xxx08` | BPM 变更 « `#BPMxx n` | bemaniaDX |
| `#xxx09` | STOP 序列 « `#STOPxx n` | DDR |
| `#xxx51-59` | 1P-side LN (RDM 记法) « `#LNTYPE 1` (O2mania 以外可省略) | MGQ |
| `#xxx61-69` | 2P-side LN (RDM 记法) « `#LNTYPE 1` (O2mania 以外可省略) | MGQ |
| **header** | | |
| `#BPMxx n` | 实数 BPM 定义 » `#xxx08` | bemaniaDX |
| `#STOPxx n` | STOP 序列定义 (1 相当于 4/4 拍小节的 1/192) » `#xxx09` | DDR |
| `#LNOBJ xx` | LN 终点符号定义 (RDM-type #2) « `#WAVxx filename` | RDM |
| `#WAV[01-ZZ] filename` | 定义编号的 36 进制化 | bemaniaDX |
| `#BMP[00-ZZ] filename` | 定义编号的 36 进制化 | nanasi |
| `#WAVxx oggfilename` | `#WAVxx` 的 Ogg 定义，仅扩展名不同的同名文件替代搜索 | DDR |
| `#BMPxx videofilename` | `#BMPxx` 的视频定义，排除文件扩展名的替代搜索 | LR |
| `#SUBTITLE string` | 注：此命令不同于“隐式副标题(bemaniaDX 扩展)” | nanasi |
| `#SUBARTIST string` | 音源切片制作、视频制作、谱师等 | LR |
| `#DIFFICULTY [1-5]` | 谱面难度类型 | nanasi |
| `#STAGEFILE imagefilename` | 加载时的启动画面 | BM98k |
| `#BANNER imagefilename` | 横幅显示 | nanasi |

## 偶尔使用的扩展

| command line | remarks | origin |
| --- | --- | --- |
| **channel** | | |
| `#xxx99` | 文本变更 « `TEXTxx "string"` | pomu |
| `#xxxA0` | 判定变更 « `#EXRANKxx n` | nanasi |
| `#xxxD1-D9` | 1P-side 地雷 « `#WAV00 soundfilename` | nanasi |
| `#xxxE1-E9` | 2P-side 地雷 « `#WAV00 soundfilename` | nanasi |
| **header** | | |
| `#WAV00 soundfilename` | 地雷爆炸音的定义 » `#xxxD1-E9` | nanasi |
| `#BACKBMP imagefilename` | 谱面背景图像的定义 | DDR |
| `#EXRANKxx n` | 判定宽度的定义 » `#xxxA0` | nanasi |
| `#CHARFILE charfilename` | 仿照 pop'n music 的角色 | pomu |
| `#COMMENT "string"` | 在选曲列表中显示的文本 | pomu |
| `#TEXTxx "string"` | 游戏中显示的文本 | pomu |
| `#LNTYPE 1` | RDM 记法的 LN (O2mania 必须声明) » `#xxx51-69` | RDM |
| `%URL string` | BMSC 自动添加 | BMSManager |
| `%EMAIL string` | BMSC 自动添加 | BMSManager |

## 测试游玩时有用的扩展

| command line | remarks | origin |
| --- | --- | --- |
| **header** | | |
| `#PATH_WAV path` | 定义文件引用目录的路径 | GDAC2 → BMEV |
| `#SETRANDOM n` | 生成常量 | pomu2 |
| `#SETSWITCH n` | 生成常量 | nanasi |
