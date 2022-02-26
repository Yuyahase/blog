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

### systemd-catコマンド
コマンドの実行結果をジャーナルファイルに書き込む

### rsyslogコマンド
rsyslogはsyslogの問題点を克服した次世代のsyslogである。

ルール設定は以下の書式で行う。
```
ファシリティ.プライオリティ アクション
```
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
### loggerコマンド
手動でログメッセージを生成するコマンド
