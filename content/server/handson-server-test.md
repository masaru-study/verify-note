+++
title = 'FreeIPA サーバ構築編'
date = 2024-05-03T01:15:32+09:00
weight = 10
draft = false
categories = ["Server"]
tags = ["Autor:nakata", "FreeIPA", "LPIC300"]
+++

## 参考サイト

---

https://www.linuxtechi.com/install-freeipa-rhel-rocky-almalinux/

## 構築環境

---

### ハードウェア

- OS：Almalinux8
- CPU：2core
- Mem：4G
- Storage：50G
- IPaddress：192.168.0.102
- Hostname：sv.freeipa.local

### インストールパッケージ

- @idm:DL1
- freeipa-server
- ipa-server-dns
- bind-dyndb-ldap

## 構築

---

- #### ホスト名を設定

```
$sudo hostnamectl set-hostname sv.freeipa.local
```

- #### /etc/hosts にレコードを追加

```
$echo "192.168.0.102 sv.freeipa.local sv" | sudo tee -a /etc/hosts
```

- #### SELinux の設定

```
$sudo setenforce 0
$sudo sed -i 's/^SELINUX=.*/SELINUX=permissive/g' /etc/selinux/config
$getenforce
Permissive
```

- #### パッケージのアップデート

```
$sudo dnf update
```

- #### 必要パッケージのインストール

```
$sudo dnf install @idm:DL1
$sudo dnf install freeipa-server ipa-server-dns bind-dyndb-ldap
```

- #### 再起動

```
$sudo reboot
```

- #### FreeIPA のセットアップインストール

```
$sudo ipa-server-install

The log file for this installation can be found in /var/log/ipaserver-install.log
==============================================================================
This program will set up the IPA Server.
Version 4.9.12

This includes:
  * Configure a stand-alone CA (dogtag) for certificate management
  * Configure the NTP client (chronyd)
  * Create and configure an instance of Directory Server
  * Create and configure a Kerberos Key Distribution Center (KDC)
  * Configure Apache (httpd)
  * Configure SID generation
  * Configure the KDC to enable PKINIT

To accept the default shown in brackets, press the Enter key.

Do you want to configure integrated DNS (BIND)? [no]: yes

Enter the fully qualified domain name of the computer
on which you're setting up server software. Using the form
<hostname>.<domainname>
Example: master.example.com


Server host name [sv.freeipa.local]: #Enter

Warning: skipping DNS resolution of host ipa.linuxtechi.lan
The domain name has been determined based on the host name.

Please confirm the domain name [freeipa.local]: #Enter

The kerberos protocol requires a Realm name to be defined.
This is typically the domain name converted to uppercase.

Please provide a realm name [FREEIPA.LOCAL]:
Certain directory server operations require an administrative user.
This user is referred to as the Directory Manager and has full access
to the Directory for system management tasks and will be added to the
instance of directory server created for IPA.
The password must be at least 8 characters long.

Directory Manager password: #LDAPスーパーユーザー用のパスワード
Password (confirm):

The IPA server requires an administrative user, named 'admin'.
This user is a regular system account used for IPA server administration.

IPA admin password: #管理者システムユーザーアカウント (admin) のパスワード
Password (confirm):

Checking DNS domain linuxtechi.lan., please wait ...
Do you want to configure DNS forwarders? [yes]: #Enter
Following DNS servers are configured in /etc/resolv.conf: 218.219.82.240, 220.208.109.240
Do you want to configure these servers as DNS forwarders? [yes]: #Enter
All detected DNS servers were added. You can enter additional addresses now:
Enter an IP address for a DNS forwarder, or press Enter to skip:
DNS forwarders: 218.219.82.240, 220.208.109.240
Checking DNS forwarders, please wait ...
DNS server 218.219.82.240 does not support DNSSEC: answer to query '. SOA' is missing DNSSEC signatures (no RRSIG data)
Please fix forwarder configuration to enable DNSSEC support.

DNS server 218.219.82.240: answer to query '. SOA' is missing DNSSEC signatures (no RRSIG data)
Please fix forwarder configuration to enable DNSSEC support.
DNS server 220.208.109.240 does not support DNSSEC: answer to query '. SOA' is missing DNSSEC signatures (no RRSIG data)
Please fix forwarder configuration to enable DNSSEC support.

DNS server 220.208.109.240: answer to query '. SOA' is missing DNSSEC signatures (no RRSIG data)
Please fix forwarder configuration to enable DNSSEC support.
WARNING: DNSSEC validation will be disabled
Do you want to search for missing reverse zones? [yes]: #Enter
Checking DNS domain 0.168.192.in-addr.arpa., please wait ...
Do you want to create reverse zone for IP 192.168.0.102 [yes]: #Enter
Please specify the reverse zone name [0.168.192.in-addr.arpa.]:
Checking DNS domain 0.168.192.in-addr.arpa., please wait ...
Using reverse zone(s) 0.168.192.in-addr.arpa.
Trust is configured but no NetBIOS domain name found, setting it now.
Enter the NetBIOS name for the IPA domain.
Only up to 15 uppercase ASCII letters, digits and dashes are allowed.
Example: EXAMPLE.


NetBIOS domain name [FREEIPA]: #Enter

Do you want to configure chrony with NTP server or pool address? [no]: #Enter

The IPA Master Server will be configured with:
Hostname:       sv.freeipa.local
IP address(es): 192.168.0.102
Domain name:    freeipa.local
Realm name:     FREEIPA.LOCAL

The CA will be configured with:
Subject DN:   CN=Certificate Authority,O=FREEIPA.LOCAL
Subject base: O=FREEIPA.LOCAL
Chaining:     self-signed

BIND DNS server will be configured to serve IPA domain with:
Forwarders:       218.219.82.240, 220.208.109.240
Forward policy:   only
Reverse zone(s):  0.168.192.in-addr.arpa.

Continue to configure the system with these values? [no]: yes

*** セットアップlogのため省略 ***

==============================================================================
Setup complete

Next steps:
	1. You must make sure these network ports are open:
		TCP Ports:
		  * 80, 443: HTTP/HTTPS
		  * 389, 636: LDAP/LDAPS
		  * 88, 464: kerberos
		  * 53: bind
		UDP Ports:
		  * 88, 464: kerberos
		  * 53: bind
		  * 123: ntp

	2. You can now obtain a kerberos ticket using the command: 'kinit admin'
	   This ticket will allow you to use the IPA tools (e.g., ipa user-add)
	   and the web user interface.

Be sure to back up the CA certificates stored in /root/cacert.p12
These files are required to create replicas. The password for these
files is the Directory Manager password
The ipa-server-install command was successful
```

- #### firewall の設定

```
$sudo firewall-cmd --add-service={http,https,dns,ntp,freeipa-ldap,freeippa-ldaps} --permanent
success
$sudo firewall-cmd --reload
success
```

- #### freeipa サーバの状態確認

```
$ipactl status
Directory Service: RUNNING
krb5kdc Service: RUNNING
kadmin Service: RUNNING
named Service: RUNNING
httpd Service: RUNNING
ipa-custodia Service: RUNNING
pki-tomcatd Service: RUNNING
ipa-otpd Service: RUNNING
ipa-dnskeysyncd Service: RUNNING
ipa: INFO: The ipactl command was successful
```

- #### チケットの発行

```
$sudo kinit admin
Password for admin@FREEIPA.LOCAL: #管理者システムユーザーアカウント (admin) のパスワード
```

- #### チケットの発行状況の確認

```
klist
Ticket cache: KCM:0
Default principal: admin@FREEIPA.LOCAL

Valid starting       Expires              Service principal
2024-04-25T22:58:59  2024-04-26T22:01:55  krbtgt/FREEIPA.LOCAL@FREEIPA.LOCAL
```

- #### Web コンソール画面の確認

```
URL:https://192.168.0.102/ipa/ui
ユーザー:admin
パスワード:#管理者システムユーザーアカウント (admin) のパスワード
```
