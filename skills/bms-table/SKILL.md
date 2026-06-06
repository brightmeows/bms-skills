---
name: bms-table
description: 处理 BMS 难度表（難易度表）、实现解析器/生成器、查询 JSON 字段定义及类型、或排查 beatoraja/GLAssist/LR2IR 难度表兼容性问题时使用。
license: Apache-2.0
---

# BMS 难度表参考

难度表（難易度表）是 BMS 生态中用于组织谱面难度分级的标准化格式，由 HTML + 头部 JSON + 数据 JSON 三文件构成。

> 来源：[難易度表の仕様 · exch-bms2/jbmstable](https://github.com/exch-bms2/jbmstable-parser/wiki/%E9%9B%A3%E6%98%93%E5%BA%A6%E8%A1%A8%E3%81%AE%E4%BB%95%E6%A7%98)
> 来源：[第2通常難易度表：難易度表導入支援ツール](https://bmsnormal2.syuriken.jp/bms_dtmanager.html)

## 声明

所有引用路径均相对于本文件所在目录。

---

## 使用方式

本文档是 BMS 难度表（難易度表）格式的规范参考。结构如下：

### 目录导航

- **正文**：难度表格式的完整规范定义，包括三文件结构、头部 JSON 与数据 JSON 的字段说明。
- **ext/**：补充参考材料，包括实现指南、创建流程、生态概览。

### 建议阅读路径

- **解析器/播放器开发者**：按顺序阅读 全体构成 → 头部 JSON → 数据 JSON → 编码与兼容性，再按需查阅 ext/implementation.md。
- **谱面作者/表管理者**：先读 全体构成 → 头部 JSON → 数据 JSON 了解格式，再查阅 ext/creation-guide.md。
- **工具/转换器开发者**：全文通读 + ext/ 全部参考文件。

---

## 全体构成

难度表由以下 3 个文件组成：

| 文件 | 说明 |
|------|------|
| 难度表 HTML | 用户浏览用，不含谱面数据 |
| 头部（ヘッダ部，JSON） | 表名称、符号等头部元数据 |
| 数据部（データ部，JSON） | 谱面条目列表 |

HTML 文件在 `<head>` 中通过 meta 标签引用头部 JSON：

```html
<meta name="bmstable" content="（头部JSON地址）" />
```

---

## 头部（ヘッダ部，Header JSON）

顶层为单个对象 `{...}`，包含以下字段：

### 必须字段（Header）

| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | String | 表名称（可用于文件名生成） |
| `data_url` | String | 数据部（データ部）JSON 的 URL |
| `symbol` | String | 表符号（如 `▼` `▽`） |

### 可选字段（Header）

| 字段 | 类型 | 说明 |
|------|------|------|
| `tag` | String | 标签标记文字。省略时使用 `symbol` |
| `level_order` | Array（String\|Integer） | 等级顺序列表。省略时按检测顺序 |
| `course` | Array（Course）\|Array（Array（Course）） | 段位认定信息。支持扁平或嵌套数组，空值为 `[[]]` |
| `mode` | String | 谱面玩法模式。同 bmson 的 mode_hint |

### Course 对象

| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | String | 段位名称 |
| `constraint` | Array（String） | 段位约束（见下） |
| `trophy` | Array（Trophy） | 奖牌条件 |
| `charts` | Array（ChartInfo） | 谱面集合，缺 `level` 时默认为 `"0"` |
| `md5` | Array（String） | MD5 简写列表，自动转为 ChartItem（level 默认 `"0"`） |
| `sha256` | Array（String） | SHA256 简写列表，自动转为 ChartItem（level 默认 `"0"`） |

> `charts`、`md5`、`sha256` 三者合并为一个统一的谱面列表，合并顺序为 charts → md5 → sha256。

**constraint 可选值：**

| 值 | 说明 |
|----|------|
| `grade_random` | 允许 RANDOM / R-RANDOM / S-RANDOM / MIRROR |
| `grade_mirror` | 允许 MIRROR |
| `no_speed` | 禁用 HI-SPEED 设定 |
| `no_good` | GOOD 判定无效（转 BAD） |
| `no_great` | GREAT/GOOD 判定无效（转 BAD） |
| `gauge_lr2` | LR2 段位槽 |
| `gauge_5k` | 5KEY 用段位槽 |
| `gauge_7k` | 7KEY 用段位槽 |
| `gauge_9k` | 9KEY 用段位槽 |
| `gauge_24k` | 24KEY 用段位槽 |

### Trophy 对象

| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | String | 奖牌类型：`goldmedal` / `silvermedal` / `bronzemedal` |
| `missrate` | Float | 失误率上限（0–100%） |
| `scorerate` | Float | 得分率下限（0–100%） |

### 头部示例

```json
{
  "name": "第2発狂難易度",
  "tag": "▼",
  "data_url": "http://rattoto10.jounin.jp/js/insane_data.json",
  "symbol": "▼",
  "level_order": [
    "0-", "0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "10",
    "11", "12", "13", "14", "15", "16", "17", "18", "19", "20",
    "21", "22", "23", "24", "?"
  ],
  "course": [
    [
      {
        "name": "発狂初段",
        "constraint": ["grade_mirror"],
        "trophy": [
          { "name": "silvermedal", "missrate": 5.0, "scorerate": 70.0 },
          { "name": "goldmedal", "missrate": 2.5, "scorerate": 85.0 }
        ],
        "md5": [
          "c07125de4ed7fbe7cb066cc41e50e51e",
          "fc7d46e7bbc9f6afd26d05e3bf2ef555",
          "b3887714270e28988ce900e4b9300994",
          "d1877ad5dc0134b27eb0238da5721eed"
        ]
      }
    ]
  ]
}
```

---

## 数据部（データ部，Data JSON）

顶层为数组 `[...]`，每个元素为一张谱面的信息对象。

### 必须字段（Data）

| 字段 | 类型 | 说明 |
|------|------|------|
| `md5` | String | BMS 文件的 MD5 哈希值 |
| `level` | String | 难度等级标记 |

> `md5` 与 `sha256` 至少定义一个即可。仅知 MD5 时解析器应仍能正常工作。

### 推荐字段（Data）

| 字段 | 类型 | 说明 |
|------|------|------|
| `sha256` | String | BMS 文件的 SHA256 哈希值（推荐，BMSON 格式必须） |
| `title` | String | 谱面标题 |
| `artist` | String | 艺术家 |
| `url` | String | BMS 本体下载 URL |
| `url_diff` | String | BMS 差分下载 URL |

### 可选字段（Data）

| 字段 | 类型 | 说明 |
|------|------|------|
| `name_diff` | String | 差分链接显示文字 |
| `url_pack` | String | 收录包下载 URL |
| `name_pack` | String | 收录包名称 |
| `comment` | String | 备注 |
| `org_md5` | String | 附属谱面（同梱譜面）的 MD5（用于自动差分导入） |
| `mode` | String | 谱面玩法模式。未定义时使用 header 的 mode |
| `ipfs` | String | BMS 本体的 IPFS path（须为目录） |
| `ipfs_diff` | String | BMS 差分的 IPFS path（文件或目录均可） |
| `lr2_bmsid` | String | LR2IR 上的 bmsid（第2通常表特有） |

此外可包含难度表自定义字段（如 `total`、`judge`、`note`、`tag`、`eval` 等）。

### 数据部示例

```json
[
  {
    "title": "Act Beloved [SP ★1]",
    "level": "12",
    "eval": 0,
    "artist": "Nene Anegasaki feat. Salita / obj:slash Movie : Ryuka",
    "url": "http://manbow.nothing.sh/event/event.cgi?action=More_def&num=268&event=74",
    "url_pack": "http://nekokan.dyndns.info/~lobsak/genoside/",
    "name_pack": "GENOSIDE収録",
    "url_diff": "http://slash24th.web.fc2.com/",
    "name_diff": "差分保管所",
    "comment": "slash難易度表△1",
    "total": 480.0,
    "judge": 3,
    "note": "（省略）",
    "tag": "同時押し",
    "md5": "6a2cf3b81a38dc28f2a5b4e0d18d26ff"
  }
]
```

---

## 编码与兼容性

- 头部（ヘッダ部）与数据部（データ部）**必须使用 UTF-8** 编码。
- Stairway 及 IRmemoPE WL 暂不支持本格式（截至原文档更新时）。
- **面向 Stairway 集计对象的难度表**：兼容完成前需保留传统格式 `var mname=[...]`。

## 差分自动导入

在数据条目中同时定义以下两个字段可启用 GLAssist 的自动差分导入：

| 字段 | 说明 |
|------|------|
| `url_diff` | 可直接链接下载的 BMS 差分 URL |
| `org_md5` | 附属谱面（同梱譜面）的 MD5 哈希值 |

## 注意事项

- 难度表 HTML 文件不含谱面数据，仅用于用户浏览。
- 难度表可定义自定义字段（如 `total`、`judge`、`note` 等），解析器应对未知字段保持宽容。
- `level_order` 支持字符串与整数混合类型（如 `["0-", 1, 2, 3, "11+", "12+"]`）。

---

## ext/ —— 补充参考

| 文件 | 说明 |
|------|------|
| [implementation.md](./ext/implementation.md) | 实现参考：Rust/Java 解析库、解析/生成设计要点 |
| [creation-guide.md](./ext/creation-guide.md) | 创建流程：从零搭建难度表的步骤与工具 |
| [ecosystem.md](./ext/ecosystem.md) | 生态概览：常见难度表、支持工具、段位体系 |
