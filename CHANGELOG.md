# Changelog

このファイルは [Keep a Changelog](https://keepachangelog.com/ja/1.1.0/) の形式に従います。

## [0.1.2] - 2026-11-24

### 変更

- 報告の「使用ツール」に、MCP ごとに応答の最後の `_server` から `<name> <version>（core <core>）` を書き写すことにしました。MCP の応答に `_server` が付く版（hub の DECISIONS の D-38、Issue [shuji-bonji/s1000d-stack#12](https://github.com/shuji-bonji/s1000d-stack/issues/12)）に合わせた変更です。`_server` の無い応答は「版の項目なし（D-38 より前の版）」と書きます。版の解釈と、npm の公開版との比較はしません。

## [0.1.1] - 2026-10-29

hub の E2E テスト（`e2e/`）で見つかった、MCP 側の修正に合わせた更新です。手順の骨組みは変えていません。

### 修正

- 前提 MCP の最低版を reader 0.7.1・verify 0.6.1 に上げました。Phase 6 に、書いた comment DM を reader・verify で確かめるとき、これらの版以降なら起動した後に書いたファイルも見えること、それより古い版では見えないことがあり、その場合は起動し直すことを書きました。

## [0.1.0] - 未リリース

- 初版（`skills/s1000d-review/SKILL.md` と `references/`、`evals/trigger-eval.json`）。
