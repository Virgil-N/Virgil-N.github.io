---
title: "Rust学习之cfg宏"
date: 2022-12-02T17:41:03+08:00
description: "cfg宏的学习"
categories: ["Rust"]
tags: ["cfg!", "宏"]
thumbnail: "/blog-post.jpg"
draft: true
---

我们可以使用`cfg!`宏来告诉编译器当前的配置条件。常用的有：

- 键值对类型：target_os、target_arch、target_env、target_family、target_vendor、target_endian、feature等。

- 单项类型：test、debug_assertions

此外，也可以在属性中使用`cfg`，比如：`#[cfg(test)]`、`#[cfg(windows)]`等。还可以使用`#[cfg_attr(predicate, attr)]`。

eg:

```rust
let cfg_test = if cfg!(target_os = "macos") {
    String::from("mac")
} else {
    String::from("else")
};
println!("{}", cfg_test); // mac

#[cfg(test)]
mod test_mod {
    #[test]
    fn just_test() {
        if cfg!(test) {
            assert_eq!(1, 1);
        } else {
            println!("...");
        }
    }
}

```
