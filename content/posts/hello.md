---
title: "hello Hugo"
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

## Hugoよく使うコマンド
------
### バージョンの表示
```bash
$ hugo version
hugo v0.92.2+extended darwin/arm64 BuildDate=unknown
```
------

### 新しい記事を作る
```bash
$ hugo new posts/test.md
```
------

### テストサーバを立てる
```bash
$ hugo server
```
------

### 公開用のファイルの生成
 - 公開用のファイルを生成するには、`hugo`コマンドを実行する
 - `public`フォルダに公開用のファイル一式が生成される

```bash
$ hugo
```

### 記事のテンプレートを作成する
 - archetypes/default.mdを作成する

```
+++
categories = ["", ""]
date = ""
description = ""
draft = true
image = ""
tags = ["", ""]
title = ""
author = ""
+++
```