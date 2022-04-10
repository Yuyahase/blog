---
title: "Rust_basic"
date: 2022-04-01T20:40:16+09:00
tags: ["Rust"] 
author: "Me"
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowCodeCopyButtons: true
---

## 一般的なプログラミングの概念

### 変数と可変性

不変変数に2回代入できない。

```rust
fn main() {
    let x = 5;
    println!("x is: {}", x);
    x = 6; // cannot assign twice to immutable variable `x`
    println!("x is: {}", x);
}
```

`mut`キーワードが使われると`x`が束縛している値を変更することができる。

```rust
fn main() {
    let mut x = 5;
    println!("x is: {}", x);
    x = 6; // cannot assign twice to immutable variable `x`
    println!("x is: {}", x);
}
```

大きなデータを扱う場合はインスタンスを可変にして変更できるようにする方が、いちいちインスタンスをコピーして新しくメモリ割り当てされたインスタンスを返すよりも速くなる。

小規模なデータ構造なら、新規インスタンスを生成して、もっと関数型っぽいコードを書く方が通して考えやすい。

定数はグルーバルスコープ含めて定義することができる。常に型注釈は必要。

```rust

const MAX_POINT: u32 = 100_000;
```

### データ型

### 関数

### 制御コメント
