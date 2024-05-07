+++
title = "docker環境構築" # ページタイトル
date = 2024-05-04T00:00:00+09:00
weight = 3
draft = false
categories = ["Server"]
tags = ["Author:nakata", "Level:中級", "Docker", "LinuC"]
+++

## 参考サイト

---

https://docs.docker.jp/engine/installation/linux/index.html \
https://supilog.supisupi.com/blog/2p77qe6np5e4/

## 構築環境

---

### ハードウェア

- OS：Almalinux9
- CPU：1core
- Mem：4G
- Storage：50G
- IPaddress：192.168.0.202
- Hostname：dockerserver

### インストールパッケージ

- wget
- docker-ce
- docker-compose(git からダウンロード)

## 構築

---

- wget をインストール

```
$sudo dnf install wget
```

- リポジトリの追加

```
# tmpへ移動
$cd /tmp
# docker-ce.repoをダウンロード
$sudo wget https://download.docker.com/linux/centos/docker-ce.repo
# docker-ce.repoをyum.repos.dへ移動
sudo mv docker-ce.repo /etc/yum.repos.d/
# dockerのリポジトリが有効できているか確認
$sudo dnf repolist
repo id                             repo name
appstream                           AlmaLinux 9 - AppStream
baseos                              AlmaLinux 9 - BaseOS
docker-ce-stable                    Docker CE Stable - x86_64 #これ
extras                              AlmaLinux 9 - Extras
```

- docker-ce のインストール

```
$sudo dnf install docker-ce
```

- docker-compose のインストール

```
# docker-composeのインストール
$sudo wget -O /usr/local/bin/docker-compose https://github.com/docker/compose/releases/download/v2.26.1/docker-compose-linux-x86_64
# docker-composeに実行権を追加
$sudo chmod +x /usr/local/bin/docker-compose
# バージョン確認
$docker --version
Docker version 26.1.1, build 4cf5afa
```

- 個人ユーザーをグループ追加

```
$sudo usermod -aG docker [個人ユーザー]
```

- docker を起動および自動起動化

```
$sudo systemctl start docker
$sudo systemctl enable docker
```
