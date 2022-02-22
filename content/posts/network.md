---
title: "network関連のコマンド"
date: 2022-02-17T11:30:03+00:00
tags: ["hugo"] 
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
 - 有効なネットワーク接続や開いているソケットの情報等を表示する

|State|説明|
|-|-|
|LISTEN|リッスン状態のポート|
|ESTABLISHED|TCPコネクションが確立して通信している状態。SYNに対するACKを受信した状態。|


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
 - netstatの後継となるコマンドで、ネットワークソケットの情報を表示する

## ipconfigコマンド
 - オプションなしだと有効かされている全てのインターフェース情報が表示される
 - eth1インターフェースを停止するには`ifconfig eth1 down`もしくは`ifdown eth1`を使用する

## ipコマンド
 - 従来はipconfigやnetstat, routeコマンドだったが、ipコマンドへの移行が進んでいる

|オブジェクト|説明|
|-|-|
|addr|IPv4, IPv6アドレス|
|link|ネットワークデバイス|
|neighbour(neighbor)|IPv4のARPキャッシュ, IPv6のNDキャッシュ|
|route|ルーティングテーブル|

## ncコマンド
 - TCPやUDPのエコーサーバを起動し、疎通確認ができるコマンド

```bash
$ yum -y install epel-release
$ yum -y install netcat
$ nc -v
```

## routeコマンド
 - ルーティングテーブルの表示、追加、削除を行うコマンド

```bash
$ ip route show
default via 172.16.0.1 dev enp0s3 proto dhcp metric 100
172.16.0.0/16 dev enp0s3 proto kernel scope link src 172.16.2.56 metric 100
172.30.0.0/24 dev cni-podman1 proto kernel scope link src 172.30.0.1
```
# 疎通確認コマンド

## tracerouteコマンド
 - リモートホストまでの通信経路を表示する

## tracepathコマンド
 - 宛先までのPathMTU(ネットワーク上で一回で送信できるデータの最大値)が確認できる

## pingコマンド
 - ネットワーク上のホストが起動しているか、IP通信ができるかを確認するコマンド
 - 指定したホストにICMPパケットを送信する

|オプション|説明|
|-|-|
|-c 回数|パケットを送信する回数を指定|
|-i 秒|パケットの送信間隔を秒単位で指定|


# 名前解決コマンド
 - 名前解決とはホスト名からIPアドレスを求めること
 - DNSサーバやドメイン名の設定は`/etc/resolv.conf`ファイルで設定する
## nslookup
## digコマンド
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
;www.google.com.			IN	A

;; ANSWER SECTION:
www.google.com.		215	IN	A	142.250.207.4

;; Query time: 7 msec
;; SERVER: 203.141.128.33#53(203.141.128.33)
;; WHEN: 土  2月 19 02:15:29 JST 2022
;; MSG SIZE  rcvd: 59
```



