+++
title = 'VLAN間ルーティングについて'
date = 2024-05-11T21:23:09+09:00
draft = false
weight = 2000
categories = ["Network"]
tags = ["Author:DD Erikson", "Type:Handson", "Level:中級", "Routing", "VLAN", "CCNA"]
+++

# VLAN間ルーティングについて

## 概要

* VLANとVLANをつなぐVLAN間ルーティングについて記載します。

## 構成図

![構成図](/img/vlan_routing/VLANkousei.png)

* 左がポートベースVLANの場合
* 右がタグVLANを使用した場合
  * 右のような構成（トポロジー）をルータ　オン・ア・スティック呼びます。

## 使われているものについて

![Router and Switch](/img/vlan_routing/routing_VLAN.png)

* ルーターとは
  * ルーターは基本的な機能として、ルーティング（経路の決定）を行います。ルーティングはOSI参照モデルのレイヤー３にあたる機能です。基本的に直接接続されたインターフェースについては自動でパケットの配送を行います。
  * 古くはマルチプロトコルルーターと呼ばれ、様々なプロトコルをつなぐ役割がありました。現在でも様々な方式の接続を収容します。
* VLANとは
  * スイッチングハブにて、LANを仮想的に分割するレイヤー2にあたる機能です。（ブロードキャストドメインを分割するとも言われる）IEEE802.1QやCiscoの独自規格であるISLがあります。
  * 主にタグをつけてポートから送出するタグVLANとポートが特定のVLANになるポートベースVLANがあります。
* これら2つの機能を取り入れたものがL3スイッチです。
  * L3スイッチはルーティングとスイッチングに特化していますが、ルーターとは違い様々なプロトコルには対応していません。
  * よく言われるルーターとの違いは他にもあり、ルーターはソフトウェアでルーティングをしているが、L3スイッチはハードウェア（ASIC）でルーティングを行うと言われます。

## 設定の概要

![設定](/img/vlan_routing/Setting.png)

### ルーターの設定

* ルーターは1つのポートにサブインターフェースを持たせてVLAN10とVLAN20に分けます。
  * サブインターフェースとは、メインインターフェースの中で複数のインターフェースを設定する方法です。主にサブインターフェースはVLANの設定を行うために設定します。
* VLAN10に192.168.10.0/24、VLAN20に192.168.20.0/24のネットワークを設定します。

### スイッチの設定 

* スイッチの1番ポートをVLAN10と20のトランクポートに設定し、2,3を10、4,5を20にします。

### PC

* 1番ポートにルーター、2番ポートにPC A（192.168.10.2）、5番ポートにPC B（192.168.20.2）を接続します。それぞれ192.168.10.1、192.168.20.1をデフォルトゲートウェイとして設定をします。

## VLAN間ルーティングをする

### ルーターのルーティングテーブル

* ルーティングについて
  * ルーティングの優先度が存在しています。以下の順序で評価されます。
    1. ロンゲストマッチ
       * サブネットマスク長が長いものを優先的に使用します。
       * 例えば、192.168.1.0/24へのルートと192.168.1.10/32へのルートの2つが設定されている場合、後者の/32のルートが優先的に処理されます。
    2. AD値（あどみにすとれーてぃぶでぃすたんす値）
       * プロトコルによって決められている優先度）
       * よくテキストに現れる主なAD値は以下の通りです。

          |種類|AD値
          |:---:|:---:
          |直接接続|0
          |スタティックルート|1
          |EIGRP|5
          |eBGP|20
          |EIGRP|90
          |OSPF|110
          |IS-IS|115
          |RIP|120
          |不明|255

    3. メトリック
       * コストとか距離とか言われるやつ。ルーティングプロトコルによって違う
    * 優先度の数値は低い順に優先されます。
* 今回のルーティングテーブル

    ```
    router(config)# show ip route
    IP Routing Table - 2 entries, 1 hidden, 2045 frees
    Entries: 2 Connected, 0 Static, 0 RIP, 0 OSPF, 0 BGP
    Codes: C - Connected, S - Static, R - RIP, O - OSPF, IA - OSPF inter area
          N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
          E1 - OSPF external type 1, E2 - OSPF external type 2, B - BGP
          * - Candidate default, s - Summary
    Timers: Age
    C    192.168.10.0/24 [0/0] is directly connected, GigaEthernet1.1, 0:54:35
    C    192.168.20.0/24 [0/0] is directly connected, GigaEthernet1.2, 0:54:35
    router(config)# 
    ```

* L3レベルでは、192.168.10.0/24と192.168.20.0/24が直接接続されているので優先的にルーティングされると予想できます。
* ルーターレベルで192.168.10.1（GigaEthernet1.1）と192.168.20.1（GigaEthernet1.2）が設定されており、直接接続されている（インターフェースがアップしている・有効になっている）ので、このインターフェースに来たパケットはルーティング処理されて転送されると予想できます。

### スイッチについて

* ルーター側ではサブインターフェースを使用してVLANが設定され、1番ポートがトランクポートとして機能しており、対向にスイッチの1番ポートが接続されています。（ルーター1番ポートからスイッチ1番ポートへのリンクが存在）
* スイッチの1番ポートはVLAN10とVLAN20のトランクポートになっています。
* 2番ポートにPC Aが接続されており、通信を待ち受ける/送出ができる状態になっています。
* 5番ポートにPC Bが接続されており、通信を待ち受ける/送出ができる状態になっています。
* スイッチではルーティング機能は無効になっているため、L2レベルではフレームの転送を行っても、L3レベルのパケットの転送は行いません。

### この状況でPC BからAに対してPingを打つ

![Packet Tracer Routing](/img/vlan_routing/vlanrouting2.gif)

1. Bの192.168.20.2より192.168.10.2に対してPingを行います。
2. Bのルーティングテーブルでは、デフォルトゲートウェイが192.168.20.1となっており、ほかのルーティング情報はないため、192.168.20.1に対して192.168.10.2宛のICMPパケットを送ります。
   * Bのイーサネットポートからフレームが送出され、接続されているスイッチの5番ポートに向けて転送されます。スイッチでは5番ポートで受け取ったらスイッチ内のMACアドレステーブルを参照し、ルーターのMACアドレスがある1番ポートからタグをつけた上で送出されます。  
   * この時、ARPテーブル（IPアドレスとMACアドレスの対応表）が空の場合は、192.168.20.1があるルーターのMACアドレスを知るためにまずはARPをして問い合わせを行います。  
3. ルーターでは、GigaEthernet0/0.2でフレームを受け取ってタグを外し、ICMPデータを192.168.20.1から直接接続されている192.168.10.1に転送します。  
4. 192.168.10.1より送出するためにデータを受け取ったGigaethernet0/0.1はタグをつけつつフレームを送出します。このときの宛先MACアドレスはPC Aです。
5. Aに向けて送出されたフレームはスイッチの1番ポートに入り、タグを外しつつMACアドレステーブルを参照した上で、AのMACアドレスがある2番ポートから送出されます。
6. スイッチの2番ポートを出たフレームはAのイーサネットインターフェースに入ります。
7. 晴れてBから送出されたICMPのデータはAに到着しました。（この後同様に折り返しがあり、Bに到着しPingの応答結果として現れます。）

### まとめ

* VLANを構成している以上L2装置であるスイッチングハブではVLAN間の通信はできない。
* VLAN間ルーティングとは、VLANとVLANの間でパケットの交換を行うこと
* LAN間もVLAN間も直接接続をされている以上同様の動きをする。
* 基本的に扱いは変わらないので不必要に怯える必要は無いはず。

詳しく書こうとしたところ読んでいてわからなくなったため、だいぶ端折った内容になりましたが、もし間違っている部分があればご指摘ください。マサカリお待ちしております…。