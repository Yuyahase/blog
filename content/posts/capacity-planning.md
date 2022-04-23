---
title: "Capacity Planning"
date: 2022-03-24T19:28:50+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## キャパシティプランニング

コンピュータのシステムリソース(CPU, メモリ, ディスク, ネットワーク帯)が将来的に不足しないようにするための設計技法。

## 総合的なリソース使用率の測定

CPU使用率やメモリ状況など、システムリソースを総合的に確認できるツールには、topコマンドやvmstatコマンドがある。

### topコマンド

`topコマンド`を使うと、システムリソースの使用状況やプロセスの実行状態などを継続的に監視することができる。

`top - 19:35:04 up 13 days,  8:25,  2 users,  load average: 0.03, 0.04, 0.01` の部分について

- 19:35:04：現在の時刻
- up 13 days, 8:25：システムが起動してからの経過時間
- 2users：ログイン中のユーザ数
- load average: 0.03, 0.04, 0.01：1分, 5分, 15分間の平均負荷(この数値が搭載されているCPU数,もしくはコア数)を超えているなら、何らかの処理待ちが発生していると考えられる。

`%Cpu(s):  0.0 us,  0.7 sy,  0.0 ni, 94.6 id,  0.0 wa,  4.1 hi,  0.7 si,  0.0 st`の部分について

- us：ユーザープロセスがCPUを使用している時間の割合
- sy：カーネルがCPUを使用している時間の割合
- ni：優先度が変更されたプロセスがCPUを使用している時間の割合
- id：CPUがアイドル状態の時間の割合
- wa：ディスクI/O待ちの時間の割合
- hi：ハードウェア割り込み要求時間の割合
- si：ソフトウェア割り込み要求時間の割合
- st：仮想環境において、ゲストOSが要求したにも関わらずCPUリソースが割り当てられず、待機させられた時間の割合

`MiB Mem :   8272.8 total,   4900.3 free,    729.1 used,   2643.4 buff/cache`

- 全物理メモリ量
- 空きメモリ量
- 使用メモリ量
- バッファ及びキャッシュサイズ

`MiB Swap:   4300.0 total,   4300.0 free,      0.0 used.   7082.8 avail Mem`

- スワップ領域のサイズ
- 空きスワップ領域
- 使用中のスワップ領域
- メモリ不足時に利用可能な物理メモリ量

```bash
$ top - 19:35:04 up 13 days,  8:25,  2 users,  load average: 0.03, 0.04, 0.01
Tasks: 206 total,   1 running, 205 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.7 sy,  0.0 ni, 94.6 id,  0.0 wa,  4.1 hi,  0.7 si,  0.0 st
MiB Mem :   8272.8 total,   4900.3 free,    729.1 used,   2643.4 buff/cache
MiB Swap:   4300.0 total,   4300.0 free,      0.0 used.   7082.8 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                            
 271115 root      20   0       0      0      0 I   0.7   0.0   0:02.07 kworker/0:2-events                 
      9 root      20   0       0      0      0 S   0.3   0.0   3:24.38 ksoftirqd/0                        
 168030 root      20   0 1354008  64032  30576 S   0.3   0.8   3:02.62 node                               
 168755 root      20   0   21.0g 125428  40296 S   0.3   1.5   1:57.93 node                               
 267404 root      20   0  456288  29588  20952 S   0.3   0.3   0:00.47 httpd                              
 268276 apache    20   0  478984  22536  13660 S   0.3   0.3   0:01.58 httpd    
```

### vmstatコマンド

メモリおよび仮想メモリの詳細情報を継続的に監視するコマンド。

最初の表示は起動時からの統計情報。

|表示項目|列|説明|
|procs|r|実行待ちプロセス数|
|procs|b|割り込み不可能なスリープ状態にあるプロセス数|
|memory|swpd|スワップサイズ(KB)|
|memory|free|空きメモリサイズ(KB)|
|memory|buff|バッファに割り当てられているメモリサイズ(KB)|
|memory|cache|キャッシュに割り当てられているメモリサイズ(KB)|
|swap|si|ディスクへスワップインされているメモリサイズ(KB/秒)|
|swap|so|ディスクへスワップアウトされているメモリサイズ(KB/秒)|
|io|bi|ブロックデバイスから受け取ったブロック数(ブロック/秒)|
|io|bo|ブロックデバイスに送られたブロック数(ブロック/秒)|
|system|in|1秒あたりの割り込み回数(クロック割り込みを含む)|
|system|cs|1秒あたりのコンテキストスイッチ数|
|cpu|us|ユーザープロセスがCPUを使用している時間の割合|
|cpu|sy|カーネルCPUを使用している時間の割合|
|cpu|id|CPUがアイドル状態の時間の割合|
|cpu|wa|ディスクI/O待ちの時間の割合|
|cpu|st|ゲストOSがCPUに割り当てられなかった時間の割合|

```bash
# 表示間隔5秒で、4回表示
$ vmstat 5 4
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 3  0      0 5992320   4228 1503920    0    0     2     5   54   28  0  7 93  0  0
```

### sarコマンド

様々なシステム統計情報のレポートを得ることができる。

システム状況を収集するsadcコマンドと、収集データを表示するsarコマンド等から構成される。

定期的な実行を導入できる`/usr/lib/sa/sa1`,`/usr/lib/sa/sa2`というスクリプトが用意されており、`sa1`は`sadc`の定期実行を、`sa2`は`sar`の定期実行を設定できる。

|オプション|説明|
|-|-|
|`-A`|全ての項目を表示する|
|`-b`|ディスクの入出力と転送レート情報を表示する|
|`-c`|プロセスの生成回数を表示する|
|`-f`|ログファイルを指定する|
|`-n DEV`|ネットワーク関連の情報を表示する|
|`-n EDEV`|ネットワーク関連のエラー情報を表示する|
|`-r`|メモリとスワップ関連の情報を表示する|
|`-P 番号|ALL`|指定したCPUの使用率を表示|
|`-u`|CPU関連情報を表示する|

ディスク関連情報を出力するコマンドは以下。

```bash
$ sar -b -f /var/log/sa/sa05 
Linux 4.18.0-348.2.1.el8_5.x86_64 (localhost.localdomain)   2022年04月05日  _x86_64_        (1 CPU)

16:13:39     LINUX RESTART      (1 CPU)
# tps: I/O転送リクエスト数/秒(rtpsとwtpsの合計)
# rtps: ディスク読み込みリクエスト数/秒
# wtps: ディスク書き込みリクエスト数/秒
# bread/s: ディスク読み込みブロック数/秒(blocks read per second)
# bwrtn/s: ディスク書き込みブロック数/秒(blocks write per second)
16時20分05秒       tps      rtps      wtps   bread/s   bwrtn/s
16時30分06秒      0.41      0.00      0.41      0.05      9.43
平均値:       0.41      0.00      0.41      0.05      9.43
```

ネットワークインターフェース情報情報を出力するコマンドは以下。

```bash
$ sar -n DEV -f /var/log/sa/sa05 
Linux 4.18.0-348.2.1.el8_5.x86_64 (localhost.localdomain)       2022年04月05日      _x86_64_        (1 CPU)

16:13:39     LINUX RESTART      (1 CPU)

16時20分05秒     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
16時30分06秒        lo      5.71      5.71      0.43      0.43      0.00      0.00      0.00      0.00
16時30分06秒    enp0s3      8.44      2.55      0.79      0.33      0.00      0.00      0.00      0.00
16時30分06秒 cni-podman1      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
16時30分06秒 veth8d94e52f      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
16時40分07秒        lo      5.68      5.68      0.43      0.43      0.00      0.00      0.00      0.00
16時40分07秒    enp0s3      8.16      2.42      0.62      0.30      0.00      0.00      0.00      0.00
16時40分07秒 cni-podman1      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
16時40分07秒 veth8d94e52f      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
16時50分06秒        lo      5.66      5.66      0.43      0.43      0.00      0.00      0.00      0.00
16時50分06秒    enp0s3      7.83      2.35      0.59      0.29      0.00      0.00      0.00      0.00
16時50分06秒 cni-podman1      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
16時50分06秒 veth8d94e52f      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00

平均値:      IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
平均値:         lo      5.68      5.68      0.43      0.43      0.00      0.00      0.00      0.00
平均値:     enp0s3      8.14      2.44      0.67      0.31      0.00      0.00      0.00      0.00
平均値:  cni-podman1      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
平均値:  veth8d94e52f      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
```

### sadfコマンド

sadcによるログを、タブ区切りテキスト(TSV)やXML形式で出力することができる。

### uptimeコマンド

uptimeコマンドを実行すると、システムの稼働時間やログインユーザ数、平均負荷が表示される。

```bash
$ uptime
 16:28:44 up 6 days, 20:46,  3 users,  load average: 0.26, 0.10, 0.03
```

### wコマンド

### psコマンド

psコマンドは稼働中のプロセスおよび、プロセス毎のCPUやメモリの使用量を表示する。

```bash
$ ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.0 225536  8684 ?        Ss   08:56   0:05 /sbin/init splash
root          2  0.0  0.0      0     0 ?        S    08:56   0:00 [kthreadd]
root          4  0.0  0.0      0     0 ?        I<   08:56   0:00 [kworker/0:0H]
root          6  0.0  0.0      0     0 ?        I<   08:56   0:00 [mm_percpu_wq]

# -e: 親プロセス(PPID)を表示する
$ ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 08:56 ?        00:00:05 /sbin/init splash
root          2      0  0 08:56 ?        00:00:00 [kthreadd]
root          4      2  0 08:56 ?        00:00:00 [kworker/0:0H]
root          6      2  0 08:56 ?        00:00:00 [mm_percpu_wq]

# -f: 階層構造表示
$ ps auxf
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          2  0.0  0.0      0     0 ?        S    08:56   0:00 [kthreadd]
root          4  0.0  0.0      0     0 ?        I<   08:56   0:00  \_ [kworker/0:0H]
root          6  0.0  0.0      0     0 ?        I<   08:56   0:00  \_ [mm_percpu_wq]
root          7  0.0  0.0      0     0 ?        S    08:56   0:01  \_ [ksoftirqd/0]
root          8  0.0  0.0      0     0 ?        I    08:56   0:08  \_ [rcu_sched]
root          9  0.0  0.0      0     0 ?        I    08:56   0:00  \_ [rcu_bh]
root         10  0.0  0.0      0     0 ?        S    08:56   0:00  \_ [migration/0]
root         11  0.0  0.0      0     0 ?        S    08:56   0:00  \_ [watchdog/0]
```

### netstatコマンド

### mpstatコマンド

マルチプロセッサシステムのCPUの使用状況を表示する。

`-P`オプションでCPUを番号で指定でき、`ALL`を指定すると全てのCPUを表示する。

## リソース監視ツール

### collectd

Cactiツールの方が上位互換

設定ファイル:`/etc/collectd.conf`

### MRTG

ネットワークやリソースの使用状況を監視し、グラフ化する。

### Cacti

MRTGより設定が楽。

ネットワークやリソースの使用状況を監視し、グラフ化する。

### Ichinga2

Nagios互換の監視ツール。

WebAPIを使って監視情報のやり取りを行う。

オブジェクト管理を主体とした監視を行う。
