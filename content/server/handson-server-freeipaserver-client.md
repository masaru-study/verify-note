+++
title = 'FreeIPA クライアント編'
date = 2024-05-03T01:15:32+09:00
weight = 11
draft = false
categories = ["Server"]
tags = ["Autor:nakata", "FreeIPA", "LPIC300"]
+++

## 参考サイト

---

https://www.linuxtechi.com/install-freeipa-client-on-rhel-rockylinux-almalinux/

## 構築環境

---

### ハードウェア

#### server 側

- OS：Almalinux8
- CPU：2core
- Mem：4G
- Storage：50G
- IPaddress：192.168.0.102
- Hostname：sv.freeipa.local

#### client 側

- OS：ubuntu20.04
- CPU：1core
- Mem：4G
- Storage：50G
- IPaddress：192.168.0.103
- Hostname：cl.freeipa.local

### インストールパッケージ

#### client 側

- freeipa-client

## 構築

---

### server 側

- #### FreeIPA サーバにアカウントを作成

```
$sudo kinit admin
$sudo ipa user-add guestuser --first=Guest --last=User --password
Password:#guestuserのパスワードを設定
```

- #### DNS レコードを追加

```
$sudo ipa dnsrecord-add freeipa.local cl.freeipa.local --a-rec 192.168.0.103
```

### client 側

- #### /etc/hosts にレコードを追加

```
$sudo echo "192.168.0.102 sv.freeipa.local sv" | sudo tee -a /etc/hosts
$sudo echo "192.168.1.103 cl.freeipa.local cl" | sudo tee -a /etc/hosts
```

- #### パッケージのアップデート

```
$sudo apt update
$sudo apt upgrade
```

- #### 必要パッケージのインストール

```
$sudo apt install freeipa-client oddjob-mkhomedir
```

- #### FreeIPA クライアントのセットアップインストール

```
$sudo ipa-client-install --hostname=`hostname -f` --mkhomedir --server=sv.freeipa.local --domain freeipa.local  --realm FREEIPA.LOCAL
realm FREEIPA.LOCAL
This program will set up FreeIPA client.
Version 4.8.6

WARNING: conflicting time&date synchronization service 'ntp' will be disabled in favor of chronyd

Autodiscovery of servers for failover cannot work with this configuration.
If you proceed with the installation, services will be configured to always access the discovered server for all operations and will not fail over to other servers in case of failure.
Proceed with fixed values and no DNS discovery? [no]: #yes
Do you want to configure chrony with NTP server or pool address? [no]: #Enter
Client hostname: samba.freeipa.local
Realm: FREEIPA.LOCAL
DNS Domain: freeipa.local
IPA Server: sv.freeipa.local
BaseDN: dc=freeipa,dc=local

Continue to configure the system with these values? [no]: #yes
Synchronizing time
No SRV records of NTP servers found and no NTP server or pool address was provided.
Using default chrony configuration.
Attempting to sync time with chronyc.
Time synchronization was successful.
User authorized to enroll computers: admin
Password for admin@FREEIPA.LOCAL:
Successfully retrieved CA cert
    Subject:     CN=Certificate Authority,O=FREEIPA.LOCAL
    Issuer:      CN=Certificate Authority,O=FREEIPA.LOCAL
    Valid From:  2024-04-25 13:51:12
    Valid Until: 2044-04-25 13:51:12

Enrolled in IPA realm FREEIPA.LOCAL
Created /etc/ipa/default.conf
Configured sudoers in /etc/nsswitch.conf
Configured /etc/sssd/sssd.conf
Configured /etc/krb5.conf for IPA realm FREEIPA.LOCAL
Systemwide CA database updated.
Adding SSH public key from /etc/ssh/ssh_host_dsa_key.pub
Adding SSH public key from /etc/ssh/ssh_host_ecdsa_key.pub
Adding SSH public key from /etc/ssh/ssh_host_rsa_key.pub
Adding SSH public key from /etc/ssh/ssh_host_ed25519_key.pub
SSSD enabled
Configured /etc/openldap/ldap.conf
Configured /etc/ssh/ssh_config
Configured /etc/ssh/sshd_config
Configuring freeipa.local as NIS domain.
Client configuration complete.
The ipa-client-install command was successful
```

- #### mkhomedir の編集

```
$sudo echo "required pam_mkhomedir.so umask=0022 skel=/etc/skel" | sudo tee -a /usr/share/pam-configs/mkhomedir
$sudo pam-auth-update
```

### server 側

- #### client 側に SSH 接続

```
ssh guestuser@192.168.0.103
```
