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
|`Type`|サービスのタイプ|
|`ExecStartPre`|Startの実行前に実行するコマンド|
|`ExecStart`|Startで実行するコマンド|
|`ExecReload`|Reloadで実行するコマンド|
|`ExecStop`|Stopで実行するコマンド|
|`WantedBy`|sytemctl enable実行時に~.wantsディレクトリ内にシンボリックリンクを作成する|
|`RequiredBy`|sytemctl enable実行時に~.requiredディレクトリ内にシンボリックリンクを作成する|

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

ログが保存されているのは、`/var/log/journal`や`/var/run/log/journal`といったディレクトリのバイナリファイル。
