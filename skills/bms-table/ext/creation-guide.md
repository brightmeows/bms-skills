# 创建流程参考

> 来源：[難易度表の作成方法について - PMS Database](http://pmsdifficulty.xxxxxxxx.jp/column_table.html)
> 来源：[スプレッドシートを使った難易度表の管理](https://bms.hexlataia.xyz/tips/googleapps.html)

创建并公开一个难度表的基本流程：

1. **准备 HTML 页面**：创建 `table.html`（GLAssist/beatoraja 读取用）和 `index.html`（TOP 页）。
2. **编写 `header.json`**：定义 `name`、`symbol`、`data_url`、`level_order` 等字段。
3. **编写 `score.json`**（数据部）：录入每条谱面的 MD5/SHA256、level、标题、艺术家等信息。
4. **部署至服务器**：推荐使用 GitHub Pages 或传统 FTP 托管。
5. **注册发现**：在 [Ribbit 難易度表まとめ](http://www.ribbit.xyz/bms/tables/table_list.html) 或 DARKSABUN 的列表中登记。

## 数据部管理技巧

- 可使用 Google スプレッドシート + Google Apps Script（GAS）将表格数据自动转为 JSON，实现即改即用。
- `sha256` 列在 2022 年后加入，用于标识 BMSON 格式谱面。
- GitHub Pages 部署时注意文件名不能以 `_` 开头，否则 GitHub 不会识别上传。

## 示例资源

- [第2通常难易度表 サンプルファイル集](https://bmsnormal2.syuriken.jp/sample/)：含 `table_sample.html`、`header.json`、`score.json`、`style.css`
