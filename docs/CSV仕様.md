# ClariFi CSV仕様書

最終更新: 2026-05-17

---

## 1. WAONとPayPayのカラム対応表（マッピング）

| DBフィールド | 型 | WAON列名 | PayPay列名 | 備考 |
|---|---|---|---|---|
| `date` | DateTime | 取引年月日 | 利用日/キャンセル日 | 日付変換して保存 |
| `store` | String | 利用店舗 | 利用店名・商品名 | そのまま保存 |
| `amount` | Int | 利用金額（税込） | 支払総額 | 手数料込みの金額を使用 |
| `usageType` | String | 利用区分 | 支払区分 | 区分名称は異なるが同用途 |
| `chargeType` | String? | チャージ区分 | ―（なし） | WAONのみ。PayPayはnull |
| `paymentMethod` | String? | ―（なし） | 決済方法 | PayPayのみ。WAONはnull |
| `source` | String | `"WAON"` | `"PAYPAY"` | インポート時に自動付与 |
| `category` | String | ―（なし） | ―（なし） | 自動分類ロジックで付与 |
| `isExcluded` | Boolean | ―（なし） | ―（なし） | 除外ルールで自動判定 |

---

## 2. PayPayの不使用列（破棄）

| PayPay列名 | 理由 |
|-----------|------|
| 利用者 | 個人利用のため不要 |
| 手数料 | 支払総額に含まれるため不要 |
| 当月支払金額 | 支出管理には不要 |
| 翌月以降繰越金額 | 支出管理には不要 |
| 調整額 | 支出管理には不要 |
| 当月お支払日 | 支出管理には不要 |

---

## 3. 除外ルール

### WAON

| 条件 | フィールド | キーワード |
|------|-----------|----------|
| チャージ取引 | chargeType | 「チャージ」「オートチャージ」 |
| 返金・取消 | usageType | 「返金」「取消」 |

### PayPay

| 条件 | フィールド | キーワード |
|------|-----------|----------|
| チャージ | store / usageType | 「チャージ」 |
| 返金 | store / usageType | 「返金」「キャンセル」 |
| ポイント利用 | store / usageType | 「ポイント」 |

> 除外されたレコードは `isExcluded = true` としてDBに保存し、集計から除外します。

---

## 4. WAONのCSV出力フォーマット（アプリ①出力形式）

```csv
取引年月日,利用店舗,利用金額（税込）,利用区分,チャージ区分
2026-05-01,イオン○○店,1980,お買い物,
2026-05-03,イオン○○店,3000,,オートチャージ
```

---

## 5. PayPayのCSVフォーマット（公式エクスポート形式）

```csv
利用日/キャンセル日,利用店名・商品名,利用者,決済方法,支払区分,利用金額,手数料,支払総額,当月支払金額,翌月以降繰越金額,調整額,当月お支払日
2026/05/01,ファミリーマート○○店,本人,PayPay残高,通常,500,0,500,500,0,0,2026/06/10
```

---

## 6. 統一フォーマット変換フロー

```
WAON CSV
  └→ waonCsvParser.ts
        ├ 取引年月日      → date
        ├ 利用店舗        → store
        ├ 利用金額（税込）→ amount
        ├ 利用区分        → usageType
        ├ チャージ区分    → chargeType
        ├ source = "WAON"
        └ 除外判定 → isExcluded

PayPay CSV
  └→ paypayCsvParser.ts
        ├ 利用日/キャンセル日  → date
        ├ 利用店名・商品名     → store
        ├ 支払総額            → amount
        ├ 支払区分            → usageType
        ├ 決済方法            → paymentMethod
        ├ source = "PAYPAY"
        └ 除外判定 → isExcluded

両方 → Transactionテーブル（統一）
```
