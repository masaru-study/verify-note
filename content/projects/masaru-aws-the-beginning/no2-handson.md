+++
title = '第2回：実施内容'
date = 2025-05-01T19:45:00+09:00
draft = false
weight = 20
categories = ["Projects"]
tags = ["Author:あゆむ", "Type:Handson", "AWS"]
+++

- [概要](#概要)
  - [日時](#日時)
  - [場所](#場所)
  - [出席者(敬称略)](#出席者敬称略)
- [実施内容](#実施内容)
  - [AWSはじめの一歩系](#AWSはじめの一歩系)
  - [ルートユーザーでしか出来ない系](#ルートユーザーでしか出来ない系)
  - [IAM個人設定系](#iam個人設定系)
  - [コスト系](#コスト系)
  - [ルール系](#ルール系)
  - [追跡系](#追跡系)
- [宿題](#宿題)
- [次回予定](#次回予定)

---

## 概要

### 日時

2025/5/15(木)　 21:00 ～ 22:30

### 場所

Discordイベントルーム

### 出席者(敬称略)

- 作業者
  - まさる
- 確認＆解説者
  - tsukabo
  - あゆむ
- 見学者
　- コミュニティメンバー２０名くらい

---

## 座学内容

### サービスの大まかな種類について

{{< figure src="/img/masaru-aws-the-beginning/service-kind.drawio.png" title="サービスの種類" class="center" width="50%" height="50%" >}}

### クラウドの課金について

- ここの内容に近いことを話した。  
  https://cloudnavi.nhn-techorus.com/archives/1865

## 実施内容

### ルートユーザーでしか出来ない系

1. コストの設定
   - 時間単位の詳細度

### コスト系

1. コスト配分タグの有効化
   - Name
   - Owner
   - Project

### 追跡系

1. ログS3バケット作成
   - ディレクトリ切る
   - ライフサイクルの設定
2. CloudTrail証跡の有効化
**＝＝＝今回はここまで＝＝＝**
3. Configの有効化

{{% notice style="参考情報" %}}
[AWS CloudTrail を触ってみた](https://www.youtube.com/watch?v=erDYixgVJ0o)
[AWS Config を有効化してみた](https://www.youtube.com/watch?v=E8GY09aEwnE&t=79s)
{{% /notice %}}

---

## 宿題

- 無し

## 次回予定

- 追跡系続き
- Route53ドメイン取得
- Route53ホストゾーン作成
- マルチアカウント設定 
