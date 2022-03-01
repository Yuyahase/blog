---
title: "User Setting"
date: 2022-03-01T23:01:36+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---
## idコマンド

ユーザのUIDやGIDを表示するコマンド。

```bash
$ id root
uid=0(root) gid=0(root) groups=0(root)
```

## useraddコマンド

ユーザアカウントを作成するコマンド。

useraddコマンドでオプションを指定しないと、デフォルト値が設定される。

```bash
cat /etc/default/useradd 
# useradd defaults file
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
```

|オプション|説明|
|-|-|
|`-c コメント`|コメントを指定|
|`-d ディレクトリ`|ホームディレクトリを指定|
|`-D`|アカウントのデフォルト値を表示または設定|
|`-g グループ名`|プライマリグループを指定|
|`-G グループ名`|プライマリグループ以外に参加する補助グループを指定|
|`-s シェル`|ログインシェルを指定|
|`-p 暗号化済みパスワード`|暗号化済みのパスワードを設定|
|`-L`|パスワードをロックする|
|`-U`|パスワードをアンロックする|

## userdelコマンド

ユーザアカウントを削除するコマンド。

## groupaddコマンド

設定した情報は`/etc/group`ファイルに記述される。

```bash
$ cat /etc/group
root:x:0:
bin:x:1:
daemon:x:2:
sys:x:3:
.
.
.
```

## groupmodコマンド

既存のグループアカウントの設定を変更するコマンド。

## groupdelコマンド

グループアカウントを削除するコマンド。

プライマリグループとしているユーザがいる場合は削除できない。

## /etc/group

グループ関する情報が格納されている。

パスワードは暗号化されているのでxが入る。

グループのメンバーリストには、プライマリグループは含まれないので、所属メンバーがいても記載されない場合がある。

```bash
$ cat /etc/group
# グループ名：グループパスワード：GID：グループのメンバーリスト
root:x:0:
bin:x:1:
daemon:x:2:
sys:x:3:
```

## passwdコマンド

|オプション|説明|
|-|-|
|`l`|パスワードをロックする|
|`-u`|パスワードをアンロックする|
