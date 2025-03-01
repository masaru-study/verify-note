+++
title = "LINUXディストリビューションについて"
date = 2025-03-01T06:19:17+09:00
weight = 7
draft = false
categories = ["Other"]
tags = ["Author:だるま", "Level:初級", "Type:Knowledge", "linux"]
+++

## 主なLINUXディストリビューションについて

### カテゴリ

※5つのパターンがある認識です。

- [1：RedHat系]
- [2：Debian系]
- [3：SlackWare系]
- [4：その他]
- [5：UNIX系（その他）]

### 各ディストリビューション

※随時拡張予定

- [01：MiracleLinux]
- [02：CentOS]
- [03：Fedora]
- [04：AlmaLinux]
- [05：RockyLinux]
- [06：Ubuntu]
- [07：AmazonLinux2]
- [08：AmazonLinux2023]
- [09：KaliLinux]
- [10：Parrot]
- [11：Raspbian]
- [12：Zorin]
- [13：LinuxMint]
- [14：MXLinux]
- [15：BunsenLabs]
- [16：elementaryOS]
- [17：SlackWare]
- [18：OpenSUSE]
- [19：EndeavourOS]
- [20：FreeBSD]

## 目的

このドキュメントでは、主要なLinuxディストリビューションを紹介し、それぞれの特徴と用途について詳しく説明します。これにより、用途に応じた最適なディストリビューションを選択するための参考にしていただければと思います。
実際に、だるま環境でProxmox上（VirtualBox上）でインストールしている、日本語版を主に記載いたしました。
いろいろありすぎて整理しきれていなかったので、これを機にアウトプットしたかった。

## RedHat系

RedHat系のディストリビューションは、エンタープライズ環境で広く使用されている商用Linuxのことが多い。安定性とサポートが重視されます。
（無償のものある）

### MiracleLinux

- [URL](https://www.miraclelinux.com/distribution/download/)
- **特徴**: 高い安定性とセキュリティを提供する。
- **用途**: エンタープライズサーバー、ミッションクリティカルなシステム
- **パッケージ管理**: yum/dnf

## CentOS

- [URL](https://www.centos.org/download/)
- **特徴**: RedHat Enterprise Linux (RHEL)の無償版。安定性が高い。
- **用途**: サーバー環境、エンタープライズシステム
- **パッケージ管理**: yum/dnf

## Fedora

- [URL](https://fedoraproject.org/ja/)
- **特徴**: 最新技術を迅速に取り入れるディストリビューション。(テストイメージ)
- **用途**: デスクトップ、開発環境、テスト環境
- **パッケージ管理**: yum/dnf

### AlmaLinux

- [URL](https://almalinux.org/ja/get-almalinux/)
- **特徴**: CentOSの後継として、コミュニティ主導で開発
- **用途**: サーバー環境、エンタープライズシステム
- **パッケージ管理**: yum/dnf

### RockyLinux

- [URL](https://rockylinux.org/ja-JP/download/)
- **特徴**: CentOSの後継として誕生。コミュニティ主導で開発
- **用途**: サーバー環境、エンタープライズシステム
- **パッケージ管理**: yum/dnf

### AmazonLinux2

- [URL](https://cdn.amazonlinux.com/os-images/2.0.20250201.0/)（現時点での最新版）
- **特徴**: AWSのサービスと容易に統合できる。オンプレミスでの使用も可能です。ISOファイルでのインストールは小細工が必要でした。CUI環境が基本ですが、MateなどでGUI環境でも可能のようです。
- **用途**: サーバー環境、エンタープライズシステム
- **パッケージ管理**: yum

### AmazonLinux2023

- [URL](https://cdn.amazonlinux.com/al2023/os-images/2023.6.20250211.0/kvm/)（現時点での最新版）
- **特徴**: AWSのサービスと容易に統合できる。オンプレミスでの使用も可能です。ISOファイルでのインストールは小細工が必要でした。
- **用途**: サーバー環境、エンタープライズシステム
- **パッケージ管理**: dnf

## Debian系

Debian系のディストリビューションは、安定性と豊富なパッケージが特徴です。幅広い用途に対応しています。

### Ubuntu

- [URL](https://jp.ubuntu.com/download/)
- **特徴**: ユーザーフレンドリーでデスクトップ環境にも強い。Ubuntuでも有償プランもある。
- **用途**: デスクトップPC、サーバー、クラウド
- **パッケージ管理**: apt
- **亜種**:
  - **Kubuntu**: KDEデスクトップ環境を搭載
  - **Xubuntu**: 軽量なXfceデスクトップ環境を搭載
  - **Lubuntu**: より軽量なLXQtデスクトップ環境を搭載

### KaliLinux

- [URL](https://www.kali.org/get-kali/#kali-platforms/)
- **特徴**: セキュリティテストとフォレンジック分析用に特化
- **用途**: セキュリティ監査、ペネトレーションテスト
- **パッケージ管理**: apt

### Parrot

- [URL](https://www.kali.org/get-kali/#kali-platforms/)
- **特徴**: セキュリティテスト、開発、プライバシー保護に特化
- **用途**: セキュリティ監査、開発環境
- **パッケージ管理**: apt

### Raspbian

- [URL](https://www.raspberrypi.com/software/)
- **特徴**: Raspberry Pi専用の最適化されたディストリビューションです。※Raspberry Pi Imagerでもインストール可能
- **用途**: 教育、エンターテイメント、プロジェクト開発
- **パッケージ管理**: apt

### Zorin

- [URL](https://zorin.com/os/download/)
- **特徴**: Windowsからの移行を容易にするデザイン。Pro(有料)/Core/Liteとある。
- **用途**: デスクトップPC、初心者向けです。
- **パッケージ管理**: apt

### LinuxMint

- [URL](https://www.linuxmint.com/download.php)
- **特徴**: ユーザーフレンドリーでデスクトップ環境に特化。Cinnamon/Xfce/MATEとある。
- **用途**: デスクトップPC、初心者向けです。
- **パッケージ管理**: apt

### MXLinux

- [URL](https://mxlinux.org/)
- **特徴**: ユーザーフレンドリーでデスクトップ環境に特化。Xfceのインターフェースです。（Windowsユーザは乗り換えやすい（GUI中心））ファイヤーウォールが初期から外部からのDenyになっているため、セキュアでした。パッケージ管理のソフトも使いやすくなってます。（SynapticやMXパッケージ）
- **用途**: デスクトップPC、初心者向けです。
- **パッケージ管理**: apt

### BunsenLabs

- [URL](https://www.bunsenlabs.org/installation.html)
- **特徴**: ユーザーフレンドリーでデスクトップ環境に特化。KDE、GNOME、Xfceのインターフェースです。落ち着いているGUI
- **用途**: デスクトップPC、初心者向けです。
- **パッケージ管理**: apt

### elementary OS

- [URL](https://elementary.io/ja/)
- **特徴**: ユーザーフレンドリーでデスクトップ環境に特化。（MACユーザは乗り換えやすい（GUI中心）寄付（無料でもカスタム0円）で導入できる。
- **用途**: デスクトップPC、初心者向けです。
- **パッケージ管理**: apt

## SlackWare系

SlackWare系のディストリビューションは、シンプルさと安定性を重視します。

### SlackWare

- [URL](https://mirrors.slackware.com/slackware/slackware-iso/)
- **特徴**: シンプルさと安定性を重視したLinuxディストリビューション。ユーザーフレンドリーなインストールプロセスや設定ツールは少ないが、その代わりにシステムのコントロールが高く、エキスパートユーザーに好まれる。（パッケージ管理じゃない）
- **用途**: システム管理者やエキスパートユーザーが好むディストリビューションで、教育、研究、サーバー環境などで利用される。
- **パッケージ管理**: pkgtool/slackpkg/sbopkg

## その他系

上記（RedHat系、Debian系、SlackWare系）以外のもの

### OpenSUSE

- [URL](https://get.opensuse.org/ja/)
- **特徴**: 安定性と最新技術のバランスが取れたディストリビューションです。Tumbleweed/Leap/Leap Micro/Microとある。RHEL系に属する。
- **用途**: デスクトップ、サーバー、開発環境
- **パッケージ管理**: zypper

### EndeavourOS

- [URL](https://endeavouros.com/)　※Arch系
- **特徴**: EndeavourOSは、★Arch Linuxベースのディストリビューションで、シンプルさとカスタマイズ性を重視しながらも、ユーザーフレンドリーなインストールと操作性を提供することで人気を集めています。Xfce、KDE Plasma、GNOME、Cinnamon
- **用途**: デスクトップ、サーバー、開発環境
- **パッケージ管理**: pacman

## UNIX系（その他）（Linuxみたいなもの）

狭義ではLINUXのディストリビューションではないですが、現場でつかっていたため、記載してあります。

### FreeBSD

- [URL](https://www.freebsd.org/)
- **特徴**: FreeBSDはシンプルさと安定性を重視したUNIX系オペレーティングシステムです。ユーザーフレンドリーなインストールプロセスや設定ツールは少ないですが、その代わりにシステムのコントロールが高く、エキスパートユーザーに好まれています。Linuxではなく、BSD系のオペレーティングシステムであることが特徴です。
- **用途**: システム管理者やエキスパートユーザーが好むオペレーティングシステムで、教育、研究、サーバー環境などで利用されます。特にネットワークやセキュリティ関連の分野で高い評価を受けています。
- **パッケージ管理**: pkg

### 参考情報

- [Wikipedia - Linuxディストリビューション](https://en.wikipedia.org/wiki/Linux_distribution)
- [DistrWatch](https://distrowatch.com/)
