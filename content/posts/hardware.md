---
title: "Hardware"
date: 2022-02-27T17:23:08+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---
## ブートシーケンスの概要

1. BIOSの起動
2. ブートローダ
3. カーネルとinitramfsの読み込み
4. カーネルの実行
5. initramfsの実行
6. init(initramfs)の起動、ルートファイルシステムのマウント
7. initの実行

最近では以下の構成が一般的。

- PCファームウェアはBIOSの代わりにUEFIを採用
- ブートローダはGRUBに代わりGRUB２を採用
- 最初のユーザプロセスはinitに代わりsystemdを採用

GRUBのインストールは、`/sbin/grub-install` コマンドで行う。

GRUBの場合の設定ファイルは、`/boot/grub/grub.conf` または`/boot/grub/menu.lst`ファイルとなる。

initramfsは、ディスク内に構築されたルートファイルシステムへアクセスするためのデバイスドライバやファイルシステムモジュールを含むディレクトリ構成として作成さ、cpioでアーカイブし、gzipで圧縮されたファイル。

システム起動時にメモリ上に展開されて一時的に使われる小さなルートファイルシステム。

`lsinitrd` コマンドでinitramfsの内容を表示できる。

```bash
$ lsinitrd | head
Image: /boot/initramfs-4.18.0-348.2.1.el8_5.x86_64.img: 29M
========================================================================
Early CPIO image
========================================================================
drwxr-xr-x   3 root     root            0 Nov  9 23:43 .
-rw-r--r--   1 root     root            2 Nov  9 23:43 early_cpio
drwxr-xr-x   3 root     root            0 Nov  9 23:43 kernel
drwxr-xr-x   3 root     root            0 Nov  9 23:43 kernel/x86
drwxr-xr-x   2 root     root            0 Nov  9 23:43 kernel/x86/microcode
-rw-r--r--   1 root     root       103424 Nov  9 23:43 kernel/x86/microcode/GenuineIntel.bin
```

## コマンド

### lspciコマンド

システム全てのPCIバスと、接続されているデバイスに関する情報を表示する。

### lsusbコマンド

USBバスと、接続されているデバイスに関数情報を表示する。

### lsmodコマンド

現在システムにロードされているモジュールの状態を表示する。

`/proc/modules` ファイルの内容が読み込まれて表示される。

`modinfo` コマンドで各モジュールの詳細を確認する。

```bash
$ lsmod
Module                  Size  Used by
binfmt_misc            20480  1
mptcp_diag             16384  0
tcp_diag               16384  0
udp_diag               16384  0
raw_diag               16384  0
inet_diag              24576  4 tcp_diag,mptcp_diag,raw_diag,udp_diag
unix_diag              16384  0
```

### modprobeコマンド

指定されたモジュールの依存関係を確認し、必要なモジュールを自動的に追加、削除する。

`insmod` コマンドもモジュールをロードするが、依存関係は考慮しない。

### unameコマンド

カーネルバージョンやマシンアーキテクチャなどのシステム情報を表示する。

```bash
$ uname -a
Linux localhost.localdomain 4.18.0-348.2.1.el8_5.x86_64 #1 SMP Mon Nov 15 20:49:28 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

### init　/ telinitコマンド

指示を与えてランレベルを変更する。

initコマンドは実行すると、ログインユーザへの通知を一切行わず、直ちにシステム変更を行う。

現在のランレベルを調べるには `runlevel` や `who` コマンドを使用する。

```bash
$ runlevel
N 3
$ who -r
run-level 3  2022-02-14 01:18
```

initプロセスに設定ファイルを再読み込みさせる際は、telinitコマンドにqオプションを指定して実行する。

### dmesgコマンド

システム起動時にカーネルが画面に表示するメッセージを表示する。

`cat /var/log/dmesg` でも同じ結果が得られる。

dmesgコマンド実行時に`--clear` オプションを指定すると、カーネルのリングバッファの内容を消去する。

## SysV init

`/etc/inittab` ファイルに記述されたランレベルを基に、initプロセスが起動する。

|主なランレベル|サービス|説明|
|-|-|-|
|0|停止|システムの停止|
|1|シングルユーザ|rootだけでログイン可能。システムのメンテナンスに利用される|
|3|マルチユーザ|rootだけでなく一般ユーザもログイン可能|
|3|マルチユーザ + GUI|ディスプレイマネージャからのログイン画面からX Window Systemにログインする|
|6|再起動|システムの再起動|

SysV initでは、起動時に使用するスクリプトをランレベル毎にディレクトリで管理している。

各スクリプトのファイル名は、ファイル名の先頭文字(Sは開始、Kは停止)でコントロールする。

- /etc/rc1.d/K01myscript1 : ランレベル1のときに、myscript1のサービスを停止する

## systemd

SysV initに代わる仕組みで、ソケットとD-BUSを利用することで並列処理を強化し、
依存関係を定義してサービスを管理する。

systemdはユニットによってシステムを管理する。

## ACPIイベント

ACPIとは、ハードウェアコンポーネントの電源管理のための規格。

電源ボタン、ノートPCの蓋開閉、バッテリーの充電状態、プロセッサの速度制御などを行う。
