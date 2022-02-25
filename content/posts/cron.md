---
title: "ジョブスケジューリング"
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

## cronコマンド
 - 指定した時間にジョブを自動実行してくれる機能。
 - ジョブが実行されなくても再実行されない。
 - crondプロセスが常駐してジョブを実行する。
 - ユーザ用の`/var/spool/cron/ユーザ名` は直接編集できない。
 - システム用の`/etc/crontab` はviで編集できる。

```
分 時 日 月 曜日 実行ユーザ名 コマンド
```
※ 曜日については、0と7が日曜日、1=月, 2=火...

----

|表記|説明|
|-|-|
|*|全ての値に一致|
|-|範囲の指定`1-3`は、1時から3時まで|
|,|複数の値の指定`1,2,3`は、1時,2時,3時|
|/|間隔の指定`1-7/2`は、1時から7時までの間で２時間おき|

----

crontabのオプションについては、以下となる

|オプション|説明|
|-|-|
|-e(edit)|設定ファイル(crontabファイル)を編集|
|-l(list)|設定ファイルを表示|
|-r(remove)|設定ファイルを削除|

----
## anacronコマンド
指定したスケジュールにランダムな遅延を挿入してジョブを実行。
指定した時刻で実行できなかった場合、実行できなかったジョブを再実行するコマンドがある。
デーモンプロセスは存在しない。
`/etc/anacrontab` でのみジョブ指定が可能で、rootユーでしか指定できない

```bash
$ cat /etc/anacrontab
# /etc/anacrontab: configuration file for anacron

# See anacron(8) and anacrontab(5) for details.

SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22 # ジョブを実行する時間帯を指定

#period in days   delay in minutes   job-identifier   command
1	5	cron.daily		nice run-parts /etc/cron.daily
7	25	cron.weekly		nice run-parts /etc/cron.weekly
```

## 1度だけ実行するジョブの予約
### atコマンド
特定の時刻での1回だけコマンドを自動実行する。

|オプション|説明|
|-|-|
|-l|予約中のジョブを表示(atqコマンドと同じ)|
|-d ジョブ番号|指定した予約中のジョブを削除(atrmコマンドと同様)|

```bash
haseyuy@mac ~ % at 23:00 # 一度だけ実行するジョブの登録
ls # 実行するコマンドを指定
job 1 at Fri Feb 25 23:00:00 2022
haseyuy@mac ~ % at -l　# 予約中のジョブの確認
1	Fri Feb 25 23:00:00 2022
```

### batchコマンド
システムの負荷が少ないタイミングで指定した時間に1回だけコマンドを自動実行する。

## at, batchコマンドのユーザ制限
1. `/etc/at.allow`ファイルがあれば、記述のあるユーザのみが利用可能
2. `/etc/at.allow`ファイルがなければ、`/etc/at.deny`ファイルを参照し、そこに記述のないユーザが利用可能
3. 両方のファイルがなければ、rootユーザのみ利用可能



