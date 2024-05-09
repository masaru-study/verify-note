+++
title = 'Vm_multitier_architecture'
date = 2024-05-10T00:45:25+09:00
draft = false
weight = 1
categories = ["Network"]
tags = ["Author:DD Erikson", "Level:???", "Network Architecture"]
+++
# 三層アーキテクチャメモ

## 概要

ハイパーバイザーを2ノード、NASを使用したVMのライブマイグレーションを可能にするネットワーク構成について記載します。

## 構成図

![構成図](/img/network_archtecture_for_vm.png)

## 詳細

* Hypervisor（SV1,SV2）
  * オンボード1GNIC・・・ILOと管理用リンクとしてVLANを設定して使用します。
  * 10GNIC（PCIe 2ports）・・・ストレージ用とVmotion用リンクとして使用します。
  * 1GNIC（PCIe）・・・サービス用リンクとして使用します。
* ネットワーク
  * サービスネットワーク・・・サービスの通信が流れる公開側ネットワークです。
  * Vmotion用リンク・・・Vmotion専用でHypervisorの10Gポート同士を接続します。
  * ストレージネットワーク・・・HypervisorとNASを接続します。
  * 管理ネットワーク・・・制御トラフィックとILOのトラフィックをVLANで分けてそれぞれ通信ができるよう設定
* 機器要件
  * ストレージ側スイッチ・・・10Gbase-T又は10Gbase-SX、LAGが組めると良い
  * 管理側1Gスイッチ・・・1Gbase-T、VLANが必要
  * サービス側1Gスイッチ・・・1Gbase-T、VLAN、LAGが組めると良い
  * ルーター・・・お好きなものをChoice
  * NAS・・・10Gbase-T又は10Gbase-SX、NFSが使えると良い
* 冗長化構成について
* 冗長化構成を組む場合はNICを2枚、LAGを組めるスイッチを使用します。
  * ストレージネットワーク側
    * 10GNICは2ポートのものを2枚使用し、別々のNICのポート同士でリンクを構成します。
    * NIC故障に備え、10GNICのそれぞれのポート同士でチーミングを行います。
  * サービスネットワーク側
    * NIC故障に備え、1GNICをチーミングし、1GスイッチでLAGを構成します。
    * VRRPを使用し、ゲートウェイ冗長化を行います。
      * WAN側については別途考える必要があります・・・。
  * いずれにしてもスイッチについてはさらなる冗長化のため、スタック構成+LAGの構成やMLAGなども考慮できます。
