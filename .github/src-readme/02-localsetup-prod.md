# 02-ローカルリポジトリ初期設定

1.  任意のディレクトリに移動して、ローカルリポジトリの作成を行います。

```bash
git init
```

2.  名前とメールアドレスを設定します。メールアドレスは架空のもので大丈夫です。

```bash
git config user.name "お名前"
git config user.email onamae@localhost.local
```

3.  リモートリポジトリに接続します。

```bash
git remote add origin https://github.com/masaru-study/verify-note.git
```

4.  リモートリポジトリの内容を取得します。

```bash
git fetch
```

5.  リモートリポジトリ内の main ブランチに追従する、ローカルリポジトリ内の default ブランチを作ります。

```bash
git branch default origin/main
```

6.  使うブランチを default ブランチに変更します。

```bash
git checkout default
```

7.  サブモジュールリポジトリの内容を取得します。

```bash
git submodule update --init --recursive
```

8.  実際に変更を加える、ローカルリポジトリ内の post/onamae ブランチを作ります。

```bash
git branch post/onamae
```
