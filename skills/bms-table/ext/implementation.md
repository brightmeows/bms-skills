# 实现参考

> 来源：[bms_table - crates.io: Rust Package Registry](https://crates.io/crates/bms-table)
> 来源：[exch-bms2/jbmstable-parser](https://github.com/exch-bms2/jbmstable-parser)

## bms_table（Rust）

社区实现的 Rust 解析库，提供完整的数据结构映射和网络获取能力。关键设计要点：

- `BmsTableHeader` 通过 `extra` 字段保留未识别的 JSON 字段，实现前向兼容。
- Course 中的 `md5`/`sha256` 列表自动转为 `ChartItem`，缺失的 `level` 以 `"0"` 填充。
- 支持一键式网络获取：网页 → 头部 JSON → 数据 JSON。

```rust
use bms_table::fetch::reqwest::Fetcher;
let fetcher = Fetcher::lenient()?;
let table = fetcher.fetch_table("https://stellabms.xyz/sl/table.html").await?.table;
println!("{}: {} charts", table.header.name, table.data.charts.len());
```

## jbmstable-parser（Java）

规范的原作者实现的 Java 解析库，位于 [exch-bms2/jbmstable-parser](https://github.com/exch-bms2/jbmstable-parser)。对应的 wiki 是规范的主要来源。

## 解析/生成要点

实现者视角的关键设计决策：

- **HTML 解析**：从 `<meta name="bmstable" content="..." />` 提取头部 JSON 的 URL。
- **头部 JSON 解析**：保留 `extra` 中未识别的字段，以兼容未来的规范扩展。
- **数据 JSON 解析**：未知字段应宽容处理，难度表可定义自定义字段（如 `total`、`judge`、`note`、`eval` 等）。
- **Course 展开**：Course 内嵌的 `md5`/`sha256` 列表应展开为 `ChartItem`，`level` 缺失时默认 `"0"`。
- **`level_order` 排序**：按 `level_order` 数组顺序排列等级，不依赖数据中的出现顺序。
