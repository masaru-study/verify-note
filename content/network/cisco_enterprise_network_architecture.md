+++
#テンプレート：ハンズオン
title = 'Ciscoエンタープライズネットワークアーキテクチャ'
date = 2025-08-11T01:59:10+09:00
draft = false
#weight = 99 # 順番を意図的に変更したい場合は変更する
categories = ["Network"]
tags = ["Author:nakata216", "Level:初級", "Type:Knowledge", "CCNP"]
+++

## 目次
- [概要](#概要)
- [本記事で学べる事](#本ハンズオンで身につくこと)
- [input資料](#input資料)
- [エンタープライズネットワークって何？](#エンタープライズネットワークって何)
- [階層型NW設計モデル](#階層型nw設計モデル)
- [3階層型と2階層型](#3階層型と2階層型)
- [SOHO(Small Office/Home Office)](#sohosmall-officehome-office)
- [まとめ](#まとめ)

### 概要

Ciscoエンタープライズネットワークアーキテクチャについて書きます。

### 本記事で学べる事

- 階層型NW設計モデルの考え方
- アクセス層、ディストリビューション層、コア層について

### input資料

  * [シスコ技術者認定教科書 CCNA 完全合格テキスト&問題集[対応試験]200-301](https://www.amazon.co.jp/%E3%82%B7%E3%82%B9%E3%82%B3%E6%8A%80%E8%A1%93%E8%80%85%E8%AA%8D%E5%AE%9A%E6%95%99%E7%A7%91%E6%9B%B8-CCNA-%E5%AE%8C%E5%85%A8%E5%90%88%E6%A0%BC%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88-%E5%AF%BE%E5%BF%9C%E8%A9%A6%E9%A8%93-200-301/dp/4798165778/ref=sr_1_3?adgrpid=79617186911&dib=eyJ2IjoiMSJ9.1yGRlZd3h8hPMiqrGX_s3HZenxnO1JtQw1UBXwoQlVD54wuXW8mBjCLbwrwrQSQTSaV4fesEz3TMwGRIuOXHajXTdpw_JL5_3Ipy9wn2ldmmZiqC9ZOGpRf0L3uNbnXp2b7-M7BhcrS8YEhuwULC68XDpE222JR4SguadZwFMQ2h4CFAUvrDALfICqj0JZ7FlYd_lur0EEeHsjqKhUA0UQyk6kyiJFNz8N5os8KQZBoi2_etme3gX8WzTGEl1R81QN6jxkd5euBmxjiy8zZ9NUugns5tzhCYhwUDZ-jlhTg.NLVROPzD-rIksiwmfPfw0XWcb4TyTKLz2nmJGGNRUWE&dib_tag=se&hvadid=679050907081&hvdev=c&hvexpln=0&hvlocphy=1028850&hvnetw=g&hvocijid=12480937827729790460--&hvqmt=e&hvrand=12480937827729790460&hvtargid=kwd-852056936806&hydadcr=18700_13654972&jp-ad-ap=0&keywords=ccna+%E7%99%BD%E6%9C%AC&mcid=9ffd6b1001f936e0afd21cef9c0e71e9&qid=1754845890&sr=8-3)
  * [ネットワークエンジニアとして](https://www.infraexpert.com/study/catalyst2.html)
  * [エンタープライズ キャンパス 3.0 のアーキ
  テクチャ : 概要とフレームワーク](https://www.cisco.com/web/JP/solution/netsol/designzone/literature/pdf/OL-15716-01-J.pdf)

### エンタープライズネットワークって何？
エンタープライズネットワークとは、所謂LANです。
規模はオフィスの1フロアの小規模のモノもあれば、病院や大学、一組織を指す大規模のモノもあります。
このLANをどうやって設計していくかの考え方として階層型NW設計モデルというものがあります。

### 階層型NW設計モデル
階層型NW設計モデルには後述で説明しますが、大きく2階層型と3階層型の考え方があります。<br>
また、階層型という名の通り、下記のように、それぞれの階層に役割が分けられています。

  * アクセス層<br>
    階層型NW設計モデルでユーザーに一番近い層です。<br>
    この層では、ユーザーが自身の端末(PCやスマホ等)をNWに繋げるため、機器として、スイッチングハブやL2スイッチ、AP等が設置されます。<br><br>

  * ディストリビューション層<br>
    階層型NW設計モデルで中間に位置する層です。<br>
    この層では、アクセス層からのNWを集約し、ポリシーに基づいて、ACLによるトラフィックの転送、拒否や各NW間のルーティングを行います。<br>
    そのため、この階層で使用される機器は、L3スイッチやルータ等が設置されます。<br><br>

  * コア層<br>
    階層型NW設計モデルでユーザーから最も遠く、トラフィックが一番集約される層です。<br>
    この層は、各ディストリビューション層の機器を束ねており、LANの中心箇所、所謂、基幹部分(バックボーン)となります。<br>
    そのため、多くのトラフィック処理や拠点間の長距離接続を担うため、Nexusシリーズ等のハイエンドモデルの機器が設置されます。<br><br>


![cisco_enterprise_network_architecture](/img/cisco_enterprise_network_architecture/cisco_enterprise_network_architecture.png) 

### 3階層型と2階層型
  前述でも述べましたが、階層型NW設計モデルには3階層型と2階層型の考え方があります。ここでは、それぞれの階層型の特徴を見ていきます。

  * 3階層型<br>
    3階層型は前述の図のように、拠点が複数分かれている等の大規模拠点を設計する時に使用される考え方です。<br>
    この階層モデルの特徴としては、コア層とディストリビューション層を分離することにより、各拠点をフルメッシュで接続した際に、リンクの数を減らすことが出来、また、構成をシンプルにすることが可能です。<br><br>
  * 2階層型<br>
    2階層型は主に小、中規模拠点を設計する時に使用される考え方です。<br>
    この階層モデルの特徴としては、コア層とディストリビューションを同じ階層にし、機器の台数やリンク数、機器のグレードを落とすことにより、コストを削減することが可能です。

### SOHO(Small Office/Home Office)
SOHOとは、マンションやビルの1室や自宅等を職場として働く業務形態です。<br>
SOHOの場合、ごく少数のユーザーのみが利用すること想定したNWとなるため、階層型NW設計モデルの考え方には当て嵌めず、単一のルータのみは少数のスイッチが設置されるケースがあります。


### まとめ
この階層型NW設計モデルはNWを設計する上での基本的な考え方であり、今後学習していく技術が、どの階層で必要なのかをイメージする材料となります。