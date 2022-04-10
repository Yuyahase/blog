---
title: "ユーザーへのシステム管理情報の通知"
date: 2022-04-10T20:40:16+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## ログイン前後のメッセージ

ログイン前やログイン後に特定のメッセージを表示させることができる。

ログイン前のメッセージ表示に`/etc/issue`を使い、ログイン後のメッセージ表示に`/etc/motd`を使う。

`/etc/issue`上でシステム情報は以下のように記述されている。

- `\r`: カーネルバージョン
- `\m`: マシンアーキテクチャ
- `\n`: ホスト名
- `\l`: 端末名

```bash
\S
Kernel \r on an \m
```

## ログイン中ユーザーへの通知

`wall`コマンドを使うと、ログイン中のユーザに対し一斉にメッセージを送信できる。

`shutdown`コマンドに`-k`オプションを使うと、シャットダウンせずメッセージだけ伝える。
