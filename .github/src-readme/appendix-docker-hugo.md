## 使い方

### 接続方法

コンテナの操作は、SSH クライアントで 1322 ポートに接続しに行くとコンソール操作できます。  
認証はパスワード認証で、ユーザー名は user です。  
hugo サーバーの Web ページは、ブラウザで 1313 ポートに接続しに行くと閲覧できます。

### データ保存方法

コンテナの~/hugo 内に保存したデータは永続化されます。

### パスワード変更方法

./bind/secrets/PW.txt に書いた文字列がコンテナ起動時にパスワードとして設定されます。

## パラメータシート

### ディレクトリ構造

```
.
├── Dockerfile          ・・・作成するコンテナを制御するパラメータを記載しているファイル
├── docker-compose.yml  ・・・コンテナを制御するパラメータを記載しているファイル
└── bind
    ├── hugo            ・・・ファイルを永続化させるディレクトリ
    └── secrets
        └── PW.txt      ・・・コンテナ起動時に自動変更するパスワードを記述するファイル
```

### docker-compose.yml

```yml
services:
  hugo-server:
    build:
      context: ./
      dockerfile: Dockerfile
    image: custom/hugo-server:latest
    container_name: hugo-server
    hostname: hugo-server
    restart: no
    secrets:
      - CHANGE_PW
    volumes:
      - type: bind
        source: "./bind/hugo/"
        target: "/hugo/"
    extra_hosts:
      - host.docker.internal:host-gateway
    ports:
      - "1313:1313"
      - "1322:22"
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
FROM debian:12

# ============パッケージインストール作業============
USER root

# OSパッケージリポジトリ設定
COPY <<-EOF /etc/apt/sources.list
deb http://deb.debian.org/debian bookworm main contrib non-free non-free-firmware
deb http://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware
deb http://deb.debian.org/debian-security/ bookworm-security main contrib non-free non-free-firmware
EOF
RUN rm -rf /etc/apt/sources.list.d/debian.sources

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
    zip \
    unzip \
    dos2unix \
    apt-utils \
    software-properties-common \
    apt-transport-https \
    ca-certificates \
    curl \
    git \
    wget \
    openssh-client \
    openssh-server

# OSパッケージインストール -Go編-
RUN apt-get update && apt-get install -y \
    golang \
    delve

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

# OS日本語設定
RUN apt-get update && apt-get install -y locales \
 && sed -i 's/# ja_JP.UTF-8 UTF-8/ja_JP.UTF-8 UTF-8/g' /etc/locale.gen \
 && locale-gen \
 && ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
ENV LANG=ja_JP.UTF-8
ENV LC_ALL=ja_JP.UTF-8
ENV LANGUAGE=ja_JP:ja

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
RUN mkdir /hugo && chmod 1777 /hugo

# ホストOSとの共有フォルダのリンク
RUN ln -s /hugo /home/user/hugo


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
EXPOSE 1313

# 起動時に実行するコマンド
CMD ["/etc/docker-entrypoint.sh"]
```
