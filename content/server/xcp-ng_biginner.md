+++
#テンプレート：デフォルト
title = 'ZEN関連'
date = 2025-05-08T23:03:34+09:00
draft = false
#weight = 108 # 順番を意図的に変更したい場合は変更する
categories = ["Server"]
tags = ["Author:だるま", "Level:？？？", "Type:Handson", "仮想化"]
+++

# Xen備忘録

## Xcp-ngとは

* 特徴
  * CentOSベースのオープンソースの仮想化プラットフォーム
  *　おフランス製、英語のみ
  * WEB管理画面から仮想マシン・LXC（Linuxコンテナ）の管理が行える　単独ではCeffが使えない
  * 有償プランもあるが、無料でも利用できる
  * ノードを3台用意することでクラスターを構成できる
  * Proxmoxへのマイグレーションができる
  *　分離しているので高可用性
  *　最新版は8.3

* [Xcp-ng公式ページ](https://xcp-ng.org/#easy-to-install)

## 使い方

* 公式ページよりISOをダウンロードしてベアメタルサーバーにインストールします。

## Windowsの場合は
Xcp-ng　Centerをインストールして操作をするイメージです

## LINUXの場合は
仮想化のLINUXを作成してそこにXOをいれるイメージです
XCP-NG上にももちろん立てられる
※初期構築するときにはWEBサーバをいれないほうがいい
※XOのインストールでシェルを起動するのですがNGINXをいれるため、Apacheと混在するため

## 参考サイト
https://poohkidsparty.com/esxi-migration-xcp-ng/install/

