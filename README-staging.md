# ハンズオンチームの秘密基地（ステージング環境）

## ここはなに？

まさるの勉強部屋参加者のハンズオンチームの検証結果置き場（ステージング環境）です。

## つかいかた

* 権限のあるメンバーはhugoを使用して記事を投稿できます。

1. Hugoをローカルに入れます
2. gitを使います。

```
# ローカルリポジトリ作成
git init
# リモートリポジトリに接続
git remote add origin https://github.com/masaru-study/test-verify-note.git
# リモートリポジトリの内容を取得
git fetch
# リモートブランチ一覧
git branch -r
# ローカルブランチ作成
git checkout -b #ローカルブランチ名# origin/main
# hugoでコンテンツを作成する
hugo new content posts/#ページ名#.md
# 記事を編集（draft = falseにしないとgit actionが走らないです…）
vim content/posts/#ページ名#.md
# ワークツリーからインデックスへのファイルの登録を行う
git add -A
# ローカルリポジトリに登録
git commit -m "#コミットコメントを入れる#"
# リモートリポジトリにコピーする
git push origin #ローカルブランチ名#
```
3. GUIでPull RequestをしてMargeをするとGit Actionが走ります。
   * Pull Request→New Pull Request→compare:#ローカルブランチ名#選択→Create request→（説明を追加）Create pull request→Merge pull request→Conform Merge
4. ページを確認して更新されていれば完了

