+++
title = 'Ubuntu Server クラウド/インフラ開発環境セットアップガイド'
date = 2025-01-27T10:00:00+09:00
draft = true
categories = ["Server"]
tags = ["Author:ayumu", "Level:中級", "Type:Handson", "Ubuntu", "Linux", "セットアップ", "自動化", "クラウド", "DevOps"]
+++

- [概要](#概要)
- [前提条件と準備](#前提条件と準備)
- [クイックスタート（自動実行）](#クイックスタート自動実行)
- [手動セットアップ手順](#手動セットアップ手順)
  - [基本システム設定](#基本システム設定)
  - [基本パッケージのインストール](#基本パッケージのインストール)
  - [クラウド・DevOps ツールの導入](#クラウドdevops-ツールの導入)
  - [コンテナ・Kubernetes 環境](#コンテナkubernetes-環境)
- [ユーザー環境設定](#ユーザー環境設定)
- [セットアップ確認](#セットアップ確認)
- [よくある問題とトラブルシューティング](#よくある問題とトラブルシューティング)
- [定期メンテナンス](#定期メンテナンス)

## 概要

現代のクラウド・インフラエンジニアリング環境に必要なツールセットを Ubuntu Server に効率的に導入するためのセットアップガイドです。本ガイドでは以下の環境を構築します：

### 導入されるツール・環境

**クラウド・DevOps ツール**

- AWS CLI v2（自動更新機能付き）
- Terraform（HashiCorp 公式リポジトリ）
- GitHub CLI（bash 補完機能付き）
- PowerShell Core

**コンテナ・Kubernetes エコシステム**

- Docker CE（公式リポジトリ）
- Podman + Buildah（Docker 代替）
- kubectl（bash 補完・エイリアス設定済み）
- Helm（パッケージマネージャー）
- Kustomize（YAML 構成管理）
- ArgoCD CLI（GitOps）
- yq（YAML 処理ツール、自動更新機能付き）

**開発・運用支援**

- 日本語環境の完全設定
- 包括的なシステムユーティリティ
- ネットワーク診断・セキュリティツール
- Active Directory 統合機能

## 前提条件と準備

### システム要件

- **OS**: Ubuntu Server 24.04 LTS（推奨）または 22.04 LTS
- **権限**: sudo 権限を持つユーザーアカウント
- **ネットワーク**: インターネット接続環境
- **ディスク容量**: 最低 10GB の空き容量

### 事前確認

```bash
# Ubuntuバージョン確認
lsb_release -a

# ディスク容量確認
df -h /

# インターネット接続確認
ping -c 3 google.com
```

## クイックスタート（自動実行）

時間を節約したい場合は、以下のワンライナーコマンドで全体のセットアップを自動実行できます：

```bash
# 自動セットアップスクリプトのダウンロードと実行
curl -fsSL https://raw.githubusercontent.com/masaru-study/setup-scripts/main/ubuntu-server-setup.sh | sudo bash
```

> **注意**: セキュリティ上の理由から、実際の運用前には必ずスクリプトの内容を確認してください。

### 個別実行版

段階的にセットアップを行いたい場合は、以下の手動セットアップ手順に従ってください。

## 手動セットアップ手順

### ルート権限での作業開始

```bash
# root権限に切り替え
sudo su -

# 作業ディレクトリに移動
cd /tmp
```

### 基本システム設定

#### 1. セキュリティとコンテナ準備

```bash
# AppArmor の無効化 (開発・テスト環境用)
if systemctl is-active --quiet apparmor; then
    echo "AppArmorを無効化しています..."
    aa-teardown 2>/dev/null || true
    systemctl disable apparmor
    echo "AppArmorを無効化しました"
fi

# コンテナ環境用 UID/GID 範囲の拡張
echo "コンテナ用のUID/GID範囲を設定しています..."
cp /etc/login.defs /etc/login.defs.backup
sed -i "s/^UID_MAX.*$/UID_MAX   2000999999/g" /etc/login.defs
sed -i "s/^SUB_UID_MIN.*$/SUB_UID_MIN   2001000000/g" /etc/login.defs
sed -i "s/^SUB_UID_MAX.*$/SUB_UID_MAX   2147000000/g" /etc/login.defs
sed -i "s/^GID_MAX.*$/GID_MAX   2000999999/g" /etc/login.defs
sed -i "s/^SUB_GID_MIN.*$/SUB_GID_MIN   2001000000/g" /etc/login.defs
sed -i "s/^SUB_GID_MAX.*$/SUB_GID_MAX   2147000000/g" /etc/login.defs

# subuid/subgid の設定
[ -f /etc/subuid ] && sed -i "s/:100000:/:2001000000:/g" /etc/subuid
[ -f /etc/subgid ] && sed -i "s/:100000:/:2001000000:/g" /etc/subgid
echo "UID/GID範囲設定完了"
```

#### 2. システム基盤の最適化

```bash
# 日本のミラーサーバーに変更（ダウンロード速度向上）
echo "パッケージソースを日本のミラーに変更しています..."
if [ -f /etc/apt/sources.list.d/ubuntu.sources ]; then
    cp /etc/apt/sources.list.d/ubuntu.sources /etc/apt/sources.list.d/ubuntu.sources.backup
    sed -i 's@http://archive.ubuntu.com/@http://jp.archive.ubuntu.com/@g' /etc/apt/sources.list.d/ubuntu.sources
fi

# sudo権限の設定（admin グループ）
echo "%adm ALL=(ALL) NOPASSWD:ALL" | tee /etc/sudoers.d/NO-PASSWD > /dev/null
chmod 440 /etc/sudoers.d/NO-PASSWD

# システム更新
echo "システムを最新状態に更新しています..."
apt update && apt full-upgrade -y
echo "システム更新完了"
```

#### 3. 日本語環境の完全設定

```bash
# 日本語パッケージのインストール
echo "日本語環境をセットアップしています..."
apt install -y \
    language-pack-ja \
    language-pack-ja-base \
    manpages-ja \
    manpages-ja-dev

# ロケールとタイムゾーンの設定
localectl set-locale LANG=ja_JP.UTF-8 LANGUAGE="ja_JP:ja"
timedatectl set-timezone Asia/Tokyo

# キーボード設定（非対話モード）
cat > /tmp/keyboard-config << EOF
keyboard-configuration keyboard-configuration/layout select Japanese
keyboard-configuration keyboard-configuration/variant select Japanese
keyboard-configuration keyboard-configuration/model select Generic 105-key PC (intl.)
keyboard-configuration keyboard-configuration/compose select No compose key
EOF

debconf-set-selections < /tmp/keyboard-config
DEBIAN_FRONTEND=noninteractive dpkg-reconfigure keyboard-configuration
rm /tmp/keyboard-config

echo "日本語環境設定完了"
```

### 基本パッケージのインストール

効率的なパッケージ管理とエラーハンドリングを含む、構造化されたインストール手順です。

#### 1. システム基盤ツール

```bash
echo "システム基盤ツールをインストールしています..."

# システムユーティリティ
apt install -y \
    vim less sudo htop expect tree acl bash-completion \
    jq zip unzip nkf gawk psmisc dos2unix sysstat \
    binutils lsb-release gnupg apt-utils \
    software-properties-common apt-transport-https

if [ $? -eq 0 ]; then
    echo "✓ システム基盤ツールのインストール完了"
else
    echo "✗ システム基盤ツールのインストールに失敗しました"
    exit 1
fi
```

#### 2. ネットワーク診断・セキュリティツール

```bash
echo "ネットワーク診断・セキュリティツールをインストールしています..."

apt install -y \
    iputils-tracepath iputils-ping ca-certificates \
    bind9-utils net-tools traceroute nmap \
    netcat-openbsd dnsutils

if [ $? -eq 0 ]; then
    echo "✓ ネットワークツールのインストール完了"
else
    echo "✗ ネットワークツールのインストールに失敗しました"
    exit 1
fi
```

#### 3. 通信・ファイル共有

```bash
echo "通信・ファイル共有ツールをインストールしています..."

apt install -y \
    curl git wget telnet \
    nfs-common smbclient cifs-utils lynx \
    openssh-client openssh-server

if [ $? -eq 0 ]; then
    echo "✓ 通信・ファイル共有ツールのインストール完了"
else
    echo "✗ 通信・ファイル共有ツールのインストールに失敗しました"
    exit 1
fi
```

#### 4. Active Directory 統合（オプション）

```bash
echo "Active Directory統合ツールをインストールしています..."

# Note: このセクションはAD統合が必要な場合のみ実行
read -p "Active Directory統合機能をインストールしますか？ (y/N): " install_ad

if [[ $install_ad =~ ^[Yy]$ ]]; then
    apt install -y \
        realmd sssd sssd-tools libnss-sss libpam-sss \
        adcli samba-common-bin oddjob oddjob-mkhomedir \
        packagekit krb5-user

    if [ $? -eq 0 ]; then
        echo "✓ Active Directory統合ツールのインストール完了"
    else
        echo "✗ Active Directory統合ツールのインストールに失敗しました"
    fi
else
    echo "Active Directory統合機能をスキップしました"
fi
```

#### 5. 開発環境基盤

```bash
echo "開発環境基盤をセットアップしています..."

apt install -y \
    build-essential \
    python3 python3-pip python3-dev python-is-python3

if [ $? -eq 0 ]; then
    echo "✓ 開発環境基盤のセットアップ完了"
else
    echo "✗ 開発環境基盤のセットアップに失敗しました"
    exit 1
fi
```

### クラウド・DevOps ツールの導入

各ツールにエラーハンドリングと自動更新機能を含む、実用的なセットアップ手順です。

#### 1. AWS CLI v2（自動更新機能付き）

```bash
echo "AWS CLI v2 をインストールしています..."

# 一時ファイルの準備
TEMP_DIR=$(mktemp -d)
cd "$TEMP_DIR"

# AWS CLI v2 のダウンロードとインストール
if wget -q https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip; then
    unzip -oq awscli-exe-linux-x86_64.zip
    if ./aws/install -i /usr/local/aws-cli -b /usr/local/bin; then
        echo "✓ AWS CLI v2 インストール完了"

        # 自動更新スクリプトの作成
        cat << "EOF" > /usr/local/aws-cli/update-awscli
#!/bin/bash
set -euo pipefail

echo "AWS CLI の最新版をダウンロードしています..."
TEMP_DIR=$(mktemp -d)
trap "rm -rf $TEMP_DIR" EXIT

cd "$TEMP_DIR"
if wget -q https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip; then
    unzip -oq awscli-exe-linux-x86_64.zip
    ./aws/install -i /usr/local/aws-cli -b /usr/local/bin --update
    echo "✓ AWS CLI の更新が完了しました"
else
    echo "✗ AWS CLI のダウンロードに失敗しました"
    exit 1
fi
EOF
        chmod 755 /usr/local/aws-cli/update-awscli
        ln -sf /usr/local/aws-cli/update-awscli /usr/local/bin/update-awscli
        echo "  更新コマンド: update-awscli"
    else
        echo "✗ AWS CLI のインストールに失敗しました"
    fi
else
    echo "✗ AWS CLI のダウンロードに失敗しました"
fi

cd - && rm -rf "$TEMP_DIR"
```

#### PowerShell

```bash
wget -q https://packages.microsoft.com/config/ubuntu/24.04/packages-microsoft-prod.deb
dpkg -i ./packages-microsoft-prod.deb && rm -rf ./packages-microsoft-prod.deb
apt update && apt install -y powershell
```

#### GitHub CLI

```bash
wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg | tee /etc/apt/keyrings/github-cli.gpg > /dev/null
chmod 0644 /etc/apt/keyrings/github-cli.gpg
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/github-cli.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null
chmod 0644 /etc/apt/sources.list.d/github-cli.list
apt update && apt install -y gh
echo "eval \"\$(gh completion -s bash)\"" > /etc/profile.d/githubcli-completion.sh
```

#### Terraform

```bash
wget -qO- https://apt.releases.hashicorp.com/gpg | gpg --dearmor -o /etc/apt/keyrings/hashicorp.gpg > /dev/null
chmod 0644 /etc/apt/keyrings/hashicorp.gpg
echo "deb [signed-by=/etc/apt/keyrings/hashicorp.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list > /dev/null
chmod 0644 /etc/apt/sources.list.d/hashicorp.list
apt update && apt install -y terraform
```

#### Docker リポジトリ追加

```bash
wget -qO- https://download.docker.com/linux/ubuntu/gpg | tee /etc/apt/keyrings/docker.asc > /dev/null
chmod 0644 /etc/apt/keyrings/docker.asc
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
chmod 0644 /etc/apt/sources.list.d/docker.list
```

#### kubectl

```bash
wget -qO- https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/k8s.gpg > /dev/null
chmod 0644 /etc/apt/keyrings/k8s.gpg
echo 'deb [signed-by=/etc/apt/keyrings/k8s.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | tee /etc/apt/sources.list.d/k8s.list > /dev/null
chmod 644 /etc/apt/sources.list.d/k8s.list
apt update && apt install --no-install-recommends -y kubectl
echo "alias k=\"kubectl\"" > /etc/profile.d/kubectl-additional.sh
echo "source <(kubectl completion bash)" >> /etc/profile.d/kubectl-additional.sh
echo "complete -F __start_kubectl k" >> /etc/profile.d/kubectl-additional.sh
```

#### Helm

```bash
wget -qO- https://baltocdn.com/helm/signing.asc | gpg --dearmor -o /etc/apt/keyrings/helm.gpg > /dev/null
chmod 0644 /etc/apt/keyrings/helm.gpg
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | tee /etc/apt/sources.list.d/helm.list > /dev/null
chmod 0644 /etc/apt/sources.list.d/helm.list
apt update && apt install -y helm
```

#### yq（YAML プロセッサー）

```bash
TMP_FILE=$(mktemp)
wget -q https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 -O "${TMP_FILE}"
chmod 0755 "${TMP_FILE}"
APP_VERSION=$("${TMP_FILE}" -V | grep -oE 'v[0-9]+(\.[0-9]+)*')
install -m 0755 -o root -g root -d /usr/local/yq
install -m 0755 -o root -g root "${TMP_FILE}" /usr/local/yq/yq_${APP_VERSION}
ln -sf /usr/local/yq/yq_${APP_VERSION} /usr/local/bin/yq
rm -rf "${TMP_FILE}"
```

yq のアップデートスクリプト：

```bash
cat << "EOF" > /usr/local/yq/update-yq
#!/bin/bash
set -euo pipefail
TMP_FILE=$(mktemp)
trap 'rm -f "${TMP_FILE}"' EXIT
echo "yqの最新版をダウンロードしています..."
if ! wget -q https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 -O "${TMP_FILE}"; then
    echo "エラー: yqのダウンロードに失敗しました。"; exit 1
fi
chmod 0755 "${TMP_FILE}"
APP_VERSION=$("${TMP_FILE}" -V | grep -oE 'v[0-9]+(\.[0-9]+)*')
if [ -z "${APP_VERSION}" ]; then
    echo "エラー: バージョン番号の取得に失敗しました。"; exit 1
fi
echo "バージョン ${APP_VERSION} をインストールします。"
install -m 0755 -o root -g root "${TMP_FILE}" /usr/local/yq/yq_${APP_VERSION}
ln -sf "/usr/local/yq/yq_${APP_VERSION}" /usr/local/bin/yq
echo "yqのアップデートが完了しました。現在のバージョン: ${APP_VERSION}"
EOF
chmod 0755 /usr/local/yq/update-yq
ln -sf /usr/local/yq/update-yq /usr/local/bin/update-yq
```

#### ArgoCD CLI

```bash
TMP_FILE=$(mktemp)
wget -q https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64 -O "${TMP_FILE}"
chmod +x "${TMP_FILE}"
APP_VERSION=$("${TMP_FILE}" version --client | head -n 1 | grep -oE 'v[0-9]+(\.[0-9]+)*')
install -m 0755 -o root -g root -d /usr/local/argocd
install -m 0755 -o root -g root "${TMP_FILE}" /usr/local/argocd/argocd_${APP_VERSION}
ln -sf /usr/local/argocd/argocd_${APP_VERSION} /usr/local/bin/argocd
rm -rf "${TMP_FILE}"
```

#### Kustomize

```bash
TMP_FILE=$(mktemp)
WGET_URL=$(curl -s https://api.github.com/repos/kubernetes-sigs/kustomize/releases/latest | grep "browser_download_url.*linux_amd64" | cut -d '"' -f 4)
wget -q ${WGET_URL} -O - | tar -xz -O kustomize > "${TMP_FILE}"
chmod 0755 "${TMP_FILE}"
APP_VERSION=$("${TMP_FILE}" version)
install -m 0755 -o root -g root -d /usr/local/kustomize
install -m 0755 -o root -g root "${TMP_FILE}" /usr/local/kustomize/kustomize_${APP_VERSION}
ln -sf /usr/local/kustomize/kustomize_${APP_VERSION} /usr/local/bin/kustomize
rm -rf "${TMP_FILE}"
```

#### Podman（Docker の代替）

```bash
apt install -y podman buildah skopeo podman-compose
podman system migrate
```

## ユーザーでの作業

root でのシステム設定が完了したら、一般ユーザーに切り替えて Node.js をインストールします：

```bash
# Node js インストール
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
source ~/.bashrc
nvm ls-remote
nvm install --lts
```

## まとめ

このセットアップスクリプトにより、以下のような包括的な開発・運用環境が構築されます：

- **言語・ランタイム**: Python 3、Node.js（LTS）
- **クラウドツール**: AWS CLI、Terraform
- **コンテナ**: Docker、Podman、buildah
- **Kubernetes**: kubectl、helm、kustomize、ArgoCD CLI
- **開発ツール**: GitHub CLI、PowerShell、yq
- **システム管理**: 各種 Linux ユーティリティ、日本語環境

一部のツールには自動アップデートスクリプトも含まれているため、継続的なメンテナンスも簡単に行えます。このセットアップガイドを使用することで、Ubuntu Server を効率的にクラウド・インフラエンジニアリング用途で使用できる状態にセットアップできます。
