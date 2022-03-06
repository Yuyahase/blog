---
title: "Filesystem"
date: 2022-02-28T19:54:49+09:00
tags: [""] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## 現在のマウントされているファイルシステムの表示

- cat /proc/self/mounts
- cat /etc/mtab
- cat /proc/mounts
- mount

## ファイルシステムのパラメ-タ変更

`tune2fs`コマンドでファイルシステムのパラメータを変更する。

|オプション|説明|
|-|-|
|`-c 回数`|マウント数がこの回数を超えると、次回ブート時にファイルシステムをチェックする|
|`-i [d\|m\|w]`|ファイルシステムをチェックする最大時間間隔を設定する|
|`-j`|ext3ジャーナルをファイルシステムに追加する|
|`-L`|ボリュームラベルを設定|

## ファイル不整合チェック

`fsck`コマンドでファイル不整合をチェックし、必要であれば修正する。

|オプション|説明|
|-|-|
|-t|ファイルシステムの種類を指定|
|-A|`/etc/fstab`に記述されている全ファイルシステムに対して実行|
|-N|実際には実行せず、実行する内容を表示|
|-a|問題を自動的に修復|
|-r|問題を対話的に修復|

## ファイルシステムのデバッグ

`debugfs`コマンドは、ext2, ext3, ext4ファイルシステムの対話的なデバッガツール。

## /etc/fstabの設定

システム運用において、どのような設定でマウントを行えばよいか事前にわかっているファイルシステムについては、`/etc/fstab`にまとめて書き、設定しておく。

```bash
＄ cat /etc/fstab 

#
# /etc/fstab
# Created by anaconda on Fri Dec 10 02:23:25 2021
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
①                       ②                       ③       ④            ⑤⑥
/dev/mapper/rl-root     /                       xfs     defaults        0 0
UUID=2d3bc8db-c107-4048-b1d6-bb1084fd2ad4 /boot                   xfs     defaults        0 0
/dev/mapper/rl-swap     none                    swap    defaults        0 0

#  1. デバイスファイル名
#  2. マウントポイント
#  3. ファイルシステムの種類
#  4. マウントオプション
#  5. バックアップの指定
#  6. ファイルシステムのチェック
```

主なマウントオプションは以下。
`user`と`users`オプションの違いは、アンマウントが本人のみ可能なのか、誰でも可能なのかの違い。

|オプション|説明|
|-|-|
|defaults|デフォルト指定(async, auto, dev, exec, nouser, rw, suid)|
|user|一般ユーザでマウント化、本人のみアンマウント化|
|users|一般ユーザでマウント化、誰でもアンマウント化|
|nouser|一般ユーザによるマウントを禁止|

## `mount`コマンド

ファイルシステムをマウントするコマンド。

|オプション|説明|
|-|-|
|`-a`|`/etc/fstab`ファイルに記載されているファイルシステムをすべてマウント|
|`-o`|マウントオプションを指定|
|`-t`|ファイルシステムの種類を指定|
|`--bind`|mount --bind dir1 dir2で、dir1をdir2にマウントする|

## `du`コマンド

ファイルやディレクトリがどれくらい圧迫しているか確認するコマンド。

|オプション|説明|
|-|-|
|`-a`|ディレクトリ以外にファイルについても表示|
|`-h`|わかりやすい単位を付加して表示|
|`-c`|合計容量も表示|
|`-s`|指定したファイルやディレクトリの合計容量のみを表示|
|`-S`|ディレクトリの容量にサブディレクトリの容量を含めずに表示|

`-s`オプションで特定のディレクトリの合計容量のみ表示

```bash
$ du -hs /usr/local/
1.2G    /usr/local/
```

## `df`コマンド

ファイルシステムのディスク容量の使用状況を表示する。

`-h`オプションで単位を見やすい形で表示する。

```bash
$ df -h
ファイルシス        サイズ  使用  残り 使用% マウント位置
devtmpfs              4.1G     0  4.1G    0% /dev
tmpfs                 4.1G   92K  4.1G    1% /dev/shm
tmpfs                 4.1G  8.6M  4.1G    1% /run
tmpfs                 4.1G     0  4.1G    0% /sys/fs/cgroup
/dev/mapper/rl-root    42G  5.1G   37G   13% /
/dev/sda1            1014M  266M  749M   27% /boot
shm                    63M   16K   63M    1% /var/lib/containers/storage/overlay-containers/c1dd7b14474af00f593825267e2d53970ffd73ca3910ceca8b1d376cc13da57c/userdata/shm
overlay                42G  5.1G   37G   13% /var/lib/containers/storage/overlay/79131ae2b42d3e42a6e041b3a365832ea276b2efbe76cd3f3e38441e7c745312/merged
tmpfs                 828M     0  828M    0% /run/user/0
```

`-T`オプションでファイルシステムタイプを表示する。

```bash
$ df -Th
ファイルシス        タイプ   サイズ  使用  残り 使用% マウント位置
devtmpfs            devtmpfs   4.1G     0  4.1G    0% /dev
tmpfs               tmpfs      4.1G   92K  4.1G    1% /dev/shm
tmpfs               tmpfs      4.1G  8.6M  4.1G    1% /run
tmpfs               tmpfs      4.1G     0  4.1G    0% /sys/fs/cgroup
/dev/mapper/rl-root xfs         42G  5.1G   37G   13% /
/dev/sda1           xfs       1014M  266M  749M   27% /boot
shm                 tmpfs       63M   16K   63M    1% /var/lib/containers/storage/overlay-containers/c1dd7b14474af00f593825267e2d53970ffd73ca3910ceca8b1d376cc13da57c/userdata/shm
overlay             overlay     42G  5.1G   37G   13% /var/lib/containers/storage/overlay/79131ae2b42d3e42a6e041b3a365832ea276b2efbe76cd3f3e38441e7c745312/merged
tmpfs               tmpfs      828M     0  828M    0% /run/user/0
```
