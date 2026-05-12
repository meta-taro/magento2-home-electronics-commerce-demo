# LEARNING LOG

AIエージェント(Claude Code)と作業した内容を残す学習ログ。
コードや設定を変更したら、下のテンプレートをコピーして **新しいエントリを上に追記**する(新しいものが上)。

記録の目的:
- 何を・なぜ・どう実装したかを後から追えるようにする
- 学んだこと・ハマったこと・解決方法を蓄積し、振り返りに使えるようにする
- AI 生成物を「確認した」記録を残す(動作確認 / 差分確認 / ログ確認)

---

## テンプレート(コピーして使う)

```markdown
### YYYY-MM-DD — <Topic>

- **Date**: YYYY-MM-DD
- **Topic**: <扱ったテーマ。例: 独自モジュールの routes.xml>
- **Phase**: <ROADMAP の Phase 番号>
- **Goal**: <この作業で達成したかったこと>
- **Files changed**:
  - `path/to/file1`
  - `path/to/file2`
- **What was implemented**: <実際に追加・変更した内容>
- **What was verified**: <どう動作確認したか。コマンド / 画面 / API レスポンス / ログの内容>
- **Errors / Issues**: <発生したエラー・詰まったこと>
- **Fixes**: <どう直したか>
- **Notes(学び・気づき)**: <学んだこと、設計上の判断、次に気をつけること>
```

---

## エントリ

### 2026-05-12 — ドキュメントの方針調整 + Phase 7 追加(Phase 0)

- **Date**: 2026-05-12
- **Topic**: ドキュメントを「学習・スキルアップ目的」に統一、ROADMAP に実運用補強フェーズを追加
- **Phase**: Phase 0
- **Goal**: 仕事・転職・案件アサイン寄りの表現を排し、純粋な学習リポジトリとして読める状態にする。あわせて「実運用との差」を意識した到達目標を残す。
- **Files changed**:
  - `README.md`(Repository Structure と docs 説明を更新)
  - `CLAUDE.md`(学習ログ項目名を変更)
  - `docs/PROJECT_OVERVIEW.md`(「案件」「実務」寄りの表現を学習寄りに修正)
  - `docs/LEARNING_LOG.md`(目的・テンプレート項目を学習寄りに修正、本エントリ追加)
  - `docs/ROADMAP.md`(`INTERVIEW_NOTES` → `SKILL_NOTES` 参照更新、Phase 7「実運用を意識した補強」を追加)
  - `docs/INTERVIEW_NOTES.md` を削除し `docs/SKILL_NOTES.md`(学習トピックの一覧と自己整理メモ)を新規作成
- **What was implemented**:
  - 「面談 / カジュアル面談 / SES / 案件アサイン / 実務経験アピール」系の記述を全削除(`grep` で関連語 0 件を確認)
  - `INTERVIEW_NOTES.md` を廃止し、内容を「扱う技術トピック一覧 + 学習の進め方 + トピック別の自己整理メモ」として `SKILL_NOTES.md` に作り直し
  - ROADMAP に Phase 7 を追加: 連携の堅牢化(冪等性/リトライ/ログ)、コーディング規約(phpcs)、軽量 CI、秘密情報の扱い、性能(FPC/インデックス/MSI)、デプロイ・運用観点メモ、「デモと実運用の差」の明文化
- **What was verified**: `grep -i` で仕事寄り用語が残っていないことを確認。リンク参照(`SKILL_NOTES.md`)の整合を目視確認。コード実装なしのため動作確認は対象外。
- **Errors / Issues**: なし
- **Fixes**: なし
- **Notes(学び・気づき)**: 学習リポジトリでも、機能実装(モジュール/Plugin/API)だけだと「実運用に耐えるか?」の問いに答えられない。運用で効くもの(冪等性・キャッシュ・インデックス・CI・秘密情報管理・デプロイ手順)の片鱗を意識的に混ぜることと、「ここまでは対象・ここからは対象外」を正直に書くことが、学習リポジトリの価値を上げる。

### 2026-05-12 — 初期ドキュメント整備(Phase 0)

- **Date**: 2026-05-12
- **Topic**: リポジトリの初期ドキュメントと Claude Code 作業ルールの整備
- **Phase**: Phase 0
- **Goal**: 公開リポジトリとして目的・進め方・ルールが伝わる状態を作る
- **Files changed**:
  - `README.md`
  - `CLAUDE.md`
  - `.claude/settings.local.json`
  - `docs/PROJECT_OVERVIEW.md`
  - `docs/ROADMAP.md`
  - `docs/LEARNING_LOG.md`
  - `docs/ARCHITECTURE.md`
  - `docs/SKILL_NOTES.md`
- **What was implemented**:
  - プロジェクトの目的・シナリオ(架空メーカー NOVA HOME Electronics の海外EC)・対象スキル・ロードマップを README に整理
  - Claude Code 用の作業ルール(公式ドキュメント優先、実装前確認、学習ログ必須、シークレット禁止、架空メーカー名使用、AI生成物を鵜呑みにしない、Windows/PowerShell 優先 等)を CLAUDE.md に明文化
  - `.claude/settings.local.json` を「読み取り系は許可、書き込み・破壊的コマンドは確認、push やシークレット読取は拒否」の慎重モードで作成
  - PROJECT_OVERVIEW / ROADMAP(Phase 0〜6)/ LEARNING_LOG テンプレート / ARCHITECTURE 設計メモ / SKILL_NOTES を作成
- **What was verified**: 各ファイルが指定の構成・内容を満たしているかを目視確認。コード実装は含まないため動作確認は対象外。
- **Errors / Issues**: なし
- **Fixes**: なし
- **Notes(学び・気づき)**: 学習リポジトリでも、最初に「目的・ロードマップ・作業ルール・ログの仕組み」を用意しておくと、後から振り返りやすく、継続もしやすい。AI を使う場合でも、確認プロセス(docs / diff / log)を最初にルール化しておくのが重要。
