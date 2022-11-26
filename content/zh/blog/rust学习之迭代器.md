---
title: "Rust学习之迭代器"
date: 2022-11-26T15:28:59+08:00
description: "迭代器的简单了解"
categories: ["Rust"]
tags: ["迭代器"]
thumbnail: "/blog-post.jpg"
draft: true
---

`Rust`迭代器的使用在日常开发中是很基础和重要的，现在了解下迭代器的使用。

我们获取迭代器有以下几种方法：

- `.iter()`返回一个迭代器，包含类型的引用
- `.iter_mut()`返回一个迭代器，包含类型的可变引用
- `.into_iter()`返回一个包含类型值的迭代器

迭代器的实现：只需要实现`Trait std::iter::Iterator`这个trait就行了。

迭代器一般会配合一些集合操作方法，比如`rev()`, `map()`等，学会组合使用会展现出强大的威力。
