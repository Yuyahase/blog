---
title: "Regex"
date: 2022-02-28T22:47:41+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## 主なメタキャラクタ

|記号|説明|
|-|-|
|`.`|任意の文字に一致|
|`^`|行の先頭|
|`$`|行の末尾|
|`*`|直前の文字が0回以上の繰り返しに一致|
|`?`|直前の文字が0回もしくは1回の繰り返しに一致|
|`+`|直前の文字が1回以上の繰り返しに一致|

## 空白行以外を表示

```bash
grep '.' file1
```

## ピリオドを含む行を全て表示

```bash
grep '\.' file1
```

## Linux, linuxのいずれかを含む行を全て表示

```bash
grep '[Linux|linux]' file1
```

## 先頭の1文字が数値以外の行をすべて表示

```bash
grep '^[0-9]' file1
```

## 先頭が#で始まる行以外の行を全て表示

```bash
grep '^[^#]' file1
```

## pgrepコマンド

現在実行中のプロセスを調べて、ユーザ名、UID、GIDなどを基にプロセスIDを検索する。

|オプション|説明|
|-|-|
|-t 端末||
|-u euid|指定された実効ユーザIDに該当するプロセスIDを検索|
|-U uid|指定された実ユーザIDに該当するプロセスIDを検索|

## 実ユーザと実効ユーザの違い

実ユーザ（real UID) : プログラムを起動したプロセス所有者を表す

実効ユーザ(effective UID) :　プログラムが動作するときの権限を表すs
