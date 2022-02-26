---
title: "ローカライゼーション"
date: 2022-02-26T11:30:03+00:00
tags: ["linux"] 
author: "Me"
canonicalURL: "https://canonical.url/to/page"
xdisableHLJS: true # to disable highlightjs
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
pygmentsCodeFences: true
---

## 文字コード

コンピュータ上で文字を利用するために各文字に割り当てたバイトコード。

|文字コード|説明|
|-|-|
|ASCII|7ビットの文字コード、128種類の文字を表現|
|ISO-8859|ASCIIを8ビットに拡張したもの、256種類の文字を表現|
|Unicode|多言語を扱うために作成された符号化文字集合|

## ロケール用の環境変数

利用者の地域情報を設定することで表示言語や書式を変更できる。

`locale` コマンドで確認できる。

|文字コード|説明|
|-|-|
|LC_CTYPE|文字の分類、文字の種類を規定|
|LC_NUMERIC|数式の書式|
|LC_TIME|日付・時刻の書式|
|LC_TIME|日付・時刻の書式|
|LC_MONETARY|通貨の書式|
|LC_ALL|指定した値が全てのカテゴリを上書きし、カテゴリ毎の設定ができない|
|LANG|指定した値が全てのカテゴリを上書きするが、カテゴリ毎の設定はできる|

## iconvコマンド

文字コードを変換できるコマンド

## localeコマンド

現在のローケル設定を確認するコマンド

```bash
$ locale -a | head # ロケールの確認
C
C.utf8
POSIX
aa_DJ
aa_DJ.iso88591
aa_DJ.utf8
aa_ER
aa_ER.utf8
aa_ER.utf8@saaho
aa_ER@saaho
```

```bash
$ locale # 現在のローケルを確認
LANG=ja_JP.UTF-8
LC_CTYPE="ja_JP.UTF-8"
LC_NUMERIC="ja_JP.UTF-8"
LC_TIME="ja_JP.UTF-8"
LC_COLLATE="ja_JP.UTF-8"
LC_MONETARY="ja_JP.UTF-8"
LC_MESSAGES="ja_JP.UTF-8"
LC_PAPER="ja_JP.UTF-8"
LC_NAME="ja_JP.UTF-8"
LC_ADDRESS="ja_JP.UTF-8"
LC_TELEPHONE="ja_JP.UTF-8"
LC_MEASUREMENT="ja_JP.UTF-8"
LC_IDENTIFICATION="ja_JP.UTF-8"
LC_ALL=
```

## タイムゾーン

地域ごとに区分された標準時間帯のこと。

`/usr/share/zoneinfo` ディレクトリ配下にタイムゾーン情報のバイナリファイルが格納されている。

利用するタイムゾーンは、上記ファイルを`/etc/localtime` にコピーするか、シンボリックリンクを作成することで定義できる。

Linuxの内部ではUTC1970年1月1日からの経過秒数であるエポック時間で時間を管理しており、設定されたタイムゾーンにしたがってエポジック時間から、日付時刻を扱う。

```bash
$ ls /usr/share/zoneinfo/
Africa      Cuba     GMT-0      Libya    Portugal   iso3166.tab
America     EET      GMT0       MET      ROC        leapseconds
Antarctica  EST      Greenwich  MST      ROK        posix
Arctic      EST5EDT  HST        MST7MDT  Singapore  posixrules
Asia        Egypt    Hongkong   Mexico   Turkey     right
Atlantic    Eire     Iceland    NZ       UCT        tzdata.zi
Australia   Etc      Indian     NZ-CHAT  US         zone.tab
Brazil      Europe   Iran       Navajo   UTC        zone1970.tab
CET         GB       Israel     PRC      Universal
CST6CDT     GB-Eire  Jamaica    PST8PDT  W-SU
Canada      GMT      Japan      Pacific  WET
Chile       GMT+0    Kwajalein  Poland   Zulu
```

`tzselect` コマンドを使用することで、一覧から表示される情報をもとにタイムゾーンを設定できる。
