+++
title = '【Linux】No.2 基本操作編'
date = 2024-05-03T01:15:32+09:00
weight = 1
draft = false
categories = ["Tutorial"]
tags = ["Author:nakata", "LPIC1"]
+++

## 目次

- [目次](#目次)
- [概要](#概要)
  - [本ハンズオンで身につくこと](#本ハンズオンで身につくこと)
  - [出てくるコマンド](#出てくるコマンド)
- [ハンズオン](#ハンズオン)
  - [コンソール画面の表示](#コンソール画面の表示)
  - [操作の記録](#操作の記録)
  - [パッケージのアップデート](#パッケージのアップデート)
  - [httpd のインストール](#httpd-のインストール)
  - [httpd の起動、自動起動設定](#httpd-の起動自動起動設定)
  - [httpd の状態確認](#httpd-の状態確認)
  - [ブラウザでテスト画面の確認](#ブラウザでテスト画面の確認)
  - [index.html の作成](#indexhtml-の作成)
  - [ブラウザで画面が切り替わったこと確認](#ブラウザで画面が切り替わったこと確認)
  - [httpd の停止](#httpd-の停止)

## 概要

Linux の基本操作を WEB サーバを構築しながら触れていきましょう。

### 本ハンズオンで身につくこと

- 操作の記録設定
- パッケージのアップデート、インストール。
- httpd の起動、自動起動設定、停止操作。
- ファイルの作成、出力。
- ディレクトリの表示、移動。

### 出てくるコマンド

- script・・・操作ログの記録
- sudo・・・スーパーユーザの権限を使用
- dnf・・・パッケージマネージャー
- systemctl・・・サービス操作
- echo・・・変数または文字列の出力
- tee・・・標準入力をファイルに格納すると同時に標準出力を行う
- ls・・・ディレクトリおよびファイルの表示
- cat・・・ファイルの内容を表示
- cd・・・ディレクトリの移動

## ハンズオン

### コンソール画面の表示

No.1 インストール編を参考にしてコンソール画面を表示します。

### 操作の記録

ハンズオン中にトラブル発生した場合に備えて操作ログを保存する設定を行います。
操作ログを記録するには script コマンドを使用します。
下記のコマンドでは/tmp に tutorial というログファイルを作成し、これからの操作をすべて記録してくれます。

```bash
script /tmp/tutorial.log
```

### パッケージのアップデート

OS をインストールしたらまずはパッケージのアップデートをしましょう。\
パッケージのアップデートを行う時はパッケージマネージャを使用します。\
RedHat 系は yum/dnf コマンド。Debian 系は apt コマンドがパッケージマネージャを操作するコマンドになります。\
また、本コマンドはスーパーユーザ権限が必要なため、sudo コマンドを使用します。

```bash
sudo dnf update
```

### httpd のインストール

httpd(Apache)という WEB サーバのパッケージをインストールします。
パッケージをインストールにするには先ほど使用した dnf コマンドを使用します。

```bash
sudo dnf install httpd
```

### httpd の起動、自動起動設定

パッケージをインストールしただけでは httpd は動作しません。
httpd を動作させるために systemctl コマンドを使用します。

```bash
sudo systemctl enable httpd
sudo systemctl start httpd
```

enable は自動起動\
start は起動を行っています。

### httpd の状態確認

httpd が起動したか確認してみましょう。\
起動状態を確認する際も systemctl コマンドを使用します。\
画像のように active(running)と表示されれば OK です。\
"q"を入力したら、コマンドが入力ができる状態に戻ります。

```bash
sudo systemctl status httpd
```

![ステータス画面](/img/tutorial-linux-no2/active.png)

### ブラウザでテスト画面の確認

では、ブラウザ(FireFox)を使用し、テスト画面を確認しましょう。\
URL ウィンドウに下記の URL を入力し。画像のようなテスト画面が表示されれば OK です。

```bash
http://localhost:80
```

![テスト画面](/img/tutorial-linux-no2/test.png)

### index.html の作成

次は HTML ファイルを作成し、hello world と表示させてみましょう。\
まずは/var/www/html に移動します。ディレクトリの移動には cd コマンドを使用します。

```bash
cd /var/www/html
```

※pwd と入力し実行すると現在自分のいるディレクトリの絶対パスを確認できます。

/var/www/html 下にディレクトリもしくはファイルがないこと確認します。\
確認するには ls コマンド使用します。

```bash
ls -l
```

※オプション-l はファイルの詳細情報を表示させます。

なにもないことが確認できましたら、下記コマンドで現在のディレクトリに index.html を作成します。\
echo は指定した文字列や数値を出力します。\
tee は標準入力をファイルに保存すると同時に標準出力を行う。

```bash
echo "hello world" | sudo tee /var/www/html/index.html
```

もう一度 ls コマンドで確認してみましょう。\
index.html が表示されれば OK です。

最後に cat コマンドを使用し、index.html ファイルの内容を出力しましょう。

```bash
sudo cat /var/www/html/index.html
```

### ブラウザで画面が切り替わったこと確認

ブラウザで hello world と表示されるか確認しましょう。\
画像のように表示されれば OK です。

![helloworld](/img/tutorial-linux-no2/helloworld.png)

### httpd の停止

最後に systemctl コマンドを使用し、httpd を停止させます。

```bash
sudo systemstl stop httpd
```
