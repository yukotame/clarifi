# ClariFi API設計書

最終更新: 2026-05-17

---

## ベースURL

```
http://localhost:3001/api
```

---

## エンドポイント一覧

### 1. 取引データ

| メソッド | エンドポイント | 概要 |
|---------|--------------|------|
| GET | `/transactions` | 取引一覧取得 |
| GET | `/transactions?month=2026-05` | 月指定で取引取得 |
| DELETE | `/transactions/:id` | 取引削除 |

#### GET /transactions レスポンス例

```json
[
  {
    "id": 1,
    "date": "2026-05-01T00:00:00.000Z",
    "store": "イオン○○店",
    "amount": 1980,
    "usageType": "お買い物",
    "chargeType": null,
    "category": "食費",
    "source": "WAON",
    "isExcluded": false
  }
]
```

---

### 2. CSVインポート

| メソッド | エンドポイント | 概要 |
|---------|--------------|------|
| POST | `/import/waon` | WAON CSVインポート |
| POST | `/import/paypay` | PayPay CSVインポート |

#### POST /import/waon リクエスト

```
Content-Type: multipart/form-data
file: [CSVファイル]
```

#### POST /import/waon レスポンス例

```json
{
  "success": true,
  "imported": 45,
  "excluded": 3
}
```

---

### 3. 集計・サマリ

| メソッド | エンドポイント | 概要 |
|---------|--------------|------|
| GET | `/summary/monthly` | 月次支出合計一覧 |
| GET | `/summary/category?month=2026-05` | 月別カテゴリ内訳 |

#### GET /summary/monthly レスポンス例

```json
[
  { "month": "2026-05", "total": 58000 },
  { "month": "2026-04", "total": 62000 }
]
```

#### GET /summary/category レスポンス例

```json
[
  { "category": "食費", "total": 25000 },
  { "category": "日用品", "total": 12000 },
  { "category": "外食", "total": 8000 },
  { "category": "その他", "total": 13000 }
]
```

---

### 4. WAONスクレイピング

| メソッド | エンドポイント | 概要 |
|---------|--------------|------|
| POST | `/scraper/waon/run` | WAON明細の手動取得実行 |
| GET | `/scraper/waon/status` | 最終取得日時の確認 |

#### POST /scraper/waon/run レスポンス例

```json
{
  "success": true,
  "message": "明細取得完了",
  "csvPath": "./output/waon_2026-05.csv",
  "count": 45
}
```

---

### 5. カテゴリルール

| メソッド | エンドポイント | 概要 |
|---------|--------------|------|
| GET | `/category-rules` | カテゴリルール一覧取得 |
| POST | `/category-rules` | カテゴリルール追加 |
| DELETE | `/category-rules/:id` | カテゴリルール削除 |

---

## エラーレスポンス共通フォーマット

```json
{
  "success": false,
  "error": "エラーメッセージ"
}
```
