# ClariFi（クラリファイ）

> 家計を、明確に。

WAON・PayPayの支出データを自動取得・可視化する個人向け家計簿Webアプリです。

---

## 📌 アプリ概要

| アプリ | 概要 |
|--------|------|
| **アプリ①** WAONスクレイパー | WAON公式サイトから明細を自動取得しCSV出力 |
| **アプリ②** 支出ダッシュボード | WAON・PayPayのCSVをインポートして支出を可視化 |

---

## 🛠️ 技術スタック

| 役割 | 技術 |
|------|------|
| フロントエンド | Next.js 14 + TypeScript |
| バックエンド | Node.js + Express + TypeScript |
| スクレイピング | Playwright (Node.js) |
| データベース | PostgreSQL |
| ORM | Prisma |
| グラフ | Recharts |
| 定期実行 | node-cron |
| パッケージ管理 | pnpm (モノレポ) |

---

## 🗂️ フォルダ構成

```
clarifi/
├── apps/
│   ├── frontend/          # Next.js 14 + TypeScript
│   └── backend/           # Node.js + Express + TypeScript
├── docs/                  # 要件定義・技術資料・進捗管理
├── .gitignore
├── pnpm-workspace.yaml
└── README.md
```

---

## 🚀 セットアップ手順

### 1. 前提条件
- Node.js 20+
- pnpm
- PostgreSQL
- Playwright

### 2. インストール

```bash
git clone https://github.com/yukotame/clarifi.git
cd clarifi
pnpm install
```

### 3. 環境変数設定

```bash
cp apps/backend/.env.example apps/backend/.env
# .envを編集してWAONログイン情報・DB接続情報を設定
```

### 4. DB初期化

```bash
cd apps/backend
pnpm prisma migrate dev
```

### 5. 起動

```bash
# バックエンド
cd apps/backend
pnpm dev

# フロントエンド
cd apps/frontend
pnpm dev
```

---

## ⚠️ セキュリティ注意事項

- `.env` は絶対にGitにコミットしない
- WAONのログイン情報は `.env` にのみ記載
- 本アプリは個人利用を目的としています

---

## 📅 開発ロードマップ

- [x] Phase 1: MVP（WAON自動取得 + 見える化）
- [ ] Phase 2: データ手動修正・カテゴリ管理
- [ ] Phase 3: 現金管理・予算設定・アラート
- [ ] Phase 4: マルチアカウント・ログイン認証
- [ ] Phase 5: レシート撮影・銀行口座連携・公開

---

## 📄 ライセンス

MIT
