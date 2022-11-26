---
title: "Rust学习之方法整理"
date: 2022-11-26T22:55:08+08:00
description: "类似方法的整理"
categories: ["Rust"]
tags: ["方法", "整理"]
thumbnail: "/blog-post.jpg"
draft: true
---

- `Option`

```rust
/// 1
pub fn ok_or<E>(self, err: E) -> Result<T, E>
pub fn ok_or_else<E, F>(self, err: F) -> Result<T, E> where F: FnOnce() -> E
/// 2
pub fn unwrap(self) -> T
pub fn unwrap_or(self, default: T) -> T
pub fn unwrap_or_else<F>(self, f: F) -> T where F: FnOnce() -> T
pub fn unwrap_or_default(self) -> T where T: Default
pub unsafe fn unwrap_unchecked(self) -> T
/// 3
pub fn and<U>(self, optb: Option<U>) -> Option<U>
pub fn and_then<U, F>(self, f: F) -> Option<U> where F: FnOnce(T) -> Option<U>
```

- `Result<T, E>`

```rust
/// 1
pub fn ok(self) -> Option<T>
pub fn err(self) -> Option<E>
/// 2
pub fn unwrap(self) -> T where E: Debug
pub fn unwrap_or(self, default: T) -> T
pub fn unwrap_or_else<F>(self, op: F) -> T where F: FnOnce(E) -> T
pub fn unwrap_or_default(self) -> T where T: Default
pub unsafe fn unwrap_unchecked(self) -> T
pub fn unwrap_err(self) -> E where T: Debug
pub unsafe fn unwrap_err_unchecked(self) -> E
/// 3
pub fn and<U>(self, res: Result<U, E>) -> Result<U, E>
pub fn and_then<U, F>(self, op: F) -> Result<U, E> where F: FnOnce(T) -> Result<U, E>
```

(持续更新)
