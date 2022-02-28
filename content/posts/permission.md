---
title: "Permission"
date: 2022-03-01T00:54:11+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## umask

ファイルであれば666,ディレクトリであれば777のパーミションからumask値の否定との論理積がデフォルトのパーミッションとなる。

## SGID

通常はファイルやディレクトリを作成すると、その所有グループは作成したユーザの一次グループが使用される。

SGIDはファイルのグループIDが実効グループIDとして設定される。

chmodコマンドでは「2000」もしくは「g+s」を使用する。

## スティッキービット

特定のディレクトリに対しアクセス権が許可されていてもファイルの削除が行えないように保護する設定。

以下の`/tmp`ディレクトリは、多くのユーザが削除できるようにアクセス権がすべて許可されているが、スティッキービットを設定することで、ファイルの削除、名前の変更に関しては所有者およびrootのみが行える。

chmodコマンドでは「1000」もしくは「o+t」を使用する。

```bash
$ ls -ld /tmp/
drwxrwxrwt. 6 root root 4096  3月  1 00:55 /tmp/
```
