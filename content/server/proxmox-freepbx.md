+++
title = "Proxmox-FreePBX"
date = 2025-04-06T11:56:41+09:00
weight = 6
draft = false
categories = ["Server"]
tags = ["Author:だるま", "Level:中級", "Type:Knowledge", "Proxmox"]
+++

## Proxmox上にFreePBXでSIPサーバを立てる

### 目的

※　現場でSIPサーバをたてる可能性があり、IP電話やVoIP環境の勉強のために作成しました
※　いろいろ、詰まったところや、TIPSなどを備忘的に残します

### 事前準備

FreePBX公式サイト参照から以下isoファイルをダウンロードします

- [URL](https://www.freepbx.org/downloads/)

SNG7-PBX16-64bit-2302-1をしようしています

### Proxmoxへのインストール

- cpu 2core hostを使用する
- Mem 4GB
※　最新版PBX17Betaも出ているようでした

### PBXのインストール時に注意

- インストール自体はそのまますんなり入ります
- しかし、モジュールのアップデートが依存関係などがあったり有償版のものもあるので、一撃ではUpdateできない点は注意
- また、インストールすぐにはアップデートの機能しか使えない点も注意

### PBXとは（FreePBX）

- PBXとは「電話交換機」のことで、内線同士の接続や外線と内線の接続を制御するハードウェアを指します
- クラウド型PBXを利用したAsteriskというOSSのようなので、多機能
- 多機能すぎて、初見何をやったらいいのかわからなかったｗ

### 2台の内線環境を設定して、お互いに通信をする

イメージ図
{{< figure src="/img/proxmox-freepbx/proxmox-freepbx.png" title="proxmox-freepbx" class="center" width="50%" height="50%" >}}

### ＩＰ電話のアプリ

ＰＣ１
Jitsi  (ジッチ)

- [URL](https://desktop.jitsi.org/Main/Download.html)

※32bit版ですが使えました。

携帯（iPhoneおよびAndroid）
AGEphone　（昔使っていたので）

- [URL](https://www.ageet.com/agephone)

Zoiper

- [URL](https://www.zoiper.com/en/voip-softphone/download/current)

※両方ともWinodws版もあるので、お好きなものを使用してください

## TIPS

- 日本語にすると、微妙にわかりづらいことがある
- コンソール機能がある
- SSHで接続は可能
- FirewallはiPtablesを使用
- GUIで状況がわかりやすい

### 使えるコマンド

- ファイヤーウォールの状況をみる

```bash
iptables -L
```

- asterisk -rvvvvv　asterisk のログをみる

```bash
asterisk -rvvvvv
```
