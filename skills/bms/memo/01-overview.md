# BMS command memo (JP)

> 来源：[BMS command memo (JP)](https://hitkey.nekokan.dyndns.info/cmdsJP.htm)

- 最新更新: 2014-07-11
- 作者: hitkey
  - 网站: <https://hitkey.nekokan.dyndns.info/>
  - 邮箱: hitkey0801[at]hotmail.com

## 参考来源

| 项目 | 链接 |
| ------ | ------ |
| 关于 BMS（Be-Music Script） | <https://en.wikipedia.org/wiki/Be-Music_Source> |
| 本文档参考了 | <https://web.archive.org/web/*/http://wiki.bms.ms/Bms:Spec> |
| *BMS Format Specification* | <http://bm98.yaneu.com/bm98/bmsformat.html> (1998-11-26) |
| BML（Be-Music Longnote format）基本规范 | <https://nvyu.net/rdm/rby_ex.php> |
| Sonorous 提出的 BMS 扩展 | <https://cosmic.mearie.org/f/sonorous/bmsexts> (2013-07-10 起，作为参考资料) |
| DTX 文件格式规范 | <http://dtxmania.net/wiki.cgi?page=qa_dtx_spec_e> (作为参考资料) |
| BMS 格式理解指南 | <https://cosmic.mearie.org/2005/03/bmsguide/> (2005-03-22，作为参考资料) |
| Angolmois Internals | <https://github.com/lifthrasiir/angolmois/blob/master/INTERNALS.md> (2013-03-09，作为参考资料) |
| 关于已废弃的 MGQ 记法 | <https://web.archive.org/web/*/http://ivy.pr.co.kr/rdm/jp/extension.htm> (2001-06-21，作为参考资料) |
| LR2beta3 皮肤 CSV 规格书 第五版 | <http://right-stick.sub.jp/lr2skinhelp.html> (2013-09-29，作为参考资料) |

## 注意事项

- 这不是规范书的翻译，这仅仅是我的个人笔记。
- 我 100% 使用了在线翻译服务。
- 我不懂英语，无法判断自动翻译的结果是否正确。
- 译文可能有所偏差，内容可能会被误读。
- 此外，我的研究可能不准确。（当然，我尽力避免错误，但无法保证内容的正确性。）
- 基于上述原因，我不对此文档承担任何责任。请见谅。
- 如有任何疑问，请随时联系我。如能指出错误，我将乐于修正。
- 字体测试: Segoe UI, Helvetica, Helvetica Neue, Verdana, Lucida Sans Unicode, Lucida, Arial

## 更新历史

- 2014-07-11: DTXCreator 026 / 补充了支持 `#VOLWAV` 的应用程序。
- 2014-07-08: Sonorous 0.1.0-pre (2014-07-08)
- 2014-06-29: HDX v1.05
- 2014-06-13: BGAEncAdv v0.034
- 2014-06-10: BGAEncAdv v0.033 / TechnicalGroove 停止更新
- 2014-06-08: HDX v1.04
- 2014-06-06: Mid2BMS / lr2_pmsview_helper
- 2014-05-25: PMSee-V v2.2.2
- 2014-05-12: HDX v1.03
- 2014-04-19: Feeling Pomu 2nd 支持 `#BGA`、`#POORBGA`、`#xxx0A` (LAYER2)
- 2014-04-17: Angolmois Rust Edition (2014-04-08) / nanasigroove2 beta (Toy Musical 3 Ver.2.2) / PMSee-V v2.2.1′
- 2014-02-27: Extended BPM / STOP Sequence
- 2014-02-10: 补充了 RLE 相关内容 (bmpfmt.htm) / 补充了关于大视频的内容
- 2014-02-07: 发现了可下载的 MacBeat
- 2014-02-04: 补充了以下项目: RLE / 按文件名分组 (`*.lr`) / `#BGA` 的兼容性 (bemaniaDX)
- 2014-02-02: [旧] mainori-se.sakura.ne.jp/dtxmania/ → [新] dtxmania.net/
- 2014-01-31: Angolmois Rust Edition (2014-01-26) / Sonorous 0.1.0-pre (2014-01-31)
- 2014-01-04: HDX v1.02
- 2013-12-28: HDX v1.01 / bms diff tool
- 2013-12-06: 修复了死链 (BMS Viewer, in_bm) / LR2SkinHELP
- 2013-11-18: Sonorous 0.1.0-pre (2013-11-18)
- 2013-11-17: Sonorous 0.1.0-pre (2013-11-13) / iBMSC 3.0.5
- 2013-10-18: Sonorous 0.1.0-pre (2013-10-13) / multi `#SUBTITLE` / multi `#COMMENT`
- 2013-10-11: Sonorous 0.1.0-pre (2013-10-09)
- 2013-10-07: 在 `#DEFEXRANK _n_` 补充了 TechnicalGroove 列 / 遗漏了 PMSee-V v2.1.5a (2013-07-15)（抱歉……抱歉……）
- 2013-10-06: 补充了关于视频倒带的内容
- 2013-09-23: Be-Music Helper (beta 4′) / woslicerIII (2013-09-22)
- 2013-09-18: Be-Music Helper (beta 4) / Sonorous 0.1.0-pre (2013-09-17) / `#OPTION` 的所有值的枚举 (option.htm)
- 2013-08-20: Angolmois 2.0 e5cea53a2cbd (SDL 2.0.\*) (fixed: `#BGA` 的兼容性)
- 2013-08-03: `#DEFEXRANK 0` / 按副标题分组 / multi `#SUBARTIST` / fractional `#STOP` / multi `#LNOBJ` / `#BGA` 的兼容性
- 2013-08-03: [旧] bit.sparcs.org/%7Etokigun/article/bmsguide.php → [新] cosmic.mearie.org/2005/03/bmsguide/
- 2013-08-03: HDX v1.00 / Sonorous 0.1.0-pre / BGAEncAdv v0.032 / TechnicalGroove
- 2013-07-11: 发现 HDX / IIDXv 支持扩展画布
- 2013-07-09: 发现 HDX / IIDXv 的缩进样式得到了增强 (U+3000)
- 2013-07-07: 在错误示例中加入了 `＃ENDIF` (U+FF03)
- 2013-07-03: uBMplay 1.5.2 / 在扩展画布中加入了 BM98
- 2013-07-02: 在 `#PATH_WAV` 中加入了 uBMplay
- 2013-07-01: uBMplay 1.5.1 / 修正了 woslicerII 的 URI (移除 "soft/index.html") / uBMplay 1.5.0 支持 `#OCT/FP`
- 2013-06-17: 添加了 BmDx，更改了 bemaniaDX 的缩写
- 2013-05-31: Angolmois Rust Edition
- 2013-05-25: woslicerIII / 补充了扩展画布的内容
- 2013-05-16: `<hgroup>` 已移除
- 2013-05-12: IIDXv 2.14, PMSee-V 2.1.3a
- 2013-05-07: HDX 0.99 / `#99902:1` / multiple `#xxxA6` / 修正了关于 `#RANK _n_` 默认值的错误描述
- 2013-04-12: PMSee-V 2.1.3
- 2013-04-01: uBMplay 1.5.0
- 2013-03-16: Angolmois 2.0 77ce3b6e2761 / 修正了 `#ExtChr` 的错误
- 2013-03-12: Angolmois 2.0 798422870970 (fixed: `#LNTYPE 2`)
- 2013-02-09: Angolmois 2.0 alpha 2 9880e98d15f8
- 2013-01-18: Angolmois 支持 alpha 混合
- 2013-01-17: 在 Comment syntax 中补充了关于字符串转义的内容
- 2013-01-17: HDX 0.98, PMSee-V 2.1.2, nanasi2 1.0, IIDXv 2.13, PMChr-V 4.0.2, Angolmois 2.0 alpha 2 pre
- 2012-10-13: HDX 0.97, PMSee-V 2.0.2, nanasi2 0.4, 修复了几个死链
- 2012-09-14: HDX 0.96, PMSee-V 2.0.1a, PMChr-V 4.0.0
- 2012-08-09: nanasi2 0.3
- 2012-08-08: iBMSC 3.0.4
- 2012-08-06: HDX 0.94, iBMSC 3.0.3 / 在 FREE ZONE 中加入了 BMSV 和 nBMplay
- 2012-07-03: HDX 0.93
- 2012-06-30: PMSV 1.10.5, nanasi2 0.2, IIDXv 2.12, HDX 0.92, `#ELSEIF`, Indent style, Comment syntax, Keyup-sounding
- 2012-04-27: PMSV 1.10.4, PMCV 3.1.0, nanasi2 0.1, SMB 1.03
- 2012-03-25: ruv-it! 2.0 b5p7 test #7
- 2012-03-18: Be-Music Helper (beta 3)
- 2012-02-10: 修正了本文档 HTML 的大量 Bug。![Valid HTML5](https://hitkey.nekokan.dyndns.info/cmd/valid-html5.png) ![Valid CSS3](https://hitkey.nekokan.dyndns.info/cmd/valid-css3.png)
- 2012-01-28: 修正了 `#TOTAL` 条目（感谢 kisama）
- 2012-01-23: 忘了反映 iBMSC 3.0 的最新版本，现已补充
- 2012-01-16: 为所有标题设置了永久链接，并补充了几个项目
- 2012-01-15: 更新了本文档。修改处太多，记不清了。
- 2011-10-24: 将本文档日文化。我的格式塔崩坏了。
- 2011-10-16: 将本文档 HTML 化。文件大小变成了 4 倍。
- 2011-09-21: 写完了本文档。我只是把纯文本用 `<pre>` 标签括了起来。
- 2011-09-12: 有了写这份文档的心情。开始动笔。
