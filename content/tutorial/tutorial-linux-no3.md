+++
#テンプレート：ハンズオン
title = '【Linux】No.3 ネットワーク設定編'
date = 2024-06-15T22:00:51+09:00
draft = false
weight = 3
categories = ["Tutorial"]
tags = ["Author:あゆむ", "LPIC1", "LinuC1"]
+++

## 目次

- [目次](#目次)
- [概要](#概要)
  - [本ハンズオンで身につくこと](#本ハンズオンで身につくこと)
  - [出てくるコマンド／操作](#出てくるコマンド操作)
  - [構成図](#構成図)
- [ハンズオン](#ハンズオン)
  - [Virtualbox のネットワーク設定](#virtualbox-のネットワーク設定)
  - [作成した NAT ネットワークを仮想マシンに反映](#作成した-nat-ネットワークを仮想マシンに反映)
  - [IP アドレスの確認](#ip-アドレスの確認)
  - [ルーティングテーブル の確認](#ルーティングテーブル-の確認)
  - [IP アドレス／デフォルトゲートウェイの設定](#ip-アドレスデフォルトゲートウェイの設定)
  - [IP アドレスの確認（設定後）](#ip-アドレスの確認設定後)
  - [ルーティングテーブルの確認（設定後）](#ルーティングテーブルの確認設定後)
  - [インターネット接続の確認](#インターネット接続の確認)

## 概要

Virtualbox の中にある Linux の静的 IP アドレスを設定しましょう。

### 本ハンズオンで身につくこと

- IP アドレスの確認・設定方法
- ルーティングテーブルの確認・設定方法

### 出てくるコマンド／操作

- ip コマンド
- nmcli (Network Manager)

### 構成図

{{< figure src="/img/tutorial-linux-no3/fig-diagram.drawio.svg" title="fig-diagram" class="center" width="50%" height="50%" >}}

## ハンズオン

### Virtualbox のネットワーク設定

Virtualbox マネージャーのツール欄にマウスポインターを当てて「横線が３本並んでいるボタン」をクリックして、
出てきたメニューで「ネットワーク」を選択します。

{{< figure src="/img/tutorial-linux-no3/vbox-tool.png" title="vbox-tool" class="center" width="50%" height="50%" >}}

「NAT ネットワーク」タブを選択した状態で「作成」ボタンを押します。その後、画像の通りにパラメータを入力して「適用」ボタンを押します。

{{< figure src="/img/tutorial-linux-no3/vbox-network.png" title="vbox-network" class="center" width="50%" height="50%" >}}

### 作成した NAT ネットワークを仮想マシンに反映

{{% notice style="note" %}}
この作業は仮想マシンが電源オフ状態で実施してください。
{{% /notice %}}

Virtualbox マネージャーの仮想マシン欄をクリックして「設定」ボタンを押します。設定画面で「ネットワーク」を選択して
割当を「NAT ネットワーク」に変更します。名前は先ほど入力した名前を選択します。最後に「OK」ボタンを押します。

{{< figure src="/img/tutorial-linux-no3/vm-network.png" title="vm-network" class="center" width="50%" height="50%" >}}

### IP アドレスの確認

仮想マシンを起動して、端末を開きます。以下のコマンドを入力して現在設定されている IP アドレスを確認します。

```bash
[study@localhost ~]$ ip address
```

以下のような結果が表示されます。先程の設定で 172.24.0.0/24 のネットワークに所属させましたが、
設定を変更する前なので別の IP アドレスが設定されています。`inet 10.0.2.15/24`

```bash
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether XX:XX:XX:XX:XX:XX brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
       valid_lft 86295sec preferred_lft 86295sec
    inet6 fe80::XXXX:XXXX:XXXX:XXXX/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether XX:XX:XX:XX:XX:XX  brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
```

{{% notice style="tip" %}} virbr0 は Virtualbox の固有設定の為、無視してください。実環境では表示されません。{{% /notice %}}

### ルーティングテーブル の確認

以下のコマンドを入力して現在設定されている ルーティングテーブルを確認します。

```bash
[study@localhost ~]$ ip route
```

以下のような結果が表示されます。172.24.0.0/24 へのルーティングとデフォルトゲートウェイが設定されていません。

```bash
192.168.122.0/24 dev virbr0 proto kernel scope link srcc 192.168.122.1 linkdown
```

{{% notice style="tip" %}} 192.168.122.0/24 は Virtualbox の固有設定の為、無視してください。実環境では表示されません。{{% /notice %}}

### IP アドレス／デフォルトゲートウェイの設定

以下のコマンドで enp0s3 の IP アドレスを設定します。

```bash
[study@localhost ~]$ nmcli con mod enp0s3 ipv4.addresses "172.24.0.101/24" ipv4.method manual
```

以下のコマンドで enp0s3 のデフォルトゲートウェイを設定します。

```bash
[study@localhost ~]$ nmcli con mod enp0s3 ipv4.gateway "172.24.0.1"
```

設定を反映させる為、以下のコマンドで enp0s3 を再接続させます。

```bash
[study@localhost ~]$ nmcli con down enp0s3
[study@localhost ~]$ nmcli con up enp0s3
```

### IP アドレスの確認（設定後）

前述と同じ手順で確認します。

```bash
[study@localhost ~]$ ip address
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether XX:XX:XX:XX:XX:XX brd ff:ff:ff:ff:ff:ff
    inet 172.24.0.101/24 brd 172.24.0.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::XXXX:XXXX:XXXX:XXXX/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether XX:XX:XX:XX:XX:XX brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
```

正しく IP アドレスが設定されたことが確認できました。`inet 172.24.0.101/24`

### ルーティングテーブルの確認（設定後）

前述と同じ手順で確認します。

```bash
[study@localhost ~]$ ip route
default via 172.24.0.1 dev enp0s3 proto static metric 100
172.24.0.0/24 dev enp0s3 proto kernel scope link src 172.24.0.101 metric 100
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1 linkdown
```

正しくルーティングテーブルが設定されたことが確認できました。`default via 172.24.0.1 dev enp0s3` `172.24.0.0/24 dev enp0s3`

### インターネット接続の確認

ブラウザで適当な Web ページを開いてみてインターネット接続を確認します。

{{< figure src="/img/tutorial-linux-no3/yahoo.png" title="yahoo" class="center" width="50%" height="50%" >}}
