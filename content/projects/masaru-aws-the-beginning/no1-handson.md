+++
title = '第1回：実施内容'
date = 2025-05-01T19:45:00+09:00
draft = false
weight = 10
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

2025/4/30(水)　 21:00 ～ 22:30

### 場所

Discordイベントルーム

### 出席者(敬称略)

- 作業者
  - まさる
- 確認＆解説者
  - tsukabo
  - あゆむ
- 見学者
　- コミュニティメンバー３０名くらい

---

## 実施内容

{{% notice style="参考情報" %}}
[やることリスト](https://dev.classmethod.jp/articles/aws-baseline-setting-202206/)
{{% /notice %}}

### AWSはじめの一歩系

1. AWSアカウントの開設
   - アカウント名※
   - メールアドレス※
   - ルートユーザーパスワード
   - クレジットカード
   - 名前、電話番号、住所の情報

※後で変えられない。
後々、マルチアカウント構成にすることも考慮すること

{{% notice style="参考情報" %}}
[アカウント解説](https://aws.amazon.com/jp/register-flow/)
{{% /notice %}}

### ルートユーザーでしか出来ない系

1. ルートユーザーのMFA
2. 初期IAMユーザーの作成
   - MFAの設定
3. 初期IAMグループの作成
   - メンバー所属
   - ポリシーアタッチ
4. IAMユーザーのコスト参照設定
5. コストの設定
   - 時間単位の詳細度
   - コスト最適化ハブの有効化

{{% notice style="参考情報" %}}
[IAMのコスト参照](https://zenn.dev/jnxjez/articles/a465a143fa7dcc)
{{% /notice %}}

### IAM個人設定系

1. MFAの設定
2. デフォルトリージョンの設定
3. 言語の設定

### コスト系

1. コスト異常検知の有効化
2. 予算の有効化
3. コスト配分タグの有効化
   - Owner
   - Project

### ルール系

1. 命名規則決める
   - Project？
   - Owner？
   - Name？（Prefix、Suffixなど）

### 追跡系

1. ログS3バケット作成
   - **＝＝＝今回はここまで＝＝＝**
   - ディレクトリ切る
   - ライフサイクルの設定
2. CloudTrail証跡の有効化
3. Configの有効化

{{% notice style="参考情報" %}}
[AWS CloudTrail を触ってみた](https://www.youtube.com/watch?v=erDYixgVJ0o)
[AWS Config を有効化してみた](https://www.youtube.com/watch?v=E8GY09aEwnE&t=79s)
{{% /notice %}}

---

## 宿題

- 時間単位の詳細度
- コスト配分タグの有効化

## 次回予定

- 追跡系続きから
- VPC初期設定

