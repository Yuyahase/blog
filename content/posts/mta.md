---
title: "MTAと印刷"
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

## 代表的なMTAの特徴

MTAとはメールの転送を行うプログラム。ネットワーク経由、サーバ内部の転送依頼に基づき、指定された宛先へメールを転送する。

|名称|特徴|
|-|-|
|sendmail|古くから使われるMTA|
|Postfix|sendmailとの高い互換性を保ちながら、処理を高速化している。セキュリティも向上しているので最近のディストリビューションで標準MTAとなっている|
|exim|Debian系の標準MTA|

## メールコマンド

### mailコマンド

メールの送信や、受信メールを表示するコマンド。
オプションをつけずにmailコマンドを実行すると、メールスプール`/var/spool/mail/`に格納されている受信メールを表示する。

### mailqコマンド

転送先メールサーバの不具合やネットワークの不具合などでメールが転送できなかった場合、MTAはキューにそのメールをおき、間隔を空けて再送を試みる。

主なMTAのキューディレクトリ

|MTA|デフォルトのキューディレクトリ|
|-|-|
|Sendmail|/var/spool/mqueue|
|Exim|/var/spool/exim/input|
|Postfix|/var/spool/postfix/deferred|

### メール転送

メールの転送を行う方法は二つある。

|設定ファイル|影響範囲|内容|反映方法|
|-|-|-|-|
|/etc/aliases|システム全体|別名と転送先を記載|newaliasesコマンドを実行|
|~/.forward|ユーザ個別|転送先を記載|即時反映|

----

## 印刷コマンド

### CUPS系コマンド

従来のlpdの印刷システムと互換性を持った印刷サービス。
WEBブラウザからプリンタの設定を行うことができる。
<http://localhost:631>

CUPSはIPP(Internet Printing Protocol)を採用している。

プリンタの機種依存情報を記述するPPDファイルをサポートしている。

設定ファイルは`/etc/cups/cupsd.conf`で接続要求を受けるポート番号やクライアントのアクセス制御などの設定を行う。

プリンターに関する設定を記述するのは`/etc/cups/printers.conf`

|コマンド|説明|
|-|-|
|`lp`|印刷ジョブを生成し、プリントキューに登録|
|`cancel`|プリントキューにある印刷ジョブを削除|
|`lpstat`|プリントキューにある印刷ジョブを表示|

### レガシー系コマンド

|コマンド|説明|
|-|-|
|`lpr`|印刷ジョブを生成し、プリントキューに登録(LPD互換)|
|`lprm`|プリントキューにある印刷所部を削除(LPD互換)|
|`lpq`|プリントキューにある印刷ジョブを表示(LPD互換)|
|`-#`|指定した部数を印刷する|
|`-Pプリンタ名`|指定したプリンタに出力する|
