---
author: "樛木"
title: "Rust习之字符串"
date: 2022-05-29T12:47:34+08:00
draft: true
description: "rust字符串学习中遇到的注意点"
tags: ["学习", "rust"]
thumbnail: /blog-post.jpg
---

今天学习rust字符串的push_str方法时发现了一个隐藏的隐患:

1. 先声明一个&str类型不可变变量
1. 调用此变量的to_string方法并后续直接调用push_str方法
1. 打印此变量

发现变量的值未发生变化，这不是我们想要的

```rust
let s = "美国";
s.to_string().push_str("众神");
println!("{}", s); // 美国
```

这是一个隐藏的问题，对此我目前想到的方法时中间声明一个可变变量，然后再调用push_str方法

```rust
let s = "德国";
let mut s2 = s.to_string();
s2.push_str("希特勒");
println!("s2: {}", s2); // 德国希特勒
```
