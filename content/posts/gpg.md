---
title: "GPG"
date: 2022-03-09T13:33:32+09:00
tags: ["linux"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## GPGとは

GNU Privacy Guardの略で、ファイルの暗号化や復号、検証を行う暗号化プログラム。

`~/.gnupg/`というディレクトリに、公開暗号鍵方式の秘密鍵と公開鍵のペアが格納される

## 鍵管理

公開暗号鍵方式では、秘密鍵(プライマリーキー)公開鍵(サブキー)の組み合わせで構成される。

### 鍵の作成(対話モード)

```bash
# 対話モードで鍵の作成を行う。
$ gpg --gen-key
gpg (GnuPG) 2.3.3; Copyright (C) 2021 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

注意: 全機能の鍵生成には "gpg --full-generate-key" を使います。

GnuPGはあなたの鍵を識別するためにユーザIDを構成する必要があります。

本名: XXXX XXXX
電子メール・アドレス: xxxx@gmail.com
次のユーザIDを選択しました:
    "XXXX XXXX <xxxx@gmail.com>"

名前(N)、電子メール(E)の変更、またはOK(O)か終了(Q)? o
たくさんのランダム・バイトの生成が必要です。キーボードを打つ、マウスを動か
す、ディスクにアクセスするなどの他の操作を素数生成の間に行うことで、乱数生
成器に十分なエントロピーを供給する機会を与えることができます。
たくさんのランダム・バイトの生成が必要です。キーボードを打つ、マウスを動か
す、ディスクにアクセスするなどの他の操作を素数生成の間に行うことで、乱数生
成器に十分なエントロピーを供給する機会を与えることができます。
gpg: 鍵0A5A47B9C4031088を究極的に信用するよう記録しました
gpg: ディレクトリ'/Users/example/.gnupg/openpgp-revocs.d'が作成されました
gpg: 失効証明書を '/Users/example/.gnupg/openpgp-revocs.d/DC5945FEE63D655D771BBF510A5A47B9C4031088.rev' に保管しました。
公開鍵と秘密鍵を作成し、署名しました。

pub   ed25519 2022-03-09 [SC] [有効期限: 2024-03-08]
      DC5945FEE63D655D771BBF510A5A47B9C4031088
uid                      XXXX XXXX <xxxx@gmail.com>
sub   cv25519 2022-03-09 [E] [有効期限: 2024-03-08]
```

### 鍵の作成(設定ファイルからの読み込み)

以下のように設定ファイルを作って、`--batch`オプションを付けて起動することも可能。

```bash
$ cat alice-key.conf
Key-Type: RSA
Key-Length: 3072
Key-Usage: sign,cert
Subkey-Type: RSA
Subkey-Length: 3072
Subkey-Usage: encrypt
Name-Real: Alice
Name-Email: alice@example.com
Expire-Date: 0
Passphrase: passwd
%commit
%echo done

$ gpg --gen-key --batch alice-key.conf
gpg: 鍵AEE3D12F6D2F7F92を究極的に信用するよう記録しました
gpg: 失効証明書を '/home/username/.gnupg/openpgp-revocs.d/0F99F15C200B30194855B93AAEE3D12F6D2F7F92.rev' に保管しました。
gpg: done

$ gpg --list-keys alice
pub   rsa3072 2020-06-27 [SC]
      0F99F15C200B30194855B93AAEE3D12F6D2F7F92
uid           [  究極  ] Alice <alice@example.com>
sub   rsa3072 2020-06-27 [E]
```
