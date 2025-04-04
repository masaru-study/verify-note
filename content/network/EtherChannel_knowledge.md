+++
#テンプレート：デフォルト
title = '実際にあったEtherChannel（LACP）の失敗'
date = 2025-04-05T00:57:00+09:00
draft = false
#weight = 1000
categories = ["Network"]
tags = ["Author:nakata", "Level:初級", "Type:Knowledge", "EtherChannel", "LACP"]
+++

## 目次

- [EtherChannel（LACP）って何？](##EtherChannel（LACP）って何？)
- [EtherChannel（LACP）設定時の注意点](##EtherChannel（LACP）設定時の注意点  )
- [実際にやらかした事象](##実際にやらかした事象)
- [まとめ](##まとめ)
## EtherChannel（LACP）って何？

EtherChannel（LACP）は機器間の物理リンクを論理的に束ねて1つのリンクとして扱う技術。  

* メリット  
  * 冗長性を確保出来る  
  複数の物理リンクを束ねているため、1本の物理リンクがDWONしてもLINKDOWNしない。  
  * 帯域幅を増強出来る  
    束ねたリンクの合計を帯域幅として扱う。
  * 負荷分散が出来る  
  どのリンクを通すか任意で指定ができる。

## EtherChannel（LACP）設定時の注意点  

EtherChannel（LACP）を構成する上で下記の項目を合わせておく必要がある。  

* ポートの種類  
   束ねるリンクでスイッチポートとルーテッドポートを混在させることは出来ません。  
   特にL3SWでEtherChannel（LACP）を構成する場合に注意が必要です。
* 速度とDuplex  
   束ねるリンクは速度とDuplexを合わせなければ EtherChannel（LACP）はアップしません。  
   例えばfastetherとGigabitetherやDuplex fullとhalfを束ねることは出来ないです。  
* スイッチモード  
   EtherChannel（LACP）をアクセスポートとして扱う場合は物理リンクを事前にアクセスポート、トランクポートとして扱う場合は物理リンクを事前にトランクポートに合わせておく必要があります。

## 実際にやらかした事象

私がやらかした失敗談をここに残しておこうと思います。  

* 前提条件  
  * スイッチモードはアクセスポートを利用する  
  * 帯域幅、Duplexは合わせている  
  * ポートの種類はスイッチポート  

前提条件を全部クリアしいざ適用とポートを解放した瞬間、LINKDOWNのsyslogが流れ、interface statusがnoconnectになっている。

最初はnoconnectで物理的な部分を疑ったが、結線時の導通試験は問題なかったと設備会社より聞いた瞬間、物理の線は消滅。  

一度冷静になって構成を見た時、あることに気づいた。  
「あれ？これVLAN違うから繋がんなくね？」

![構成図](/img/Etherchannel/Etherchannel.png)  
※実際の構成ではもっと色々な設定がされております。  

configを見る限りでは確かにアクセスポートですし、speed,Duplexも同じだし、スイッチポートだから注意点は全てクリアしている。だけど、L2SW側ではなぜか最初からVLAN100が適用されています。  

ここでEtherChannel（LACP）の設定が適用されるタイミングを確認すると  
**EtherChannel（LACP）がアップした段階** で適用される。  
そのため、VLAN不一致によりLINKDOWN状態ではEtherChannel（LACP）はアップしません。  
結果、L3SW側ではVLAN1のままポートが開かれてしまいました。

## まとめ
EtherChannel（LACP）は非常に便利な技術です。ですが、頭が空っぽの状態で設計してしまうと変なところで躓いてしまいます。  
皆さんもEtherChannel（LACP）を構成する際は対向もしっかり意識して設計しましょう。