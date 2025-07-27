---
inclusion: always
---

# Hugo 開発ワークフロー

## 開発コマンド

### ローカル開発
```bash
# Hugo開発サーバーを起動
hugo server -D

# 特定のポートで起動
hugo server -D -p 1313

# 本番用ビルド
hugo --minify
```

### コンテンツ作成ワークフロー
1. 適切なアーキタイプを使用して新しいコンテンツを作成：
   ```bash
   hugo new content/[category]/[filename].md --kind [handson|know|minutes]
   ```
2. 日本語記述標準に従ってコンテンツを編集
3. 公開準備ができたら `draft = false` に設定
4. `hugo server -D` でローカルテスト
5. `hugo --minify` でビルドしてデプロイ

### テーマカスタマイズ
- テーマの場所：`themes/hugo-theme-relearn/`
- カスタムオーバーライドはプロジェクトルートの layouts/ に配置
- 静的アセットは `static/` ディレクトリに配置

## ファイル整理のベストプラクティス

### 静的アセット
- 画像：`static/img/[category]/`
- ファイル：`static/file/[category]/`
- コンテンツでの参照：`![alt text](/img/category/image.png)`

### コンテンツ構造
- カスタム順序にはweightパラメータを使用
- 関連コンテンツは同じカテゴリフォルダに保持
- ハイフン付きの説明的なファイル名を使用

## サイト設定メモ
- サイト言語：日本語（ja）
- 正規URLが有効
- ポータビリティのため相対URLが有効
- 検索機能がアクティブ
- GitHubとYouTube用のメニューショートカットが設定済み

## 品質チェックリスト
- [ ] 適切なアーキタイプを使用
- [ ] 必須フロントマターフィールドをすべて完了
- [ ] 日本語記述標準に従っている
- [ ] 画像が最適化され適切に参照されている
- [ ] リンクがテスト済みで動作している
- [ ] ライト・ダークテーマ両方で正しくレンダリングされている