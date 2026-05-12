# ARCHITECTURE(初期設計メモ)

> ⚠️ 現時点では実装前。これは「これから組み立てる構成」の設計メモであり、確定仕様ではない。Phase が進むごとに更新する。

## 全体像

```
                         ┌─────────────────────────────────────────────┐
                         │            Magento Open Source              │
                         │  (PHP / MySQL / OpenSearch / Redis / Varnish)│
                         │                                             │
  ブラウザ ───────────▶  │  ┌───────────────┐   ┌──────────────────┐  │
  (US / EU / SG)         │  │  Theme layer   │   │  Custom module    │  │
                         │  │  NovaHome theme│   │  layer (NovaHome) │  │
  REST / GraphQL ──────▶ │  │  layout / tmpl │   │  di / plugin /    │  │
  クライアント            │  └───────────────┘   │  observer / route │  │
                         │                       │  webapi / graphql │  │
                         │                       └─────────┬─────────┘  │
                         │                                 │            │
                         │                  ┌──────────────▼──────────┐ │
                         │                  │ External integration     │ │
                         │                  │ layer (adapters/services)│ │
                         │                  └──────┬──────────┬────────┘ │
                         └─────────────────────────┼──────────┼──────────┘
                                                   │          │
                       Batch import / sync (CLI command / cron)│
                                                   │          │
        ┌──────────────┬──────────────┬────────────┴──┬───────┴──────┬───────────────┐
        ▼              ▼              ▼               ▼              ▼               ▼
  ┌──────────┐  ┌──────────┐  ┌────────────┐  ┌────────────┐  ┌──────────┐    ┌──────────┐
  │ Mock ERP │  │ Mock WMS │  │Mock Payment│  │Mock Shipping│  │ Mock CRM │    │   Logs   │
  │   API    │  │   API    │  │   API      │  │   API       │  │  / MA    │    │ var/log/ │
  └──────────┘  └──────────┘  └────────────┘  └────────────┘  └──────────┘    └──────────┘
   受発注/商品   倉庫別在庫/    決済ステータス   配送ステータス   顧客/購買履歴
   マスタ/価格   出荷                                            /メール施策
```

## レイヤー別メモ

### Magento Open Source(基盤)

- 対象バージョン: 2.4.x 系(Phase 1 で確定)。
- 標準構成: PHP-FPM、MySQL/MariaDB、OpenSearch(または Elasticsearch)、Redis(セッション/キャッシュ)、必要に応じて Varnish。
- ローカルは Docker Compose で構成する想定(Windows ホスト + コンテナ内 Linux)。
- ストア構造案: Website を地域ごと(US / EU / Singapore)に分け、その下に Store / Store View。通貨・税・配送・言語を Store View 単位で切り替える。

### Theme layer(`app/design/frontend/NovaHome/<theme>/`)

- 親テーマ: `Magento/blank`(軽量にカスタムしたい場合)または `Magento/luma`(早く形にしたい場合)。Phase 2 で決定。
- 構成要素: `theme.xml`、`registration.php`、`web/`(CSS/JS/画像)、`Magento_Theme/layout/`、`<Module>/templates/` のオーバーライド。
- 役割: 家電メーカーらしい商品一覧・商品詳細(スペック表、対応フィルター表示)、地域別の見た目調整。

### Custom module layer(`app/code/NovaHome/...`)

- モジュール分割案(暫定):
  - `NovaHome_Catalog` — 商品属性拡張、商品詳細の対応フィルター表示、フロントの検索ページ。
  - `NovaHome_Integration` — 外部連携の入口(サービスインターフェース、アダプタ、Observer、CLI コマンド)。
  - `NovaHome_Api`(必要なら) — 独自 REST(`webapi.xml`)/ GraphQL(`schema.graphqls` + Resolver)。
- 各モジュールに `registration.php` / `etc/module.xml` / `etc/di.xml` / 必要に応じ `etc/frontend/routes.xml`・`etc/adminhtml/routes.xml`・`etc/events.xml`・`etc/webapi.xml`・`etc/schema.graphqls`。
- DI: プリファレンス(インターフェース → 実装)、バーチャルタイプ(同一クラスを別設定で使う)、コンストラクタ引数注入の例を作る。
- Plugin: 商品ビューブロックに対する after プラグインで「対応する交換フィルター」を差し込む等。
- Observer: `sales_order_place_after`(注文を疑似ERP連携キューへ)、`checkout_cart_product_add_after`(ログ)等。

### External integration layer

- Magento 内部の「外部システムと話す」責務を集約するレイヤー。
- 構成イメージ:
  - サービスインターフェース(`Api/ErpClientInterface` 等)
  - アダプタ実装(最初はローカルの JSON ファイルを読む/書くスタブ、後でモックHTTPサーバ対応に差し替え可能に)
  - DTO / マッパー(外部スキーマ ⇔ Magento エンティティ)
  - リトライ・エラーハンドリング・ログ
- 差し替え可能性を DI(プリファレンス)で担保する。

### Mock 外部システム(リポジトリ内の `mocks/` などに配置予定)

| Mock | 役割(デモ用) | やり取りするデータ | 方向 |
|------|----------------|--------------------|------|
| Mock ERP API | 受発注、商品マスタ、価格 | 商品(SKU/名称/カテゴリ/属性)、価格、受注の引き渡し | ERP → Magento(商品・価格)、Magento → ERP(注文) |
| Mock WMS API | 倉庫別在庫、出荷 | 倉庫(ソース)別在庫数、出荷ステータス、トラッキング番号 | WMS → Magento(在庫・出荷)、Magento → WMS(出荷指示) |
| Mock Payment API | 決済ステータス | authorized / captured / refunded / failed | Payment → Magento(ステータス更新) |
| Mock Shipping API | 配送ステータス | label_created / in_transit / delivered / returned | Shipping → Magento(ステータス更新) |
| Mock CRM / MA | 顧客情報、購買履歴、メール施策 | 顧客プロファイル、注文履歴の集約、セグメント、メール送信トリガ | Magento → CRM(顧客・注文)、CRM → Magento(セグメント等) |

- 実装方針(暫定): まず「JSON ファイルを置く / 書き出す」レベルのスタブで始め、必要になったら軽量モックサーバ(任意言語の最小HTTP)に拡張する。実値・本物のエンドポイントは使わない。

### Batch import / sync

- 手段: Magento のカスタム CLI コマンド(`bin/magento novahome:erp:import-products` のような名前)を作り、cron からも呼べるようにする。
- 処理例:
  - ERP 商品・価格取込: JSON 読込 → 商品リポジトリで作成/更新 → インデックス再構築。
  - WMS 在庫取込: 倉庫別在庫を MSI のソース/ソースアイテム/ストックにマッピングして更新。
  - 注文連携: Observer で拾った注文を JSON で書き出し or モックエンドポイントへ POST。
  - 決済/配送ステータス取込: ステータス JSON 読込 → 該当注文のステータス/コメント更新。
- 冪等性(同じファイルを2回流しても壊れない)とエラー時の継続/中断方針をメモする。

### Logs

- アプリログ: `var/log/`(`system.log`, `exception.log`, `debug.log`)。
- 連携専用ログ: カスタムロガー(Monolog ハンドラを `di.xml` で設定)で `var/log/novahome_integration.log` 等に分離する。
- 学習ログ: コード/設定変更の経緯は `docs/LEARNING_LOG.md` に必ず残す(運用ログとは別物)。

## 未決事項(Phase 進行で確定する)

- Magento の正確なバージョン
- 親テーマ(blank か luma か)
- Docker 構成の具体(自前 compose か既存ボイラープレートか)
- Mock API の実装形態(ファイルスタブのみ か モックサーバ追加か)
- モジュールの分割粒度(1モジュールに集約 か 機能別に分割か)
- 多通貨・税・配送ルールをどこまで作り込むか
