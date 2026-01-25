# 缶つまハンズオン - K&K 商品データ分析プロジェクト

## 📋 プロジェクト概要

このプロジェクトは、K&K 缶つまブランドの商品データを活用した、**Snowflake Cortex** を使用した AI 駆動型の小売データ分析プラットフォームの構築ハンズオンです。

AI を用いた商品情報の自動抽出から、セマンティック検索、および AI エージェントによる自然言語クエリまで、最新のデータ分析技術を統合したエンドツーエンドのソリューションを実装します。


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


## 🔑 主要な Snowflake 機能

| 機能 | 用途 |
|-----|------|
| **AI_COMPLETE** | 自然言語による画像説明生成 |
| **AI_EXTRACT** | 構造化データの自動抽出 |
| **Cortex Search** | セマンティック検索 |
| **Semantic View** | ビジネス用語の定義と SQL 自動生成 |
| **Cortex Agent** | マルチツール AI アシスタント |



## 📞 サポート

質問や問題がある場合は、以下をご確認ください：
- Snowflake ドキュメント: https://docs.snowflake.com
- Cortex 機能ガイド: https://docs.snowflake.com/en/user-guide/cortex

## 📄 ライセンス

このプロジェクトは教育目的のハンズオン資料です。

---

**最終更新**: 2026年1月26日
**作成**:Hiroki Watari
