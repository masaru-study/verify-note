+++
title = 'Vm_multitier_architecture'
date = 2024-05-10T00:45:25+09:00
draft = false
weight = 1
categories = ["Network"]
tags = ["Author:DD Erikson", "Level:???", "Type:Knowledge", "Network Architecture"]
+++

# 三層アーキテクチャメモ

## 概要

ハイパーバイザーを 2 ノード、NAS を使用した VM のライブマイグレーションを可能にするネットワーク構成について記載します。

## 構成図

![構成図](/img/vm_multitier_architecture/network_archtecture_for_vm.png)

## 詳細

- Hypervisor（SV1,SV2）
  - オンボード 1GNIC・・・ILO と管理用リンクとして VLAN を設定して使用します。
  - 10GNIC（PCIe 2ports）・・・ストレージ用と Vmotion 用リンクとして使用します。
  - 1GNIC（PCIe）・・・サービス用リンクとして使用します。
- ネットワーク
  - サービスネットワーク・・・サービスの通信が流れる公開側ネットワークです。
  - Vmotion 用リンク・・・Vmotion 専用で Hypervisor の 10G ポート同士を接続します。
  - ストレージネットワーク・・・Hypervisor と NAS を接続します。
  - 管理ネットワーク・・・制御トラフィックと ILO のトラフィックを VLAN で分けてそれぞれ通信ができるよう設定
- 機器要件
  - ストレージ側スイッチ・・・10Gbase-T 又は 10Gbase-SX、LAG が組めると良い
  - 管理側 1G スイッチ・・・1Gbase-T、VLAN が必要
  - サービス側 1G スイッチ・・・1Gbase-T、VLAN、LAG が組めると良い
  - ルーター・・・お好きなものを Choice
  - NAS・・・10Gbase-T 又は 10Gbase-SX、NFS が使えると良い
- 冗長化構成について
- 冗長化構成を組む場合は NIC を 2 枚、LAG を組めるスイッチを使用します。
  - ストレージネットワーク側
    - 10GNIC は 2 ポートのものを 2 枚使用し、別々の NIC のポート同士でリンクを構成します。
    - NIC 故障に備え、10GNIC のそれぞれのポート同士でチーミングを行います。
  - サービスネットワーク側
    - NIC 故障に備え、1GNIC をチーミングし、1G スイッチで LAG を構成します。
    - VRRP を使用し、ゲートウェイ冗長化を行います。
      - WAN 側については別途考える必要があります・・・。
  - いずれにしてもスイッチについてはさらなる冗長化のため、スタック構成+LAG の構成や MLAG なども考慮できます。
