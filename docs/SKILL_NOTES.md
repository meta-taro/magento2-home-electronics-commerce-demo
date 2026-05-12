# SKILL NOTES(学習トピックと自己整理メモ)

このリポジトリで扱う技術トピックの一覧と、学習の進め方・理解した内容を自分用に整理するためのメモ。
目的はあくまで **Magento / Adobe Commerce 周辺の理解とスキルアップ**であり、各トピックを「手を動かして・確認しながら・記録しながら」身につけることに使う。

---

## 学習の進め方

- 一次情報は **Adobe Commerce / Magento の公式ドキュメント(DevDocs)**。二次情報は補助。
- AIエージェント(Claude Code)は未知領域のキャッチアップに使うが、生成物は下書き扱い。必ず **公式ドキュメント確認・動作確認・差分確認(`git diff`)・ログ確認** を行う(ルールは `CLAUDE.md`)。
- 進め方は Phase 0〜6 のロードマップ(`docs/ROADMAP.md`)に沿う。
- 作業ごとに `docs/LEARNING_LOG.md` に「何を・なぜ・どう実装し、どう確認したか」を残す。
- 大きな設計判断は `docs/ARCHITECTURE.md` に追記する。

## 扱う技術トピック一覧

| トピック | 何を理解したいか | 主に出てくる Phase |
|---------|------------------|--------------------|
| ローカル開発環境 | Magento Open Source 2.4.x のインストール、Docker 構成、`bin/magento` の基本操作、サンプルデータ | Phase 1 |
| テーマ開発 | 子テーマ、`theme.xml`、レイアウトXML、テンプレートオーバーライド、地域別ストアビュー | Phase 2 |
| カタログ設計 | 商品カテゴリ、商品属性・属性セット、関連商品(本体↔消耗品) | Phase 2 |
| 独自モジュール | `registration.php`、`module.xml`、ディレクトリ規約、`setup:upgrade` | Phase 3 |
| DI / `di.xml` | プリファレンス、バーチャルタイプ、コンストラクタ引数注入 | Phase 3 |
| ルーティング | `routes.xml`(frontend / adminhtml)、コントローラ | Phase 3 |
| Plugin | before / after / around、差し込みの順序、適用先の選び方 | Phase 4 |
| Observer / `events.xml` | イベント駆動、`sales_order_place_after` 等の代表的イベント | Phase 4 |
| ブロック / テンプレート拡張 | レイアウトXMLでのブロック追加、商品詳細の表示拡張 | Phase 4 |
| ロギング | `LoggerInterface`、`di.xml` でのカスタムロガー、`var/log/` | Phase 4〜 |
| 外部システム連携 | サービスインターフェース + アダプタ、DTO/マッパー、差し替え可能な設計 | Phase 5 |
| データ同期(商品/在庫/注文) | ERP 商品・価格取込、WMS 在庫(MSI ソース/ストック)、注文の書き出し、決済・配送ステータス取込、冪等性 | Phase 5 |
| バッチ実行 | カスタム CLI コマンド、cron | Phase 5 |
| REST API | 標準 API の利用、`webapi.xml` での独自エンドポイント | Phase 6 |
| GraphQL | 標準クエリの利用、`schema.graphqls` + Resolver での拡張 | Phase 6 |
| テスト | Integration / Unit のサンプル、`magento-coding-standard` | Phase 6 / バックログ |

## トピック別メモ(随時追記)

> 学習が進んだら、トピックごとに「分かったこと」「ハマったこと」「公式ドキュメントの該当箇所」をここに追記する。
> 詳細な作業経緯は `docs/LEARNING_LOG.md`、設計判断は `docs/ARCHITECTURE.md` 側に書き、ここは概念の自分用まとめにする。

### 例: DI / `di.xml`

- (まだ未着手)
- 学んだら: プリファレンスとバーチャルタイプの使い分け、`type` ノードでの引数注入、`generated/` との関係 などを書く。

### 例: Plugin

- (まだ未着手)
- 学んだら: `sortOrder` による順序制御、around プラグインの注意点(`$proceed` を呼ばないと後続が動かない 等)を書く。

### 例: データ同期(商品/在庫/注文)

- (まだ未着手)
- 学んだら: MSI のソース/ソースアイテム/ストック/在庫予約の関係、商品リポジトリ経由の更新とインデックス再構築のタイミング、取込の冪等化方法 などを書く。

## なぜこの題材(家電メーカー海外EC)か

- マルチストア・多地域(US / EU / Singapore)、商品属性が豊富(電圧・プラグタイプ・対応フィルター型番など)、ERP / WMS / 決済 / 配送 / CRM 連携が現実的に発生する構成で、Magento が得意とする領域とデータ同期を題材として扱いやすいから。
- 架空メーカー「NOVA HOME Electronics」にすることで、商標・実在企業の問題を避けつつ、現実的な業務シナリオで学べる。

## 補足

- Adobe Commerce(有償版)と Magento Open Source はコアアーキテクチャ(モジュール構造・DI・Plugin・Observer・レイアウトXML・REST/GraphQL)を共有しているため、Open Source で学んだ内容は Adobe Commerce の開発にも応用できる。
- Adobe Commerce 固有機能(B2B、ページビルダーの一部、Commerce 専用モジュール等)は本リポジトリの対象外。基礎〜中級の実装の理解に絞る。
