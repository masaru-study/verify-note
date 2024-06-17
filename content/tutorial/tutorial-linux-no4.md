+++
#テンプレート：ハンズオン
title = '【Linux】No.4 SSH編'
date = 2024-06-15T22:00:51+09:00
draft = false
weight = 4
categories = ["Tutorial"]
tags = ["Author:あゆむ", "LPIC1", "LinuC1"]
+++

## 目次

- [目次](#目次)
- [概要](#概要)
  - [本ハンズオンで身につくこと](#本ハンズオンで身につくこと)
  - [出てくるコマンド／操作／キーワード](#出てくるコマンド操作キーワード)
  - [事前準備](#事前準備)
  - [構成図](#構成図)
- [ハンズオン](#ハンズオン)
  - [Virtualbox のネットワーク設定](#virtualbox-のネットワーク設定)
  - [SSH デーモンの確認](#ssh-デーモンの確認)
  - [Windows から Linux に接続](#windows-から-linux-に接続)
  - [SSH で使用する鍵を生成](#ssh-で使用する鍵を生成)
  - [生成した公開鍵を Linux へ転送](#生成した公開鍵を-linux-へ転送)
  - [鍵認証を使用した SSH 接続](#鍵認証を使用した-ssh-接続)
  - [パスワード認証の禁止](#パスワード認証の禁止)
  - [teraterm で Linux に接続](#teraterm-で-linux-に接続)

## 概要

Virtualbox の中にある Linux に Teraterm で CLI 接続しましょう。

### 本ハンズオンで身につくこと

- SSH による遠隔操作
- SSH 公開鍵認証方式

### 出てくるコマンド／操作／キーワード

- systemctl
- ssh
- ssh-keygen
- /etc/ssh/sshd_config

### 事前準備

- ホスト OS に teraterm をインストール

### 構成図

{{< figure src="/img/tutorial-linux-no4/fig-diagram.drawio.svg" title="fig-diagram" class="center" width="50%" height="50%" >}}

## ハンズオン

### Virtualbox のネットワーク設定

Virtualbox マネージャーのツール欄にマウスポインターを当てて「横線が３本並んでいるボタン」をクリックして、
出てきたメニューで「ネットワーク」を選択します。

{{< figure src="/img/tutorial-linux-no4/vbox-tool.png" title="vbox-tool" class="center" width="50%" height="50%" >}}

上の欄で「NAT ネットワーク」タブを選択して、下の欄で「ポートフォワーディング」タブを開きます。
画像の通りに値を入力して「適用」ボタンを押します。

{{< figure src="/img/tutorial-linux-no4/vbox-portforwarding.png" title="vbox-portforwarding" class="center" width="50%" height="50%" >}}

### SSH デーモンの確認

仮想マシンを起動します。以下のウインドウが表示されたら、２つともチェックを入れて「アクセスを許可する」ボタンを押します。

{{< figure src="/img/tutorial-linux-no4/windows-firewall.png" title="windows-firewall" class="center" width="50%" height="50%" >}}

Virtualbox のウインドウで端末を開きます。以下のコマンドを入力して SSH デーモンの状態を表示します。

```bash
[study@localhost ~]$ systemctl status sshd
```

以下のような結果が表示されます。`Active: active (running) `になっていたら起動しています。

```bash
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2024-06-16 22:37:04 JST; 6min ago
     Docs: man:sshd(8)
           man:sshd_config(5)
 Main PID: 902 (sshd)
    Tasks: 1 (limit: 10937)
   Memory: 1.6M
   CGroup: /system.slice/sshd.service
           mq902 /usr/sbin/sshd -D -oCiphers=aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes256-ctr,aes256-cbc,aes128-gcm@openssh.…

 6月 16 22:37:03 localhost.localdomain systemd[1]: Starting OpenSSH server daemon...
 6月 16 22:37:04 localhost.localdomain sshd[902]: Server listening on 0.0.0.0 port 22.
 6月 16 22:37:04 localhost.localdomain sshd[902]: Server listening on :: port 22.
 6月 16 22:37:04 localhost.localdomain systemd[1]: Started OpenSSH server daemon.
```

{{% notice style="tip" %}}
デーモンの起動・停止方法は[【Linux】No.2 基本操作編]({{< ref "/tutorial/tutorial-linux-no2" >}})を参照してください。
{{% /notice %}}

### Windows から Linux に接続

Windows の PowerShell を起動して以下のコマンドを入力します。

```pwsh
PS C:\Users\*****> ssh -p 10022 study@localhost
```

初回接続時は以下のメッセージが出ますので、`yes`を入力します。

```cmd
The authenticity of host '[localhost]:10022 ([127.0.0.1]:10022)' can't be established.
ECDSA key fingerprint is SHA256:********************************************
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

以下のメッセージが出たら、study ユーザーのパスワードを入力します。

```cmd
Warning: Permanently added '[localhost]:10022' (ECDSA) to the list of known hosts.
study@localhost's password:
```

以下のメッセージが表示されたら成功です。`exit`を入力して終了できます。

```bash
Activate the web console with: systemctl enable --now cockpit.socket

Last login: XXX XXX XX XX:XX:XX XXXX from 172.24.0.2
[study@localhost ~]$
```

{{% notice style="tip" %}} 今回は Windows→Linux ですが、Linux→Linux でも操作は同じです。{{% /notice %}}

### SSH で使用する鍵を生成

Windows の PowerShell で以下のコマンドを入力します。コメント欄は好きな文字を入力してください。

```pwsh
PS C:\Users\*****> ssh-keygen -t ed25519 -C "comment"
```

以下のメッセージが表示されます。ウィザード方式なので必要なパラメータを入力します。

```bash
Generating public/private ed25519 key pair.
Enter file in which to save the key (C:\Users\*****/.ssh/): 【C:\Users\*****\Desktop\id_ed25519 と入力する】
Enter passphrase (empty for no passphrase): 【鍵のパスフレーズ、空欄だと無しになる】
Enter same passphrase again:【鍵のパスフレーズ、上と同じものを入力する】
```

入力したパラメータから鍵を生成した結果が表示されます。

```bash
Your identification has been saved in C:\Users\*****\Desktop\id_ed25519.  『秘密鍵を保存した場所が表示される』
Your public key has been saved in C:\Users\*****\Desktop\id_ed25519.pub.　『公開鍵を保存した場所が表示される』
The key fingerprint is:
SHA256:******************************************* comment
The key's randomart image is:
+--[ED25519 256]--+
～省略～
+----[SHA256]-----+
```

{{% notice style="tip" %}} 今回は Windows ですが、Linux でも操作は同じです。{{% /notice %}}

### 生成した公開鍵を Linux へ転送

Windows の PowerShell で以下のコマンドを入力します。

```pwsh
PS C:\Users\*****> cat ~/Desktop/id_ed25519.pub | ssh -p 10022 study@localhost `
" `
mkdir -p ~/.ssh && chmod 700 ~/.ssh && `
cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys `
"
```

### 鍵認証を使用した SSH 接続

Windows の PowerShell で以下のコマンドを入力します。パスフレーズを設定している場合はコマンド実行後に入力します。

```pwsh
PS C:\Users\*****> ssh -p 10022 study@localhost -i ~/Desktop/id_ed25519
```

以下のメッセージが表示されたら成功です。

```bash
Activate the web console with: systemctl enable --now cockpit.socket

Last login: XXX XXX XX XX:XX:XX XXXX from 172.24.0.2
[study@localhost ~]$
```

{{% notice style="tip" %}} 今回は Windows→Linux ですが、Linux→Linux でも操作は同じです。{{% /notice %}}

### パスワード認証の禁止

Virtualbox のウインドウで端末を開きます。以下のコマンドを入力して/etc/ssh/sshd_config を編集します。
パスワードを求められるので、study ユーザーのパスワードを入力します。

```bash
[study@localhost ~]$ sudo vim /etc/ssh/sshd_config
```

70 行目付近に以下の記述があるので、`PasswordAuthentication yes`を`PasswordAuthentication no`にします。

```bash
# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords no
PasswordAuthentication yes 【noに変更する】

# Change to no to disable s/key passwords
#ChallengeResponseAuthentication yes
ChallengeResponseAuthentication no
```

以下のコマンドを入力して SSH デーモンを再起動します。

```bash
[study@localhost ~]$ sudo systemctl restart sshd
```

Windows の PowerShell で以下のコマンドを入力します。

```pwsh
PS C:\Users\*****> ssh -p 10022 study@localhost
```

以下のメッセージが表示されたら、正しく設定されていてパスワード認証が拒否されています。

```bash
study@localhost: Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
```

Windows の PowerShell で以下のコマンドを入力します。パスフレーズを設定している場合はコマンド実行後に入力します。

```pwsh
PS C:\Users\*****> ssh -p 10022 study@localhost -i ~/Desktop/id_ed25519
```

以下のメッセージが表示されたら、鍵認証でログインが成功しています。

```bash
Activate the web console with: systemctl enable --now cockpit.socket

Last login: XXX XXX XX XX:XX:XX XXXX from 172.24.0.2
[study@localhost ~]$
```

### teraterm で Linux に接続

teraterm を起動して以下の画像のように値を入力して「OK」ボタンを押します。

{{< figure src="/img/tutorial-linux-no4/teraterm1.png" title="teraterm1" class="center" width="50%" height="50%" >}}

以下の画像が出た場合は「このホストを～追加する」にチェックを入れて「続行」ボタンを押します。

{{< figure src="/img/tutorial-linux-no4/teraterm2.png" title="teraterm1" class="center" width="50%" height="50%" >}}

「ユーザ名」には、Linux のユーザ名とパスワードを入力します。  
「パスフレーズ」には、鍵に設定したパスフレーズを入力します。（無しの場合は空欄）  
「認証方式」を「RSA/DSA ～/ED25519 鍵を使う」にして、前述で作った「id_ed25519」を選択します。  
入力後に以下のような画面が表示されたら成功です。

{{< figure src="/img/tutorial-linux-no4/teraterm3.png" title="teraterm1" class="center" width="50%" height="50%" >}}
