+++
title = '【チュートリアル】Linuxハンズオンversion1'
date = 2024-05-03T01:15:32+09:00
weight = 1
draft = false
categories = ["Server"]
tags = ["Author:nakata", "Level:初級", "LPIC1"]
+++
## 目次

- [概要](#概要)
    - [本ハンズオンで行う基本操作](#本ハンズオンで行う基本操作)
    - [出てくるコマンド](#出てくるコマンド)
- [ハンズオン](#ハンズオン)
    - [コンソール画面の表示](#コンソール画面の表示)
    - [操作の記録](#操作の記録)
    - [パッケージのアップデート](#パッケージのアップデート)
    - [httpdのインストール](#httpdのインストール)
    - [httpdの起動、自動起動設定](#httpdの起動、自動起動設定)
    - [httpdの状態確認](#httpdの状態確認)
    - [ブラウザでテスト画面の確認](#ブラウザでテスト画面の確認)
    - [index.htmlの作成](#indexhtmlの作成)
    - [ブラウザで画面が切り替わったこと確認](#ブラウザで画面が切り替わったこと確認)
    - [httpdの停止](#httpdの停止)

## 概要
Linuxの基本操作をWEBサーバを構築しながら触れていきましょう。

### 本ハンズオンで行う基本操作
* 操作の記録設定
* パッケージのアップデート、インストール。
* httpdの起動、自動起動設定、停止操作。
* ファイルの作成、出力。
* ディレクトリの表示、移動。

### 出てくるコマンド
* script・・・操作ログの記録
* sudo・・・スーパーユーザの権限を使用
* dnf・・・パッケージマネージャー
* systemctl・・・サービス操作
* echo・・・変数または文字列の出力
* tee・・・標準入力をファイルに格納すると同時に標準出力を行う
* ls・・・ディレクトリおよびファイルの表示
* cat・・・ファイルの内容を表示
* cd・・・ディレクトリの移動

## ハンズオン

### コンソール画面の表示
はじめに画像のように左上のアイコンをクリックすると、左側にメニュー欄が表示されるので、端末のアプリをクリックします。

![start](/img/tutorial-linux-hands-on-verson1/start.png)

### 操作の記録
ハンズオン中にトラブル発生した場合に備えて操作ログを保存する設定を行います。
操作ログを記録するにはscriptコマンドを使用します。
下記のコマンドでは/tmpにtutorialというログファイルを作成し、これからの操作をすべて記録してくれます。
```bash
script /tmp/tutorial.log
```

### パッケージのアップデート
OSをインストールしたらまずはパッケージのアップデートをしましょう。\
パッケージのアップデートを行う時はパッケージマネージャを使用します。\
RedHat系はyum/dnfコマンド。Debian系はaptコマンドがパッケージマネージャを操作するコマンドになります。\
また、本コマンドはスーパーユーザ権限が必要なため、sudoコマンドを使用します。
```bash
sudo dnf update
```

### httpdのインストール
httpd(Apache)というWEBサーバのパッケージをインストールします。
パッケージをインストールにするには先ほど使用したdnfコマンドを使用します。
```bash
sudo dnf install httpd
```

### httpdの起動、自動起動設定
パッケージをインストールしただけではhttpdは動作しません。
httpdを動作させるためにsystemctlコマンドを使用します。
```bash
sudo systemctl enable httpd
sudo systemctl start httpd
```
enableは自動起動\
startは起動を行っています。

### httpdの状態確認
httpdが起動したか確認してみましょう。\
起動状態を確認する際もsystemctlコマンドを使用します。\
画像のようにactive(running)と表示されればOKです。\
"q"を入力したら、コマンドが入力ができる状態に戻ります。
```bash
sudo systemctl status httpd
```
![ステータス画面](/img/tutorial-linux-hands-on-verson1/active.png)

### ブラウザでテスト画面の確認
では、ブラウザ(FireFox)を使用し、テスト画面を確認しましょう。\
URLウィンドウに下記のURLを入力し。画像のようなテスト画面が表示されればOKです。
```bash
http://localhost:80
```
![テスト画面](/img/tutorial-linux-hands-on-verson1/test.png)

### index.htmlの作成
次はHTMLファイルを作成し、hello worldと表示させてみましょう。\
まずは/var/www/htmlに移動します。ディレクトリの移動にはcdコマンドを使用します。

```bash
cd /var/www/html
```
※pwdと入力し実行すると現在自分のいるディレクトリの絶対パスを確認できます。

/var/www/html下にディレクトリもしくはファイルがないこと確認します。\
確認するにはlsコマンド使用します。

```bash
ls -l
```
※オプション-lはファイルの詳細情報を表示させます。

なにもないことが確認できましたら、下記コマンドで現在のディレクトリにindex.htmlを作成します。\
echoは指定した文字列や数値を出力します。\
teeは標準入力をファイルに保存すると同時に標準出力を行う。

```bash
echo "hello world" | sudo tee /var/www/html/index.html
```

もう一度lsコマンドで確認してみましょう。\
index.htmlが表示されればOKです。

最後にcatコマンドを使用し、index.htmlファイルの内容を出力しましょう。

```bash
sudo cat /var/www/html/index.html
```

### ブラウザで画面が切り替わったこと確認
ブラウザでhello worldと表示されるか確認しましょう。\
画像のように表示されればOKです。

![helloworld](/img/tutorial-linux-hands-on-verson1/helloworld.png)

### httpdの停止
最後にsystemctlコマンドを使用し、httpdを停止させます。

```bash
sudo systemstl stop httpd
```
