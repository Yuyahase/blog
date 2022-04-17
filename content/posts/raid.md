---
title: "RAID"
date: 2022-04-14T19:28:50+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## RAID0(ストライピング)

複数のディスクに分散してデータを書き込み、１台のディスクのように扱う。

## RAID1

複数のディスクに全く同じ内容を並行して書き込み、1台のディスクのように扱う。

## RAID4

RAID0のようりストライピングを行うが、RAID4はエラー訂正ようのパリティ情報を保存する専用ディスクを用意し、万一データが保存されているディスクがクラッシュしても、パリティ情報が復旧できる利点がある。

RAID構成のための最低台数、利用可能容量は以下。

|レベル|最低台数|利用可能容量|
|-|-|
|RAID0|２台|全ディスクの合計|
|RAID1|２台|1台分|
|RAID5|3台|合計から１台分を引いた分|

## RAIDの構築と運用

RAIDを構成するパーティションを作成するときは、パーティションタイプを`fd`に設定しておく。

RAIDアレイの制御には`mdadm`コマンドを使う。

```bash
# dev/sdc5, /dev/sdc6, /dev/sdc7から構成されるRAID1のRAIDアレイmd0を作成する。アクティブなデバイスは二つで、一つは予備。
$ mdadm -C /dev/md0 --level 1 --raid-devices 2 --spare-devices 1 /dev/sdc5 /dev/sdc6 /dev/sdc7
```

`mdadm`には設定ファイル`/etc/mdadm/mdadm.conf`もしくは`/etc/mdadm.conf`がある。

このファイルに設定を記述しておうと、システム起動時にRAIDアレイを自動認識させることができる。

RAIDアレイの状態は、`/proc/mdstat`ファイルで確認できる。

障害が発生したデバイスを新しいデバイスに交換する方法は以下。

```bash
# 障害の発生したデバイスに不良マークをつける(-f)
# これによりRAIDコントローラがそのデバイスは故障とみなす。
$ mdadm --manage /dev/md0 -f /dev/sdb1
# 障害の発生したデバイスをRAID構成デバイスから取り除く(-r)
$ mdadm --manage /dev/md0 -r /dev/sdb1
# 最後にRAID構成デバイスとして代わりに新しいデビアスを追加する(-a)
# 障害発生した/dev/sdb1を/dev/sdb1に交換
$ madadm /dev/md0 -a /dev/sddd1
```
