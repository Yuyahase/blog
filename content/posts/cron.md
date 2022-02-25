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

## crontabの書式

分 時 日 月 曜日 実行ユーザ名 コマンド

|表記|説明|
|-|-|
|*|全ての値に一致|
|-|範囲の指定`1-3`は、1時から3時まで|
|,|複数の値の指定`1,2,3`は、1時,2時,3時|
|/|間隔の指定`1-7/2`は、1時から7時までの間で２時間おき|

## 1度だけ実行するジョブの予約
### atコマンド

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

## at, batchコマンドのユーザ制限
1. `/etc/at.allow`ファイルがあれば、記述のあるユーザのみが利用可能
2. `/etc/at/allow`ファイルがなければ、`/etc/at.deny`ファイルを参照し、そこに記述のないユーザが利用可能
3. 両方のファイルがなければ、rootユーザのみ利用可能



