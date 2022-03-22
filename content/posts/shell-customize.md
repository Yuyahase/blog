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
## シェル変数と環境変数の違い

### シェル変数

設定されたシェルだけが使用する変数。子プロセスには引き継がれない。

|変数|説明|
|-|-|
|$?|直前に実行されたコマンドの終了ステータス|
|$n|シェルスクリプトに渡されたn番目の引数、10個目以降は${10}のように指定する|
|$$|現在のシェルのPID|
|$#|実行時に指定された引数の数|

### 環境変数

設定されたシェルとそのシェルで起動したプログラムが使用する変数。子プロセスに引き継がれる。

### exportコマンド

環境変数を設定するコマンド。
環境変数は子プロセスとして起動したアプリケーションに引き継がれるので、アプリケーションから利用できる。

```bash
$ export
declare -x DBUS_SESSION_BUS_ADDRESS="unix:path=/run/user/0/bus"
declare -x HISTCONTROL="ignoredups"
declare -x HISTSIZE="1000"
declare -x HOME="/root"
declare -x HOSTNAME="localhost.localdomain"
declare -x LANG="ja_JP.UTF-8"
```

### envコマンド

環境変数を一時的に変更したり、削除するコマンド。

|オプション|説明|
|-|-|
|-i|何も環境変数が設定されていない状態にする|
|-u|環境変数を一時的に削除|
|オプションなし|全ての環境変数を表示|

### setコマンド

setコマンドはオプションを指定せずに実行すると、シェル変数、シェル関数を表示する。

|オプション|説明|
|-|-|
|-o|シェルオプションを有効にする|
|+o|シェルオプションを無効にする|
|allexport|新規作成・変更した変数を自動的に環境変数とする|
|emacs|emacsエディタと同じキービンどにする|
|ignoreeof|Ctrl + Dを押してもログアウトしない設定にする|
|noclobber|リダイレクト演算子で既存ファイルの上書きを無効にする|
|noglob|パス名展開を向こうにする(*や?を無効にする)|
|noexec|シェルスクリプトを読み込むが、構文エラーのみ確認する|

bash起動時に実行される主な環境設定ファイル

`/etc/profile`,`~/bash_profile`,`~/.bash_login`,`/.profile`

|ファイル|起動タイミング|対象範囲|
|-|-|-|
|`/etc/profile`|ログイン時に実行|全ユーザ|
|`/etc/bashrc`|`~/.bashrc`から参照|全ユーザ|
|`~/.bash_profile`|ログイン時に実行|HOMEユーザ|
|`~/.bash_login`|ログイン時に実行(`~/.bash_profile`がない場合)|HOMEユーザ|
|`~/.profile`|ログイン時に実行(上記二つがない場合)|HOMEユーザ|
|`~/.bashrc`|bash起動時に実行|HOMEユーザ|
|`~/.bash_logout`|ログアウト時に実行|HOMEユーザ|

## alias/unaliasコマンド

エイリアスの設定は `alias` コマンドを使用する。

`alias エイリアス名='コマンド'`

登録したエイリアスを削除するには `unalias` コマンドを使用する。

`unalias -a` で登録している全てのエイリアスが削除される。

一時的にエイリアスを無効にするには、\\を入力する。

## unsetコマンド

設定済みの環境変数やシェル変数の削除を行うコマンド。

## コマンドの実行履歴

`~/.bash_history` に保存される。

## シェルスクリプトの実行方法

`sourceコマンド`は実行権限がなくても実行でき、現在のシェルで実行する。

`./`はシェルスクリプトに実行権をつけてコマンドのように実行する方法。
サブシェル内で実行されるので、現在のシェルには設定は反映されない。

```bash
$ cat test.txt 
X=123
$ bash ./test.txt 
$ echo $X

# サブシェルで実行されたので、現在のシェル変数には何も定義されていない。
$ source ./test.txt
$ echo $X # 現在のシェルで実行されるので、シェル変数に定義されている。
123
```

## whichコマンド

指定されたコマンドの絶対パスを表示する。

```bash
$ which passwd
/usr/bin/passwd
```

## whereisコマンド

指定されたコマンドの絶対パス、ソース、マニュアルを表示する。

```bash
$ whereis passwd
passwd: /usr/bin/passwd /etc/passwd /usr/share/man/man5/passwd.5.gz /usr/share/man/man1/passwd.1.gz
```

## locateコマンド

ファイルやディレクトリを高速で検索するコマンド。

データベースを更新するupdatedbコマンドを使用し、データベース情報を更新させる。

```bash
$ updatedb
$ locate test1
/usr/share/doc/pecl/zip/examples/test1.zip
```

## whatisコマンド

コマンドのマニュアルを検索するコマンド(完全一致)

```bash
$ whatis cp
cp: 適切なものはありませんでした。
$ mandb # manコマンドで表示されるマニュアルindexを更新する
/usr/share/man 配下のマニュアルページを処理しています...
$ whatis cp
cp (1)               - copy files and directories
cp (1p)              - copy files
$ man -f cp
cp (1)               - copy files and directories
cp (1p)              - copy files
```

`apropos`コマンドでマニュアルを部分一致で検索する。

`man -k`でも同じ結果を表示する。

```bash
$ apropos cp
rpm2cpio (8)         - RPM (RPM Package Manager)パッケージか...
chcpu (8)            - configure CPUs
clnttcp_create (3)   - library routines for remote procedure calls
clock (3p)           - report CPU time used
clock_getcpuclockid (3) - obtain ID of a process CPU-time clock
clock_getcpuclockid (3p) - access a process CPU-time clock (ADVANCE...
cp (1)               - copy files and directories
```

## lsattrコマンド

ext2fs上にあるファイル属性を表示する。

```bash
$ lsattr test.txt 
-------------------- test.txt
$ chattr +i test.txt $ # 属性が設定されているファイルは、変更することができない
$ lsattr test.txt 
----i--------------- test.txt
$ rm -rf test.txt 
rm: 'test.txt' を削除できません: Operation not permitted
$ mv test.txt testtest.txt 
mv: 'test.txt' から 'testtest.txt' へ移動できません: Operation not permitted
```
