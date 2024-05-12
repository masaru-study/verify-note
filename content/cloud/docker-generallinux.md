+++
title = "Dockerレシピ -汎用Linux-"
date = 2024-05-03T02:56:41+09:00
weight = 3
draft = false
categories = ["Server", "Cloud"]
tags = ["Author:あゆむ", "Level:中級", "Type:Knowledge", "Docker"]
+++

## 使い方

### 接続方法

工事中

### データ保存方法

工事中

### パスワード変更方法

工事中

## パラメータシート

### ディレクトリ構造

工事中

### docker-compose.yml

```yml
services:
  common-debian12:
    build:
      context: ./
      dockerfile: Dockerfile
    image: custom/common-debian12:latest
    container_name: common-debian12
    hostname: common-debian12
    restart: no
    secrets:
      - CHANGE_PW
    volumes:
      - type: bind
        source: "./bind/host/"
        target: "/host/"
    extra_hosts:
      - host.docker.internal:host-gateway
    ports:
      - "9999:22"
    networks:
      - frontend

networks:
  frontend:
    external: true

secrets:
  CHANGE_PW:
    file: ./bind/secrets/PW.txt
```

### Dockerfile

```Dockerfile
# syntax=docker/dockerfile:1.7
FROM debian:11

# ============パッケージインストール作業============
USER root

# OSパッケージインストール -基本編-
RUN apt-get update && apt-get install -y \
    vim \
    less \
    sudo \
    htop \
    expect \
    tree \
    acl \
    bash-completion \
    apt-utils \
    software-properties-common \
    zip \
    unzip \
    dos2unix

# OSパッケージインストール -ネットワーク設定編-
RUN apt-get update && apt-get install -y \
    iputils-tracepath \
    iputils-ping \
    apt-transport-https \
    ca-certificates \
    bind9-utils \
    network-manager \
    net-tools \
    traceroute \
    nmap \
    netcat-openbsd \
    dnsutils

# OSパッケージインストール -ネットワークツール編-
RUN apt-get update && apt-get install -y \
    curl \
    git \
    wget \
    telnet \
    nfs-common \
    smbclient \
    openssh-client \
    openssh-server \
    lynx

# OSパッケージインストール -フォント編-
RUN apt-add-repository contrib -y \
 && apt-add-repository non-free -y \
 && apt-get update && apt-get install -y \
    build-essential \
    ttf-mscorefonts-installer \
    fontconfig \
    fonts-noto-cjk \
    fonts-noto-cjk-extra \
    fonts-noto-color-emoji \
    fonts-ipaexfont \
    fonts-ipaexfont-gothic \
    fonts-ipaexfont-mincho \
    fonts-ipafont \
    fonts-ipafont-gothic \
    fonts-ipafont-mincho \
    fonts-ipamj-mincho \
 && fc-cache -r

# OSパッケージインストール -汎用開発編-
RUN apt-get update && apt-get install -y \
    build-essential

# OSパッケージインストール -python編-
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    python3-dev \
    python-is-python3 \
    python3-numpy \
    python3-pandas \
    python3-matplotlib \
    python3-seaborn \
    python3-openpyxl \
    python3-pil \
    python3-sphinx \
    python3-sphinx-rtd-theme \
    python3-sphinx-autobuild

# OSパッケージインストール -Go編-
RUN apt-get update && apt-get install -y \
    golang \
    delve

# Pwshインストール
RUN wget -q https://packages.microsoft.com/config/debian/11/packages-microsoft-prod.deb \
 && dpkg -i ./packages-microsoft-prod.deb \
 && apt-get update && apt-get install -y powershell \
 && rm -rf ./packages-microsoft-prod.deb

# AWS CLIインストール
RUN wget -q https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip \
 && unzip ./awscli-exe-linux-x86_64.zip \
 && ./aws/install -i /usr/local/aws-cli -b /usr/local/bin \
 && rm -rf ./awscli-exe-linux-x86_64.zip ./aws

# Hugoインストール
RUN wget -q https://github.com/gohugoio/hugo/releases/download/v0.125.5/hugo_extended_0.125.5_linux-amd64.deb \
 && dpkg -i ./hugo_extended_0.125.5_linux-amd64.deb \
 && rm -rf ./hugo_extended_0.125.5_linux-amd64.deb

# Github CLIインストール
RUN mkdir -p -m 755 /etc/apt/keyrings \
 && wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg | tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
 && chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
 && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
 && apt-get update && apt-get install -y gh \
 && echo "eval \"\$(gh completion -s bash)\"" > /etc/profile.d/githubcli-completion.sh

# Firpleフォントインストール
RUN wget -q https://github.com/negset/Firple/releases/download/4.000/Firple.zip \
 && unzip ./Firple.zip  \
 && wget -q https://github.com/negset/Firple/releases/download/4.000/FirpleSlim.zip \
 && unzip ./FirpleSlim.zip  \
 && cp -vf ./Firple*.ttf /usr/local/share/fonts/ \
 && rm -rf ./Firple* \
 && fc-cache -r

# ============OS基本設定作業============
USER root

# OS日本語設定

RUN apt-get update && apt-get install -y locales \
 && sed -i 's/# ja_JP.UTF-8 UTF-8/ja_JP.UTF-8 UTF-8/g' /etc/locale.gen \
 && locale-gen \
 && echo "export LANG='ja_JP.UTF-8'" > /etc/profile.d/lang-jp.sh \
 && echo "export LC_ALL='ja_JP.UTF-8'" >> /etc/profile.d/lang-jp.sh \
 && echo "export LANGUAGE='ja_JP:ja'" >> /etc/profile.d/lang-jp.sh \
 && ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime

# sudo可能な一般ユーザー作成
RUN groupadd -g 1000 group \
 && useradd -m -s /bin/bash -u 1000 -g 1000 -G sudo user \
 && echo "user:P@ssw0rd" | chpasswd \
 && echo "user   ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# SSHサーバーの準備
RUN mkdir -p /var/run/sshd \
 && chmod 755 /var/run/sshd \
 && chown root.root /var/run/sshd \
 && sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/g' /etc/ssh/sshd_config \
 && ssh-keygen -A

# ホストOSとの共有フォルダ作成
RUN mkdir /host && chmod 1777 /host



# ============実行ユーザー作業============
USER user

# ホストOSとの共有フォルダのリンク
RUN ln -s /host /home/user/host


# ============起動設定============
USER root

# OSパッケージの不要ファイル掃除
RUN apt-get upgrade -y \
 && apt-get autoremove -y \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# 起動スクリプトを生成する。
COPY <<-EOF /etc/docker-entrypoint.sh
#!/bin/bash
# ----------------Start---------------
[ -n "$(cat /run/secrets/CHANGE_PW)" ] && echo "user:$(cat /run/secrets/CHANGE_PW)" | chpasswd
/usr/sbin/sshd -D
# ----------------End-----------------
EOF
RUN chmod +x /etc/docker-entrypoint.sh

# 公開するポート
EXPOSE 22

# 起動時に実行するコマンド
CMD ["/etc/docker-entrypoint.sh"]
```
