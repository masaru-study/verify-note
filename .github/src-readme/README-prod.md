# ハンズオンチームの秘密基地

- [ここはなに？](#ここはなに)
- [つかいかた](#つかいかた)
  - [01-インフラ初期設定](#01-インフラ初期設定)
  - [02-ローカルリポジトリ初期設定](#02-ローカルリポジトリ初期設定)
  - [03-記事作成](#03-記事作成)
  - [04-公開](#04-公開)
  - [05-公開 Web ページを確認して更新されていれば完了](#05-公開-web-ページを確認して更新されていれば完了)
- [付録:ページ作成ルール](#付録ページ作成ルール)
  - [ディレクトリ](#ディレクトリ)
  - [カテゴリ](#カテゴリ)
  - [タグ](#タグ)
- [付録:Discord 通知](#付録discord-通知)
  - [通知の著者名](#通知の著者名)
  - [通知の投稿内容文](#通知の投稿内容文)
  - [通知の無効化](#通知の無効化)
- [付録:hugo ディレクトリ構造](#付録hugo-ディレクトリ構造)

## ここはなに？

まさるの勉強部屋参加者のハンズオンチームの検証結果置き場です。

## つかいかた

権限のあるメンバーは hugo を使用して記事を投稿できます。

> [!TIP]
> ２回目以降の記事投稿は[03-記事作成](./src-readme/03-createreport.md)／[04-公開](./src-readme/04-release-prod.md)
> を行ってください。

> [!IMPORTANT]
> 手順内の"お名前"や"onamae"と言った文字列は任意の文字列に書き換えてください。

### [01-インフラ初期設定](./src-readme/01-firstsetup.md)

### [02-ローカルリポジトリ初期設定](./src-readme/02-localsetup-prod.md)

### [03-記事作成](./src-readme/03-createreport.md)

### [04-公開](./src-readme/04-release-prod.md)

### [05-公開 Web ページ](https://masaru-study.github.io/verify-note/)を確認して更新されていれば完了

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

Merge Message の本文が表示されます。複数行も表示可能です。([04-公開](./src-readme/04-release-prod.md)の 7.~8.)
なお、タイトル文は表示されません。
![Merge Message](./src-readme/img/MergeMessage.PNG)

### 通知の無効化

以下のどちらかの条件を満たすと Discord 通知を行わなくなります。

- プロジェクトのルートディレクトリに no_discord.txt を配置する
- Merge Message の本文に「no_discord」という文字列を含む([04-公開](./src-readme/04-release-prod.md)の 7.~8.)

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
