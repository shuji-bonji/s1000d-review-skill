# comment DM の入力の作り方

SKILL.md の Phase 6 で読む。`s1000d-writer-mcp` の `create_comment` の入力を、レビューの指摘から作る。

## 決まりを利用者に確かめる項目

`commentType` と `commentPriorityCode` の意味は、S1000D もサーバーも決めない。プロジェクトの決まり（BREX、運用の文書、既存の comment DM の使い方）で選ぶ。

| 項目 | 値の範囲 | この Skill の扱い |
|---|---|---|
| `commentType` | `q` / `i` / `r` | プロジェクトの決まりで選ぶ。**決まりが無ければ利用者に聞く。** 既定値を置かない。聞くときは、3 つの値だけを示し、意味はプロジェクトに尋ねる |
| `commentPriorityCode` | `cp01`〜`cp99`（`cp00` は不可） | 同じ。指摘の重さから Skill が推測して決めない |
| `securityClassification` | `01`〜`99` | 省略できない。値の意味は解釈しない。対象 DM の `securityClassification.dataModule` を候補として示し、利用者に確かめる |
| `language` | `{ languageIsoCode, countryIsoCode }` | comment DM の言語。対象 DM と同じか、プロジェクトの決まりか、利用者に確かめる。`paragraphs` の文もこの言語で書く |
| `responseType` | `rt01`〜`rt99` | 省略する（返信の欄を出さない）。利用者が指定したときだけ渡す |
| `issueType` | XSD の列挙 | 省略する。利用者が指定したときだけ渡す |

## 発信者（`originator`）

| 項目 | 必須 | 注意 |
|---|---|---|
| `originator.enterpriseName` | 必須 | |
| `originator.address.city` | **必須** | comment.xsd が要求する。無いと入力の誤り（`input-error`）になる |
| `originator.address.country` | **必須** | 同上 |
| `originator.person.lastName` | 任意（`person` を渡すなら必須） | 氏名は利用者が渡したものだけを使う。Skill が補わない |
| `originator.enterpriseCode` | 任意 | XML には出ない（comment.xsd の `enterprise` に属性が無い）。送り手を表す値は `senderIdent` |
| `senderIdent` | 必須 | 5 文字の企業コード |
| `modelIdentCode` | 必須 | 対象 DM のモデル識別コードに合わせるのが普通だが、プロジェクトの決まりを確かめる |

住所・企業コード・氏名は利用者が一度答えたら、この会話では使い回す。答えが無いものを推測で埋めない。

## 対象（`refs`）

- 対象の DM を `{ "kind": "dmRef", "code": "<DMC>", "issueInfo": { "issueNumber": "005", "inWork": "00" }, "language": { "languageIsoCode": "en", "countryIsoCode": "US" } }` で渡す。`issueInfo` と `language` は `read_data_module` の `target`（`issueInfo`）とファイル名から取る
- 1 つの comment DM の `refs` は、すべて同じ種類（`dmRef` / `pmRef` / `dmlRef` / `ddnRef`）にする。混ぜると入力の誤りになる
- DM 全体への指摘で、特定の DM を指さないときは `refs` を空にする（`noReferences`）

## 本文（`paragraphs`）

- 対象の DM 1 件につき comment DM 1 件。選ばれた指摘を 1 件 1 段落で並べる
- 段落の書き出しに、指摘の番号と観点を入れる。例: `R-3 [BREX手動] BIKE-BR-00078: …`
- 場所（手順の番号、節の題名）を書く。`BREX手動` と `文章` の指摘は、所見であることが読み取れる文末にする（「確認してください」）
- 段落は平文。マークアップは使えない
- 機密区分の確認で「引用しない」と決まった DM では、本文の文を段落に写さない
- `title` は短く（例: レビューの指摘 N 件）

## 呼び方

```json
{
  "refs": [{ "kind": "dmRef", "code": "S1000DBIKE-AAA-D00-00-01-00AA-520A-A",
             "issueInfo": { "issueNumber": "005", "inWork": "00" },
             "language": { "languageIsoCode": "en", "countryIsoCode": "US" } }],
  "commentType": "<プロジェクトの決まり>",
  "commentPriorityCode": "<プロジェクトの決まり>",
  "title": "<題>",
  "paragraphs": ["R-1 [機械] …", "R-3 [BREX手動] …"],
  "originator": { "enterpriseName": "<名>", "address": { "city": "<市>", "country": "<国>" } },
  "senderIdent": "<5 文字>",
  "modelIdentCode": "<モデル識別コード>",
  "securityClassification": "<利用者が確かめた値>",
  "language": { "languageIsoCode": "en", "countryIsoCode": "US" },
  "csdbDirs": ["<CSDB のフォルダー>"],
  "write": false
}
```

- **`csdbDirs` を必ず渡す。** 渡さないと、連番（`seqNumber`）が CSDB を見ずに `00001` になり、既存の comment DM と重なる。`write: true` の書き込み先も `csdbDirs` の先頭になる
- `brexDmRef` は省略すると、`specDir` の既定 BREX になる。プロジェクトの comment が従う BREX があれば、利用者に確かめて渡す
- `yearOfDataIssue`・`issueDate` は省略すると実行環境の今年・今日になる

## 応答の読み方

| 項目 | 意味 |
|---|---|
| `fileName` / `commentCode` | 作られる名前。`COM-….XML` の規則は仕様書で確認されていない推定（`_notes` に書いてある）。報告でも「推定の名前」と書く |
| `verification` | comment.xsd と BREX の検証状態。`valid` / `passed` をそのまま準拠と書かない |
| `fixes[]` | 残った修正。comment DM の `references` が `no-csdb` で未解決のときは、`csdbDirs` の渡し忘れ |
| `written` / `writeSkipped` | 書いたファイル／書かなかった理由（`no-csdb-dir`、`write-conflict`、`schema-invalid`） |
| `error.code: "input-error"` | `path` の箇所の入力が誤り。直して呼び直す（例: `/originator/address`） |
