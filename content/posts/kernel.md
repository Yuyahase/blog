---
title: "Linuxカーネル"
date: 2022-04-11T11:30:03+00:00
tags: ["linux"] 
author: "Me"
canonicalURL: "https://canonical.url/to/page"
xdisableHLJS: true # to disable highlightjs
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
pygmentsCodeFences: true
---

## カーネルバージョン

2.6より前だと2.X.YのXが奇数だと開発版、偶数だと安定板。

カーネル3.0に対してバグ修正を行ったものは3.0.Xとしてリリースされる。

1桁目がメジャーバージョン、２桁目がマイナーバージョン、３桁目が安定板のリリース番号。

|リリースカテゴリ|説明|
|-|-|
|`prepatch`|リリース候補|
|`mainline`|prepatchの後にリリースされる正式版|
|`stable`|mainlineの後にリリースされる安定板|
|`longterm`|stableの中から選ばれ、新しいカーネルに行われたものと同様のバグフィックスが行われるリリース,longterm版は約2年の長い期間のバグフィックスが行われる|

現在稼働しているカーネルのバージョンを知るには`uname`コマンドを使う。

|オプション|説明|
|-|-|
|`-s`|カーネルの名前を表示|
|`-r`|カーネルのバージョンを表示|
|`-m`|ハードウェア名を表示|
|`-p`|CPUタイプを表示|
|`-a`|以上全ての情報を表示|

`/proc/version`を参照することでもカーネルバージョンを確認できる。

```bash
$ uname -a
Linux localhost.localdomain 4.18.0-348.2.1.el8_5.x86_64 #1 SMP Mon Nov 15 20:49:28 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
# アーキテクチャ表示
$ arch
x86_64
$ cat /proc/version 
Linux version 4.18.0-348.2.1.el8_5.x86_64 (mockbuild@dal1-prod-builder001.bld.equ.rockylinux.org) (gcc version 8.5.0 20210514 (Red Hat 8.5.0-3) (GCC)) #1 SMP Mon Nov 15 20:49:28 UTC 2021
```

カーネルソースからコンパイルしたカーネルを利用している場合は、カーネルのMakefileを参照することでも確認できる。(/usr/src/linuxや/usr/src/kernels/カーネルバージョン等)

## カーネルイメージ

Linuxカーネルは通常、イメージファイルとしてファイルシステム上に格納されている。

カーネルイメージは一般的にvmlinuz-カーネルバージョンといったファイル名が付けられている。

```bash
$ ls /boot/vmlinuz*
/boot/vmlinuz-0-rescue-9c8ebcfd226d4c37aa0284b668e03b47  /boot/vmlinuz-4.18.0-348.el8.0.2.x86_64
/boot/vmlinuz-4.18.0-348.2.1.el8_5.x86_64
```

## カーネルモジュールの管理

Linuxカーネルは、ローダブルモジュールをサポートを有効にした場合、デバイスドライバなどカーネル機能の一部をモジュール化し、カーネルから分離して、必要なときに読み込んで使用することができる。

カーネルモジュールが配置されているディレクトリは、`/lib/modules/カーネルバージョン`となる。

モジュールファイルには「.ko」という拡張子が付けられる。

カーネルモジュールを操作するコマンドには次のようなものがある。

|コマンド|説明|
|-|-|
|`lsmod`|ロードされているモジュールをリスト表示する|
|`insmod`|モジュールをロードする|
|`rmmod`|モジュールをアンロードする|
|`modprobe`|依存関係を解決してモジュールをロードもしくはアンロードする|
|`depmod`|モジュールの依存関係情報を更新する|
|`modinfo`|モジュールの情報を表示する。|

`modinfo`のオプションは以下の通り。

|オプション|説明|
|-|-|
|`-d | --description`|モジュールの説明|
|`-l | --license`|モジュールのライセンス|
|`-n | --filename`|モジュールのファイル名(パス)|
|`-p | --parameters`|モジュールのサポートしているパラメータ|

```bash
# ext4モジュールのファイル名、説明、作者名、ライセンス、カーネルやコンパイラのバージョン、依存関係にあるモジュールを表示する。
$ modinfo ext4
filename:       /lib/modules/4.18.0-348.2.1.el8_5.x86_64/kernel/fs/ext4/ext4.ko.xz
softdep:        pre: crc32c
license:        GPL
description:    Fourth Extended Filesystem
author:         Remy Card, Stephen Tweedie, Andrew Morton, Andreas Dilger, Theodore Ts'o and others
alias:          fs-ext4
alias:          ext3
alias:          fs-ext3
alias:          ext2
alias:          fs-ext2
rhelversion:    8.5
```

`modprobe`コマンドは、モジュールのロードやアンロードを行う。

`modprobe`コマンドが参照するモジュールの依存関係は、`modules.dep`ファイルに記述されている。

`modules.dep`ファイルを作成するには`depmod`コマンドを実行する。

|オプション|説明|
|-|-|
|`-a`|すべてのモジュールをロードする|
|`-c`|現在使われている設定を表示する|
|`-n`|実際には何も行わず表示するだけ|
|`-r`|モジュールをアンロードする|
|`-f`|強制的に操作を行う|
|`--show-depends モジュール名`|指定モジュールの依存関係を表示する|

`insmod`や`rmmode`は依存関係を配慮する必要があるが、`modprobe`は依存関係を調べ、自動的にロードする。

```bash
# xfsモジュールをロード
$ modprobe xfs
# xfsモジュールをアンロード
$ modprobe -r xfs
# ext4モジュールと依存関係のあるモジュール一覧を表示
$ modprobe --show-depends ext4
insmod /lib/modules/4.18.0-348.2.1.el8_5.x86_64/kernel/fs/jbd2/jbd2.ko.xz 
insmod /lib/modules/4.18.0-348.2.1.el8_5.x86_64/kernel/fs/mbcache.ko.xz 
insmod /lib/modules/4.18.0-348.2.1.el8_5.x86_64/kernel/arch/x86/crypto/crc
```

## カーネルコンパイル

カーネルは、必要に応じてソースコードからコンパイルできる。

次のような場合にはカーネルの再構築を検討する必要がある。

- 必要なデバイスドライバがカーネルに含まれていない
- 使用しているハードウェアに最適化したカーネルを利用したい
- カーネルの最新機能を使いたい

カーネルを再構築するには、gccコンパイラやmakeユーティリティ、カーネルソース、カーネルヘッダなどが必要となる。

## カーネルコンパイルとインストール

1.カーネルソースを用意する

カーネルのソースコードは通常、`/usr/src`ディレクトリ以下のlinuxカーネルバージョンという名前のディレクトリに格納される。

このディレクトリ以下に、カーネルソースコードや、カーネルコンパイルに必要なファイルなどが配置されている。

ディストリビュータの手が入っていないカーネルをvannila kernelという。

以下は`/usr/src/linux`以下の構造

|ディレクトリ名|説明|
|-|-|
|`arch`|アーキテクチャ(i386, x86_64, arm)に依存したコード|
|`configs`|目的別の.configファイル|
|`crypto`|暗号処理関数|
|`drivers`|各種デバイスドライバ関連|
|`fs`|仮想ファイルシステムと各種ファイルシステム関連|
|`include`|C言語のインクルードファイル|
|`mm`|メモリ管理|
|`net`|ネットワーク|

|ファイル名|説明|
|-|-|
|`.config`|カーネルの設定ファイル|
|`Makefile`|カーネルのMakefile|

2.カーネルコンフィギュレーションを設定する

カーネルの設定は、それぞれカーネルに組み込むか、組み込まずローダブルモジュールとするか、組み込まないか、を設定する。以下のコマンドを使う。

- `make config`:コンソールで質問に答える形で順に設定する。
- `make menuconfig`:コンソール上でメニュー形式で設定する。
- `make xconfig`:X(KDE)上で設定する。
- `make gconfig`:X(GNOME)上で設定する。

カーネルの設定は、`.config`ファイルに記録されている。

`make mrproper`で設定ファイルを含めてディレクトリ内を初期化する。カーネルのソースをダウンロードしてきたら、ビルドする前に、まずこのコマンドで設定ファイルを確実に初期化することを推奨される。

`make clear`は設定ファイルは残してソースディレクトリ内を削除する。

現在のカーネルコンフィギュレーションを新しい設定に反映させたい場合、`make oldconfig`を実行する。

3.カーネルとカーネルモジュールをコンパイルする

`make`をターゲット指定なしで実行すると、カーネルのコンパイルとカーネルモジュールのコンパイルの両方が実行される。

カーネルmakeの主要ターゲット

|ターゲット|説明|
|-|-|
|`clean`|.config以外の不要なファイルを削除する|
|`mrproper`|.configやバックアップファイルも含め不要なファイルを削除する|
|`defconfig`|デフオルト値を設定した.configを作成する|
|`all`|すべてビルドを実施する|
|`rpm`|カーネルをビルド後にRPMパッケージを作成する|
|`rpm-pkg`|ソースRPMパッケージを作成する|

4.カーネルとカーネルモジュールを配置する

カーネルモジュールを適切なディレクトリにインストールする。この作業は`makemodules_install`を使う。

インストール先のディレクトリは`/lib/modules/カーネルバージョン`となる。

5.ブートローダの設定を変更する

```bash
# カーネルをインストールする
# カーネルを/boot以下に、バージョンをファイル名に付加してコピー
# 初期RAMディスクが必要な場合は作成
# ブートローダの設定ファイルに新しいカーネル用のエントリを追加
$ make install
# コンパイルしたカーネルは0番目なので、デフォルトのカーネルを変更する
$ grub2-set-default 0
# 設定ファイルを更新する
$ grub2-mkconfig -o /boot/grub2/grub.cfg
$ reboot
# カーネルが新しくなっているか確認
$ uname -r
```

## カーネルパラメータの変更

`/proc`ディレクトリ以下のファイルを使って、カーネル情報を取得したり、カーネルの動作を変更することができる。

カーネルパラメータは、カーネルに備わった機能の有効/無効の切り替えや、システムのパフォーマンスチューニング、カーネルが認識しているシステム情報の参照などに使われる。

`/proc/sys`以下のカーネルパラメータを読み書きを通じて操作することができる。

このようにして変更したカーネルパラメータは、再起動によって消える。

設定をずっと有効にしたい場合は、その設定を`/etc/sysctl.conf`ファイルに追加する。

`sysctl`コマンドオプションは以下の通り

|オプション|説明|
|-|-|
|`-a`|現在利用できる全てのパラメータを表示|
|`-p`|指定したファイルから設定を読み込む(デフォルトは/etc/sysctl.conf)|
|`-w カーネルパラメータ=値`|パラメータを更新する|

```bash
# カーネルパラメータIPフォワードを有効にする
$ sysctl -w net.ipv4.ip_forwward=1
# 現在利用できるカーネルパラメータを表示
$ sysctl -a | head
abi.vsyscall32 = 1
crypto.fips_enabled = 0
debug.exception-trace = 1
debug.kprobes-optimization = 1
dev.cdrom.autoclose = 1
dev.cdrom.autoeject = 0
dev.cdrom.check_media = 0
dev.cdrom.debug = 0
dev.cdrom.info = CD-ROM information, Id: cdrom.c 3.20 2003/12/17
dev.cdrom.info = 
# 共有メモリセグメントを確認する
$ sysctl kernel.shmmax
kernel.shmmax = 18446744073692774399
$ cat /proc/sys/kernel/shmall 
18446744073692774399
```

## 初期RAMディスク

Linuxシステムの起動途中で一時的に利用される小さなルートファイルシステム。

Linuxでは、メモリ上にファイルシステムを作成する`RAMディスク`という機能やファイルをファイルシステムとしてマウントすることのできる`ループバックマウント`という機能を持っている。

初期RAMディスクにはファイルシステムイメージを圧縮した`initrd形式`とcpioアーカイブを圧縮した`initramfs`形式がある。

最近のディストリビューションでは、一般的に`initramfs`が利用される。

```bash
# カーネル4.4.0-57用の初期RAMディスクinitramfs-4.4.0-57.imgを作成している。
# 書式：mkinitramfs -o RAMディスクイメージファイル カーネルバージョン
$ mkinitramfs -o initramfs-4.4.0-57.img 4.4.0-57
```

## デバイスファイル

/devディレクトリ以下には、デバイスを抽象化したデバイスファイルが多数存在する。

デバイスファイルには`ブロックデバイス`と`キャラクタデバイス`がある。

### ブロックデバイス

- バッファを持ち、ランダムアクセス可能なデバイス。
- ブロック単位でアクセスでき、HDD, RAMディスク, RAIDボリュームが挙げられる。
- 一定の総容量をもつ。
- ファイルタイプは`b`となる。

```bash
$ ls -l /dev/sda{,1,2}
brw-rw---- 1 root disk 8, 0  4月 12 15:44 /dev/sda
brw-rw---- 1 root disk 8, 1  4月 12 15:44 /dev/sda1
brw-rw---- 1 root disk 8, 2  4月 12 15:44 /dev/sda2
```

### キャラクタデバイス

- キャラクタデバイスとは、ランダムアクセスが不可能なデバイス。
- キーボードや、マウス、端末、プリンタなどが挙げられる。
- ファイルタイプは`c`となる

```bash
$ cat /proc/devices 
Character devices:
  1 mem
  4 /dev/vc/0
  4 tty
  4 ttyS
  5 /dev/tty
  5 /dev/console
  5 /dev/ptmx
  7 vcs
 10 misc
 13 input
 21 sg
 29 fb
128 ptm
136 pts
162 raw
180 usb
188 ttyUSB
189 usb_device
202 cpu/msr
203 cpu/cpuid
226 drm
244 aux
245 hidraw
246 usbmon
247 bsg
248 watchdog
249 ptp
250 pps
251 rtc
252 dax
253 tpm
254 gpiochip

Block devices:
  8 sd
  9 md
 11 sr
 65 sd
 66 sd
 67 sd
 68 sd
 69 sd
 70 sd
 71 sd
128 sd
129 sd
130 sd
131 sd
132 sd
133 sd
134 sd
135 sd
253 device-mapper
254 mdp
259 blkext
```

## /proc

カーネル内のデータへのインターフェースとなる特殊なファイルシステム。

`/proc`を通じて、カーネルが認識しているハードウェア、実行中のプロセス、システムリソースなどの情報

## udev

ハードウェアが接続されると、カーネルがそれを検知し、カーネル情報を監視しているudevdデーモンが必要なデバイスファイルを動的に作成する。

新しいデバイスをカーネルが認識すると、udevdはsyfsの情報をもとにして、必要なデバイスファイルを作成する。

その際に参照される設定ファイルは、`/etc/udev/rules.d`ディレクトリ以下にある。

```bash
$ ls /etc/udev/rules.d/
69-vdo-start-by-dev.rules  70-persistent-ipoib.rules
# 
$ cat 69-vdo-start-by-dev.rules 
ENV{ID_FS_TYPE}=="vdo",  ENV{SYSTEMD_WANTS}+="vdo-start-by-dev@%k"
```
