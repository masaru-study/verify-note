+++
#テンプレート：デフォルト
title = 'Cisco_BGPの基本_AS編'
date = 2025-09-06T15:04:00+09:00
draft = false
#weight = 99 # 順番を意図的に変更したい場合は変更する
categories = ["Network"]
tags = ["Author:nakata216", "Level:初級", "Type:Knowledge", "CCNP"]
+++

- [概要](#概要)
- [IGPとEGPについて](#igpとegpについて)
- [ASとは](#asとは)
- [グローバルASとプライベートAS](#グローバルasとプライベートas)

## input

- [JPNIC:インターネット10分講座：BGP](https://www.nic.ad.jp/ja/newsletter/No35/0800.html)
- [JPNIC:AS番号の割り当てに関する参考情報](https://www.nic.ad.jp/doc/jpnic-01244.html)

## 概要

本記事ではBGPを学習する上での前提となるASの概念について記載します。

## IGPとEGPについて

ASを学習する上でIGPとEGPの知識は必須となります。<br>
*   IGP<br>
    自律システム(AS:Autonomous System)内で経路交換を行うルーティングプロトコルとなり、OSPFやEIGRP、RIP等のプロトコルが対象となります。<br>
*   EGP<br>
    AS間の経路交換を行うルーティングプロトコルとなり、BGPやEGPが対象となります。<br>

```mermaid
%% IGPとEGPの関係（AS内はIGP、AS間はBGP）
flowchart LR
  subgraph AS65000["AS65000 (IGP: OSPF)"]
    R1[R1] --- R2[R2]
    R2 --- R3[R3]
  end
  subgraph AS65001["AS65001 (IGP: EIGRP)"]
    A1[A1] --- A2[A2]
  end
  R3 ==>|BGP| A1
```


```mermaid
%% BGP 経路広告の流れ（AS間）
sequenceDiagram
  participant C as AS65010(顧客)
  participant T1 as AS100(トランジット)
  participant T2 as AS200(トランジット)
  C->>T1: 10.0.0.0/24 を広告
  Note over C,T1: iBGP/IGPはAS内、eBGPはAS間
  T1->>T2: 10.0.0.0/24 AS_PATH [100 65010]
  T2-->>T1: 受信・選択・ポリシー適用
  Note over T2: 条件に応じて他ピアへ再広告
```

## ASとは

ASとは、組織毎の運用ポリシーを持った一つのネットワークのかたまりを示します。このASの管理を行っているは一般的にISPになりますが、学術系ネットワークやデータセンターなどである場合もあります。このASを識別するのに使用されるのがAS番号です。<br>
ASは誰でも自由に作成できるわけではありません。<br>
ASはIANAという組織が世界的に管理しており、IANAから各地域の地域インターネットレジストリ(RIR:Regional Internet Registry)にASが分配されます。<br>
ASを作成するにはRIRまたは国別インターネットレジストリ(NIR:National Internet Registry)に申請する必要があります<br>
※日本ではJPNICが申請を受け付けてます。<br>
※後述するプライベートASは自由に使用することが出来ます。

```mermaid
%% AS番号の配布階層
flowchart TB
  IANA[IANA]
  RIR["RIR(例: APNIC)"]
  NIR["NIR(例: JPNIC)"]
  LIR[ISP/LIR]
  USER[企業/組織]

  IANA --> RIR --> NIR --> LIR --> USER
```

## グローバルASとプライベートAS
AS番号は2バイトまたは4バイトで表現され、0～4294967295までの番号空間を持ちます。<br>
この番号空間の中ではグローバルASとプライベートASに分かれており、番号空間は下記の表になります。<br>

|AS番号|用途|
|:---:|:---:|
|0～64511、65535～4199999999、4294967295|グローバルAS|
|64512～65534、4200000000～4294967294|プライベートAS|
※0、64496～64511、65535、 65536～131071と4294967295はICANNによって予約されています。

*   グローバルAS<br>
    インターネット上に経路広報を行う場合に使用するAS番号となり、使用する場合はRIRやNIRに申請する必要があります。<br>

*   プライベートAS<br>
    自AS内で自由に使用できるAS番号。ISPが提供する同時のIP-VPN網はこのプライベートASが利用されている。<br>
    プライベートASの経路情報は自AS以外に広報してはいけません。<br>

```mermaid
%% AS番号の種別と範囲
flowchart TB
  subgraph Private AS
    P2B[64512–65534]
    P4B[4200000000–4294967294]
  end
  subgraph Public AS
    PUB[上記以外の利用可能範囲]
  end
  R[予約: 0, 64496–64511, 65535, 65536–131071, 4294967295]
  R -. 予約値 .- P2B
  R -. 予約値 .- P4B
  R -. 予約値 .- PUB
```

## ASの接続形態について

各ASの接続形態は複数あり、それぞれでASを通過できたり、出来なかったりします。<br>

*   スタブAS<br>
    シングルホームASとも呼ばれており、他に繋がっているASが一つのみの場合を指します。<br>

*   トランジットAS<br>
    複数のASが接続されており、自組織以外のアドレスを接続先にアナウンスするASを「トランジットAS」と言いいます。

*   非トランジットAS<br>
    複数のASが接続されており、自組織のアドレスだけを接続先にアナウンスするASを「非トランジットAS」と言います。非トランジットASは一般的に、フルルートを上流ISPから取得する必要があります。

```mermaid
graph LR
    %% Transit AS
    subgraph T_AS[Transit AS]
        T1[AS100]
        T2[AS200]
        T3[AS300]
    end

    %% Non-Transit (Single-homed)
    subgraph NT1[Non-Transit AS Single-homed]
        N1[AS65001]
    end

    %% Non-Transit (Multi-homed)
    subgraph NT2[Non-Transit AS Multi-homed]
        N2[AS65002]
    end

    %% Peer AS
    subgraph Peer[Non-Transit AS Peering]
        P1[AS65003]
    end

    %% Transit connections
    T1 --- T2
    T2 --- T3
    T3 --- T1

    %% Single-homed customer
    N1 --- T1

    %% Multi-homed customer
    N2 --- T1
    N2 --- T2

    %% Peering (no transit)
    P1 --- N2
```

## まとめ

BGPを学習する上でASの概念がないと、パスディストリビューリストを理解することが困難になります。また、白本での学習でもいいですが、BGPに関してはJPNICの記事で学習することをお勧めします。
