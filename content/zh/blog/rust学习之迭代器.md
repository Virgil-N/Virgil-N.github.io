---
title: "Rust学习之迭代器"
date: 2022-11-26T15:28:59+08:00
description: "迭代器的简单了解"
categories: ["Rust"]
tags: ["迭代器"]
thumbnail: "/blog-post.jpg"
draft: false
---

`Rust`迭代器的使用在日常开发中是很基础和重要的，现在了解下迭代器的使用。

我们获取迭代器有以下几种方法：

- `.iter()`返回一个迭代器，包含类型的引用
- `.iter_mut()`返回一个迭代器，包含类型的可变引用
- `.into_iter()`返回一个包含类型值的迭代器

迭代器的实现：只需要实现`Trait std::iter::Iterator`这个trait就行了，不多说了。

迭代器一般会配合一些集合操作方法，比如`rev()`，`map()`，`fold()`等，学会组合使用会展现出强大的威力。并且相比较`for in`，`loop`等循环，迭代器是惰性的，对性能有一定优化。

注意`.into_iter()`在receiver为引用的情况下会依情况调用`.iter()`或`.inter_mut()`方法。观察以下代码：

```rust
// block 1
// 我们都清楚.into_iter()方法是会转移所有权的，因此下面打印报错在意料之中
let v1 = vec![1, 2, 3];
let i1 = v1.into_iter();
println!("{:?}, {:?}", v1, i1); // borrow of moved value: `v1`

// block 2
// 有没有一点吃惊😱
let v1 = vec![1, 2, 3];
let i1 = (&v1).into_iter();
println!("{:?}, {:?}", v1, i1); // [1, 2, 3]
// 或者
let v1 = &vec![1, 2, 3];
let i1 = v1.into_iter();
println!("{:?}, {:?}", v1, i1); // [1, 2, 3]
```

看到这里小伙伴是不是产生疑惑了，所以，现在我们查看下源码。

```rust
impl<'a, T, A: Allocator> IntoIterator for &'a Vec<T, A> {
    type Item = &'a T;
    type IntoIter = slice::Iter<'a, T>;

    fn into_iter(self) -> slice::Iter<'a, T> {
        self.iter()
    }
}

impl<'a, T, A: Allocator> IntoIterator for &'a mut Vec<T, A> {
    type Item = &'a mut T;
    type IntoIter = slice::IterMut<'a, T>;

    fn into_iter(self) -> slice::IterMut<'a, T> {
        self.iter_mut()
    }
}
```

现在清楚了吧，rust的`.into_iter()`方法此时分别调用了`.iter()`和`.iter_mut()`方法。所以，我们的打印语句还是可以引用声明的Vec。

再看几个例子，加深理解。

```rust
// block 1
let mut v1 = vec![1, 2, 3];
let i1 = (&v1).into_iter();
println!("{:?}, {:?}", v1, i1); // [1, 2, 3], Iter([1, 2, 3])
let ii1 = (&mut v1).into_iter();
let r = ii1
    .map(|x| {
        *x += 2;
        *x + 1
    })
    .collect::<Vec<_>>();
println!("{:?}, {:?}", v1, r); // [3, 4, 5], [4, 5, 6]

// block 2
let vs1 = &mut vec!["a".to_string(), "b".to_string(), "c".to_string()];
let is1 = vs1.into_iter();
let rs1 = is1
    .map(|x| {
        *x = "ss".to_string();
        "zz".to_string()
    })
    .collect::<Vec<_>>();
println!("{:?}, {:?}", vs1, rs1); // ["ss", "ss", "ss"], ["zz", "zz", "zz"]

// block 3
let vs2 = vec!["a".to_string(), "b".to_string(), "c".to_string()];
let is2 = vs2.into_iter();
let rs2 = is2
    .map(|mut _x| {
        _x = "ss".to_string();
        "zz".to_string()
    })
    .collect::<Vec<_>>();
println!("{:?}, {:?}", vs2, rs2); // borrow of moved value: `vs2`

// block 4
let mut vs2 = vec!["a".to_string(), "b".to_string(), "c".to_string()];
let is2 = vs2.iter_mut();
let rs2 = is2
    .map(|x| {
        *x = "ss".to_string();
        "zz".to_string()
    })
    .collect::<Vec<_>>();
println!("{:?}, {:?}", vs2, rs2); // ["ss", "ss", "ss"], ["zz", "zz", "zz"]

```
