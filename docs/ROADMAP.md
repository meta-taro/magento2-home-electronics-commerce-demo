# ROADMAP

NOVA HOME Electronics デモの段階的ロードマップ。各 Phase は完了したらチェックを入れ、`docs/LEARNING_LOG.md` に作業ログを残す。

凡例: ☐ 未着手 / ◐ 進行中 / ☑ 完了

---

## Phase 0 — ドキュメント整備 ◐

- ☑ README.md 作成
- ☑ CLAUDE.md 作成(Claude Code 作業ルール)
- ☑ .claude/settings.local.json 作成(慎重モード)
- ☑ docs/PROJECT_OVERVIEW.md 作成
- ☑ docs/ROADMAP.md 作成
- ☑ docs/LEARNING_LOG.md 作成(テンプレート)
- ☑ docs/ARCHITECTURE.md 作成(初期設計メモ)
- ☑ docs/SKILL_NOTES.md 作成(学習トピックの一覧と自己整理メモ)
- ☑ .gitignore 作成(`var/`, `pub/static/`, `pub/media/`, `generated/`, `app/etc/env.php`, `app/etc/config.php`, `auth.json`, `.env` などを除外)

**ゴール**: 公開リポジトリとして目的・ルール・計画が伝わる状態になっていること。

---

## Phase 1 — Magento Open Source ローカル環境構築 ☐

- ☐ 対象バージョン決定(2.4.x 系)とシステム要件確認(PHP / MySQL / OpenSearch or Elasticsearch / Redis)
- ☐ ローカル構築方式の検討・選定(Docker Compose ベースの開発環境を第一候補とする。Windows ホスト + コンテナ内 Linux で運用)
- ☐ Composer での Magento Open Source インストール手順整理(`auth.json` はコミットしない)
- ☐ サンプルデータ(Luma sample data 等)の投入と動作確認
- ☐ 管理画面ログイン・フロント表示確認
- ☐ `bin/magento` の基本コマンド(`setup:upgrade`, `cache:flush`, `setup:di:compile`, `setup:static-content:deploy`, `deploy:mode:set`)の把握
- ☐ 構築手順を `docs/ARCHITECTURE.md` または `docs/SETUP.md`(新規)に記録

**ゴール**: ローカルで Magento Open Source が起動し、サンプルストアが見られること。

---

## Phase 2 — 家電メーカーEC向けテーマ・カタログ設計 ☐

- ☐ 子テーマ作成(`app/design/frontend/NovaHome/<theme>/`、親は `Magento/blank` または `Magento/luma`)
- ☐ `theme.xml` / `registration.php` / レイアウトXML / 最小限のテンプレートオーバーライド
- ☐ ストア構造設計(Website / Store / Store View を US / EU / Singapore で分ける案)
- ☐ 商品カテゴリ設計: Air Purifiers / Rice Cookers / Vacuum Cleaners / Hair Dryers / Replacement Filters / Accessories
- ☐ 商品属性・属性セット設計(例: voltage, plug_type, compatible_filter_sku, warranty_months)
- ☐ サンプル商品の登録(架空の型番・スペック)

**ゴール**: 家電メーカー海外EC らしいカタログとテーマの土台ができていること。

---

## Phase 3 — 独自モジュール作成 ☐

- ☐ モジュールスケルトン `app/code/NovaHome/Catalog/`(または機能ごとに分割)
- ☐ `registration.php`
- ☐ `etc/module.xml`
- ☐ `etc/di.xml`(プリファレンス / バーチャルタイプ / コンストラクタ引数の例)
- ☐ `etc/frontend/routes.xml` とフロントコントローラ(例: 対応フィルター検索ページ)
- ☐ `etc/adminhtml/routes.xml`(必要なら管理画面側ルート)
- ☐ `setup:upgrade` で有効化、`bin/magento module:status` で確認

**ゴール**: 独自モジュールが有効化され、ルーティング・DI が動作すること。

---

## Phase 4 — Plugin / Observer 実装 ☐

- ☐ `etc/events.xml` + Observer(例: `sales_order_place_after` で疑似ERP連携キューに積む、`checkout_cart_product_add_after` でログ)
- ☐ Plugin(`di.xml` で `before` / `after` / `around` を定義。例: 商品ビューに「対応する交換フィルター」を表示する after プラグイン)
- ☐ 商品表示拡張(レイアウトXML + ブロック + テンプレートで関連消耗品セクションを追加)
- ☐ ログ出力(`Psr\Log\LoggerInterface` / カスタムロガーで `var/log/novahome.log`)
- ☐ 動作確認(注文作成 → ログ確認、商品ページ表示確認)

**ゴール**: イベント駆動とPluginでコア挙動を拡張できることを確認。

---

## Phase 5 — 疑似 ERP / WMS / 外部API連携 ☐

- ☐ Mock API の方式決定(スタブ JSON ファイル取込 から始め、必要なら軽量モックサーバへ拡張)
- ☐ 商品マスタ・価格の取込(ERP → Magento。CSV/JSON → 商品更新コマンド)
- ☐ 在庫の取込(WMS → Magento。倉庫別在庫を MSI のソース/ストックにマッピング)
- ☐ 注文データ連携(Magento → 疑似ERP/CRM。Observer で拾った注文を JSON で書き出す or モックエンドポイントへ送る)
- ☐ 決済ステータス・配送ステータスの取り込み(Payment / Shipping モック → 注文ステータス更新)
- ☐ バッチ実行手段(`bin/magento` のカスタムコマンド、または cron)
- ☐ 同期失敗時のリトライ・ログ設計のメモ

**ゴール**: 商品・在庫・注文・決済・配送のデータが、疑似外部システムと双方向にやり取りできること。

---

## Phase 6 — GraphQL / REST API 確認・仕上げ ☐

- ☐ 標準 REST API(`/rest/V1/products`, `/rest/V1/orders`, `/rest/V1/inventory/...`)を叩いて挙動確認
- ☐ 標準 GraphQL(`products`, `category`, `cart`)クエリ確認
- ☐ 独自モジュールに REST エンドポイント追加(`etc/webapi.xml` + サービスインターフェース)
- ☐ 独自 GraphQL スキーマ追加(`etc/schema.graphqls` + Resolver。例: 商品の対応フィルター情報)
- ☐ 簡易テスト(Integration / Unit のサンプル)
- ☐ ログ整理、README / docs の更新、学習ログの総まとめ

**ゴール**: API の利用と拡張ができ、リポジトリ全体が「学習成果が伝わる」状態になっていること。

---

## Phase 7 — 実運用を意識した補強 ☐

> このリポジトリ自体は学習・検証用デモであり、本番システムを目指すものではない。
> ただし「実運用の勘所を理解している」状態に近づけるため、運用で重要になる要素の"片鱗"を意識的に取り入れる。

- ☐ 連携処理の堅牢化: 冪等性(同じ取込を2回流しても壊れない)、リトライ、専用ログ、失敗時の整合性をどう保つかをコードとメモに残す
- ☐ コーディング規約: `magento/magento-coding-standard`(phpcs)を導入し、最低限のルールで通る状態にする
- ☐ CI: GitHub Actions で `php -l` / phpcs などの軽量チェックを回す
- ☐ 秘密情報の扱い: `.gitignore` と `.env.example` / `auth.json` の取り扱い方針を明示(Phase 0 の残タスクと連動)
- ☐ パフォーマンス: フルページキャッシュ、インデックス、MSI(在庫)の挙動を実際に触り、何が効くかを `docs/LEARNING_LOG.md` に記録
- ☐ 運用観点メモ: デプロイ手順(`deploy:mode:set production` / `setup:di:compile` / `setup:static-content:deploy`)、バックアップ、障害時の確認ポイントを `docs/ARCHITECTURE.md` に追記
- ☐ 「このデモと実運用の差」を `docs/PROJECT_OVERVIEW.md` か `docs/SKILL_NOTES.md` に明文化(インフラ冗長化、本物の外部連携、PCI DSS 等は対象外であることを正直に書く)

**ゴール**: 機能実装だけでなく、運用で何が重要になるかを理解した形跡が残っていること。実運用システムそのものではないが、実運用を意識した学習リポジトリになっていること。

---

## バックログ / 任意

- 多通貨・税・配送ルールの設定例
- 顧客セグメント / メール施策(CRM/MA 連携)の掘り下げ
- Integration / Unit テストのカバレッジ拡充
- 多言語(翻訳ファイル / `i18n`)対応の確認
