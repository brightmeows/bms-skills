# 生态概览

> 来源：[Difficulty Tables - wcko87/beatoraja-english-guide](https://github-wiki-see.page/m/wcko87/beatoraja-english-guide/wiki/Difficulty-Tables)
> 来源：[GLAssist 公式対応難易度表](https://bmsnormal2.syuriken.jp/bms_dtimporter.html)

## 常见难度表与评级标度

| 表名称 | 符号 | 等级范围 | 说明 |
|--------|------|----------|------|
| 通常難易度表 | ☆ | ☆1–☆12 | 旧标准表，收录 2013 年以前曲目 |
| 発狂BMS難易度表 | ★ | ★1–★25 | 旧发狂表 |
| Overjoy | ★★ | ★★1–★★7 | 超高难度表，★★1≈★21 |
| 第2通常難易度（Normal2） | ▽ | ▽1–▽12 | 收录较新曲目，beatoraja 默认 |
| 第2発狂難易度（Insane2） | ▼ | ▼0–▼25 | 收录较新曲目，beatoraja 默认 |
| Satellite | sl | sl0–sl12 | 活跃更新，覆盖 ☆11–★19 |
| Stella | st | st0–st11 | 活跃更新，覆盖 ★19 以上 |
| Stardust | ξ | ξ1–ξ13 | 低难度，覆盖 ☆1–☆7 |
| Starlight | sr | sr0–sr13 | 中低难度，覆盖 ☆7–☆12 |
| LN 難易度表 | ◆ | ◆1–◆25 | 长条专用 |
| Turbow's BMS Library | T☆/T★/T★★ | T☆B–T★★11 | 全面收录近期 BMS 活动曲目 |

## 主要支持工具

| 工具 | 说明 |
|------|------|
| **beatoraja** | 原生支持本格式。配置 → 资源 → 添加表 URL → 读取难度表 |
| **GLAssist** | Java 桌面工具。支持表导入、差分自动导入、Score 查看 |
| **BeMusicSeeker** | LR2 上的表管理工具 |
| **Lunatic Rave 2 (LR2)** | 需配合 BeMusicSeeker 使用；LR2IR 与 bmsid 关联 |
| **bms_table (Rust)** | 编程库，支持网络获取、解析、序列化 |
| **jbmstable-parser (Java)** | 规范原作者的参考实现 |

## 段位认定

难度表可嵌入段位认定（Course）数据。社区公认的段位体系：

- **GENOSIDE 2018**：22 级段位（初段–十段 → 発狂初段–発狂十段 → 発狂皆伝 → Overjoy），分别位于第2通常/第2発狂表中。
- **Satellite Skill Analyzer / Stella Skill Simulator**：26 级（sl0–sl12 + st0–st12），对应 Satellite/Stella 表。
- **NEW GENERATION**：22 级段位，基于 2022 年新版难度表。
