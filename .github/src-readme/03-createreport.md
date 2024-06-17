# 03-記事作成

1.  使うブランチを default ブランチに変更します。

```bash
git checkout default
```

2.  リモートリポジトリ内の main ブランチの内容を取得してローカルリポジトリ内の default ブランチに適用します。

```bash
git pull origin main
```

3.  使うブランチを post/onamae ブランチに変更します。

```bash
git checkout post/onamae
```

4.  ローカルリポジトリ内の default ブランチの内容を post/onamae ブランチに適用します。

```bash
git merge default post/onamae
```

5.  hugo でコンテンツを作成します。
    - 任意ですが以下のようにするとテンプレートが読み込まれます。
      `hugo new --kind=●●● #カテゴリ名#/#ページ名#.md`
      対応テンプレートは handson と know です。

```bash
hugo new #カテゴリ名#/#ページ名#.md
```

6.  記事の Front Matter を編集します。ルールは付録を参照してください。

```bash
vim content/#カテゴリ名#/#ページ名#.md
```

7.  記事の本文を編集します。

```bash
vim content/#カテゴリ名#/#ページ名#.md
```

8.  作成した記事をワークツリーからインデックスへ登録を行います。

```bash
git add -A
```

9.  インデックスの情報を post/onamae ブランチに適用します。

```bash
git commit -m "#コミットコメントを入れる#"
```

10. リモートリポジトリ内の post/onamae ブランチに適用します。
    初回適用時はリモートリポジトリ内の post/onamae ブランチの新規作成になります。

```bash
git push origin post/onamae
```
