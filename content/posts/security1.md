---
title: "security関連のコマンド"
date: 2022-02-22T11:30:03+00:00
tags: ["linux"] 
author: "Me"
canonicalURL: "https://canonical.url/to/page"
xdisableHLJS: true # to disable highlightjs
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

### ulimitコマンド
ユーザやシェルが利用できるリソースを制限するコマンド

|オプション|説明|
|-|-|
|-a|リソースの制限値を全て表示|
|-c|コアファイルのサイズを制限|
|-f|出力ファイルのサイズを制限|
|-u|ユーザ一人が起動できるプロセス数を制限|
|-v|シェルが利用できる仮想メモリを制限|

```bash
$ ulimit -a
-t: cpu time (seconds)              unlimited
-f: file size (blocks)              unlimited
-d: data seg size (kbytes)          unlimited
-s: stack size (kbytes)             8176
-c: core file size (blocks)         0
-v: address space (kbytes)          unlimited
-l: locked-in-memory size (kbytes)  unlimited
-u: processes                       2666
-n: file descriptors                256
```
### fuserコマンド
システムの特定リソースを使用中のプロセスを検索し、強制終了ができるコマンド

|オプション|説明|
|-|-|
|-k|プロセスにSIGKILLを送信|
|-m|マウントされたファイルシステムを指定|
|-n|検索する領域(名前空間を指定)|
|-u|検索結果にユーザ名を追加|
|-v|詳細な情報を表示|

```bash
$ haseyuy@mac ~ % fuser -u /bin/bash
/bin/bash: 9048(test)
```

### lastコマンド
`var/log/wtmp`ファイルを参照し、最近ログインしたユーザの一覧を表示するコマンド

### lsofコマンド
特定のポートを使用しているプロセスなど、ローカルホストの様々な情報を表示する

```bash
$ lsof -i:22
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd      740 root    4u  IPv4  21696      0t0  TCP *:ssh (LISTEN)
sshd      740 root    6u  IPv6  21698      0t0  TCP *:ssh (LISTEN)
```

### chageコマンド
パスワードの有効期限の設定に特化したコマンド
パスワードの有効期限に関する情報は`/etc/shadow`ファイルに保存される

```bash
# パスワードの有効期限に関する情報を表示
$ chage -l root
最終パスワード変更日				:なし
パスワード期限:					: なし
パスワード無効化中					: なし
アカウント期限切れ						: なし
パスワードが変更できるまでの最短日数		: 0
パスワードを変更しなくてよい最長日数		: 99999
パスワード期限が切れる前に警告される日数		: 7
```

### suコマンド
su(Switch User)コマンドを使うと、ログイン中に別のユーザに切り替わることができる

- 引数に`-`をつけると、切り替わる先のユーザの環境変数を使用する
- ユーザ名を指定しない場合、rootユーザへ切り替わる
- ユーザを切り替える時に求められるパスワードは、切り替わる先のユーザのパスワード
- rootユーザから別のユーザに切り替わる時はパスワードを求められない

### sudoコマンド
管理者から実行権限を委譲するコマンド
`visudo`コマンドで設定ファイルは`/etc/sudoers`を編集する

### gpgコマンド
GnuPGという暗号化プログラムを使用して、ファイルの暗号化や復号を行う
`~/.gnupg/`というディレクトリに、公開暗号鍵方式の秘密鍵と公開鍵のペアが格納される

### sshkeygenコマンド

### スーパーサーバについて
登録された利用頻度の低いサービスを監視し、リクエストがきた場合にだけサービスを実行する。
TCPラッパーと組み合わせることで、アクセス制御ができる。

スーパーサーバには`inetd`と`xinetd`がある
`inetd`については、セキュリティ問題や柔軟性のなさから、ほとんど利用されていない。

#### inetdの設定
`/etc/inetd.conf`が設定ファイル

#### xinetdの設定
`/etc/xinetd.conf`が設定ファイル
`/etc/xinetd.d`ディレクトリが各サービスの設定ファイル

#### TCPラッパー(tcpd)の制御ファイルの評価順
xinetdが監視しているサービスにアクセスがあると、tcpdにその制御を渡す。許可する場合のみ、対象のサービスを起動して制御する。

1. `/etc/hosts.allow`に記載されているホストは許可
2. `/etc/hosts.deny`に記載さいれているホストは拒否
3. 両方のファイルの記載の無いホストは全て許可
