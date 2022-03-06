---
title: "Text Customize"
date: 2022-03-03T20:26:42+09:00
tags: [""] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## nlコマンド

指定したファイルを行番号をつけて出力するコマンド

```bash
$ cat test.txt 
1

3
4
$ nl test.txt # デフォルトは空行を除いた行に行番号
     1  1
       
     2  3
     3  4
$ nl -b a test.txt # 空行も含めて全ての行に行番号をつける
     1  1
     2
     3  3
     4  4
$ cat -n test.txt # 空行も含めて全ての行に行番号をつける
```

## tailコマンド

指定したファイルの末尾部分を表示するコマンド。

```bash
$ cat test.txt 
1

3
4
$ tail -n 1 test.txt
4
$ tail -1 test.txt
4
```

## cutコマンド

## pasteコマンド

## sortコマンド

行単位でソートするコマンド。
デフォルトは、昇順ソート。

```bash
$ cat sort.txt 
 spase
000
10
2
4
3
aa
cc
b
$ sort sort.txt # 昇順でソート
 spase
000
10
2
3
4
aa
b
cc
```

降順でソートは`-r`オプションを使う。

```bash
$ sort -r sort.txt 
cc
b
aa
4
3
2
10
000
 spase
```

```bash



```

## splitコマンド
