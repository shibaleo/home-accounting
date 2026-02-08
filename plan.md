# 目的

- 日頃の仕訳入力は Zaim で行う（変更なし）
- Beanpost（PostgreSQL）で複式簿記データを管理し、費用の適正配分・可視化・決算整理を行う
- Zaim → Beanpost パイプラインで自動変換
- MCP 経由で PostgreSQL に直接クエリ可能
- Fava で状態確認（BS/PL/GL/試算表）
- 個人利用前提、低コスト・運用容易・復旧容易

---

# アーキテクチャ

```
Neon PostgreSQL (既存)
├── data_warehouse スキーマ  ← Zaim 生データ (raw_zaim__money, etc.)
├── beanpost スキーマ        ← 複式簿記データ (account, transaction, posting, ...)
└── トリガー/関数            ← data_warehouse → beanpost 自動変換

日常:     Zaim アプリ → Zaim API → data_warehouse.raw_zaim__money
変換:     トリガー → beanpost.transaction + beanpost.posting
決算整理: MCP → beanpost に直接仕訳投入（減価償却、配賦等）
可視化:   MCP クエリ / Fava (beancount エクスポート経由)
```

---

# 採用技術

## DB: Neon PostgreSQL（既存）

- data_warehouse スキーマに Zaim マスタ・取引データが既存
- beanpost スキーマを新設し、同一 DB 内で複式簿記データを管理
- スケールゼロ対応、無料枠で運用可能

## 会計エンジン: Beanpost

- PostgreSQL ネイティブの複式簿記スキーマ（beancount 互換）
- カスタム型 `amount (number, currency)` による通貨別集計
- `account_change()`, `account_hierarchy_change()` で残高・期間増減計算
- `sum(posting)` で cost/price を考慮した集計
- トランザクション貸借一致チェック (`transaction_is_balanced()`)
- 原価計算: strict / avg / FIFO / LIFO

## UI: Fava（状態確認用）

- beanpost-export.py で PostgreSQL → beancount ファイル出力
- Fava で BS/PL/GL/試算表を確認
- Docker Compose でローカル起動

## 管理会計（PostgreSQL 関数で実装）

- 減価償却: 固定資産台帳テーブル + 月次償却仕訳生成関数
- 費用配賦: posting の tags/meta による多軸分類 + 集計ビュー
- 決算整理仕訳: MCP 経由で直接 transaction/posting に投入

---

# Zaim → Beanpost マッピング

## 口座 (raw_zaim__account → beanpost.account)

| Zaim prefix | Beanpost account |
|---|---|
| BK_* | Assets:Bank:* |
| CY_* | Assets:EMoney:* |
| DP_* | Assets:Deposit:* |
| CD_* | Liabilities:CreditDeposit:* |
| CC_* | Liabilities:CreditCard:* |
| LN_* | Liabilities:Loan:* |
| AD_* | Liabilities:AutoDebit:* |
| PD_* | Liabilities:PeriodicDebit:* |

## 費用科目 (raw_zaim__category + raw_zaim__genre → beanpost.account)

- Zaim category (支出) → Expenses:{Category} (勘定科目)
- Zaim genre → Expenses:{Category}:{Genre} (補助科目)
- Zaim category (収入) → Income:{Genre}

---

# 現状の課題

## Zaim 勘定科目の分類混在

Zaim のカテゴリ・ジャンル体系は **費目別分類**（食費・交通費・住居費）と **機能別分類**（交際費・仕事関連費）が混在している。

### 問題例

- 「交際費」カテゴリに食事・交通・贈答が混在 → 費目別に見ると食費・交通費に分散すべき
- 「仕事」カテゴリの「飲食」→ 費目別では食費
- 「趣味・娯楽」の「カフェ」→ 費目別では食費
- 同じジャンル名が複数カテゴリに重複（洗濯、ゴミ、美容院、病院、自動車保険、火災保険等）

### 影響

- Zaim のカテゴリをそのまま Beanpost の勘定科目にマッピングすると、費目別集計が不正確になる
- 「食費の月間合計」が実態より少なく出る（交際費・仕事費用に食事代が紛れる）

---

# フェーズ構成

## Phase 1: Zaim 勘定科目の整理（現在）

**目的**: Zaim のカテゴリ・ジャンルを費目別分類に統一し、Beanpost への clean mapping を確立する

### やること

1. Zaim の全カテゴリ・ジャンルを棚卸し
2. 費目別分類の正規化ルールを策定
   - 機能別分類（交際費、仕事関連等）を費目別に再分類
   - 重複ジャンルの統合方針を決定
3. Zaim 上でカテゴリ・ジャンルを修正
4. **過去仕訳をすべて見直し**、新しい分類に再分類
5. Zaim → Beanpost マッピングテーブルを確定

### 成功条件

- Zaim の全カテゴリが費目別分類として一貫している
- 過去仕訳が新分類に基づいて再分類済み
- マッピングテーブルが 1:1 で対応し、ambiguity がない

## Phase 2: Beanpost スキーマ構築 + パイプライン

1. Neon に beanpost スキーマを作成（schema.sql を beanpost スキーマに修正）
2. Zaim マッピングテーブルを作成
3. data_warehouse → beanpost 変換トリガーを実装
4. 過去データの一括変換
5. Fava でのデータ確認

## Phase 3: 管理会計機能

1. 固定資産台帳テーブル + 減価償却関数
2. 決算整理仕訳の投入フロー（MCP 経由）
3. 費用配賦・多軸分類ビュー
4. 可視化（Sankey diagram 等）

---

# ローカル開発環境

```yaml
# docker-compose.yml
services:
  db:      postgres:15 (beanpost スキーマ, port 5432)
  fava:    yegle/fava (sample.beancount, port 5000)
```

---

# 検討経緯・却下した選択肢

- **Odoo CE**: 管理会計は充実しているが、個人利用にはオーバースペック。インフラコスト・運用負荷が高い
- **ERPNext**: MariaDB 必須、不要機能が大量
- **Firefly III**: 管理会計のデータモデルなし
- **自作 FastAPI + PG**: 会計エンジンの再開発になる
- **beancount 単体**: plain text のみでクエリ性が低い。PostgreSQL に入れることで解決 → beanpost 採用
