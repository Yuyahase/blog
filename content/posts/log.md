---
title: "システムログ"
date: 2022-02-25T11:30:03+00:00
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

### systemd-journald

systemdの動作するシステムではsystemd-journaldデーモンを動作させて、ログの一元管理を行う。

バイナリ形式のログは`/run/log/journal`配下にシステム固有のディレクトリを作成し、その中の`system.journal`というジャーナルファイルに記録する。

systemd-journaldが書き込む形式がバイナリ形式のため、catコマンドで表示することはできない。その為、`journalctl`コマンドを使用する。
journalctlの主なオプションは以下

|&nbsp;&nbsp;オプション&nbsp;&nbsp;|&nbsp;説明&nbsp;|
|-|-|
|-a(--all)|画面表示できない文字も含めて全て表示|
|-b(--boot)|特定システム起動時のログを表示する。0は今回の起動時を意味する。直近起動時は-1、2回目の起動時は-2|
|-D|指定したディレクトリのログを表示|
|-l(--full)|画面表示可能なすべてのログを表示|
|-n(--lines)|直近のログから指定行数分を表示|
|-o(--output)|ログの出力形式を指定|
|-r(--reverse)|最古のログからの表示を逆にし、最新のログから表示|
|-x|エマージェンシーモードで起動時に`journalctl -xb` でログを確認するように表示される|

`/run`ディレクトリはメモリ上に作成されたファイルシステムの為、再起動するとデータが消える。

#### ジャーナルログ表示

オプションなしの場合は古い方のログからページャ形式で表示。

```bash
$ journalctl
-- Logs begin at Fri 2022-03-11 11:09:41 JST, end at Tue 2022-03-22 23:03:41 JST. --
 3月 11 11:09:41 localhost.localdomain kernel: Linux version 4.18.0-348.2.1.el8_5.x86_64 (mockbui>
 3月 11 11:09:41 localhost.localdomain kernel: Command line: BOOT_IMAGE=(hd0,msdos1)/vmlinuz-4.18>
 3月 11 11:09:41 localhost.localdomain kernel: x86/fpu: x87 FPU will use FXSAVE
 3月 11 11:09:41 localhost.localdomain kernel: BIOS-provided physical RAM map:
```

#### エラー以上のプライオリティのみ表示

特定のプライオリティ以上の重要度のみ出力する。

```bash
$ journalctl -p err
-- Logs begin at Fri 2022-03-11 11:09:41 JST, end at Tue 2022-03-22 23:10:00 JST. --
 3月 11 11:09:47 localhost.localdomain kernel: [drm:vmw_host_log [vmwgfx]] *ERROR* Failed to send>
 3月 11 11:09:47 localhost.localdomain kernel: [drm:vmw_host_log [vmwgfx]] *ERROR* Failed to send>
 3月 11 11:09:53 localhost.localdomain smbd[764]: [2022/03/11 11:09:53.373759,  0] ../../lib/util>
 3月 11 11:09:53 localhost.localdomain smbd[764]:   daemon_ready: daemon 'smbd' finished starting>
 3月 12 04:25:12 localhost.localdomain sshd[34655]: error: kex_exchange_identification: Connectio>
 3月 12 16:19:00 localhost.localdomain sshd[38540]: fatal: Access denied for user sample by PAM a>
 3月 17 12:14:23 localhost.localdomain systemd[1]: Failed to start The Apache HTTP Server.
 3月 17 12:16:41 localhost.localdomain systemd[1]: Failed to start The Apache HTTP Server.
```

#### 特定時刻のログを表示する

`--since`オプションと`--until`オプションを組み合わせて時刻指定して出力できる。

```bash
$ journalctl -p err --since "2022-03-16"
-- Logs begin at Fri 2022-03-11 11:09:41 JST, end at Tue 2022-03-22 23:10:00 JST. --
 3月 17 12:14:23 localhost.localdomain systemd[1]: Failed to start The Apache HTTP Server.
 3月 17 12:16:41 localhost.localdomain systemd[1]: Failed to start The Apache HTTP Server.
```

#### 特定のユニットのみ表示する

`-uオプション`もしくは`_SYSTEMD_UNIT=`でユニット名を指定する。

```bash
$ journalctl -u sshd.service 
-- Logs begin at Fri 2022-03-11 11:09:41 JST, end at Thu 2022-03-24 23:10:00 JST. --
 3月 11 11:09:48 localhost.localdomain systemd[1]: Starting OpenSSH server daemon...
 3月 11 11:09:48 localhost.localdomain sshd[736]: Server listening on 0.0.0.0 port 22.
 3月 11 11:09:48 localhost.localdomain sshd[736]: Server listening on :: port 22.
 3月 11 11:09:48 localhost.localdomain systemd[1]: Started OpenSSH server daemon.
 ```

### systemd-catコマンド

コマンドの実行結果をジャーナルファイルに書き込む。

```bash
$ systemd-cat def -h
$ journalctl -r
3月 22 23:00:43 localhost.localdomain unknown[163754]: overlay                42G  6.5G   35G   >
3月 22 23:00:43 localhost.localdomain unknown[163754]: shm                    63M   16K   63M   >
q3月 22 23:00:43 localhost.localdomain unknown[163754]: tmpfs                 828M     0  828M   >
```

### rsyslogコマンド

rsyslogはsyslogの問題点を克服した次世代のsyslogである。

ルール設定は以下の書式で行う。

`ファシリティ.プライオリティ アクション`

`/etc/rsyslog.conf`が設定ファイルとなる。

大量のログ発生時はディスクへの書き込みでパフォーマンスが低下するおそれがあるので、アクションの前に `-` を付けると、ログを非同期で書き込む。

主なファシリティは以下となる。

|ファシリティ|説明|
|-|-|
|auth, authpriv|認証サービス|
|daemon|デーモン各種|
|kern|カーネル|
|lpr|印刷サービス|
|mail|メールサービス|
|syslog|syslogデーモン|
|user|ユーザーアプリケーション|
|local0-7|0~7のローカルシステム|

出力先の記述形式は以下の通り。

|出力先|内容|
|`/var/log/message`|ログファイルに出力|
|`@ホスト名`|ホストにUDPで出力|
|`@@ホスト名`|ホストにTCPで出力|
|`*`|ログイン中の全てのユーザに出力|

### loggerコマンド

手動でログメッセージを生成するコマンド
