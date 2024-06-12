+++
#テンプレート：ハンズオン
title = 'VM_routing'
date = 2024-05-30T00:06:24+09:00
draft = false
#weight = 99 # 順番を意図的に変更したい場合は変更する
categories = ["Server", "Network"]
tags = ["Author:yoshihiro", "Level:中級", "Type:Handson", "ESXi"]
+++

- [概要](#概要)
- [ハンズオン](#ハンズオン)
  - [Switchのvlan設定](#switchのvlan設定)
  - [物理構成図](#物理構成図)
  - [論理構成図](#論理構成図)

## 概要
ESXi、物理スイッチ、物理ルーター間でのルーティング


## ハンズオン
ESXi、物理ルーター、物理スイッチ間でのvlan間ルーティングに挑戦します。

### Switchのvlan設定
![TP-Link(Switch)](/img/VM_routing/Switch_config.png)


### 物理構成図
![物理構成図](/img/VM_routing/物理構成図.png)


### 論理構成図
![論理構成図](/img/VM_routing/論理構成図.png)