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

ユーザアカウントを作成する。また`/etc/passwd`と`/etc/shadow`へのエントリ作成とホームディレクトリを作成する。

useraddコマンドでオプションを指定しないと、デフォルト値が設定される。

`/etc/skel`ディレクトリの下に置かれているファイルあるいはディレクトリは、
`useradd`コマンドでユーザを作成したときに自動的にユーザのホームディレクトリに
配られる。

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

`/etc/passwd`の書式については以下の通り。

```bash
$ cat /etc/passwd
# 7つのフィールドから構成される
# ログイン名:xまたは暗号化パスワード:UID:GID:コメント:ホームディレクトリ:ログインシェル
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
```

## usermodコマンド

既存ユーザのアカウント情報を変更するコマンド。

|オプション|説明|
|-|-|
|`-d ディレクトリ`|ホームディレクトリを指定|
|`-g グループ名|GID`|プライマリグループを指定|
|`-G グループ名|GID`|補助グループを指定|
|`-s シェル`|ログインシェルを指定|
|`-L`|パスワードをロックする|
|`-U`|パスワードをアンロックする|
|`-p 暗号化済みのパスワード`|暗号化済みのパスワードを設定すr|

アカウント失効日の設定、変更をするには`usermod -e`もしくは`chage -E`を実行する。

```bash
$ grep sample /etc/shadow
sample:$6$Zed3e01r6Bvjpta0$eMGL6vx8CTBRTHYsDCS2VlsNHxxZWEB9deJWyNhx6RNJjqJaRPTNftAaKlj2Bqt.CkWk/siAmtA8e7Trp9hB11:19063:0:99999:7:::
$ usermod -e 2019-12-31 sample # アカウント失効日を指定
$ grep sample /etc/shadow
sample:$6$Zed3e01r6Bvjpta0$eMGL6vx8CTBRTHYsDCS2VlsNHxxZWEB9deJWyNhx6RNJjqJaRPTNftAaKlj2Bqt.CkWk/siAmtA8e7Trp9hB11:19063:0:99999:7::18261:
# 1970年1月1日の18261日後が2019-12-31となる
```

パスワードの有効期限を調べるいは`chage -l ユーザ名`を実行する。

```bash
$ chage -l sample
最終パスワード変更日                            : 3月 12, 2022
パスワード期限:                                 : なし
パスワード無効化中                                      : なし
アカウント期限切れ                                              : 12月 31, 2019
パスワードが変更できるまでの最短日数            : 0
パスワードを変更しなくてよい最長日数            : 99999
パスワード期限が切れる前に警告される日数                : 7
```

ログインシェルに`/bin/false`を指定することで、対話的なログインを禁止することができる。

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

|オプション|説明|
|-|-|
|`-g GID`|GIDを変更|
|`-n グループ名`|グループ名を変更|

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

ユーザのパスワードを設定、変更するコマンド。

|オプション|説明|
|-|-|
|`l`|パスワードをロックする|
|`-u`|パスワードをアンロックする|

`/etc/shadow`の２番目のフィールドが`!!`となっている場合、
アカウントを作成したがパスワード設定はされていないことを意味する。

## getentコマンド

`/etc/nsswitch.conf`は、名前解決やサービス名解決の際の問い合わせ順序を指定するファイルで、`getent`コマンドはそれらの情報を問い合わせる。

## パスワードとアカウントの有効期限変更コマンド

|コマンド|maxdays(パスワード変更なしで有効な最長日数)|inactive(パスワード失効までの猶予日数)|expiredate(アカウントの失効日)|
|-|-|-|-|
|useradd|-|useradd -D -f, useradd -f|useradd -D -e, useradd -e|
|usemod|-|usermod -f|usermod -e|
|chage|chage -M|chage -l|chage -E|
|passwd|passwd -x|passwd -i|-|
