---
title: "Library"
date: 2022-03-02T00:32:08+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## スタティックリンク

プログラムをコンパイルする段階で、コンパイラがライブラリを実行ファイルに埋め込む。

メリット : 一つのプログラムだけで動作させられる。
デメリット : 他のプログラムとライブラリが重複する可能性がある。

## ダイナミックリンク

プログラム実行時にライブラリの機能を呼び出す。

メリット : ライブラリが共通で使用されるので、プログラム全体のサイズが小さくなる。

## 共有ライブラリの格納場所

- /lib
- /lib64

## 共有ライブラリの検索パス更新

`ldconfig`コマンドを使う。

`ldconfig`コマンドによって、共有ライブラリの検索パスが記述されている`/etc/ld.so.conf`ファイルを参照し、`/etc/ld.so.cache`ファイルを更新する。

`/etc/ld.socache`ファイルはバイナリファイルであるため、`/etc/ld/so/conf`ファイルより早く検索できる。

`LD_LIBRARY_PATH`は共有ライブラリのパスが保存された環境変数である。
