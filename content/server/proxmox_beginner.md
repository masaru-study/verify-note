+++
#テンプレート：デフォルト
title = 'Proxmox関連'
date = 2024-06-12T23:03:34+09:00
draft = false
#weight = 99 # 順番を意図的に変更したい場合は変更する
categories = ["Server"]
tags = ["Author:DD_Erikson", "Level:？？？", "Type:Handson", "任意文字列"]
+++

# Proxmox備忘録

![proxmox](/img/proxmox_beginner/proxmox-full-lockup-inverted.png)

## Proxmox Virtual Environmentとは

* 特徴
  * Debianベースのオープンソースの仮想化プラットフォーム
  * WEB管理画面から仮想マシン・LXC（Linuxコンテナ）の管理が行える
  * エンタープライズリポジトリとサポートチケットがついた有償プランもあるが、無料でも利用できる
  * ノードを3台用意することでクラスターを構成できる
    * クラスターを構成することでHAやライブマイグレーションができる

* [PROXMOX公式ページ](https://www.proxmox.com/en/)

## 使い方

* 公式ページよりISOをダウンロードしてベアメタルサーバーにインストールします。

## 後日追記します

## GUIでできる！クラスターの構成について

* クラスターでできること
  * クラスタ内の全てのノードの操作を一括で行える
  * HA（3台構成時）
  * マイグレーション
  * ストレージ共有
  * Ceph（分散ストレージの一種）
* 手順
  * クラスタを作成するノードの管理画面を開く
  * 管理画面の左側ペインにあるデータセンターより、クラスタを選択
  * クラスタを作成を押すと作成できる。
  * 作成したクラスタをクリックし、Join情報をクリックし、コピーする。
  * 参加側管理画面を開き、左側ペインのデータセンターよりクラスタを選択
  * クラスタに参加をクリックし、先ほどコピーしたJoin情報を貼り付ける
  * クラスタを作成したノードのパスワードを入力する
  * クラスタを作成したノードの管理画面を開き、ノードが参加できていればOK

## クラスターの解除について

* 【CLI】クラスター参加しているノードで以下のコマンドを行う

  ```
  systemctl stop pve-cluster
  systemctl stop corosync
  pmxcfs -l
  rm -rf /etc/pve/corosync.conf
  rm -rf /etc/corosync/*
  killall pmxcfs
  systemctl start pve-cluster

  //WEB UI上から削除する
  rm -rf /etc/pve/nodes/（ノードの名前）/
  ```

* 2台クラスター構築時に1台落ちている時にVMを起動するとcluster not ready - no quorum? (500)が表示される件について
  * https://forum.proxmox.com/threads/another-cluster-not-ready-no-quorum-500-case.56104/
  * pvecm expected 1をすると一時的に定足数を増やすことができる。クォーラムがないノードではこれをいれる必要がある。
* corosync.confの編集方法
  * https://pve.proxmox.com/pve-docs/pve-admin-guide.html#pvecm_edit_corosync_conf
