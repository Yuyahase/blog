---
title: "シェル環境のカスタマイズ"
date: 2022-02-17T11:30:03+00:00
tags: ["linux"] 
author: "Me"
canonicalURL: "https://canonical.url/to/page"
xdisableHLJS: true # to disable highlightjs
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---
## 環境変数

### envコマンド

環境変数を一時的に変更したり、削除するコマンド。

|オプション|説明|
|-|-|
|-i|何も環境変数が設定されていない状態にする|
|-u|環境変数を一時的に削除|
|オプションなし|全ての環境変数を表示|

### setコマンド

|オプション|説明|
|-|-|
|allexport|新規作成・変更した変数を自動的に環境変数とする|
|emacs|emacsエディタと同じキービンどにする|
|ignoreeof|Ctrl + Dを押してもログアウトしない設定にする|
|noclobber|リダイレクト演算子で既存ファイルの上書きを無効にする|
|noglob|パス名展開を向こうにする(*や?を無効にする)|
|noexec|シェルスクリプトを読み込むが、構文エラーのみ確認する|

bash起動時に実行される主な環境設定ファイル

/etc/profile
/.bash_profile
/.bash_login
/.profile

## alias/unaliasコマンド

エイリアスの設定は `alias` コマンドを使用する。

`alias エイリアス名='コマンド'`

登録したエイリアスを削除するには `unalias` コマンドを使用する。

`unalias -a` で登録している全てのエイリアスが削除される。

一時的にエイリアスを無効にするには、\\を入力する。

## unsetコマンド

設定済みの環境変数やシェル変数の削除を行うコマンド。
