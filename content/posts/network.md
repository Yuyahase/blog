---
title: "network関連のコマンド"
date: 2022-02-17T11:30:03+00:00
tags: ["network","linux"] 
author: "Me"
canonicalURL: "https://canonical.url/to/page"
xdisableHLJS: true # to disable highlightjs
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
pygmentsCodeFences: true
---

## netstatコマンド

TCPとUDPのサービスポートの状態、UNIXドメインソケットの状態、ルーティング情報などを表示する。

オプションなしで実行した場合は、TCPポートのLISTEN以外のESTABLISHEDなどの状態とUNIXドメインソケットの状態を表示する。

|State|説明|
|-|-|
|LISTEN|リッスン状態のポート|
|ESTABLISHED|TCPコネクションが確立して通信している状態。SYNに対するACKを受信した状態。|

|フィールド名|説明|
|-|-|
|Proto|ソケットが使用するプロトコル|
|Recv-Q|ソケットが接続しているプロセスに渡されなかったデータのバイト数|
|Send-Q|ソケットが接続しているプロセスに渡されなかったデータのバイト数|
|Local Address|ローカル側のIPアドレスとポート番号|
|Foreign Address|リモート側のIPアドレスとポート番号|
|State|ソケットの状態|

```bash
$ netstat
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 localhost.localdoma:ssh 172.16.136.136:51802    ESTABLISHED
tcp        0      0 localhost.localdoma:ssh 172.16.3.60:51528       ESTABLISHED
udp        0      0 localhost.locald:bootpc _gateway:bootps         ESTABLISHED
Active UNIX domain sockets (w/o servers)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  6      [ ]         DGRAM                    11526    /run/systemd/journal/socket
```

## ssコマンド

netstatの後継となるコマンドで、ネットワークソケットの情報を表示するコマンド。

```bash
# TCPポートの情報を表示する
$ ss -ta
State  Recv-Q Send-Q Local Address:Port             Peer Address:Port Process                                                               
LISTEN 0      128          0.0.0.0:ssh                   0.0.0.0:*                                                                          
LISTEN 0      64         127.0.0.1:9915                  0.0.0.0:*                                                                          
LISTEN 0      128        127.0.0.1:35453                 0.0.0.0:* 
```

## ifconfigコマンド

オプションなしだと有効かされている全てのインターフェース情報が表示される。

eth1インターフェースを停止するには`ifconfig eth1 down`もしくは`ifdown eth1`を使用する。

ネットワークインターフェースeth0に、IPアドレス、サブネットマスクを設定し、有効にする。

```bash
$ ifconfig eth0 192.168.120.27 netmask 255.255.255.0
$ ifconfig eth0 up
# まとめることもできる
$ ifconfig eth0 192.168.120.27 netmask 255.255.255.0 up
```

ネットワークインターフェースは、48ビットからなるMACアドレスが割り当てられる。
`ether 32:49:25:f3:49:33`の部分がMACアドレス。

```bash
$ ifconfig
cni-podman1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.30.0.1  netmask 255.255.255.0  broadcast 172.30.0.255
        inet6 fe80::3049:25ff:fef3:4933  prefixlen 64  scopeid 0x20<link>
        ether 32:49:25:f3:49:33  txqueuelen 1000  (Ethernet)
        RX packets 486162  bytes 195131939 (186.0 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 499930  bytes 54428411 (51.9 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

## arpコマンド

TCP/IPを使った通信では、個々のホストのIPアドレスしかわからない。
その為、ARPを使いMACアドレスをIPアドレスに変換する。

流れとしては以下の通り。

1. 「自分のMACアドレス」、「自分のIPアドレス」、「相手のIPアドレス」といった情報が含まれたパケットをブロードキャストする。
2. 指定されたIPアドレスを持つ相手がこのパケットを受け取ると、自分のMACアドレスを返す。
3. IPアドレスからMACアドレスを知ることができる。

一度取得した情報は、`ARPキャッシュ`と呼ばれるテーブルに一定時間キャッシュされる。

ARPキャッシュの内容は、`ip neigh show`コマンドでも確認できる。

主要なオプションは以下の通り

|オプション|説明|
|-|-|
|`-i`|続けて表示対象インターフェースを指定|
|`-a`|続けて表示対象ホスト名を指定|
|`-n`|逆引きを行わず、IPアドレスのまま表示|
|`-d`|続けて削除対象ホスト名を指定|
|`-f`|続けてファイルを指定し、その内容をキャッシュに追加|

## ipコマンド

ネットワークインターフェースやルーティングテーブル、ARPテーブル等の管理をするコマンド。

従来はifconfigやnetstat, routeコマンドだったが、ipコマンドへの移行が進んでいる

|オブジェクト|説明|
|-|-|
|addr|IPv4, IPv6アドレス|
|link|ネットワークデバイス|
|neighbour(neighbor)|IPv4のARPキャッシュ, IPv6のNDキャッシュ|
|route|ルーティングテーブル|

ネットワークインタフェースのupは以下の通り。

```bash
ip link set up dev eth0
```

ネットワークインタフェースのIPアドレスの追加/削除は以下の通り。

```bash
ip addr add 172.16.0.2/16 dev eth0
ip addr del 172.16.0.2/16 dev eth0
```

ルーティングテーブルのエントリ追加は以下の通り。

```bash
# ゲートウェイを172.16.255.254として、宛先ネットワーク172.17.0.0/16のエントリを追加
ip route add 172.17.0.0/16 via 172.16.255.254

# デフォルトルートのエントリを削除
ip route del default
```

## iwコマンド

```bash
# 利用可能なアクセスポイントをスキャン
$ iw dev wlan0 scan
# リンクの状態の確認
$ iw dev wlp1s0 link
# 現在の接続を切断
$ iw dev wlp1s0 disconnect
# ESSID(testid)とkey(testkey)で接続
$ iw dev wlp1s0 connect testid key 0:testkey
# 接続端末の送受信状況をダンプ
$ iw dev wlp1s0 station dump
# モニタインターフェイスを追加
$ iw phy phy0 interface add monwlan0 type monitor
# モニタインターフェイスを削除
$ iw dev monwaln0 del
```

## iwconfigコマンド

無線LANインターフェースについての操作を行うコマンド。

```bash
# ESSIDにwindsorを設定
$ iwconfig wlan0 essid "windsor"

# 無線LANアクセスポイントに接続
# 暗号キーは先頭に「s:」をつける必要がある。
$ iwconfig wlan0 essid "Atmark2" key "s:password"

# 無線LANの接続をアドホックモードに変更
# アドホック:デバイス同士がルータ/アクセスポイントを介さず直接接続
$ iwconfig wlan0 mode Ad-Hoc
```

## iwlistコマンド

無線インターフェースの情報を取得できる。

```bash
$ iwlist wlan0 scan

# 無線LANアクセスポイントの速度をチェック
$ iwlist wlan0 rate
wlan0     4 available bit-rates :
   1 Mb/s
   2 Mb/s
   5.5 Mb/s
   11 Mb/s
   Current Bit Rate:72.2 Mb/s
# アクセスポイントの認証方式を確認
$ iwlist wlan0  auth
wlan0     Authentication capabilities :
    WPA
    WPA2
    CIPHER-TKIP
    CIPHER-CCMP
```

## ncコマンド

- TCPやUDPのエコーサーバを起動し、疎通確認ができるコマンド

```bash
yum -y install epel-release
yum -y install netcat
nc -v
```

## routeコマンド

ルーティングテーブルの表示、追加、削除を行うコマンド。

引数なしで実行すると、ルーティングテーブルが表示される。(`netstat -r`と同じ)

ルーティングテーブルの項目は以下
|項目|説明|
|Destination|宛先ネットワークもしくはホスト|
|Gateway|宛先ネットワークもしくはホスト|
|Genmask|宛先のネットマスク|
|Flags|経路の状態U:経路が有効, H:宛先はホスト, G:ゲートウェイを使用, !:経路は無効|
|Metric|宛先までの距離|
|Ref|ルートの参照数(不使用)|
|Use|経路の参照回数|
|Iface|この経路を使うネットワークインターフェース|

```bash
$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    100    0        0 enp0s3
172.16.0.0      0.0.0.0         255.255.0.0     U     100    0        0 enp0s3
172.30.0.0      0.0.0.0         255.255.255.0   U     0      0        0 cni-podman1
# デフォルトルートの設定
# -hostや-netは付加しない
$ route add default gw 192.168.0.1
# ipコマンドの場合 viw を付加する
$ ip route add default via 192.168.0.1
```

```bash
$ ip route show
default via 172.16.0.1 dev enp0s3 proto dhcp metric 100
172.16.0.0/16 dev enp0s3 proto kernel scope link src 172.16.2.56 metric 100
172.30.0.0/24 dev cni-podman1 proto kernel scope link src 172.30.0.1
```

## 疎通確認コマンド

### tracerouteコマンド

IPパケットが最終的な宛先ホストまでたどりつくまでの経路をトレースして表示するコマンド。

pingコマンドでは宛先から反応がなかった場合、ホスト自身に問題があるかわからない。tracerouteでは、宛先までのルータやホストが順に表示されるため、ネットワーク経路上に障害があった場合はその位置を特定できる可能性がある。

宛先ホストに対して送信パケットのTTLを1,2,3とインクリメントしながらパケット送信を繰り返す。経由したルータの数がTTLの値を超えるとICMPエラーであるTIME_EXCEEDEDを返す。

デフォルト送信プロトコルはUDP。

### tracepathコマンド

宛先までのPathMTU(ネットワーク上で一回で送信できるデータの最大値)が確認できるコマンド。

### pingコマンド

ネットワーク上のホストが起動しているか、IP通信ができるかを確認するコマンド。
指定したホストにICMPパケットを送信する。

IPv6アドレスを指定する場合はping6コマンドを使用する。

|オプション|説明|
|-|-|
|-c 回数|パケットを送信する回数を指定|
|-i 秒|パケットの送信間隔を秒単位で指定|

## 名前解決コマンド

名前解決とはホスト名からIPアドレスを求めること。

ホスト名をIPアドレスに変換することを、正引きと呼ぶ。

IPアドレスをホスト名に変換することを、逆引きと呼ぶ。

DNSサーバやドメイン名の設定は`/etc/resolv.conf`ファイルで設定する。

|主なオプション|説明|
|-|-|
|domain|ローカルドメイン名を指定する。ドメイン名を含まないホスト名を検索する場合、このドメイン内を検索する|
|search|ドメイン名に含まないホスト名を検索する場合の検索するドメイン名を指定する。ドメインは複数指定できる。|
|nameserver|問い合わせするDNSサーバのIPアドレスを指定する。通常は最大3台まで指定できる。|

`/etc/hosts`ファイルとは、ホスト名とIPアドレスを対応させたファイル。

DNSが普及する前は、`/etc/hosts`ファイルによって名前解決が行われていた。

ほとんどのディストリビューションではDNSよりも`/etc/hosts`を優先して名前解決を行う。

### nslookup

### digコマンド

- DNSサーバに問い合わせて、指定した名前に関するDNSサーバからの応答を表示する
- 一時的に外部のDNSサーバを使用したい場合は、`@DNSサーバ`を指定する

|検索タイプ|説明|
|-|-|
|a|IPアドレス|
|any|全ての情報|
|mx|メールサーバの情報|
|ns|ネームサーバの情報|

```bash
$ dig www.google.com

; <<>> DiG 9.11.26-RedHat-9.11.26-6.el8 <<>> www.google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 63869
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;www.google.com.   IN A

;; ANSWER SECTION:
www.google.com.  215 IN A 142.250.207.4

;; Query time: 7 msec
;; SERVER: 203.141.128.33#53(203.141.128.33)
;; WHEN: 土  2月 19 02:15:29 JST 2022
;; MSG SIZE  rcvd: 59
```

## nmcli

NetworkManagerの制御を行うコマンドラインツール。

ちなみにnmtuiは、テキストユーザインターフェース(TUI)となる。

nmcliの第一引数には、操作対象となるgeneral, device, connection, networkingなどを指定する。操作対象はオブジェクトと呼ばれる。

主なオブジェクトは以下。

|オブジェクト|説明|
|general|NetworkManagerの状態表示および管理|
|device|デバイスの表示と管理|
|connection|接続の管理|
|networking|ネットワークの状態表示、有効/無効設定|

networking connectivityコマンドにより、ネットワークの接続状態を表示できる。

```bash
$ nmcli networking connectivity
full
```

|状態|説明|
|none|どのネットワークにも接続してない|
|portal|認証前により、インターネットに到達できない|
|limited|ネットワークに接続しているが、インターネットへアクセスできない|
|full|ネットワークに接続しており、インターネットにアクセスできる|
|unknown|ネットワークの接続が確認できない|

### tcmpdumpコマンド

指定したネットワークインターフェースを監視し、そこに到達したデータをコンソール上に表示する。

暗号化されていないパスワードなどは平文で流されるため、tcpdumpでネットワークを監視されていると、アカウント情報が盗まれる可能性がある。

tcpdumpを実行すると、ネットワークデバイスはプロミスキャスモード(自分宛以外のパケットも受け取る状態)で動作する。

|オプション|説明|
|-|-|
|`-i インターフェース`|監視するインターフェースを指定する|
|`-s バイト数`|パケットから取り出すバイト数を指定する|
|`-X`|16進数とASCII文字を表示する|
|`-n`|アドレスを名前解決せずに表示する|
|`port`|ポート番号を指定|
|`tcp`|TCPのパケットを対象とする|
|`udp`|UDPのパケットを対象とする|
|`icmp`|ICMPのパケットを対象とする|
|`arp`|ARPのパケットを対象とする|
|`src`|指定した送信元からのパケットを対象とする|
|`dst`|指定した送信先へのパケットを対象とする|

キャプチャに使用できるネットワークインターフェイスの確認は`-D`オプションを使う。

```bash
$ tcpdump -D
1.cni-podman1 [Up, Running]
2.enp0s3 [Up, Running]
3.vethba7fae8e [Up, Running]
4.lo [Up, Running, Loopback]
5.any (Pseudo-device that captures on all interfaces) [Up, Running]
6.bluetooth-monitor (Bluetooth Linux Monitor) [none]
7.nflog (Linux netfilter log (NFLOG) interface) [none]
8.nfqueue (Linux netfilter queue (NFQUEUE) interface) [none]
9.usbmon0 (Raw USB traffic, all USB buses) [none]
10.usbmon1 (Raw USB traffic, bus number 1)
```

## nmap

詳細が不明なネットワーク構造をスキャンできる。

スキャンタイプは以下がある。

|スキャンタイプ|説明|
|-|-|
|-sT|TCPスキャン|
|-sU|UDPスキャン|
|-sP|Pingスキャン|
|-p|続けて対象ポート範囲を指定|
|-F|有名ポートを対象にした高速スキャン|
|-O|対象ホストのOS識別を試みる|

## bondingモジュール

bondingとは複数のネットワークインタフェースを一つに束ねる技術。

Linuxではbondingモジュールを読み込むことでbondingが設定可能となる。

|bondingポリシー|説明|
|`balance-rr`|ラウンドロビンによる送信の負荷分散|
|`active-backup`|一つのスレーブインターフェースのみがアクティブとなり、障害発生時にのみ他のスレーブに切り替わる|
|`balance-tlb`|ステーブインターフェースの負荷に応じた送信の負荷分散|
|`balance-alb`|ステーブインターフェースの負荷に応じた送受信の負荷分散|
|`802.3ad`|LACPを使用したリンクアグリゲーション|

bondingではネットワークインタフェースを以下のように分類している。

- マスターインタフェース:bondingで束ねられた仮想インタフェース

- スレーブインタフェース:bondingを構成する個々の物理インタフェース

- アクティブインタフェース:bondingの中で実際にパケットの送受信を行うインタフェース
