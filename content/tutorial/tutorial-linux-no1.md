+++
title = '【Linux】No.1 インストール編'
date = 2024-05-03T01:15:32+09:00
weight = 1
draft = false
categories = ["Tutorial"]
tags = ["Author:あゆむ", "LPIC1", "LinuC1"]
+++

## 目次

- [目次](#目次)
- [概要](#概要)
  - [本ハンズオンで身につくこと](#本ハンズオンで身につくこと)
  - [事前準備](#事前準備)
  - [構成図](#構成図)
- [ハンズオン](#ハンズオン)
  - [Oracle VM VirtualBox のダウンロード](#oracle-vm-virtualbox-のダウンロード)
  - [仮想マシンイメージのダウンロード](#仮想マシンイメージのダウンロード)
  - [VirtualBox のインストール](#virtualbox-のインストール)
  - [仮想マシンイメージのインポート](#仮想マシンイメージのインポート)
  - [仮想マシンの起動](#仮想マシンの起動)
  - [仮想マシン内 Linux のパスワード変更](#仮想マシン内-linux-のパスワード変更)
  - [仮想マシン内 Linux の CLI 操作](#仮想マシン内-linux-の-cli-操作)
  - [仮想マシン内 Linux の シャットダウン／再起動](#仮想マシン内-linux-の-シャットダウン再起動)

## 概要

Linux の操作する環境をパソコン上に用意しましょう。

### 本ハンズオンで身につくこと

- 仮想化ソフトウェア(VirtualBox)のインストール
- 仮想マシンのインポート
- 仮想マシンの起動
- Linux の CLI 操作
- Linux の シャットダウン／再起動

### 事前準備

- 高速なインターネット環境（目安は下り 100Mbps）
- 中くらいのスペックのパソコン
  - CPU：4 コア以上
  - メモリ：8GB 以上
  - ストレージ：30GB 以上の空き
  - OS：Windows10 もしくは 11

### 構成図

{{< figure src="/img/tutorial-linux-no1/fig-diagram.drawio.svg" title="fig-diagram" class="center" width="50%" height="50%" >}}

## ハンズオン

### Oracle VM VirtualBox のダウンロード

以下の URL を開き、上部にある「VirtualBox xx.x.xx platform packages」の「Windows hosts」を選択して
インストーラーをパソコン上にダウンロードします。

[Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads)

### 仮想マシンイメージのダウンロード

以下の URL を開き、一覧にある「AlmaLinux8.9_v2.ova」をインストーラーをパソコン上にダウンロードします。

{{% notice style="note" %}}
容量は約 4.4GB あります。ご注意ください。
{{% /notice %}}

[Google Drive ハンズオンチーム](https://drive.google.com/drive/folders/1fWVWWtukEUQca8SfQ2roBza9jYE5-fMd?usp=sharing)

### VirtualBox のインストール

インストーラーをダブルクリックしてインストールを行います。  
下記の画像と同じ画面が出た場合は「はい」を推します。

{{< figure src="/img/tutorial-linux-no1/uac.png" title="UAC" class="center" width="50%" height="50%" >}}

ウィザード内の進め方は以下です。

1. Next
2. Next
3. Yes
4. Yes
5. Install
6. Finish

終わった後にウインドウが表示されたら成功です。

{{< figure src="/img/tutorial-linux-no1/vbox_manager.png" title="VBOX Manager" class="center" width="50%" height="50%" >}}

### 仮想マシンイメージのインポート

ウインドウ上部にある「インポート」ボタンを押します。ウインドウが表示されたら、
先ほどダウンロードした仮想マシンイメージを選択して「次へ」ボタンを押します。

{{< figure src="/img/tutorial-linux-no1/vbox_import1.png" title="VM Import 1" class="center" width="50%" height="50%" >}}

詳細な設定画面が表示されますが、変更せず「完了」ボタンを押します。

{{< figure src="/img/tutorial-linux-no1/vbox_import2.png" title="VM Import 2" class="center" width="50%" height="50%" >}}

前の画面に戻り、進捗が表示されます。約 10 分ほどで完了します。
完了したら、以下の画面になります。

{{< figure src="/img/tutorial-linux-no1/vbox_import3.png" title="VM Import Complete" class="center" width="50%" height="50%" >}}

### 仮想マシンの起動

ウインドウ上部にある「起動」ボタンを押します。新しいウインドウが表示されて仮想マシンが起動します。
少し待つと以下のようなログイン画面になりますので、「study」ユーザーを選択してパスワードを入力します。

{{< figure src="/img/tutorial-linux-no1/vm_start.png" title="VM Login" class="center" width="50%" height="50%" >}}

{{% notice style="info" %}}
初期パスワードは　 Rainbow 　です。
{{% /notice %}}

{{% notice style="tip" %}}
VirtualBox 内の仮想マシンを操作中はマウスカーソルが仮想マシン内しか動かせません。  
元に戻りたい場合は、キーボード右下の Ctrl キーを押してください。
{{% /notice %}}

### 仮想マシン内 Linux のパスワード変更

{{% notice style="red" %}}
この項目の方法は本ハンズオン利用環境固有の操作です。実環境で操作する場合は
ほとんどの場合が CLI になりますが、セキュリティ面を考慮してわかりやすい方法を記載しています。
{{% /notice %}}

画面右上の ▼ を押します。メニュー内で「study」を選択し「アカウント設定」を押します。

{{< figure src="/img/tutorial-linux-no1/vm_pwchange.png" title="VM Password Change" class="center" width="50%" height="50%" >}}

表示されたウインドウでパスワード欄の ●●●●● となっている部分を押してパスワードを変更します。

### 仮想マシン内 Linux の CLI 操作

画面右上の「アクティビティ」を押し、暗転した画面の左側にアイコンが並びます。その中から「端末」を押して起動します。

{{< figure src="/img/tutorial-linux-no1/terminal.png" title="terminal launch" class="center" width="50%" height="50%" >}}

{{% notice style="info" %}}
CLI 操作を行う為に必要な黒背景に白文字のウインドウのことを「端末」「ターミナル」「コンソール」と呼びます。今後は頻出します。
{{% /notice %}}

### 仮想マシン内 Linux の シャットダウン／再起動

端末が表示されたら、以下の表示なるようにキーボードで文字を入力します。入力が終わったらエンターキーを押して実行します。
次にパスワードが求められるのでパスワードを入力します。

```bash
[study@localhost ~]$ sudo shutdown -h now
```

{{< figure src="/img/tutorial-linux-no1/shutdown.png" title="terminal launch" class="center" width="50%" height="50%" >}}

{{% notice style="note" %}}
パスワードを入力した後にエンターを押したらすぐにシャットダウンされます。開いているものは事前に閉じてください。
{{% /notice %}}

{{% notice style="tip" %}}
再起動を行いたい場合は入力コマンドの -h を -r に変えてください。
{{% /notice %}}
