+++
title = 'CML2を使おう'
date = 2025-04-05T00:57:00+09:00
draft = false
#weight = 1000
categories = ["Network"]
tags = ["Author:DD Erikson", "Level:中級？", "Type:Knowledge", "Cisco Mpdelong Labs2", "Proxmox"]
+++

# Proxmox上でのCML2の立ち上げ

## 概要

* ProxmoxにCML2の環境を作ります。

## 要件

* CML2の最低要件はこちら [Cisco Modeling Labs 2.0 Quick Start Guide](https://www.cisco.com/c/en/us/td/docs/cloud_services/cisco_modeling_labs/v200/quick/start/b_cml_quick_start_2-0/m_deploy_and_configure.html)

## 簡単な手順

1. Ciscoからダウンロードをします。公式ページから[Downloadページ](https://software.cisco.com/download/home)を開き、Modeling Labsを選択するとダウンロード画面がでます。
2. CML-Freeをクリックし、「Cisco Modeling Labs 2.8.1 server. 」（cml2_f_2.8.1-14_amd64-35.ova）と「Cisco Modeling Labs reference platform ISO file (October 2024).」（refplat-20241016-freetier-iso.zip）をダウンロードします。右側にある箱に下矢印がついたようなアイコンをクリックするとダウンロードできます。
![download](/img/CML2_on_Proxmox/1.download.png)
3. ダウンロードが終わったら、「refplat-20241016-freetier-iso.zip」を解凍し、ISOファイルをProxmoxのISOイメージの置き場にアップロードします。
![isoupload](/img/CML2_on_Proxmox/2.ISOUpload.png)
4. ProxmoxのノードにSSHをします。TeratermなどでSSHアクセスをしてください。
5. SCPで「cml2_f_2.8.1-14_amd64-35.ova」を転送します。TeratermであればSSHで接続したあとにウィンドウにドラッグアンドドロップすると転送できます。ホームディレクトリに転送されます。
6. 今回はこのovaを使ってVMを仕立てていきますので、一旦移動します。ProxmoxのVMなどが入っている「/var/lib/vz/」というのがあるので、今回はわかりやすくここにuploadディレクトリを作り、ovaファイルを入れます。

    ```
        root@pve:~# mkdir /var/lib/vz/upload
        root@pve:~# mv cml2_f_2.8.1-14_amd64-35.ova /var/lib/vz/upload/
        root@pve:~# cd /var/lib/vz/upload/
        root@pve:/var/lib/vz/upload#
    ```

7. tarでovaを解凍します。いくつかファイルが現れます。

    ```
        root@pve:/var/lib/vz/upload# tar -xvf cml2_f_2.8.1-14_amd64-35.ova
    ```

8. 現れたファイルの中でovfというのがあるのでProxmoxにインポートします。インポートが始まると画像のように進捗が表示され、100%になると完了です。

    ```
        root@pve:/var/lib/vz/upload# qm importovf <VMID（他と被らない）> cml2_2.8.1-14_amd64-35_SHA256.ovf <storagename> --format raw
        ## 以下は一例です
        root@pve:/var/lib/vz/upload# qm importovf 301 cml2_2.8.1-14_amd64-35_SHA256.ovf local-lvm --format raw
    ```
![progress](/img/CML2_on_Proxmox/image.png)

9. 完了するとGUI画面上にﾆｮｷて新しいVMが立ち上がるので、最低要件を確認しつつチューニングします。以下の画像を参考にしてください。このとき、最初にアップロードしたISOをCDドライブ（IDEで結構です）で設定します。

![Hardware](/img/CML2_on_Proxmox/image-1.png)

1.  開始をクリックすると起動します。あとは通常のCML2のセットアップと同様です。

![CML2](/img/CML2_on_Proxmox/image-2.png)

Hello CML2!  
以上です。
