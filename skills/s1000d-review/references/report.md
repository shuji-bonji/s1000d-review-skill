# Review Report の雛形

SKILL.md の Phase 7 で使う。複数の DM をレビューしたときは、冒頭に DM ごとの件数の表を置き、DM ごとに「観点ごとの指摘」を続ける。

```markdown
# DM レビューレポート

- 対象: <DMC、版、言語、ファイル名（複数なら件数と一覧）>
- 使用ツール: <MCP の名前と、分かる範囲の版>
- 範囲: <利用者と決めた範囲。読んだ節点数と文字数（`content.size`）。`maxNodes` で切れたかどうか>

## 機密区分

- 対象 DM の値: <`securityClassification.dataModule`>、本文の要素の値: <`inContent`>
- 引用の可否: <利用者の答え。「引用しない」なら、本文の文を書いていない旨>

## 検証状態

| 工程 | 状態 | 備考 |
|---|---|---|
| XSD | <valid / invalid / schema-not-found / not-run> | <schema-not-found は「未検証」> |
| BREX | <passed / violations / incomplete / not-run> | <manualChecks の件数> |
| 参照 | <resolved / unresolved / not-run> | <no-csdb の理由なら「CSDB のフォルダー未指定のため未実施」> |
| 適用性 | <checked: true / false> | <product を渡したか> |

`valid` と `passed` は、XSD と機械で検査できる BREX のルールに誤りが無かったことだけを意味する。S1000D への準拠は判定していない。

## 指摘（観点ごと）

### A. 機械で判定できること（サーバーの判定）

| 番号 | 場所 | 指摘 | 種類・出どころ | 修正の案（確からしさ） |
|---|---|---|---|---|
| R-1 | <XPath と行> | <`problem`> | <`kind` / `source` / `brSeverityLevel`> | <候補 / 利用者に聞く> |

### B. BREX の nonContextRules（LLM の所見。確認してください）

| 番号 | 取り決め | 本文の該当 | 所見 |
|---|---|---|---|
| R-4 | <`ruleId`> | <手順の番号・節の題名> | <抵触の疑い / 本文からは判断できない（人が確認） / 該当する本文なし> |

BREX の手動確認 <N> 件のうち、本文と照らしたもの <M> 件、DM の外にあるため人が確認するもの <K> 件。

### C. 文章（用語・書き方）

用語集: <ファイル名と形式 / なし>。STE の辞書: <渡された / 渡されていない（STE への適合は判定していない）>

| 番号 | 場所 | 指摘 | 根拠（用語集の行 / 用語集の根拠なし） |
|---|---|---|---|

## comment DM

| 作ったもの | 内容 | 状態 |
|---|---|---|
| <`commentCode`、推定の `fileName`> | <含めた指摘の番号> | <作成して書き込み済み / `write: false` の確認用 / `writeSkipped`: 理由> |

選ばれなかった指摘: <番号>

## 未実施の項目

- <「未実施（ツール未接続）」: brex-mcp 未接続による BREX 手動確認の本文、writer-mcp 未接続による comment DM>
- <CSDB のフォルダー未指定による参照の確認>
- <product 未指定による適用性>

## 次の操作

- <利用者が決めること（修正の値、機密区分の扱い、用語集の追加）>
```
