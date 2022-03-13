---
title: "時刻設定"
date: 2022-02-26T19:27:22+09:00
draft: false
disableHLJS: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

Linuxの動作するシステムで扱う時刻には、ハードウェアクロックとシステムクロックがある。

## ハードウェアクロックとシステムクロック

|種別|コマンド|内容|
|-|-|-|
|ハードウェアクロック|hwclock|コンピュータに内蔵された時刻。電源Offの状態でも動作|
|システムクロック|date|Linuxカーネルが持つ時刻。システム起動時にハードウェアクロックの値が設定される。|

## chronycコマンド

chronydの管理を行うコマンド。

```bash
$ chronyc sources # 時刻のソースを表示
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^* four10.gac.edu                2   6    17    24  +2143us[+2783us] +/-   92ms
^+ ntp.speculation.org           2   6    17    24  -1537us[ -896us] +/-   85ms
^- ntp.rack66.net                2   6    17    23  -9550us[-9550us] +/-  160ms
^+ ntp.univ-angers.fr            2   6    17    24  -9839us[-9839us] +/-  170ms
```

|項目|説明|
|-|-|
|`MS`|`M`:ソースのモード(`^`はserver、`=`はpeer) `S`:ソースの状態(`*`は同期している、`+`は同期可能)|
|`Stratum`|ソースのストレイタム。原始時計やGPSなどの正確な時計|
|`Poll`|ソースに問い合わせるポーリング間隔|
|`Reach`|過去8回分の問い合わせ結果を8進数で表示。全て成功で377|
|`LastRx`|ソースから最後のサンプルを受信した時期|
|`Last sample`|同期した時刻とのずれ表示|

## hwclockコマンド

ハードウェアクロックを表示、設定するコマンド。

|オプション|説明|
|-|-|
|`-r`|ハードウェアクロックを表示|
|`-s` \| --hctosys|ハードウェアクロックの時刻をシステムクロックに反映|
|`-w`\| --systhohc |システムクロックの時刻をハードウェアクロックに反映|

```bash
$ hwclock -r
2022-02-26 21:11:04.037913+09:00
```

## dateコマンド

システムクロックの表示や設定を行うコマンド。

date [MMDDhhmm[CC[YY]][.ss]]

## timedatectlコマンド

systemdの動作するシステムにおいてシステム時刻の表示や設定を行うコマンド。

|サブコマンド|説明|
|-|-|
|`status`|ハードウェアクロックを表示|
|`set-ntp yes \| no`|NTPによる時刻同期の有効 \| 無効|
|`set-timezone タイムゾーン`|タイムゾーンの設定|
|`list-timezones`|タイムゾーンの一覧表示|

```bash
$ timedatectl
               Local time: 土 2022-02-26 22:04:42 JST
           Universal time: 土 2022-02-26 13:04:42 UTC
                 RTC time: 土 2022-02-26 13:04:48
                Time zone: Asia/Tokyo (JST, +0900)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

```bash
$ timedatectl list-timezones | head
Africa/Abidjan
Africa/Accra
Africa/Addis_Ababa
Africa/Algiers
Africa/Asmara
Africa/Bamako
Africa/Bangui
Africa/Banjul
Africa/Bissau
Africa/Blantyre
```

## ntpdateコマンド

手動でNTPサーバから正確な時刻を取得し、システムクロックに反映させるコマンド。

NTPサーバとシステム時計との差分を確認するには、`-q`オプションをつける。問い合わせのみで、時計の設定は行わない。
