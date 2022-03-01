---
title: "Package Setting"
date: 2022-03-02T01:03:22+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## yum

インストール済みのパッケージを表示

```bash
yum list installed | head
インストール済みパッケージ
NetworkManager.x86_64                1:1.32.10-4.el8                         @anaconda 
NetworkManager-config-server.noarch  1:1.32.10-4.el8                         @anaconda 
NetworkManager-libnm.x86_64          1:1.32.10-4.el8                         @anaconda 
NetworkManager-team.x86_64           1:1.32.10-4.el8                         @anaconda 
```

指定したrpmパッケージの詳細情報を表示

```bash
yum info php
メタデータの期限切れの最終確認: 1:17:34 時間前の 2022年03月02日 00時35分09秒 に実施しました。
インストール済みパッケージ
名前         : php
バージョン   : 7.4.19
リリース     : 1.module+el8.5.0+696+61e7c9ba
Arch         : x86_64
サイズ       : 4.5 M
ソース       : php-7.4.19-1.module+el8.5.0+696+61e7c9ba.src.rpm
リポジトリー : @System
repo から    : appstream
概要         : PHP scripting language for creating dynamic web sites
URL          : http://www.php.net/
ライセンス   : PHP and Zend and BSD and MIT and ASL 1.0 and NCSA
説明         : PHP is an HTML-embedded scripting language. PHP
             : attempts to make it easy for developers to write
             : dynamically generated web pages. PHP also offers
             : built-in database integration for several commercial
             : and non-commercial database management systems, so
             : writing a database-enabled webpage with PHP is fairly
             : simple. The most common use of PHP coding is probably
             : as a replacement for CGI scripts.
             : 
             : The php package contains the module (often referred to
             : as mod_php) which adds support for the PHP language to
             : Apache HTTP Server when running in prefork mode.
```

設定ファイルは以下。

- `/etc/yum.conf`は基本設定ファイル
- `/etc/yum.repos.d`はリポジトリの設定ファイル

設定ファイル内では$releaseverや$basearchといった変数が使用できる。

## yumdownloader

個別にrpmファイルをダウンロードするコマンド。

## zypperコマンド

Libzyppライブラリを利用したパッケージ管理ツール。

openSUSEはパッケージ管理はRPM形式だが、管理ツールはzypperを使用している。

|コマンド|説明|
|-|-|
|`remove パッケージ名`|パッケージの削除|
|`info パッケージ名`|パッケージの情報を調べる|
|`repos`|リポジトリの一覧表示|

## dkpgコマンド

Debian系で採用されているパッケージ管理システム

|コマンド|説明|
|-|-|
|`l`,`--list`|インストール済みの全depパッケージの情報を表示|
|`L`,`--listfiles`|指定したパッケージに含まれる全てのファイルを表示|
|`I`,`--info`|指定したパッケージの詳細情報を表示|
|`S`,`--search`|インストール済みのパッケージから指定されたファイルを検索|
|`C`,`--audit`|インストールが完了していないパッケージを検索|

アンインストールに関するオプションは以下。

|コマンド|説明|
|-|-|
|`-r`,`--remove`|パッケージは削除するが、設定ファイルは残したままとする|
|`-P`,`--purge`|設定ファイルも含め完全にパッケージを削除|

インストールに関するオプションは以下。

|コマンド|説明|
|-|-|
|`-i`,`--install`|パッケージは削除するが、設定ファイルは残したままとする|
|`-E`|既に同じパージョンがインストールされている場合は、何もしない|
|`-G`|既に新しいパージョンがインストールされている場合は、何もしない|

## dkpg-reconfigure

指定したインストール済みのパッケージに対し設定内容を変更すること。

## apt-cacheコマンド

debパッケージ情報の検索、表示に使用するコマンド。

|コマンド|説明|
|-|-|
|`search`|指定したキーワドを含むパッケージを表示|
|`show`|指定したパッケージの詳細情報と、依存情報を表示|
|`depends`|指定したパッケージの依存関係と、その依存関係を満たす他のパッケージ一覧を表示|

## apt-getコマンド

debパッケージのインストール、アップグレード、アンインストールに使用するコマンド。

`/etc/apt/source/list`に記述された入手リストに従い、パッケージをインストールする。

|コマンド|説明|
|-|-|
|`upgrade`|アップグレード可能なdebパッケージを全て更新する|
|`update`|パッケージデータベースを更新|
|`dist-upgrade`|古いパッケージの削除も含め、現在インストールされている全パッケージを更新する|
