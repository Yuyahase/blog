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

Apache財団によって開発されているWebサーバで、現在2.0系, 2.2系、2.4系がリリースされている、

2.0系:MPM(マルチプロセッシングモジュール)機能
2.2系:キャッシュの改良や負荷分散機能
2.4系:パフォーマンスの向上、非同期I/Oサポート、メモリ使用量の削減

### Apacheのインストール

一般的なソフトフェアのインストールと同様に、configure~make~make installを実行する。

以下のオプションがある。

|オプション|説明|
|-|-|
|--prefix=ディレクトリ|インストール先のディレクトリ|
|--sysconfdir=ディレクトリ|インストール先のディレクトリ|
|--enable-module=モジュール|標準モジュールを組み込む|
|--disable-module=モジュール|標準モジュールを組み込まない|
|--enable-shared=モジュール|標準モジュールをDSOで組み込む|
|--disable-shared=モジュール|標準モジュールをDSOで組み込まない|

### Apacheの設定

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

### 主なモジュール

|モジュール名|説明|

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
