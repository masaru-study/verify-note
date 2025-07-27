---
inclusion: always
---

# コンテンツ作成ガイドライン

## Hugo アーキタイプの使用

新しいコンテンツを作成する際は、常に適切なアーキタイプを使用してください：

### ハンズオンコンテンツ
```bash
hugo new content/[category]/[filename].md --kind handson
```

**必須構造：**
- 概要
- 本ハンズオンで身につくこと
- 出てくるコマンド／操作
- 事前準備
- ハンズオン（各セクション）

**タグ：** "Type:Handson"、"Author:"、"Level:" を必ず含める

### ナレッジコンテンツ
```bash
hugo new content/[category]/[filename].md --kind know
```

**必須構造：**
- 概要

**タグ：** "Type:Knowledge"、"Author:"、"Level:" を必ず含める

### 議事録
```bash
hugo new content/projects/[filename].md --kind minutes
```

**必須構造：**
- 概要（日時、場所、出席者、次回会議を含む）
- 議題
- 議論内容
- 決定事項
- アクション項目

**タグ：** "Type:Minutes"、"Author:" を必ず含める

## コンテンツ整理

### ディレクトリ構造
- `content/general/` - 一般的なお知らせと情報
- `content/tutorial/` - チュートリアルコンテンツ
- `content/server/` - サーバー関連コンテンツ
- `content/network/` - ネットワーク関連コンテンツ
- `content/cloud/` - クラウドコンピューティングコンテンツ
- `content/projects/` - 議事録とプロジェクトドキュメント
- `content/other/` - その他のコンテンツ

### ファイル命名規則
- ハイフン付きの小文字を使用：`my-tutorial-name.md`
- 説明的だが簡潔に
- 時間に敏感なコンテンツには日付プレフィックスを含める：`2024-01-15-meeting-minutes.md`

## フロントマター要件

### 必須フィールド
- `title`：日本語での説明的なタイトル
- `date`：ISO形式の日付
- `draft`：公開コンテンツの場合は `false` に設定
- `categories`：適切なカテゴリの配列
- `tags`：Author、Level、Type、および説明的なタグを含む配列

### オプションフィールド
- `weight`：カスタム順序用（小さい数字が先に表示される）
- `description`：検索・SEO用の簡潔な説明