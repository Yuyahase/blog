---
title: "Systemd"
date: 2022-03-17T20:49:39+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## systemdの概要

systemdを採用したシステムでは、initプロセスの代わりにsystemdプロセスが起動し、各種サービスを管理する。

## Unit設定ファイル

Unit設定ファイルは`/etc/systemd/system`ディレクトリ以下に配置される。

[Unit], [Service], [Install]のセクションに分かれる。

主なパラメータ

|パラメータ|説明|
|-|-|
|`Description`|Unitの説明|
|`Documentation`|ドキュメントの場所|
|`After`|ここに書かれたUnit以降に自身を起動する|
|`Before`|ここに書かれたUnit以前に自身を起動する|
|`Wants`|ここに書かれたUnitが必要|
|`Type`|サービスユニットのプロセス起動タイプ|
|`ExecStartPre`|Startの実行前に実行するコマンド|
|`ExecStart`|Startで実行するコマンド|
|`ExecReload`|Reloadで実行するコマンド|
|`ExecStop`|Stopで実行するコマンド|
|`WantedBy`|sytemctl enable実行時に~.wantsディレクトリ内にシンボリックリンクを作成する|
|`RequiredBy`|sytemctl enable実行時に~.requiredディレクトリ内にシンボリックリンクを作成する|
|`PrivateTmp`|このサービス専用の/tmpと/var/tmpを用意する|
|`RuntimeDirectory`|/runの下にプライベートラインライムディレクトリを作成する|
|`RuntimeDirectoryMode`|プライベートラインライムディレクトリのパーミッション|

プロセスの起動方法は以下の6通り。

|Type|説明|
|-|-|
|`simple`|デフォルト。プロセス起動した時点で起動完了|
|`forking`|フォークして親プロセスが終了した時点で起動完了|
|`oneshot`|simpleと似ているが、次のユニットを実行する前に自信のプロセスを終了する|
|`dbus`|D-Busを使うプロセスで、D-Busの接続名を見つけると起動完了|
|`notify`|simpleと似ているが、sd_notify関数で起動完了のメッセージを受け取ったときに起動完了|
|`idle`|simpleと似ているが、他のジョブが終了するまで待機する|

```bash# It's not recommended to modify this file in-place, because it
# will be overwritten during upgrades.  If you want to customize,
# the best way is to use the "systemctl edit" command.

[Unit]
Description=The PHP FastCGI Process Manager
After=syslog.target network.target

[Service]
Type=notify
ExecStart=/usr/sbin/php-fpm --nodaemonize
ExecReload=/bin/kill -USR2 $MAINPID
PrivateTmp=true
RuntimeDirectory=php-fpm
RuntimeDirectoryMode=0755

[Install]
WantedBy=multi-user.target
```

## systemdのログ

systemdを採用したシステムでは、journalctlコマンドを使用してsystemdのログを閲覧できる。

journalctlコマンドの主なオプション

|オプション|説明|
|-|-|
|`-f`|ログの末尾を表示し続ける|
|`-n 行数`|指定した行数だけログを末尾を表示する|
|`-p priority`|メッセージのプライオリティがpriority以上のものだけを表示する|
|`-r`|ログを新しい順に表示する|
|`-u`|指定したUnitのログを出力する|
|`--since`|指定日時以降を表示|
|`--until`|指定日時以前を表示|

ログが保存されているのは、`/var/log/journal`や`/run/log/journal`といったディレクトリのバイナリファイル。

`/etc/systemd/journal.conf`のStorageオプションによって保存先を決定する。

|オプション設定値|効果|
|-|-|
|`volatile`|/run/log/journalに記録する|
|`persistent`|/var/log/journalに記録する|
|`auto(default)`|/var/log/journalが存在すればそちらに、なければ/run/log/journalに記録する|
|`none`|ログを記録せず破棄する|
