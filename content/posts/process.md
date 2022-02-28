---
title: "Process"
date: 2022-02-28T01:22:31+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## システムでアクティブなプロセスを全て表示するコマンド

```bash
ps -ef | head #-e : 全てのプロセスを表示する, -f : 詳細情報を表示する
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0  2月14 ?      00:01:59 /usr/lib/systemd/systemd --switched-root --system --deserialize 17
root           2       0  0  2月14 ?      00:00:01 [kthreadd]
root           3       2  0  2月14 ?      00:00:00 [rcu_gp]
root           4       2  0  2月14 ?      00:00:00 [rcu_par_gp]
root           6       2  0  2月14 ?      00:00:00 [kworker/0:0H-events_highpri]
root           8       2  0  2月14 ?      00:00:00 [mm_percpu_wq]
root           9       2  0  2月14 ?      00:01:34 [ksoftirqd/0]
root          10       2  0  2月14 ?      00:00:13 [rcu_sched]
root          11       2  0  2月14 ?      00:00:00 [migration/0]
```

```bash
ps ax | head # a:全てのプロセスを表示する, x:制御端末のないプロセスも表示する
    PID TTY      STAT   TIME COMMAND
      1 ?        Ss     1:59 /usr/lib/systemd/systemd --switched-root --system --deserialize 17
      2 ?        S      0:01 [kthreadd]
      3 ?        I<     0:00 [rcu_gp]
      4 ?        I<     0:00 [rcu_par_gp]
      6 ?        I<     0:00 [kworker/0:0H-events_highpri]
      8 ?        I<     0:00 [mm_percpu_wq]
      9 ?        S      1:34 [ksoftirqd/0]
     10 ?        R      0:13 [rcu_sched]
     11 ?        S      0:00 [migration/0]
```

## プロセスの優先度について

ある時点でCPUが実行するプロセスは一つだが、実際にはタイムスライスと呼ばれるごく短い時間でプロセスを切り替えて、そのたびに別のプロセスをCPUに割り当てて実行している。

### niceコマンド

nice値を付与するコマンド。

nice値は-20~19まで変更可能。

優先度にマイナスの値を指定可能なのは、root権限の持つユーザのみ。

オプションを指定しないと10を付与する。

```bash
nice -n 19 bc
```

### reniceコマンド

動作中のプロセスの優先度を変更するコマンド。

変更対象のプロセスを明示するため、-pオプションでPIDを指定する。

## ジョブ

コマンドライン1行で実行された処理単位のこと。
1行のコマンドラインで複数のコマンドが実行された場合でも、その処理全体を一つのジョブとして扱う。

ジョブには以下の2種類がある。

|ジョブ|説明|
|-|-|
|フォアグラウンドジョブ|キーボードと画面を占有して対話的な操作をするジョブ|
|バックグラウンドジョブ|キーボード入力を受け取ることができないジョブ|

`$`をつけて実行するとバックグラウンドジョブとして実行される。

実行中のジョブを表示するには `jobs` コマンドを使用する。

ログアウトしてもプログラムを実行するには `nohup` コマンドを使用する。

実行しているジョブを一時停止するには`Ctrl + z`

## シグナル

割り込みによってプロセスに特定の動作をするように通知するための仕組み。

|シグナル番号|シグナル名|説明|
|-|-|-|
|1|SIGHUP|端末の切断によるプロセス終了|
|2|SIGINT|割り込みによるプロセス終了(Ctrl + C)|
|9|SIGKILL|プロセスの強制終了|
|15|SIGTERM|プロセスの終了|
|18|SIGCONT|一時停止したプロセスを再開|

## killコマンド

明示的にシグナルを指定しない場合のデフォルトのシグナルはSIGTERM(15)。

ジョブIDを指定するには`%`を使用する。

## killallコマンド

同じ名前のプロセスが複数存在し、まとめて終了したい場合に有効なコマンド。
