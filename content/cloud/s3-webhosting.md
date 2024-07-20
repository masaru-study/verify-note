+++ #テンプレート：ハンズオン
title = 'Amazon S3 簡易Webサーバー'
date = 2024-07-14T14:50:00+09:00
draft = true
#weight = 99 # 順番を意図的に変更したい場合は変更する
categories = ["Cloud"]
tags = ["Author:あゆむ", "Type:Handson", "Level:初級", "AWS", "S3", "Web"]
+++

- [概要](#概要)
  - [構成図](#構成図)
  - [本ハンズオンで身につくこと](#本ハンズオンで身につくこと)
  - [出てくる用語](#出てくる用語)
  - [出てくるコマンド／操作](#出てくるコマンド操作)
  - [事前準備](#事前準備)
- [ハンズオン](#ハンズオン)
  - [S3 バケットの作成](#s3-バケットの作成)
  - [yyy](#yyy)
  - [zzz](#zzz)

## 概要

AWS の代表的なサービス Amazon S3(以後、S3) はオブジェクトストレージと呼ばれるサービスです。
主にデータを保管する為のサービスですが、保管以外の使い方もできます。
それが**簡易 Web サーバー機能**です。  
機能を有効にすると、Web ブラウザで指定された URL を開くことでバケット内に保管されているファイルが Web ページとして表示されます。

{{% notice style="tip" %}}
クライアント側で表示内容を動的に生成する仕組みは利用できます。  
例：JavaScript
{{% /notice %}}

{{% notice style="warning" %}}
サーバー側で表示内容を動的に生成する仕組みは利用できません。  
例：php、CGI
{{% /notice %}}

### 構成図

{{< figure src="/img/s3-webhosting/overview.drawio.svg" class="center" width="100%" height="100%" >}}

### 本ハンズオンで身につくこと

- S3 バケットの作成方法
- AWS CLI で S3 バケットへのアップロード方法
- 簡易 Web サーバーの有効化方法

### 出てくる用語

| 用語        | 通称 | 意味                                   |
| ----------- | ---- | -------------------------------------- |
| Amazon S3   | S3   | オブジェクトストレージのサービス       |
| AWS CLI     | -    | AWS を CLI で操作するツール            |
| S3 バケット | -    | データを格納する場所。単位のようなもの |

### 出てくるコマンド／操作

- AWS マネージメントコンソール操作
- AWS CLI コマンド

### 事前準備

- AWS アカウント自体
- S3 を操作できる権限を持つ IAM ユーザー
- AWS CLI をインストールしたパソコン
- 適当な index.html ファイル

```html
<html>
  <head>
    <title>テストページ</title>
  </head>
  <body>
    <br />
    <br />
    これはテストページです。<br />
    <br />
    新しいサイトのチェックに使います。<br />
    <br />
    <br />
  </body>
</html>
```

## ハンズオン

### S3 バケットの作成

AWS マネージメントコンソールにログインして S3 をページを開きます。
「バケットの作成」を押します。

{{< figure src="/img/s3-webhosting/s3-start.png" class="center" width="100%" height="100%" >}}

バケットの初期設定を入力します。[ページ全体はこちら。](/img/s3-webhosting/s3-create-origin.png)記載の無い項目は既定値です。  
まずは、バケット名
{{< figure src="/img/s3-webhosting/s3-create-1.png" class="center" width="100%" height="100%" >}}

次に、パブリックアクセスの許可。チェックを外します。下の同意にチェックを入れます。
{{< figure src="/img/s3-webhosting/s3-create-2.png" class="center" width="100%" height="100%" >}}

{{% notice style="warning" %}}
この項目を設定することで全世界に向けて公開します。取り扱うファイルにはご注意ください。
{{% /notice %}}

最後に、「バケットの作成」を押します。

{{< figure src="/img/s3-webhosting/s3-create-3.png" class="center" width="100%" height="100%" >}}

### yyy

### zzz
