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
