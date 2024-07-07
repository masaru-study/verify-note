+++
title = 'IPSec/VPN'
date = 2024-05-19T18:26:51+09:00
draft = false
weight = 3000
categories = ["Network"]
tags = ["Author:DD Erikson", "Type:Handson", "Level:中級", "VPN", "YAMAHA", "Cisco"]
+++
# IPSec/VPN

## 概要

みんな大好き<font color="yellow">IPSec/VPN</font>について書いていきます。

## IPSec/VPNの語について

* IPSecとは
  * Security Architecture for internet Protocol(IP Security Architectureなどとも)
  * 認証、暗号化や改ざん検知を実現するためのプロトコルスイーツ（プロトコル群）
    * この中にはいろいろなものが含まれるが後述
* VPNとは
  * Virtual Private Network
  * 仮想的にプライベートネットワークに接続すること
  * 「VPNを組む」とか「VPNを通る」とか「VPNをつなげる」とか言われる
  * VPNの種類
    * 閉域網、回線事業者の網を利用する。
      * 専用線
      * 広域イーサネット
      * IP-VPN
      * エントリーVPN
    * インターネットVPN・・・文字通りインターネットを利用する。レイヤ3でつなげる。IPSec/VPNはここで使用される。

## IPSec以外のVPNについて

* SSL/VPN
  * みんながもっと大好きなSSL/TLSを使用したVPNの仕組み
  * 443番ポートを使用するのでファイアウォールを貫通できたりすることもある
  * リバースプロキシ・ポートフォワーディング・L2フォワーディングの3種類が存在している。
* IPIP
  * IP over IP
  * 名前の通りIPとIPでつなぐVPNで、暗号化などは存在しない
  * IPv4/IPv6デュアルスタック接続のためのトンネルなどで活用される印象
  * 拠点間接続の形となる
* PPTP
  * Point to Point Tunneling Protocol
  * Microsoftによって提案されたプロトコル
  * PPPを拡張して2点間をつなぐVPN接続
  * GRE(Generic Routing Encapsulation)とMPPE(Microsoft Point-to-Point Encryption)を使用してPPPフレームをカプセリングして暗号化します
  * 拠点間接続とリモートアクセスが可能
* L2TPv3
  * Layer 2 Tunneling Protocol version 3
  * レイヤー2で拠点間（ルーター間）のトンネリングを実現するVPN
  * v2とv3で用途が全く違うので注意
  * 暗号化が存在しないので暗号化する場合はIPSecを併用する

など。他にもあれば教えて下さい。

## VPNのモードについて

* VPNの方式にトランスポートモードとトンネルモードがあります
* トンネルモード
  * 主に拠点間アクセスにて使われる
* トランスポートモード
  * 主にリモートアクセスVPNで使われる
  * 認証・暗号化範囲がトンネルモードとは違う

* それぞれのVPN区間について  
![VPN_Mode1](/img/VPN/VPN_Mode1.drawio.png)

* モード別の認証・暗号化範囲について  
![VPN_Mode](/img/VPN/VPN_Mode.drawio.png)

## IPSec/VPNの愉快な仲間たち

* IPSecに含まれる仲間たちは主に以下のようなものがあります。
  * 鍵交換
    * IKEv1
    * IKEv2
  * 暗号化・認証
    * ESP（暗号化・認証）
    * AH（認証のみ）
  * 暗号化方式
    * AES
    * DES
    * 3DESなど多数
  * ハッシュアルゴリズム
    * SHA
    * MD5など多数
  * その他
    * Diffie-Hellman

## 主な用語

* センター側（レスポンダ）
  * VPNなどの通信を集約しているルーターがある側を指します。本社とか本部とか、VPNをつなげて利用したい資源が存在している場所がセンター側（レスポンダ）となることが多いです。
* 拠点側（イニシエーター）
  * VPNを接続して資源を利用したい側のことを指します。基本的にセンター側へのVPNのみ接続されて、センター側の資源を利用する形を取ることが多いです。
* IKE
  * Internet Key Exchange
  * このプロトコルでIPSec接続前の情報のやり取りを行います。
  * 認証用セッション鍵と暗号化用のセッション鍵が生成・交換・更新が行われ、SAが生成され、ISAKMP（Internet Security Association and Key Management Protocol）によりSA（Security Asosiation）の交換が行われます。
* SA
  * 情報の保護に使用されるポリシーと鍵のセット。暗号化アルゴリズム・ハッシュアルゴリズム・認証方法・Diffie-Hellmanを実行するグループ（オークリーグループと呼ばれるヤツ）に関する情報を含みます。

## 大まかな接続順序

* ここでは、IKEv1を使用したIPSec/VPNの接続について考えます。
* IKEによるSAの確立が行われる（ISAKMPトンネルと呼ばれるもの）
* UDP500および4500ポートを使用して確立します。
  * IKEv1に存在するフェーズ１とフェーズ2
  * フェーズ１
    * フェーズ１では暗号化と認証済みのトンネルを確立し、ISAKMPを使用したネゴシエーションメッセージを保護しつつやり取りします。このトンネルをISAKMP SA（IKE SA）と呼ぶこともあります。
    * ISAKMPにはメインモードとアグレッシブモードの２つのモードがあります。
      * メインモード
        * センター側、拠点側両者とも固定IPアドレスの場合に使用されるモード
      * アグレッシブモード
        * センター側が固定IPアドレス・拠点側が動的IPアドレスの場合使用されるモード
        * 拠点側が動的IPアドレスのため、イニシエーターより通信を開始し、レスポンダが応答する形でVPNを構成する。
        * メインモードとの違いはIKEでの暗号化範囲とレスポンダ側との疎通が取れなくなった場合の挙動と、一般にメインモードよりセキュリティは落ちるとされています。認証について、メインモードでは暗号化が行われたあとに構成しますが、アグレッシブモードでは暗号化する前に構成をします。
  * フェーズ2
    * クイックモード
      * ここでIPSec SAを確立する
      * IPSecによる暗号化通信で用いる暗号化アルゴリズムや認証アルゴリズムを決定する。
  * IKEv2ではここがわかりやすく簡略化されています。
* ESPによる暗号化通信の開始
  * IKEで確立されたIPSec SA上でESPプロトコルを使ってIPSec通信が行われます。
* 以下、確立されたIPSec通信を使用して拠点間通信が行われます。

## 注意点

* NATとの兼ね合い
  * NATする装置がVPNを構成するルーターの前段階にある場合、ESPやAHが改ざんされたとして拒否する場合があります。
  * この場合の問題点は、ESPやAHはポート番号の概念が存在しないプロトコルのため起こります。所謂「VPNパススルー」ができる装置であれば回避ができる場合もあります。
  * だめな場合はNATトラバーサルと呼ばれる機能を使用して回避します。
    * ESPヘッダの前にUDPヘッダを付加してカプセル化を行います。このUDPヘッダは認証や暗号化の範囲にいないため、NAT装置でのポート番号の書き換えを可能にしています。
* 同じネットワークアドレス（セグメント）同士をVPN接続した場合、ルーティングの兼ね合いから通信は不可能になります。別のネットワークアドレスを使用するよう構成するか、TwiceNATを使用します。

## 実際に通信を行う

### 環境

* YAMAHA RTX1100*2
* Cisco 3825（PPPoEサーバーとして）

### 構成図

![構成図](/img/VPN/VPN_kousei.drawio.png)

### 説明

* 日本では従来、やんごとなき事情によりインターネットへの接続にPPPoEが使われています。そのため、インターネットVPNを使用する際PPPoEを使用した上で接続することが多いです。
* 今回の構成では、CiscoルーターでPPPoEサーバーを立てて、PPPoEサーバー経由でRTX1100 2台をIPSec/VPNにて接続します。

### 構成概要

* Cisco 3825ではFa1/0、Fa1/1をそれぞれVLAN101，102として、PPPoE着信をしています。
* Fa1/0にRT1のLAN3、Fa1/1にRT2のLAN3を接続し、PPPoEの接続設定を行います。
  * ルーティング設定でデフォルトルートはPPPoE、それぞれの対向にあるネットワークはTunnelを通すように設定します。（IPSecの通信はPPPoE、それ以外の対向ネットワーク宛はトンネルを通したいため）
  * Configは長いので末に記載します。
* RT1とRT2のLAN1にはクライアントを接続し、通信を行えるようにします。
* RT1とRT2で使用するIPSecのパラメータ

    |項目|パラメータ
    |:---:|:---:
    |認証方式|pre-shared-key
    |暗号アルゴリズム|aes-cbc
    |ハッシュアルゴリズム|SHA-1(SHA)
    |PFSのon/off(有無)|off
    |DH(Diffie-Hellman)グループ|768bit(グループ1)
    |ISAKMP SAの寿命|28800
    |IPsec SAの寿命|28800
    |交換モード(exchange mode)|main mode, quick mode
    |Vendor ID	送信しない


### 実際の構成写真

![写真](/img/VPN/2024-05-20_003417.png)

### 接続確認

RT1

```YAMAHA
YAMAHA-SUDOU# show log reverse
1980/01/01 09:23:14: 'administrator' succeeded for Serial user
1980/01/01 09:23:13: Login succeeded for Serial
1980/01/01 09:02:49: IP Tunnel[1] Up
1980/01/01 09:02:49: [IKE] respond IPsec phase to 200.1.1.1
1980/01/01 09:02:48: [IKE] initiate IPsec phase to 200.1.1.1
1980/01/01 09:02:47: [IKE] respond ISAKMP phase to 200.1.1.1
1980/01/01 09:02:46: [IKE] initiate ISAKMP phase to 200.1.1.1 (local address 100.1.1.1)
1980/01/01 09:02:43: PP[01] PPP/IPCP up  (Local: 100.1.1.1, Remote: 50.1.1.1)
1980/01/01 09:02:43: PPPOE[01] PPPoE Connect
1980/01/01 09:02:22: PPPOE[01] Connecting to PPPoE server
1980/01/01 09:02:22: PP[01] IP Commencing: UDP 100.1.1.1:500 > 200.1.1.1:500
1980/01/01 09:02:22: LANC3: link up (100BASE-TX Full Duplex)
1980/01/01 09:02:16: PPPOE[01] Disconnected, cause [PPPoE: PADI Timeout]
1980/01/01 09:02:01: LANC3: link down
1980/01/01 09:01:58: LANC3: link up (100BASE-TX Full Duplex)
1980/01/01 09:01:19: PPPOE[01] Connecting to PPPoE server
1980/01/01 09:01:19: PP[01] IP Commencing: UDP 100.1.1.1:500 > 200.1.1.1:500
1980/01/01 09:01:08: PPPOE[01] Disconnected, cause [PPPoE: PADI Timeout]
1980/01/01 09:00:11: PPPOE[01] Connecting to PPPoE server
1980/01/01 09:00:11: PP[01] IP Commencing: UDP 100.1.1.1:500 > 200.1.1.1:500
1980/01/01 09:00:11: [IKE] initiate ISAKMP phase to 200.1.1.1 (local address 100.1.1.1)
1980/01/01 09:00:05: main:  RTX1100 ver=f0 serial=N1A120183 MAC-Address=00:a0:de:36:56:52 MAC-Address=00:a0:de:36:56:53 MAC-Address=00:a0:de:36:56:54
1980/01/01 09:00:05: RTX1100 Rev.8.03.92 (Thu Feb 16 20:50:25 2012) starts
1980/01/01 09:00:05: Power-on boot
1980/01/01 09:00:03: LANC1: link up
1980/01/01 09:00:03: LANC1: PORT1 link up (100BASE-TX Full Duplex)
YAMAHA-SUDOU# show status pp 1
PP[01]:
説明:
PPPoEセッションは接続されています
接続相手: PPPoE_Server
通信時間: 20分39秒
受信: 395 パケット [47514 オクテット]  負荷: 0.0%
送信: 397 パケット [44134 オクテット]  負荷: 0.0%
PPPオプション
    LCP Local: Magic-Number MRU, Remote: CHAP Magic-Number MRU
    IPCP Local: IP-Address, Remote: IP-Address
    PP IP Address Local: 100.1.1.1, Remote: 50.1.1.1
    CCP: None
YAMAHA-SUDOU# ping 192.168.2.1
192.168.2.1から受信: シーケンス番号=0 ttl=254 時間=2.056ミリ秒
192.168.2.1から受信: シーケンス番号=1 ttl=254 時間=1.742ミリ秒

2個のパケットを送信し、2個のパケットを受信しました。0.0%パケットロス
往復遅延 最低/平均/最大 = 1.742/1.899/2.056 ミリ秒
YAMAHA-SUDOU# show status tunnel 1
TUNNEL[1]:
説明:
  インタフェースの種類: IPsec
  トンネルインタフェースは接続されています
  開始: 1980/01/01 09:02:49
  通信時間: 20分43秒
  受信: (IPv4) 7 パケット [644 オクテット]
        (IPv6) 0 パケット [0 オクテット]
  送信: (IPv4) 7 パケット [644 オクテット]
        (IPv6) 0 パケット [0 オクテット]
YAMAHA-SUDOU#
```

* LAN1 OpenBlocs

```text
root@obsax3:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1024
    link/ether 00:0a:85:06:1a:dc brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.2/24 brd 192.168.1.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::20a:85ff:fe06:1adc/64 scope link
       valid_lft forever preferred_lft forever
3: eth1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1024
    link/ether 00:0a:85:06:1a:dd brd ff:ff:ff:ff:ff:ff
    inet 192.168.210.2/24 brd 192.168.210.255 scope global eth1
       valid_lft forever preferred_lft forever
root@obsax3:~# ping 192.168.2.1
PING 192.168.2.1 (192.168.2.1) 56(84) bytes of data.
64 bytes from 192.168.2.1: icmp_seq=1 ttl=254 time=2.40 ms
64 bytes from 192.168.2.1: icmp_seq=2 ttl=254 time=1.41 ms
64 bytes from 192.168.2.1: icmp_seq=3 ttl=254 time=1.41 ms
^C
--- 192.168.2.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 1.412/1.745/2.404/0.465 ms
root@obsax3:~# ping 192.168.2.2
PING 192.168.2.2 (192.168.2.2) 56(84) bytes of data.
64 bytes from 192.168.2.2: icmp_seq=1 ttl=62 time=4.16 ms
64 bytes from 192.168.2.2: icmp_seq=2 ttl=62 time=1.49 ms
64 bytes from 192.168.2.2: icmp_seq=3 ttl=62 time=1.45 ms
^C
--- 192.168.2.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2001ms
rtt min/avg/max/mdev = 1.456/2.371/4.164/1.268 ms
root@obsax3:~#
```

RT2

```YAMAHA
YAMAHA-MARIO# show log reverse
1980/01/01 11:28:48: 'administrator' succeeded for Serial user
1980/01/01 11:28:47: Login succeeded for Serial
1980/01/01 11:24:14: Logout from Serial
1980/01/01 11:19:13: Login succeeded for Serial
1980/01/01 11:11:42: IP Tunnel[1] Up
1980/01/01 11:11:42: [IKE] respond IPsec phase to 100.1.1.1
1980/01/01 11:11:41: [IKE] initiate IPsec phase to 100.1.1.1
1980/01/01 11:11:40: [IKE] respond ISAKMP phase to 100.1.1.1
1980/01/01 11:11:39: [IKE] initiate ISAKMP phase to 100.1.1.1 (local address 20 0.1.1.1)
1980/01/01 11:11:35: PP[01] PPP/IPCP up  (Local: 200.1.1.1, Remote: 50.1.1.1)
1980/01/01 11:11:35: PPPOE[01] PPPoE Connect
1980/01/01 11:11:13: PPPOE[01] Connecting to PPPoE server
1980/01/01 11:11:13: PP[01] IP Commencing: UDP 200.1.1.1:500 > 100.1.1.1:500
1980/01/01 11:11:13: LANC3: link up (100BASE-TX Full Duplex)
1980/01/01 11:11:07: PPPOE[01] Disconnected, cause [PPPoE: PADI Timeout]
1980/01/01 11:10:52: LANC3: link down
1980/01/01 11:10:49: LANC3: link up (100BASE-TX Full Duplex)
1980/01/01 11:10:10: PPPOE[01] Connecting to PPPoE server
1980/01/01 11:10:10: PP[01] IP Commencing: UDP 200.1.1.1:500 > 100.1.1.1:500
1980/01/01 11:09:59: PPPOE[01] Disconnected, cause [PPPoE: PADI Timeout]
1980/01/01 11:09:21: LANC1: PORT1 link changed (100BASE-TX Full Duplex)
1980/01/01 11:09:18: LANC1: PORT1 link changed (10BASE-T Half Duplex)
1980/01/01 11:09:02: PPPOE[01] Connecting to PPPoE server
1980/01/01 11:09:02: PP[01] IP Commencing: UDP 200.1.1.1:500 > 100.1.1.1:500
1980/01/01 11:09:02: [IKE] initiate ISAKMP phase to 100.1.1.1 (local address 200.1.1.1)
1980/01/01 11:08:56: main:  RTX1100 ver=f0 serial=N1A134325 MAC-Address=00:a0:de:37:45:2f MAC-Address=00:a0:de:37:45:30 MAC-Address=00:a0:de:37:45:31
1980/01/01 11:08:56: RTX1100 Rev.8.03.94 (Thu Dec  5 19:06:16 2013) starts
1980/01/01 11:08:56: Power-on boot
1980/01/01 11:08:54: LANC1: link up
1980/01/01 11:08:54: LANC1: PORT1 link up (100BASE-TX Full Duplex)
YAMAHA-MARIO#
YAMAHA-MARIO#
YAMAHA-MARIO#
YAMAHA-MARIO# show status pp?
? pp pptp
YAMAHA-MARIO# show status pp 1
PP[01]:
説明:
PPPoEセッションは接続されています
接続相手: PPPoE_Server
通信時間: 18分53秒
受信: 361 パケット [42978 オクテット]  負荷: 0.0%
送信: 364 パケット [40116 オクテット]  負荷: 0.0%
PPPオプション
    LCP Local: Magic-Number MRU, Remote: CHAP Magic-Number MRU
    IPCP Local: IP-Address, Remote: IP-Address
    PP IP Address Local: 200.1.1.1, Remote: 50.1.1.1
    CCP: None
YAMAHA-MARIO# ping 192.168.1.1
192.168.1.1から受信: シーケンス番号=0 ttl=254 時間=2.323ミリ秒
192.168.1.1から受信: シーケンス番号=1 ttl=254 時間=1.749ミリ秒

2個のパケットを送信し、2個のパケットを受信しました。0.0%パケットロス
往復遅延 最低/平均/最大 = 1.749/2.036/2.323 ミリ秒
YAMAHA-MARIO# ping 192.168.1.2
192.168.1.2から受信: シーケンス番号=0 ttl=62 時間=2.863ミリ秒
192.168.1.2から受信: シーケンス番号=1 ttl=62 時間=2.260ミリ秒
192.168.1.2から受信: シーケンス番号=2 ttl=62 時間=2.158ミリ秒

3個のパケットを送信し、3個のパケットを受信しました。0.0%パケットロス
往復遅延 最低/平均/最大 = 2.158/2.427/2.863 ミリ秒
YAMAHA-MARIO# show status tunnel 1
TUNNEL[1]:
説明:
  インタフェースの種類: IPsec
  トンネルインタフェースは接続されています
  開始: 1980/01/01 11:11:42
  通信時間: 19分10秒
  受信: (IPv4) 5 パケット [460 オクテット]
        (IPv6) 0 パケット [0 オクテット]
  送信: (IPv4) 5 パケット [460 オクテット]
        (IPv6) 0 パケット [0 オクテット]
YAMAHA-MARI
```

* RT2 LAN1 MIRACLE LINUX

![MLIP](/img/VPN/2024-05-20_004620.png)

### まとめ

* 情報量が多いものの、少しずつ分解をするとわかるような気がします。現在は今回使用したIKEv1は使われることは少なくなり、IKEv2が使われます。IKEv1のフェーズごとの通信をまとめて、暗号化範囲を広げて更にシンプルにしたものなので、覚えるのも難しくはないと思います。
* また、今回は古のルーターであるRTX1100を使用したため、暗号化やハッシュアルゴリズムも弱いものになっていますが、現在のRTXシリーズは暗号化アルゴリズムとハッシュアルゴリズムを自動で最強のものにするという設定があったと思います。
* 注意したいのは異なるベンダーの場合、パラメータをきっちり合わせてそれぞれの仕様を確認した上で接続を試しましょう。

### 設定情報

* YAMAHA RTX1100 RT1

```YAMAHA
YAMAHA-SUDOU# show config
# RTX1100 Rev.8.03.92 (Thu Feb 16 20:50:25 2012)
# MAC Address : 00:a0:de:36:56:52, 00:a0:de:36:56:53, 00:a0:de:36:56:54,
# Memory 32Mbytes, 3LAN, 1BRI
# main:  RTX1100 ver=f0 serial=N1A120183 MAC-Address=00:a0:de:36:56:52 MAC-Address=00:a0:de:36:56:53 MAC-Address=00:a0:de:36:56:54
# Reporting Date: Jan 1 09:21:26 1980
security class 1 on on
console prompt YAMAHA-SUDOU
ip route default gateway pp 1
ip route 192.168.2.0/24 gateway tunnel 1
ip lan1 address 192.168.1.1/24
pp select 1
 pppoe use lan3
 pp auth accept chap
 pp auth myname sudou@caos.local sudou@pass
 ppp lcp mru on 1454
 ppp ipcp ipaddress on
 ppp ipcp msext on
 ppp ccp type none
 ip pp mtu 1454
 pp enable 1
tunnel select 1
 ipsec tunnel 101
  ipsec sa policy 101 1 esp aes-cbc sha-hmac
  ipsec ike keepalive log 1 off
  ipsec ike keepalive use 1 on heartbeat 10 6
  ipsec ike local address 1 100.1.1.1
  ipsec ike pre-shared-key 1 text ciscodaisuk1
  ipsec ike remote address 1 200.1.1.1
 ip tunnel tcp mss limit auto
 tunnel enable 1
nat descriptor type 1 masquerade
nat descriptor address outer 1 ipcp
nat descriptor address inner 1 auto
nat descriptor masquerade static 1 1 192.168.1.1 udp 500
nat descriptor masquerade static 1 2 192.168.1.1 esp
ipsec auto refresh on
syslog notice on
YAMAHA-SUDOU#
```

* YAMAHA RTX1100 RT2

```YAMAHA
YAMAHA-MARIO# show config
# RTX1100 Rev.8.03.94 (Thu Dec  5 19:06:16 2013)
# MAC Address : 00:a0:de:37:45:2f, 00:a0:de:37:45:30, 00:a0:de:37:45:31,
# Memory 32Mbytes, 3LAN, 1BRI
# main:  RTX1100 ver=f0 serial=N1A134325 MAC-Address=00:a0:de:37:45:2f MAC-Address=00:a0:de:37:45:30 MAC-Address=00:a0:de:37:45:31
# Reporting Date: Jan 1 09:46:22 1980
security class 1 on on
console prompt YAMAHA-MARIO
ip route default gateway pp 1
ip route 192.168.1.0/24 gateway tunnel 1
ip lan1 address 192.168.2.1/24
pp select 1
 pppoe use lan3
 pp auth accept chap
 pp auth myname mario@caos.local mario@pass
 ppp lcp mru on 1454
 ppp ipcp ipaddress on
 ppp ipcp msext on
 ppp ccp type none
 ip pp mtu 1454
 pp enable 1
tunnel select 1
 ipsec tunnel 101
  ipsec sa policy 101 1 esp aes-cbc sha-hmac
  ipsec ike keepalive log 1 off
  ipsec ike keepalive use 1 on heartbeat 10 6
  ipsec ike local address 1 200.1.1.1
  ipsec ike pre-shared-key 1 text ciscodaisuk1
  ipsec ike remote address 1 100.1.1.1
 ip tunnel tcp mss limit auto
 tunnel enable 1
nat descriptor type 1 masquerade
nat descriptor address outer 1 ipcp
nat descriptor address inner 1 auto
nat descriptor masquerade static 1 1 192.168.2.1 udp 500
nat descriptor masquerade static 1 2 192.168.2.1 esp
ipsec auto refresh on
syslog notice on
YAMAHA-MARIO#
```

* Cisco 3825

```cisco
PPPoE_Server#show running-config
Building configuration...


Current configuration : 2821 bytes
!
! Last configuration change at 12:05:19 UTC Mon Jan 2 2006
! NVRAM config last updated at 12:08:01 UTC Mon Jan 2 2006
! NVRAM config last updated at 12:08:01 UTC Mon Jan 2 2006
version 15.1
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname PPPoE_Server
!
boot-start-marker
boot-end-marker
!
!
!
no aaa new-model
!
!
dot11 syslog
ip source-route
!
ip cef
!
!
!
!
no ipv6 cef
!
multilink bundle-name authenticated
!
!
!
!
!
voice-card 0
!
!
!
!
!
!
!
crypto pki token default removal timeout 0
!
!
!
!
license udi pid CISCO3825 sn FHK1238F2D0
!
no spanning-tree vlan 101
no spanning-tree vlan 102
username sudou@caos.local password 0 sudou@pass
username mario@caos.local password 0 mario@pass
!
redundancy
!
!
!
!
!
!
!
!
!
bba-group pppoe PPPoE-sudou
 virtual-template 1
!
bba-group pppoe PPPoE-mario
 virtual-template 2
!
!
interface Loopback1
 ip address 50.1.1.1 255.255.255.255
!
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
 media-type rj45
!
interface GigabitEthernet0/1
 no ip address
 shutdown
 duplex auto
 speed auto
 media-type rj45
!
interface Serial0/0/0
 no ip address
 shutdown
 no fair-queue
 clock rate 2000000
!
interface FastEthernet1/0
 switchport access vlan 101
 no ip address
!
interface FastEthernet1/1
 switchport access vlan 102
 no ip address
!
interface FastEthernet1/2
 no ip address
!
interface FastEthernet1/3
 no ip address
!
interface FastEthernet1/4
 no ip address
!
interface FastEthernet1/5
 no ip address
!
interface FastEthernet1/6
 no ip address
!
interface FastEthernet1/7
 no ip address
!
interface FastEthernet1/8
 no ip address
!
interface FastEthernet1/9
 no ip address
!
interface FastEthernet1/10
 no ip address
!
interface FastEthernet1/11
 no ip address
!
interface FastEthernet1/12
 no ip address
!
interface FastEthernet1/13
 no ip address
!
interface FastEthernet1/14
 no ip address
!
interface FastEthernet1/15
 no ip address
!
interface Virtual-Template1
 mtu 1454
 ip unnumbered Loopback1
 peer default ip address pool sudou-pool
 ppp authentication chap
!
interface Virtual-Template2
 mtu 1454
 ip unnumbered Loopback1
 peer default ip address pool mario-pool
 ppp authentication chap
!
interface Vlan1
 no ip address
!
interface Vlan101
 no ip address
 pppoe enable group PPPoE-sudou
!
interface Vlan102
 no ip address
 pppoe enable group PPPoE-mario
!
interface Vlan103
 no ip address
!
interface Vlan104
 no ip address
!
ip local pool mario-pool 200.1.1.1
ip local pool sudou-pool 100.1.1.1
ip forward-protocol nd
no ip http server
no ip http secure-server
!
!
!
!
!
!
!
!
!
control-plane
!
!
!
!
mgcp profile default
!
!
!
!
!
gatekeeper
 shutdown
!
!
!
line con 0
line aux 0
line 2/0 2/15
line vty 0 4
 login
 transport input all
!
scheduler allocate 20000 1000
end

PPPoE_Server#
PPPoE_Server#show version
Cisco IOS Software, 3800 Software (C3825-ADVENTERPRISEK9_IVS-M), Version 15.1(4)M12a, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2016 by Cisco Systems, Inc.
Compiled Tue 04-Oct-16 04:18 by prod_rel_team

ROM: System Bootstrap, Version 12.4(13r)T15, RELEASE SOFTWARE (fc1)

PPPoE_Server uptime is 15 minutes
System returned to ROM by power-on
System restarted at 11:59:46 UTC Mon Jan 2 2006
System image file is "flash:c3825-adventerprisek9_ivs-mz.151-4.M12a.bin"
Last reload type: Normal Reload


This product contains cryptographic features and is subject to United
States and local country laws governing import, export, transfer and
use. Delivery of Cisco cryptographic products does not imply
third-party authority to import, export, distribute or use encryption.
Importers, exporters, distributors and users are responsible for
compliance with U.S. and local country laws. By using this product you
agree to comply with applicable laws and regulations. If you are unable
to comply with U.S. and local laws, return this product immediately.

A summary of U.S. laws governing Cisco cryptographic products may be found at:
http://www.cisco.com/wwl/export/crypto/tool/stqrg.html

If you require further assistance please contact us by sending email to
export@cisco.com.

Cisco 3825 (revision 1.2) with 485376K/38912K bytes of memory.
Processor board ID FHK1238F2D0
16 FastEthernet interfaces
2 Gigabit Ethernet interfaces
1 Serial(sync/async) interface
16 terminal lines
1 Virtual Private Network (VPN) Module
DRAM configuration is 64 bits wide with parity enabled.
479K bytes of NVRAM.
250368K bytes of ATA System CompactFlash (Read/Write)


License Info:

License UDI:

-------------------------------------------------
Device#   PID                   SN
-------------------------------------------------
*0        CISCO3825             FHK1238F2D0



Configuration register is 0x2102

PPPoE_Server#
```

## 【補足】AHを使う場合

* YAMAHAルーターの以下を書き換えます

```YAMAHA
tunnel select 1
  ipsec sa policy 101 1 ah sha-hmac
nat descriptor masquerade static 1 2 192.168.x.1 ah
```

* RTX1100同士の場合、片方がESPで片方がAHの設定を行ってある状態でIKEのネゴシエーションをした場合、AHを設定している側のRTX1100がリブートするバグがあるようです。設定の際はご注意ください。

以上です。もしおかしい点がありましたらご指摘ください…。  
マサカリお待ちしております。
