# 缶つまハンズオン - K&K 商品データ分析プロジェクト

## 📋 プロジェクト概要

このプロジェクトは、K&K 缶つまブランドの商品データを活用した、**Snowflake Cortex** を使用した AI 駆動型の小売データ分析プラットフォームの構築ハンズオンです。

AI を用いた商品情報の自動抽出から、セマンティック検索、および AI エージェントによる自然言語クエリまで、最新のデータ分析技術を統合したエンドツーエンドのソリューションを実装します。

## 🎯 プロジェクトの 4 ステップ

### **Step1: 画像からAIで情報を抽出してテーブルに格納**
- 商品の画像ファイルから `AI_COMPLETE` を使用して詳細な説明を自動生成
- `AI_EXTRACT` で構造化データ（商品名、JANコード、原材料、アレルギー情報など）を抽出
- 抽出結果を JSON として保存し、RAW_DATA スキーマに格納
- JSON を展開して ANALYTICS スキーマの PRODUCT_MASTER テーブルに統合

### **Step2: Cortex Search サービスの作成**
- PRODUCT_MASTER テーブルを基に Cortex Search サービスを構築
- 商品説明、AI キャプション、原材料、アレルギー情報による全文検索を実現
- `snowflake-arctic-embed-l-v2.0` エンベディングモデルを使用した意味的検索

### **Step3: Cortex Analyst用セマンティックビューの作成**
- INVENTORY、SALES、PRODUCT_MASTER をリレーションシップで連携
- ビジネス用語（日本語シノニム）を定義したセマンティックビューを実装
- ファクト、ディメンション、メトリクスの定義により、自然言語から SQL への自動変換を実現

### **Step4: Cortex Agent の作成**
- Cortex Analyst と Cortex Search を統合したマルチツール AI エージェント
- ユーザーの自然言語質問に対して、適切なツールを選択して回答
- 複合的な分析クエリに対応（例：アレルギー情報 + 売上分析 + 在庫予測）

## 📁 ファイル構成

```
Retail_Handson/
├── AI_agent_Handson.ipynb      # メインの実装ノートブック
├── README.md                    # このファイル
├── setup.sql                    # 初期セットアップ用 SQL スクリプト
├── csv/
│   ├── inventory.csv            # 在庫データ
│   └── sales.csv                # 売上データ
└── images/                      # 商品画像フォルダ
```

## 🔧 セットアップ手順

### 前提条件
- Snowflake アカウント（Cortex 機能が利用可能）
- 必要な権限：WAREHOUSE、DATABASE、SCHEMA の作成権限

### インストール
1. リポジトリをクローン
   ```bash
   git clone <repository-url>
   cd Retail_Handson
   ```

2. `setup.sql` を Snowflake で実行
   ```sql
   -- DATABASE, SCHEMA, WAREHOUSE の初期化
   ```

3. `AI_agent_Handson.ipynb` をJupyter環境で開き、セル順に実行

### 必要な Snowflake 設定
- Cortex 機能の有効化
- 画像ファイル用の Stage 作成（HANDSON_RESOURCES）
- 必要なコンピュート リソース（COMPUTE_WH）の確保

## 📊 データ構造

### 主要テーブル

| テーブル名 | スキーマ | 説明 |
|-----------|---------|------|
| PRODUCT_AI_EXTRACT | RAW_DATA | AI抽出結果（JSON形式） |
| PRODUCT_MASTER | ANALYTICS | 展開済み商品マスタ |
| INVENTORY | RAW_DATA | 在庫スナップショット |
| SALES | RAW_DATA | 売上取引記録 |

### PRODUCT_MASTER スキーマ
```sql
FILE_NAME          VARCHAR      -- 画像ファイル名
PRODUCT_NAME       VARCHAR      -- 商品名
DESCRIPTION        VARCHAR      -- 商品説明
JAN_CODE           VARCHAR      -- JANコード
CONTENT            VARCHAR      -- 内容量(g)
PRICE              VARCHAR      -- 参考売価(円)
INGREDIENT         VARCHAR      -- 原材料
ALLERGY            VARCHAR      -- アレルギー成分
AI_DETAILED_CAPTION VARCHAR(MAX) -- AI生成キャプション
```

## 🔍 Cortex Search サービス

### PRODUCT_SEARCH_SERVICE
- **検索対象テキスト**: 商品名 + AI キャプション + 原材料 + アレルギー情報
- **属性**: JAN_CODE、PRODUCT_NAME、FILE_NAME、CONTENT、PRICE、ALLERGY
- **エンベディング**: snowflake-arctic-embed-l-v2.0

#### 検索例
```
"低脂肪で高たんぱく"
"ピーナッツアレルギー対応"
```

## 📈 Cortex Analyst セマンティックビュー

### KANTSUMA_ANALYST

#### テーブル定義
- **INVENTORY**: 在庫データ（在庫数、評価額）
- **PRODUCT_MASTER**: 商品マスタ（説明、アレルギー、原材料）
- **SALES**: 売上取引（数量、金額）

#### メトリクス例
- `total_sales_amount`: 売上合計金額
- `total_sales_quantity`: 販売合計数量
- `average_unit_price`: 平均販売単価
- `total_inventory_quantity`: 総在庫数量
- `total_inventory_value`: 総在庫金額

#### 自然言語クエリ例
```
"2025年の月別売上を表示"
"アレルギー品目が小麦以下の商品を検索"
"在庫金額が高い top 10 商品"
```

## 🤖 Cortex Agent (KANTSUMA_AGENT)

### 機能
- **マルチツール統合**: Cortex Analyst + Cortex Search
- **知的ルーティング**: クエリ内容に応じた最適なツール選択
- **複合分析**: 商品検索 → 売上分析 → 在庫予測

### 活用シーン
1. 商品詳細検索 + 売上分析の組み合わせ
2. アレルギー制約を含めた商品提案
3. 在庫最適化のための売上トレンド分析
4. 栄養価・成分ベースの商品レコメンデーション

### 例：複合クエリ
```
"来月のフェアで、『健康志向（低糖質・高タンパク）』かつ
『アレルギー品目"小麦"を含まない』新しいおつまみ缶詰を提案してほしい。
ただし、現在在庫があって即納できるものに限る。"
```

## 📊 ワークフロー

```
商品画像
    ↓
AI_COMPLETE (詳細説明生成)
AI_EXTRACT (構造化データ抽出)
    ↓
PRODUCT_AI_EXTRACT (RAW_DATA)
    ↓
JSON展開
    ↓
PRODUCT_MASTER (ANALYTICS)
    ↓
┌─────────────────────────┐
│ Cortex Search Service   │  ← テキスト検索
│ Cortex Analyst SV       │  ← SQL自動生成
│ Cortex Agent            │  ← 自然言語クエリ
└─────────────────────────┘
    ↓
インサイト・可視化
```

## 🚀 使用方法

### ノートブックの実行
1. **AI_agent_Handson.ipynb** を Jupyter で開く
2. **Step1 - Step4** をセル順に実行
3. 各ステップ後、結果確認用のクエリを実行

### クエリの実行例

#### Cortex Search での検索
```sql
SELECT 
    SCORE, 
    PRODUCT_NAME,
    DESCRIPTION,
    ALLERGY
FROM SEARCH(
    HANDSON.ANALYTICS.PRODUCT_SEARCH_SERVICE,
    'グルテンフリー',
    top_k => 5
);
```

#### Cortex Analyst での分析
```sql
SELECT
    PRODUCT_NAME,
    TOTAL_SALES_AMOUNT,
    TOTAL_SALES_QUANTITY
FROM HANDSON.ANALYTICS.KANTSUMA_ANALYST
WHERE YEAR = 2025
ORDER BY TOTAL_SALES_AMOUNT DESC;
```

#### Cortex Agent への質問
```sql
CALL HANDSON.ANALYTICS.KANTSUMA_AGENT(
    'グルテンフリーの商品で2025年の売上トップ3を教えてください'
);
```

## 🔑 主要な Snowflake 機能

| 機能 | 用途 |
|-----|------|
| **AI_COMPLETE** | 自然言語による画像説明生成 |
| **AI_EXTRACT** | 構造化データの自動抽出 |
| **Cortex Search** | セマンティック検索 |
| **Semantic View** | ビジネス用語の定義と SQL 自動生成 |
| **Cortex Agent** | マルチツール AI アシスタント |

## 📝 カスタマイズ

### セマンティックビューへの追加フィールド
KANTSUMA_ANALYST セマンティックビューの `dimensions` または `facts` セクションにフィールドを追加

### 新しいメトリクスの追加
```sql
METRICS (
    SALES.metric_name AS FUNCTION(sales.column)
        WITH SYNONYMS = ('日本語シノニム')
        COMMENT = '説明'
)
```

### Cortex Agent への新機能追加
`KANTSUMA_AGENT` の `tools` セクションに新規 Cortex Search サービスを追加

## ⚠️ 注意点

- Cortex 機能の利用には **Snowflake のプレミアム機能** が必要
- 大規模なデータセットではウェアハウスのサイズを調整してください
- 画像ファイルは PNG 形式を推奨

## 📞 サポート

質問や問題がある場合は、以下をご確認ください：
- Snowflake ドキュメント: https://docs.snowflake.com
- Cortex 機能ガイド: https://docs.snowflake.com/en/user-guide/cortex

## 📄 ライセンス

このプロジェクトは教育目的のハンズオン資料です。

---

**最終更新**: 2026年1月22日