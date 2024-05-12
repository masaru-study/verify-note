# ハンズオンチームの秘密基地

> [!IMPORTANT]
> ここはステージング環境です。

- [ここはなに？](#ここはなに)
- [つかいかた](#つかいかた)
- [付録:ページ作成ルール](#付録ページ作成ルール)
  - [ディレクトリ](#ディレクトリ)
  - [カテゴリ](#カテゴリ)
  - [タグ](#タグ)
- [付録:Discord 通知](#付録discord-通知)
  - [通知の著者名](#通知の著者名)
  - [通知の投稿内容文](#通知の投稿内容文)
  - [通知の無効化](#通知の無効化)
- [付録:hugo ディレクトリ構造](#付録hugo-ディレクトリ構造)
- [付録：ステージング環境について](#付録ステージング環境について)
  - [Discord 通知のテスト](#discord-通知のテスト)
  - [本番環境からの同期](#本番環境からの同期)

## ここはなに？

まさるの勉強部屋参加者のハンズオンチームの検証結果置き場です。

## つかいかた

権限のあるメンバーは hugo を使用して記事を投稿できます。

> [!TIP]
> ２回目以降の記事投稿は手順 6 ～ 8 を行ってください。

> [!IMPORTANT]
> 手順内の"お名前"や"onamae"と言った文字列は任意の文字列に書き換えてください。

1. Hugo をローカルにインストールします。

2. git をローカルにインストールします。

3. github CLI をローカルにインストールします。

4. git の初期設定を行います。

   1. おまじないを入力します。

   ```bash
   git config --global init.defaultBranch default
   git config --global user.name "名無し"
   git config --global user.email nanasi@localhost.local
   ```

   2. github CLI でログインします。

   ```bash
   gh auth login
   > GitHub.Com
   > HTTPS
   > Paste an authentication token
   > （githubで発行したPersonal access tokenを貼り付ける）
   gh auth status
   ```

   3. github CLI のログイン情報を git で参照するようにします。

   ```bash
   gh auth setup-git
   ```

5. ローカルリポジトリの初期設定を行います。

   1. 任意のディレクトリに移動して、ローカルリポジトリの作成を行います。

   ```bash
   git init
   ```

   2. 名前とメールアドレスを設定します。メールアドレスは架空のもので大丈夫です。

   ```bash
   git config user.name "お名前"
   git config user.email onamae@localhost.local
   ```

   3. リモートリポジトリに接続します。

   ```bash
   git remote add origin https://github.com/masaru-study/-----test-----verify-note.git
   ```

   4. リモートリポジトリの内容を取得します。

   ```bash
   git fetch
   ```

   5. リモートリポジトリ内の main ブランチに追従する、ローカルリポジトリ内の default ブランチを作ります。

   ```bash
   git branch default origin/main
   ```

   6. 使うブランチを default ブランチに変更します。

   ```bash
   git checkout default
   ```

   7. サブモジュールリポジトリの内容を取得します。

   ```bash
   git submodule update --init --recursive
   ```

   8. 実際に変更を加える、ローカルリポジトリ内の post/onamae ブランチを作ります。

   ```bash
   git branch post/onamae
   ```

6. 記事を作成します。

   1. 使うブランチを default ブランチに変更します。

   ```bash
   git checkout default
   ```

   2. リモートリポジトリ内の main ブランチの内容を取得してローカルリポジトリ内の default ブランチに適用します。

   ```bash
   git pull origin
   ```

   3. 使うブランチを post/onamae ブランチに変更します。

   ```bash
   git checkout post/onamae
   ```

   4. ローカルリポジトリ内の default ブランチの内容を post/onamae ブランチに適用します。

   ```bash
   git merge default
   ```

   5. hugo でコンテンツを作成します。
      - 任意ですが以下のようにするとテンプレートが読み込まれます。  
        `hugo new --kind=●●● #カテゴリ名#/#ページ名#.md`  
        対応テンプレートは handson と know です。

   ```bash
   hugo new #カテゴリ名#/#ページ名#.md
   ```

   6. 記事の Front Matter を編集します。ルールは付録を参照してください。

   ```bash
   vim content/#カテゴリ名#/#ページ名#.md
   ```

   7. 記事の本文を編集します。

   ```bash
   vim content/#カテゴリ名#/#ページ名#.md
   ```

   8. 作成した記事をワークツリーからインデックスへ登録を行います。

   ```bash
   git add -A
   ```

   9. インデックスの情報を post/onamae ブランチに適用します。

   ```bash
   git commit -m "#コミットコメントを入れる#"
   ```

   10. リモートリポジトリ内の post/onamae ブランチに適用します。  
       初回適用時はリモートリポジトリ内の post/onamae ブランチの新規作成になります。

   ```bash
   git push origin
   ```

7. [Github リポジトリ](https://github.com/masaru-study/-----test-----verify-note/)に Web アクセスして結合する。

   1. Pull Request
   2. New Pull Request
   3. base:main ← compare:post/onamae
   4. Create request
   5. Create pull request
   6. Merge pull request
   7. Confirm Merge

8. [公開 Web ページ](https://masaru-study.github.io/-----test-----verify-note/)を確認して更新されていれば完了

## 付録:ページ作成ルール

### ディレクトリ

投稿する記事のジャンルによって、格納するディレクトリを分けています。

- General
  - 技術文書以外の記事
- Tutorial
  - 初学者向けの記事
- Server / Network / Cloud
  - それぞれの技術文書
- Other
  - どれにも属さない技術文書

### カテゴリ

投稿する記事のジャンルによって、Front Matter の categories 属性を設定しています。  
原則はディレクトリと同じになりますが、こちらは複数設定可能です。

### タグ

投稿する記事に関連するワードによって、Front Matter の tags 属性を設定しています。  
設定する値は著者の判断にお任せしますが、以下の３点はディレクトリによっては必須です。  
どのディレクトリにどの必須タグなのかは後述のディレクトリ構造を参照してください。

- Author
  - 著者名
- Level
  - 難易度。目安は以下としてください。
    - 初級は、LPIC100 ／ CCENT ／ AWS CLF
    - 中級は、LPIC200 ／ CCNA ／ AWS SAA
    - 上級は、LPIC300 ／ CCNP ／ AWS SAP
    - 超上級は、それぞれのご判断にお任せします。
- Type
  - Handson もしくは Knowledge

## 付録:Discord 通知

### 通知の著者名

Github 上の名前が反映されます。デフォルトはアカウント ID です。  
日本語名にしたい場合は、アカウント設定の Public profile の Name を変更してください。

### 通知の投稿内容文

Merge Message の本文が表示されます。複数行も表示可能です。(上記手順の 7-vi ～ 7-vii)  
なお、タイトル文は表示されません。
![Merge Message](./src-readme/MergeMessage.PNG)

### 通知の無効化

以下のどちらかの条件を満たすと Discord 通知を行わなくなります。

- プロジェクトのルートディレクトリに no_discord.txt を配置する
- Merge Message の本文に「no_discord」という文字列を含む(上記手順の 7-vi ～ 7-vii)

## 付録:hugo ディレクトリ構造

```
│
├── content
│   ├── _index.md         ・・・トップページの記事ファイル。
│   │
│   ├── cloud             ・・・Cloudカテゴリの記事を入れるディレクトリ。_index.mdはカテゴリトップページ。
│   │   ├── _index.md           必須タグ：Author、Level、Type
│   │   └── ＊＊＊.md
│   ├── general           ・・・Generalカテゴリの記事を入れるディレクトリ。_index.mdはカテゴリトップページ。
│   │   ├── _index.md           必須タグ：Author
│   │   └── ＊＊＊.md
│   ├── network           ・・・Networkカテゴリの記事を入れるディレクトリ。_index.mdはカテゴリトップページ。
│   │   ├── _index.md           必須タグ：Author、Level、Type
│   │   └── ＊＊＊.md
│   ├── other             ・・・Otherカテゴリの記事を入れるディレクトリ。_index.mdはカテゴリトップページ。
│   │   ├── _index.md           必須タグ：Author、Type
│   │   └── ＊＊＊.md
│   ├── server            ・・・Serverカテゴリの記事を入れるディレクトリ。_index.mdはカテゴリトップページ。
│   │   ├── _index.md           必須タグ：Author、Level、Type
│   │   └── ＊＊＊.md
│   └── tutorial          ・・・Tutorialカテゴリの記事を入れるディレクトリ。_index.mdはカテゴリトップページ。
│       ├── _index.md           必須タグ：Author
│       └── ＊＊＊.md
│
├── static
│   ├── file              ・・・画像以外の添付ファイルを入れるディレクトリ。記事内で/file/＝記事名＝/＊＊＊とすると参照する。
│   │   ├── ＝記事名＝
│   │   │    └＊＊＊.py
│   │   └── ＝記事名＝
│   │        └＊＊＊.zip
│   │
│   └── img               ・・・画像を入れるディレクトリ。記事内で/img/＝記事名＝/＊＊＊とすると参照する。
│       ├── ＝記事名＝
│       │    └＊＊＊.svg
│       └── ＝記事名＝
│            └＊＊＊.jpg
│
├── hugo.toml             ・・・公開Webページの設定ファイル。原則編集しない。
│
├── archetypes            ・・・記事ファイルのテンプレートファイル。原則編集しない。
│   ├── know.md
│   ├── handson.md
│   └── default.md
│
└── themes                ・・・テーマが入っているディレクトリ。原則編集しない。
    └── ＊＊＊
```

## 付録：ステージング環境について

### Discord 通知のテスト

Github の Web ページ上から設定を変更することで、自分の好きな環境で Discord 通知のテストができます。

> [!WARNING]
> ステージング環境ではテストする際に Github の設定から secrets の書き換えが必要になります。
> 書き換えると他の人がテストできないので注意してください。書き換え後に復元はできません。

1. Settings
2. Secrets and variables
3. Actions
4. Repository secrets → DISCORD_WEBHOOK_STAGING → 鉛筆マーク
5. Discord から発行してきた URL を貼り付ける

### 本番環境からの同期

Github の Web ページ上から Github Actions を手動で動かすことで、本番環境の内容を取り込みます。

> [!WARNING]
> ステージング環境の内容が上書きされるので注意してください。

> [!TIP]
> .github/workflow 内のファイルは.github/prod-workflow にコピーされます。
> 反映する場合は手動でコピーなどしてください。

1.  Actions
2.  SYNC-FROM-PROD
3.  Run workflow
4.  Run workflow
5.  Pull Request
6.  Changes by create-pull-request action を承認、マージ
