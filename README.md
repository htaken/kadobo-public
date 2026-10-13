# kadobo

個人事業主が Slack のボタン操作で業務委託の稼働時間と経費を記録し、月次の請求とマネーフォワード（MF）クラウドの仕訳までをつなぐ自己管理ツール。

## 構成

```
Slack（本人だけが参加するプライベートチャンネル）
  │ ボタン・スラッシュコマンド・モーダル
  ▼
Cloudflare Worker   署名検証、D1 受付ジャーナル、即時 ACK、Cron 再送
  │ HMAC 署名付きの封筒で POST
  ▼
GAS Web アプリ      状態遷移の検証、生ログ追記、集計、カード表示
  ├─ Google スプレッドシート（記録の正）
  ├─ Google Drive（証憑）
  └─ MF クラウド請求書・会計 API（請求書と仕訳）
```

| ディレクトリ | 内容 |
|---|---|
| `shared/` | Worker と GAS の両方で使う純粋 TypeScript（封筒の型、ID、時刻） |
| `worker/` | Cloudflare Worker と D1 マイグレーション |
| `gas/` | Google Apps Script（esbuild で `dist/Code.js` に1ファイル化） |
| `docs/` | 要件定義、実装設計、受入試験、runbooks |

## 必要なもの

- Node.js 24 以上
- Cloudflare アカウント（Workers Free プランで動作。D1 と Cron Triggers を使う）
- Google アカウント（Apps Script、スプレッドシート、Drive）
- 自分が所有する Slack ワークスペース
- MF クラウド請求書・会計（MF 連携を使う場合）

## コマンド

| コマンド | 内容 |
|---|---|
| `npm install` | 依存関係を入れる（npm workspaces） |
| `npm test` | 全ワークスペースの Vitest を実行する |
| `npm run typecheck` | 全ワークスペースで `tsc --noEmit` を実行する |
| `npm run build` | `gas/dist/Code.js` を生成し、Worker のバンドルを検証する |

## セットアップ

デプロイ手順は [`docs/runbooks/`](docs/runbooks/README.md) にある。Worker と GAS は URL を互いに参照するので、[01 初回デプロイ](docs/runbooks/01_初回デプロイ.md) の順序どおりに進める。

設計の詳細は次の文書にある。

- [要件定義 v1.1](docs/稼働記録・経費管理システム_要件定義_v1.1.md)
- [実装設計 MVP](docs/実装設計_MVP.md) / [経費フェーズ](docs/実装設計_経費フェーズ.md) / [MF 連携](docs/実装設計_MF連携.md)

文書中の `docs/未決事項・デプロイ前確認.md`、`docs/契約前確認事項.md`、`docs/事務処理規程_電子取引.md`、`reviews/` は、個人の契約・税務情報を含むため公開していない。

## ライセンス

ライセンスは付与していない。© 2026 htaken. All rights reserved.
