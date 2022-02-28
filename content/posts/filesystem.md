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

## `mount`コマンド

ファイルシステムをマウントするコマンド。

|オプション|説明|
|-|-|
|`-a`|`/etc/fstab`ファイルに記載されているファイルシステムをすべてマウント|
|`-o`|マウントオプションを指定|
|`-t`|ファイルシステムの種類を指定|
|`--bind`|mount --bind dir1 dir2で、dir1をdir2にマウントする|
