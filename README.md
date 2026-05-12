# magento2-home-electronics-commerce-demo

NOVA HOME Electronics — Magento Open Source 学習・検証用デモリポジトリ

## Overview

This repository is a public learning/demo project built on **Magento Open Source**.
It models the official cross-border e-commerce site of a fictional home-electronics manufacturer, **"NOVA HOME Electronics"**, and is used to study and verify the technical building blocks that frequently appear in Adobe Commerce / Magento engagements.

このリポジトリは商用案件のソースコードではなく、Adobe Commerce / Magento案件で求められる技術要素を学習・検証するための公開デモです。

Magento Open Source を利用し、Adobe Commerce / Magento案件で頻出するテーマ開発、モジュール開発、DI、Plugin、Observer、REST/GraphQL、外部システム連携、商品・在庫・注文同期の理解を目的としています。

## Purpose

- Magento Open Source のローカル開発環境を構築・運用できる状態にする
- テーマ開発・独自モジュール開発・DI・Plugin・Observer・routes.xml・events.xml の実装パターンを手を動かして確認する
- REST API / GraphQL の利用と拡張を検証する
- 疑似 ERP / WMS / Payment / Shipping / CRM 連携を通じて、商品・在庫・注文データ同期の設計を理解する
- AIエージェントを活用した未知領域の学習・検証プロセスを、ログとして残す

## Scenario

架空の家電メーカー **NOVA HOME Electronics** が、海外向け公式ECサイトを運営する想定。

- 対象地域: US / EU / Singapore
- 商品カテゴリ: Air Purifiers / Rice Cookers / Vacuum Cleaners / Hair Dryers / Replacement Filters / Accessories
- 想定する業務連携:
  - **ERP**: 受発注、商品マスタ、価格
  - **WMS**: 倉庫別在庫、出荷
  - **Payment**: 決済ステータス
  - **Shipping**: 配送ステータス
  - **CRM / MA**: 顧客情報、購買履歴、メール施策

> 実在のメーカー名・商標は使用しません。すべて架空の設定です。

## Target Skills

- Magento Open Source ローカル開発環境
- テーマ開発(レイアウトXML / テンプレート / 静的アセット)
- 独自モジュール開発(`registration.php` / `module.xml`)
- DI / `di.xml`
- Plugin(before / after / around)
- Observer / `events.xml`
- `routes.xml` とコントローラ
- REST API
- GraphQL
- 商品・在庫・注文データ同期
- 疑似 ERP / WMS / 決済 / 配送 / CRM 連携
- AIエージェントを使った学習・検証ログの整理

## Tech Stack

- Magento Open Source (2.4.x 系を想定)
- PHP / Composer
- MySQL / MariaDB, OpenSearch / Elasticsearch, Redis(Magento 標準構成に準拠)
- Docker(ローカル環境構築の候補。Phase 1 で検討)
- Mock 連携 API(言語・実装は Phase 5 で検討。スタブ JSON / 軽量サービスを想定)
- Claude Code(AIアシスト開発)
- 開発ホスト: Windows / PowerShell 環境を想定

## Planned Features

- 家電メーカー海外EC向けテーマ構成
- 商品カテゴリ・商品属性(電圧 / プラグタイプ / 対応フィルター型番など)の設計
- 独自モジュールによる注文イベントフック(Observer / Plugin)
- 商品表示拡張(対応フィルターの自動表示など)
- 疑似 ERP/WMS からの商品・在庫 JSON 取込バッチ
- 注文データの疑似 ERP/CRM 連携
- REST / GraphQL での商品・在庫・注文データ確認

## Repository Structure

```
.
├─ README.md                     # このファイル
├─ CLAUDE.md                     # Claude Code 用の作業ルール
├─ .claude/
│  └─ settings.local.json        # Claude Code ローカル設定(慎重モード)
├─ docs/
│  ├─ PROJECT_OVERVIEW.md        # 背景・題材選定理由・学習方針
│  ├─ ROADMAP.md                 # Phase 0〜6 のロードマップ
│  ├─ LEARNING_LOG.md            # AIエージェントとの作業ログ(テンプレート)
│  ├─ ARCHITECTURE.md            # 初期アーキテクチャ設計メモ
│  └─ SKILL_NOTES.md             # 学習トピックの一覧と自己整理メモ
└─ (今後) app/code/..., app/design/..., dev/, etc.   # Magento モジュール・テーマ等
```

## Development Roadmap

詳細は [docs/ROADMAP.md](docs/ROADMAP.md) を参照。

- **Phase 0**: ドキュメント整備(README / CLAUDE.md / docs)← 現在ここ
- **Phase 1**: Magento Open Source ローカル環境構築 / Docker 構成検討 / サンプルデータ確認
- **Phase 2**: 家電メーカーEC向けテーマ構成 / 商品カテゴリ・属性設計
- **Phase 3**: 独自モジュール作成(`registration.php` / `module.xml` / `di.xml` / `routes.xml`)
- **Phase 4**: Plugin / Observer 実装(注文イベントフック、商品表示拡張)
- **Phase 5**: 疑似 ERP / WMS API 連携(商品・在庫 JSON 取込、注文データ連携)
- **Phase 6**: GraphQL / REST API 確認 / テスト / ログ / README 更新

## AI-assisted Development Policy

このリポジトリは Claude Code(AIエージェント)を活用して開発します。ただし AI 任せにはしません。

- 公式ドキュメント(Adobe Commerce / Magento DevDocs)を一次情報として優先する
- 実装前に対象ファイルと変更方針を確認する
- AI 生成コードはそのまま信用せず、動作確認・差分確認(`git diff`)・ログ確認を行う
- 推測で危険な実装をしない
- 変更後は [docs/LEARNING_LOG.md](docs/LEARNING_LOG.md) に学習ログを残す
- APIキー・認証情報・秘密情報はコミットしない

詳細なルールは [CLAUDE.md](CLAUDE.md) を参照。

## Disclaimer

- 本リポジトリは **学習・検証目的の公開デモ**であり、商用案件のソースコードや本番システムではありません。
- "NOVA HOME Electronics" は架空のメーカーであり、実在の企業・商標とは一切関係ありません。
- Magento / Adobe Commerce は Adobe Inc. の製品・商標です。本リポジトリは非公式であり、Adobe とは関係ありません。
- 含まれるサンプルデータ・連携仕様はすべてデモ用に作成した架空のものです。
