---
title: "OpenSSH"
date: 2022-03-09T22:25:08+09:00
tags: ["OpenSSH","linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## OpenSSH

SSHはリモートホスト間の通信において高いセキュリティを実現するもので、ファイル転送やリモート操作を安全に行うことができる。

OpenBSDグループによるSSH実装であるOpenSSHが一般的に利用されている。

SSHサーバの機能はsshdデーモンによって提供される。

sshdの設定ファイルは`/etc/ssh/sshd_config`

### ホスト認証とユーザー認証

#### ホスト認証

SSHでは、ユーザ認証に先立ってホストの正当性を確認するホスト認証が行われる。

ホスト鍵は、`/.ssh/known_hosts`に格納される。
