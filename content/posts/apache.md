---
title: "Apache"
date: 2022-03-09T19:46:46+09:00
tags: ["Apache","linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## Apacheとは

Apache財団によって開発されているWebサーバで、現在2.0系, 2.2系、2.4系がリリースされている。

- 2.0系:MPM(マルチプロセッシングモジュール)機能
- 2.2系:キャッシュの改良や負荷分散機能
- 2.4系:パフォーマンスの向上、非同期I/Oサポート、メモリ使用量の削減

## prefork, worker

preforkは、リクエストごとに小プロセスを割り当てる。同時に接続するクライアントが増えると、Apacheの子プロセスも増大する。

workerはスレッド対応したモデルで、クライアントからのリクエストを子プロセスのスレッドに対応したモデルで、クライアントからのリクエストを子プロセス内のスレッドで処理し、負荷が増えると子プロセスも増える。

## Apacheのインストール

一般的なソフトフェアのインストールと同様に、configure~make~make installを実行する。

以下のオプションがある。

|オプション|説明|
|-|-|
|`--prefix=ディレクトリ`|インストール先のディレクトリ|
|`--sysconfdir=ディレクトリ`|インストール先のディレクトリ|
|`--enable-module=モジュール`|標準モジュールを組み込む|
|`--disable-module=モジュール`|標準モジュールを組み込まない|
|`--enable-shared=モジュール`|標準モジュールをDSOで組み込む|
|`--disable-shared=モジュール`|標準モジュールをDSOで組み込まない|

## Apacheの設定

メインの設定ファイルは`httpd.conf`だが、機能ごとに複数の設定ファイルに分割し、`httpd.conf`でインクルードして利用するのが一般的

Apacheの主な設定ファイル(ソースからインストール)
|ファイル/ディレクトリ|説明|
|-|-|
|`/usr/local/apache2/conf/httpd.conf`|メイン設定ファイル|
|`httpd-languages.conf`|言語・文字コードの設定|
|`httpd-userdir.conf`|server-info, server-statusの設定|
|`httpd-vhost.conf`|バーチャルホストの設定|
|`httpd-default.conf`|デフォルトのサーバ全般設定|
|`httpd-ssl.conf`|SSL/TLSの設定|

Apacheの主な設定ファイル(RedHat系)
|ファイル/ディレクトリ|説明|
|-|-|
|`/etc/httpd/conf/httpd.conf`|メイン設定ファイル|
|`ssl.conf`|SSL/TSLの設定|
|`php.conf`|PHPモジュールの設定|
|`perl.conf`|Perlモジュールの設定|

## httpd.conf

Apacheでは複数のプロセスが処理を分担する。クライアントからの接続を親プロセスが待ち受け、個々のクライアントごとの対応は子プロセスが処理する。

待機している子プロセス数よりもクライアント数が大きくなれば、不足分の子プロセスをその時点で起動しなければいけなく、レスポンスが遅くなる。

子プロセス数が多ければ、メモリ消費量が大きくなる。

|ディレクティブ|説明|
|-|-|
|`ServerRoot パス`|httpdが利用するトップディレクトリを指定|
|`ServerName サーバのホスト名`|Apacheが稼働しているホストのホスト名|
|`StartServers 子プロセス数`|起動時の子プロセス数|
|`MinSpareServers 子プロセス数`|待機子プロセスの最小数|
|`MaxSpareServers 子プロセス数`|待機子プロセスの最大数|
|`MaxClients 同時接続数`|最大同時接続数(Apache2.2)|
|`MaxRequestWorkers 同時接続数`|最大同時接続数(Apache2.4)|
|`Timeout タイムアウト時間`|クライアントからの接続がタイムアウトになる時間を秒数で指定|
|`Listen IPアドレス`|待ち受けるポート番号を指定|
|`User ユーザ名`|子プロセスの実行ユーザを指定|
|`Group グループ名`|子プロセスの実行グループを指定|
|`DocumentRoot ディレクトリパス`|ドキュメントルートとなるディレクトリを指定|

## 主なモジュール

|モジュール名|説明|
|-|-|
|mod_so|動的モジュールを組み込む機能を提供|
|mod_ssl|SSLによる暗号化通信を提供|

Apacheのインストール後にモジュールを組み込むには、`apxs`コマンドを使う。
その後、`httpd.conf`に`LoadModule`ディレクティブを追加する。

`httpd -l`コマンドで組み込んだモジュールを確認できる。

```bash
$ httpd -l
Compiled in modules:
  core.c
  mod_so.c
  http_core.c
```

## apachectlコマンド

Apacheの制御にはapachectlコマンドを利用する。

|サブコマンド|説明|
|-|-|
|`start`|Apacheを起動する|
|`stop`|Apacheを終了する|
|`restart`|Apacheを再起動する|
|`graceful`|Apacheを安全に再起動する|
|`reload`|設定ファイルを再読み込みする|
|`configtest`|設定ファイルの構文をチェックする|

## ログファイル

Apacheのログは主に、アクセスログファイルとエラーログファイルがあり、
`/var/log/httpd`ディレクトリ以下に格納される。

アクセスログのログフォーマットは`httpd.conf`の`LogFormat`ディレクティブでフォーマットを指定し、`CustomLog`ディレクティブでログファイル名とフォーマットを結びつける。

エラーログには様々なエラー情報やサーバの挙動が記録されている。エラー情報のログレベルは`LogLevel`ディレクティブによって指定する。

## バーチャルホスト

1台のサーバで複数のWebサイトを管理することができる。

バーチャルホストには以下の二つの種類がある。

- 名前ベースのバーチャルホスト:1台のコンピュータに1つのIPアドレスと複数のドメイン名を設定する

- IPベースのバーチャルホスト:1台のコンピュータに複数のIPアドレスと複数のドメイン名を設定する

### 名前ベースのバーチャルホスト

バーチャルホストごとに`VirtualHost`ディレクティブ内に記述する。

### IPベースのバーチャルホスト

## SSL/TLS

公開鍵暗号を使ったセキュリティ技術。SSLを使うとWebブラウザとWebサーバ間の通信を暗号化することで、セキュアな通信を行うことができる。

認証曲によって発光されたサーバ証明書を使って、サイトの正当性を証明することができる。

Apacheではmod_sslモジュールを利用してSSLに対応する。

1. 公開鍵と暗号鍵を作成する
2. 作成した公開鍵を、利用企業の身元を証明する書類、手数料などとともに認証局に送付する
3. CAでは証明書を発行して返送する。この証明書を使って、WebサーバがWebブラウザに対して身元を明らかにする
4. 送られてきた証明書をWebサーバにインストールする

SSLでアクセスするには、リクエストURLを`https://~`にする。一般的に443番ポートが使われる。

サイト自身の独自の認証局とし、その認証局によって証明書に署名を行う方法を自己署名証明書という。

SSL/TLS関連の主なディレクティブは以下。

|ディレクティブ|説明|
|-|-|
|SSLEngine|SSL/TLSの有効/無効|
|SSLProtocol|SSL/TLSプロトコルバージョン|
|SSLCipherSuite|SSL/TLSで利用する暗号アルゴリズムのリスト|
|SSLCertificateFile|サーバ証明書ファイル|
|SSLCertificateKeyFile|サーバ秘密鍵ファイル|
