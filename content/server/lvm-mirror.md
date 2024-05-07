+++
title = 'LVMを使用したミラー（RAID1）構成をした話'
date = 2024-05-04T17:18:05+09:00
weight = 12
draft = false
categories = ["Server"]
tags = ["Author:DD Erikson", "Level:中級", "Storage", "Linux", "LPIC/LinuC"]
+++

## 目次

- [目次](#目次)
- [概要](#概要)
- [でてくるコマンド](#でてくるコマンド)
- [環境](#環境)
- [概念図](#概念図)
- [やったこと](#やったこと)
  - [ミラー構成](#ミラー構成)
  - [障害検証](#障害検証)
  - [おまけ](#おまけ)

## 概要

TX100 S3 の MegaRAID で 1TBHDD を 2 台つかって RAID1 しようとしたところ、SSD を認識したりしなくなったり、作った論理ボリュームが急に消えたりと不安定なため、あゆむさんより LVM での RAID 構成についてメモをいただいたので構築しました。

## でてくるコマンド

- pvs・・・物理ボリュームの情報を表示する
- pvcreate・・・物理ボリュームを作成する
- vgcreate・・・ボリュームグループを作成する
- vgextend・・・ボリュームグループへ物理ボリュームを追加する
- vgreduce・・・ボリュームグループから物理ボリュームを削除する
- vgs・・・ボリュームグループの情報を表示する
- lvcreate・・・論理ボリュームを作成する
- lvremove・・・論理ボリュームを削除する（参考）
- lvconvert・・・論理ボリュームに含まれるミラー（RAID）を操作する
- lvs・・・論理ボリュームの情報を表示する
- parted・・・パーティションの操作に使用する
- fdisk・・・パーティションテーブルの操作に使用する
- mkfs・・・指定したファイルシステムでフォーマットをする
- blkid・・・ブロックデバイスの属性を表示する
- dd・・・ブロック単位でファイルをコピーする

## 環境

- 本体：Fujitsu PRIMAGY TX100 S3
- OS：ProxmoxVE8.2.2
- CPU:Intel Xeon E3-1240
- メモリ
  - 4GB*2 2GB*2 合計 12GB
- ストレージ
  - /dev/sda Samsung SSD 840 120GB
  - /dev/sdb KIOXIA-EXCERIA S 447.13 GiB
  - /dev/sdc TOSHIBA DT01ABA1 931.51 GiB
  - /dev/sdd TOSHIBA DT01ABA1 931.51 GiB

## 概念図

![概念図](/img/lvm-mirror.svg)

## やったこと

1. /dev/sdc と/dev/sdd をミラー構成にした
2. 障害検証をした

### ミラー構成

1. パーティションテーブルを MBR（msdos）にする
   ```
   parted /dev/sdc mklabel msdos
   parted /dev/sdd mklabel msdos
   ```
2. 物理ボリュームを作成する
   ```
   pvcreate /dev/sdc /dev/sdd
   ```
3. ボリュームグループを作成して sdc と sdd を加える
   ```
   vgcreate vg1t /dev/sdc /dev/sdd
   ```
4. 論理ボリュームを RAID1、ボリュームグループ vg1t の領域 100％で raid1 という名前で作成する。
   ```
   lvcreate --type raid1 -m 1 -l 100%VG -n raid1 vg1t
   ```
5. 論理ボリュームを ext4 でフォーマットする
   ```
   mkfs.ext4 /dev/vg1t/raid1
   ```
6. マウントする
   ```
   mount /dev/vg1t/raid1 /mnt/storage1
   ```
7. 起動時に自動でマウントするように設定
   ```
   blkid /dev/vg1t/raid1
   /dev/vg1t/raid1: UUID="c2347c4b-e525-4d28-96e5-ab6971d77f8b" BLOCK_SIZE="4096" TYPE="ext4"
   vim /etc/fstab
   # 追記(ext4はautoでもいいという噂を聞いたけど敢えて指定する)
   UUID=c2347c4b-e525-4d28-96e5-ab6971d77f8b       /mnt/storage1   ext4    defaults        0       0
   ```
8. 再起動してマウントされることを確認
   ```
   cat /etc/mtab
   /dev/mapper/vg1t-raid1 /mnt/storage1 ext4 rw,relatime 0 0
   ```
9. 実際にアクセスしてファイルが置ければ OK

- DD コマンドで Read 速度を確認したところ 140MB/s ぐらいでした
  ```
  root@pve:~# dd if=/dev/vg1t/raid1 of=/dev/null bs=16k status=progress
  3366289408 bytes (3.4 GB, 3.1 GiB) copied, 24 s, 140 MB/s^C
  206446+0 records in
  206445+0 records out
  3382394880 bytes (3.4 GB, 3.2 GiB) copied, 24.1147 s, 140 MB/s
  ```

### 障害検証

1. 片方壊す（parted コマンドでパーティションを作成する）
   ```
   parted /dev/sdd mklabel msdos
   ```
2. lvs pvs で確認すると unknown になっている

   ```
   root@pve:~# lvs -a -o +devices
   WARNING: Couldn't find device with uuid 7SYOSV-qNwe-ijgv-IwCC-Qz64-DpIH-hTs8w6.
   WARNING: VG vg1t is missing PV 7SYOSV-qNwe-ijgv-IwCC-Qz64-DpIH-hTs8w6 (last written to [unknown]).
   WARNING: Couldn't find all devices for LV vg1t/raid1-1t_rimage_1 while checking used and assumed devices.
   WARNING: Couldn't find all devices for LV vg1t/raid1-1t_rmeta_1 while checking used and assumed devices.
   LV                  VG   Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert Devices
   data                pve  twi-a-tz--  <53.93g             0.00   1.59                             data_tdata(0)
   [data_tdata]        pve  Twi-ao----  <53.93g                                                     /dev/sda3(12175)
   [data_tmeta]        pve  ewi-ao----    1.00g                                                     /dev/sda3(25980)
   [lvol0_pmspare]     pve  ewi-------    1.00g                                                     /dev/sda3(26236)
   root                pve  -wi-ao----  <39.56g                                                     /dev/sda3(2048)
   swap                pve  -wi-ao----    8.00g                                                     /dev/sda3(0)
   raid1-1t            vg1t rwi-a-r-p- <931.51g                                    15.20            raid1-1t_rimage_0(0),raid1-1t_rimage_1(0)
   [raid1-1t_rimage_0] vg1t Iwi-aor--- <931.51g                                                     /dev/sdc(1)
   [raid1-1t_rimage_1] vg1t Iwi-aor-p- <931.51g                                                     [unknown](1)
   [raid1-1t_rmeta_0]  vg1t ewi-aor---    4.00m                                                     /dev/sdc(0)
   [raid1-1t_rmeta_1]  vg1t ewi-aor-p-    4.00m                                                     [unknown](0)
   root@pve:~# pvs
   WARNING: Couldn't find device with uuid 7SYOSV-qNwe-ijgv-IwCC-Qz64-DpIH-hTs8w6.
   WARNING: VG vg1t is missing PV 7SYOSV-qNwe-ijgv-IwCC-Qz64-DpIH-hTs8w6 (last written to [unknown]).
   WARNING: Couldn't find all devices for LV vg1t/raid1-1t_rimage_1 while checking used and assumed devices.
   WARNING: Couldn't find all devices for LV vg1t/raid1-1t_rmeta_1 while checking used and assumed devices.
   PV         VG     Fmt  Attr PSize    PFree
   /dev/sda3  pve    lvm2 a--  <118.24g   14.75g
   /dev/sdb   ssd512 lvm2 a--  <447.13g <447.13g
   /dev/sdc   vg1t   lvm2 a--   931.51g       0
   [unknown]  vg1t   lvm2 a-m   931.51g       0
   ```

3. 壊れた HDD を取り外す
   ```
   vgreduce --removemissin vg1t
   ```
   pvs で確認すると unknown が消える
   ```
   root@pve:~# pvs
   PV         VG     Fmt  Attr PSize    PFree
   /dev/sda3  pve    lvm2 a--  <118.24g   14.75g
   /dev/sdb   ssd512 lvm2 a--  <447.13g <447.13g
   /dev/sdc   vg1t   lvm2 a--   931.51g       0
   ```
4. 新しいディスクでフィジカルボリュームを作成する
   ```
   root@pve:~# pvcreate /dev/sdd
   Physical volume "/dev/sdd" successfully created.
   root@pve:~# pvs
   PV         VG     Fmt  Attr PSize    PFree
   /dev/sda3  pve    lvm2 a--  <118.24g   14.75g
   /dev/sdb   ssd512 lvm2 a--  <447.13g <447.13g
   /dev/sdc   vg1t   lvm2 a--   931.51g       0
   /dev/sdd          lvm2 ---   931.51g  931.51g
   ```
5. ボリュームグループに物理ボリュームを追加する
   ```
   root@pve:~# vgextend vg1t /dev/sdd
   Volume group "vg1t" successfully extended
   ```
6. ミラー化ボリュームをリペアします。
   ```
   root@pve:~# lvconvert --repair vg1t/raid1-1t
   Attempt to replace failed RAID images (requires full device resync)? [y/n]: y
   Faulty devices in vg1t/raid1-1t successfully replaced.
   ```
7. 論理ボリューム一覧を確認して、ボリュームグループが正常なことを確認します。
   ```
   root@pve:~# lvs -a -o +devices
   LV                  VG   Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert Devices
   data                pve  twi-a-tz--  <53.93g             0.00   1.59                             data_tdata(0)
   [data_tdata]        pve  Twi-ao----  <53.93g                                                     /dev/sda3(12175)
   [data_tmeta]        pve  ewi-ao----    1.00g                                                     /dev/sda3(25980)
   [lvol0_pmspare]     pve  ewi-------    1.00g                                                     /dev/sda3(26236)
   root                pve  -wi-ao----  <39.56g                                                     /dev/sda3(2048)
   swap                pve  -wi-ao----    8.00g                                                     /dev/sda3(0)
   raid1-1t            vg1t rwi-a-r--- <931.51g                                    0.22             raid1-1t_rimage_0(0),raid1-1t_rimage_1(0)
   [raid1-1t_rimage_0] vg1t iwi-aor--- <931.51g                                                     /dev/sdc(1)
   [raid1-1t_rimage_1] vg1t Iwi-aor--- <931.51g                                                     /dev/sdd(1)
   [raid1-1t_rmeta_0]  vg1t ewi-aor---    4.00m                                                     /dev/sdc(0)
   [raid1-1t_rmeta_1]  vg1t ewi-aor---    4.00m                                                     /dev/sdd(0)
   ```

### おまけ

fdisk した時に「<font color="red">The backup GPT table is corrupt, but the primary appears OK, so that will be used.</font>」というのが出ることがある。これはパーティションテーブルが読み取れない時に出るらしい。GPT はパーティションテーブルをプライマリ・バックアップと持っているが、今回はバックアップが壊れている状態。

    ```
    root@pve:~# fdisk -l
    The backup GPT table is corrupt, but the primary appears OK, so that will be used.
    Disk /dev/sda: 119.24 GiB, 128035676160 bytes, 250069680 sectors
    Disk model: Samsung SSD 840
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: gpt
    Disk identifier: F5A57025-3A16-4AC0-AFA5-48EC657D7B09

    Device       Start       End   Sectors   Size Type
    /dev/sda1       34      2047      2014  1007K BIOS boot
    /dev/sda2     2048   2099199   2097152     1G EFI System
    /dev/sda3  2099200 250069646 247970447 118.2G Linux LVM
    ```

パーティションテーブルに対して操作を行えばこのエラーは解消される。

````
root@pve:~# fdisk /dev/sda

    Welcome to fdisk (util-linux 2.38.1).
    Changes will remain in memory only, until you decide to write them.
    Be careful before using the write command.

    The backup GPT table is corrupt, but the primary appears OK, so that will be used.
    This disk is currently in use - repartitioning is probably a bad idea.
    It's recommended to umount all file systems, and swapoff all swap
    partitions on this disk.

    # パーティションテーブルを表示する
    Command (m for help): p

    Disk /dev/sda: 119.24 GiB, 128035676160 bytes, 250069680 sectors
    Disk model: Samsung SSD 840
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: gpt
    Disk identifier: F5A57025-3A16-4AC0-AFA5-48EC657D7B09

    Device       Start       End   Sectors   Size Type
    /dev/sda1       34      2047      2014  1007K BIOS boot
    /dev/sda2     2048   2099199   2097152     1G EFI System
    /dev/sda3  2099200 250069646 247970447 118.2G Linux LVM

    # パーティションテーブルを検証する
    Command (m for help): v
    No errors detected.
    Header version: 1.0
    Using 3 out of 128 partitions.
    A total of 0 free sectors is available in 0 segments (the largest is 0 B).

    # テーブルをディスクに書き込む
    Command (m for help): w
    The partition table has been altered.
    Syncing disks.
    ```

エラーが消えた
````

root@pve:~# fdisk -l
Disk /dev/sda: 119.24 GiB, 128035676160 bytes, 250069680 sectors
Disk model: Samsung SSD 840
Units: sectors of 1 \* 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: F5A57025-3A16-4AC0-AFA5-48EC657D7B09

    Device       Start       End   Sectors   Size Type
    /dev/sda1       34      2047      2014  1007K BIOS boot
    /dev/sda2     2048   2099199   2097152     1G EFI System
    /dev/sda3  2099200 250069646 247970447 118.2G Linux LVM
    ```
