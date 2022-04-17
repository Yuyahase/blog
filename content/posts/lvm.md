---
title: "LVM"
date: 2022-04-14T19:28:50+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## LVMの仕組み

１台のハードディスクやパーティションを物理ボリュームとする。

物理ボリュームは、一定サイズの物理エクステントから構成される。

一つ以上の物理ボリュームからボリュームグループが構成される。

ボリュームグループ内で、物理エクステントから論理ボリュームが構成される。

ボリュームグループ内で、物理エクステントから論理ボリュームが構成される。

```bash
# LVM用パーティション準備
$ parted /dev/sdd
$ parted /dev/sde

# 物理ボリュームの初期化
$ pvcreate /dev/sdd1 /dev/sde1
Physical voulume "/dev/sdd1" successfully created.
Physical voulume "/dev/sde1" successfully created.

# ボリュームグループの作成
$ vgcreate testvg /dev/sdd1 /dev/sde1
Volume group "testvg" successfully created.

# -sオプションを使って物理エクステントのサイズを指定できる
$ vgcreate -s 32M testvg /dev/sdd1 /dev/sde1
```

## LVM関連のコマンド

|コマンド|説明|書式|
|-|-|-|
|`pvcreate`|PVを作成する|pvcreate PV名|
|`pvdisplay`|PVの情報を表示する|pvdisplay PV名|
|`pvremove`|PVを削除する|pvremove PV名|
|`pvscan`|PVを検索する|pvscan|
|`pvs`|PVの情報を出力する|pvs|

## スナップショットの作成

スナップショットを利用すると、ファイルシステムをアンマウントせずにパックアップをとることができる。

スナップショットはある時点での論理ボリュームの状態の記録。

スナップショットの作成は、`lvcreate`コマンドに`-s`オプションをつける。

```bash
# 論理ボリューム/dev/testvg/lv01のスナップショットをsnap0という名前で、サイズ100MBとして作成する。
$ lvcreate -s -L 100M -n snap0 /dev/testvg/lv01
```

## 記憶装置へのアクセス

Linuxでは、ハードディスクなどの記憶装置へのアクセスはデバイスファイルを通じて行われる。

### ハードディスクの管理

Linuxでは、IDE以外のハードディスクは全てSCSIデバイスとして扱う。

IDEの転送モードには、メインメモリとハードディスクとのデータ転送について、以下がある。

現在ではDMAモードを使うのが一般的。

- PIOモード：CPUがハードディスクに直接命令してデータ転送する方法
- DMAモード：CPUに代わって専用のコントローラチップがデータ転送を管理する。

DMAモードやIDEハードディスクのパラメータを確認したり、設定したりするには`hdparm`コマンドを使う。

ライトキャッシュを無効にすると、ディスクへの書き込みはキャッシュを経由せず、CPUから書き込み命令時に行われるため、書き込み速度や条件によりディスクI/O待ちが発生しやすくなるが、突発的な電源OFF等の場合でも、CPUが把握する状態との不整合は発生しにくくなる。

|オプション|説明|
|-|-|
|`-i`|ハードディスクの詳細情報を表示する|
|`-I`|ドライブから直接詳細情報を表示する|
|`-c0`|32ビットI/Oを無効|
|`-c1`|32ビットI/Oを有効|
|`-d0`|DMAモード無効|
|`-d1`|DMAモード有効|
|`-t`|バッファキャッシュを使わずに読み込み速度を計測|
|`-T`|バッファキャッシュの読み込み速度を計測|
|`-W0`|ライトキャッシュのオフ|
|`-W1`|ライトキャッシュのオン|

### iSCSI

ローカルストレージと同じようにネットワーク上のストレージを利用できる。

ファイバチャネルを使ったSANでは専用のデバイスが必要だが、iSCSIでは一般的なネットワーク機器が利用できる。

iSCSIは処理要求を行うイニシエータ、レスポンスを返す側をターゲットという。

iSCSIイニシエータからiSCSIターゲットへアクセスし、デバイスとして使用可能にするには、`iscsiadm`コマンドを使う

サーバをいiSCSIイニシエータとして動作させるには、`iscsidデーモン`を起動する必要がある。

`iscsidデーモン`と`iscsiadm`コマンドの設定ファイルは`iscsid.conf`となる。

ローカルデータベースに登録済みiSCSIターゲットに自動的にログインするのは`iscsi`デーモンとなる。

|オプション|説明|
|-|-|
|`-m | --mode モード`|モードの指定(discovery:接続先ターゲットの一覧取得, node:登録済みターゲット取得, session:セッションの一覧取得)|
|`-l | --login`|iSCSIターゲットにログイン|
|`-u | --logout`|iSCSIターゲットにログアウト|
|`-t | --type`|タイプの指定、主にsendtargetsを指定する|
|`-p | --portal IPアドレス`|接続先の指定。デフォルトポート番号は3260|
|`-T | --targetname=IQN`|iSCSIターゲットの指定|

### SSD

SSDに記録されているファイルを削除した場合、実際にはSSD上のデータは削除されず、当該ブロックに削除マークが付けられた状態になる。

Trimを使うと、ファイルを削除したときにブロックのデータを削除するため、速度低下が起こりにくくなる。

```bash
# SSDがTrimに対応しているか確認
$ hdparm -I /dev/sda | greo TRIM
# Trimを実行するにはfstrimコマンドを使う
$ fstrim -v /
```

## AHCI

Advanced Host Controller InterfaceはIDEに比べて、はるかに高速なシリアルATA(SATA)を活かせるインターフェース仕様としてインテルが策定してる。

## WWID

WWID(World Wide Identifier)はWWN(World Wide Name)と言い、
ファイバーチャネルやSCSIによるSANにおいて、記憶装置を一意に識別するもの。iSCSIにおけるIQNに相当する。

SCSI接続の記憶装置の場合、`scsi_id`コマンドで確認できる。
