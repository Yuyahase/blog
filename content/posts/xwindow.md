---
title: "Xwindow"
date: 2022-03-12T17:00:35+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## xorg.conf

X.Orgの設定ファイルは、`/etc/X11/xorg.conf`だがディレクトリにも`/etc/X11/xorg.conf.d/`にもconfファイルを保存できる。

GUIログイン時のX Window Systemのエラーは、ユーザのホームディレクトリの`xsession-errors`ファイルに出力される。

|セクション名|説明|
|-|-|
|`ServerLayout`|入出力デバイスに関する設定|
|`Files`|RGBカラーデータベースの場所やフォントの格納場所に関する設定|
|`InputDevice`|キーボードやマウスなどの入力デバイスの設定|
|`Module`|組み込みモジュールの指定|
|`Monitor`|モニタータイプの設定|
|`Device`|ビデオカードの設定|
|`Screen`|ディスプレイの表示色数や画面サイズの設定|

## Xクライアント上で実行したプログラムをXサーバのディスプレイに表示させる

サーバ側では、`xhost`コマンドでクライアントからのアクセスを許可する。

```bash
$ xhost +172.16.0.4
172.16.0.4 being added to access control list
```

環境変数DISPLAYで表示先(Xサーバ)を指定し、Xサーバに表示させたいプログラム(xeyes)を実行する。

```bash
DISPLAY=172.16.0.1:0
export DISPLAY
xeyes &
```

## ディスプレイマネージャ

ランレベル5の時にユーザがにグラフィカルログインを提供し、ログイン認証、ログイン後のデスクトップ環境の準備を行う。

主なディスプレイマネージャは以下の通り。

- XDM
- GDM
- KDM
- LightDM

`/etc/X11/xdm/xdm-config`はXDMの全体的な設定を行うファイル。

## ウィンドウマネージャ

ウィンドウの外観、アイコンやカーソル、メニューなどを提供するXクライアントのアプリケーション。

twmは最小限の機能を備えたウィンドウマネージャ。

## 統合デスクトップ環境

ディスプレイマネージャとウィンドウマネージャをまとめて統一的な操作を提供する。

主な統合デスクトップ環境は以下。

- GNOME:GTK+というGUIツールキットをベースに開発される。Ubuntuなどで使われている。
- KDE:QtというGUIツールキットをベースに開発される。openSUSEなどで使われている。
- Xfce:メモリやCPUの消費量が少ない軽量デスクトップ環境。
