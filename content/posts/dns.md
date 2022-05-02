---
title: "DNS"
date: 2022-05-02T13:33:32+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## rndc(remote name daemon control)

namedの設定や管理を、ローカルやリモートから行えるコマンド。

主なサブコマンドは以下の通り。

|サブコマンド|説明|
|`status`|namedの状態を表示|
|`reload`|サーバの設定を再読み込みする|
|`halt`|namedを直ちに停止|
|`stop`|動的な更新等を保持してからnamedを停止|

## BINDの設定ファイル

設定ファイルの場所は`/etc/named.conf`

構文チェックコマンドは`named-checkconfig`コマンド。

ログファイルの場所は以下。

- (RedHat)/var/log/messages
- (Debian)/var/log/syslog

主なステートメントは以下。

|ステートメント|説明|
|-|-|
|`acl`|アクセス制御の対象とするIPアドレスやネットワークアドレスのリストを任意の名前で定義|
|`controls`|namedを操作するrndcコマンドの使用を許可するホストを指定|
|`include`|指定された外部ファイルを読み込み、その内容を/etc/named.confに追加|
|`zone`|ゾーン(管理するドメインの範囲)を定義|

optionsステートメントで設定する主なオプションは以下。

|オプション|説明|
|-|-|
|`directory`|namedの作業ディレクトリ|
|`recursion[yes|no]`|再帰的問い合わせを受け付けるかの設定|
|`fowarders`|自信が保持しないドメイン情報の問い合わせを転送するDNSサーバアドレスを指定|
|`foward[first|only]`|問い合わせ転送の失敗時の動作を設定|
|`allow-query`|問い合わせを受け付けるホストを指定|
|`allow-recursion`|再帰的問い合わせを受け付けるホストを指定|
|`allow-transfer`|ゾーン転送を許可するホストを指定|
|`blackhole`|問い合わせを受け付けないホストを指定|
|`version`|BINDのバージョンの問い合わせに対し、出力される文字列を設定|

## DNSサーバの種類

- djbdns : DNSキャッシュサーバとコンテンツサーバの機能に分かれているDNSサーバ。

- PowerDNS : RDBなどをバックエンドデータベースとして使用できるDNSサーバ。

- BIND : Linuxで標準的に使用されているDNSサーバ。

## ゾーンファイルの管理

DNSサーバは複数のゾーンを管理することができる。

ゾーンに関する情報は、ゾーンごとにゾーンファイルに記述する。

ゾーンファイルには以下の種類がある。

|種類|説明|
|-|-|
|`hint 情報ファイル`|ルートDNSサーバの一覧|
|`正引きファイル`|ホスト名からIPアドレスへの対応を記述|
|`逆引きファイル`|IPアドレスからホスト名への対応を記述|

## リソースレコード

ゾーンファイルには`$ORIGINディレクティブ`、`$TTLディレクティブ`、リソースレコードが含まれる。

`$ORIGINディレクティブ`には、ドメイン名が明示されていないレコードを補完するドメイン名を指定する。

`$TTLディレクティブ`には、他のDNSサーバがゾーンデータにキャッシュに保存しておく時間を指定する。

リソースレコードの中では、`@`はそのドメイン自身を表す。

|リソースレコードタイプ|説明|
|-|-|
|`SOA`|管理情報を記述|
|`NS`|ゾーンを管理にするDNSサーバを記述|
|`MX`|メールサーバを記述|
|`A`|ホスト名に対するIPアドレスを記述|
|`AAAA`|ホスト名に対するIPv6アドレスを記述|
|`CNAME`|ホスト名の別名に対応するホスト名を記述|
|`PTR`|IPアドレスに対応するホスト名を記述(逆引きファイルのみ)|

ゾーンファイルの構文チェックには`named-checkzone`コマンドを使う。

## DNSサーバのセキュリティ

DNSの仕組みを強化する仕組みとして、`DNSSEC(DNS Security)`と`TSIG(Transaction Signature)`がある。

## DNSSEC(DNS Security Extensions)

公開鍵暗号方式を用いた電子署名により、正当なDNSサーバからの情報であることを認証し、ゾーン情報が改ざんされていないことを保証する技術。

2種類の鍵を使う。

- ゾーンの署名鍵(ZSK: Zone Signing Key)
- 鍵の署名鍵(KSK: Key Singing Key)

## TSIG(Transaction Sinagure)

マスターDNSとスレーブDNSに共通の秘密鍵を設定することで、DNSサーバの偽装など、なりすましを防ぐ技術。ゾーン転送の安全性を向上させる仕組み。

クライアントとサーバの時刻があっていることが必要。

`dnssec-keygen`で作成した鍵は二つとも、同じ値が入っている。

作成した共通鍵は、マスター/スレーブDNSサーバ双方の`/etc/named.conf`にkeyステートメントとして設定する。

設定した共通鍵と通信先のホストと関連づけるために、スレーブDNSサーバのserverステートメントにマスターDNSサーバのIPアドレスと共通鍵名を設定する。

## DANE(DNS-based Authentication of Named Entities)

DNSSECの技術を応用して認証にDNSを使う仕組み。
