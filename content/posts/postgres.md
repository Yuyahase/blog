---
title: "postgresql.conf"
date: 2022-03-16T00:54:11+09:00
tags: ["postgresql"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## postgresql.conf

`postgresql.conf`はostgreSQL全体の動作を制御する設定ファイル。

`SET`コマンドにより稼働中のパラーメータ設定を変更することができる。

`SET パラメータ名 TO 設定値/DEFAULT;`
`SET パラメータ名 = 設定値/DEFAULT;`

`SET`の後に`SESSION`と`LOCAL`というパラメータを追加できる。

- `SESSION`:`SET`コマンドの有効範囲を、現在のセッションのみにする設定
- `LOCAL`:`SET`コマンドの有効範囲を、現在のトランザクションのみにする設定

|分類|パラメータ|設定値の説明|デフォルト|
|-|-|-|-|
|接続|`listen_addresses`|接続要求を受け付けるPostgreSQLサーバ自身のIPアドレス|localhost|
|接続|`port`|接続要求を受け付けるポート番号|5432|
|接続|`max_connections`|PostgreSQLに同時に接続が可能である、接続数の最大値|100|
|クライアント接続|`search_path`|予めスキーマ名を省略されたテーブルを検索するためのパス|'"$user",public'|
|クライアント接続|`default_transaction_isolation`|新しいトランザクションのデフォルト分離レベル|'read_committed'|
|クライアント接続|`client_encoding`|クライアントエンコーディング|'SQL_ASCII'|

設定できるログレベルは下記の通り。

INFO : ユーザから出力を要求された情報
NOTICE : ユーザにとって役立つ情報
WARNING : 不適切なコマンド使用等に関するユーザへの警告
ERROR : 特定のコマンドを中断させたエラー
FATAL : 特定のセッションを中断させたエラー
PANIC : 全てのセッションを中断させた致命的なエラー

## pg_hba.conf

クライアント認証はデータベースクラスタ内の`pg_hba.conf`という設定ファイルで管理される。

認証の判定はレコードの上から順に行われ、一致していればその行で指定されている方式で認証する。

認証に成功もしくは失敗した場合、次に続くレコードを確認することはない。

主な認証方式は以下の通り

|認証方式|説明|
|-|-|
|trust|常に許可|
|reject|常に不許可|
|md5|MD5で暗号化されたパスワード認証を行う|
|password|暗号化されていない平文でのパスワード認証を行う|
