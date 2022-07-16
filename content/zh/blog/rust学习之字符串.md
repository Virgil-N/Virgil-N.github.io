---
title: "Rust学习之字符串"
date: 2022-05-31T14:55:57+08:00
description: "rust字符串学习中遇到的注意点"
categories: ["Rust"]
tags: [""]
thumbnail: /blog-post.jpg
draft: false
---

今天学习rust字符串的push_str方法时发现了一个隐藏的隐患:

1. 先声明一个&str类型不可变变量
1. 调用此变量的to_string方法并后续直接调用push_str方法
1. 打印此变量

发现变量的值未发生变化，这不是我们想要的，
其实不是这样的，因为`to_string`方法返回了一个新的`String`类型，他才是`push_str`方法的调用者，而`s`没有发生任何变化，所以最后我们打印的还是“美国”，而且从声明变量时没有加`mut`而编译器未报错就可以看出，`s`的值并没有改变。

```rust
let s = "美国";
s.to_string().push_str("众神");
println!("{}", s); // 美国
```

中间声明一个可变变量，然后再调用push_str方法

```rust
let s = "德国";
let mut s2 = s.to_string();
s2.push_str("希特勒");
println!("s2: {}", s2); // 德国希特勒
```

啊，过了几天再看自己之前写的这篇文章，感觉好low啊！😂

rust中切片是大小不确定的，无法直接使用，但是切片引用是大小固定的，可以使用。

|切片|切片饮用|
| --- | --- |
|str|&str|
|[..]|&[..]|
