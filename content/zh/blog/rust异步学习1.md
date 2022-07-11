---
title: "Rust异步学习1"
date: 2022-07-11T11:25:36+08:00
description: ""
categories: ["Rust"]
tags: [""]
thumbnail: "/blog-post.jpg"
draft: false
---

`rust`异步机制学习。

- tokio的线程必须在`runtime`中运行

公用代码

```rust
use chrono::Local;

pub fn now() -> String {
  Local::now().format("%F %T").to_string()
}
```

案例代码

```rust
fn main() {
  println!("system here");
  // rust的线程正常运行
  std::thread::spawn(|| {
    std::thread::sleep(std::time::Duration::from_secs(1));
    println!("thread out block_on: {}", now());
  });

  // 报错，只能在tokio的runtime中运行
  // tokio::spawn(async {
  //   std::thread::sleep(std::time::Duration::from_secs(1));
  //   println!("thread out block_on: {}", now());
  // });

  std::thread::sleep(std::time::Duration::from_secs(3));
  println!("system ended: {}", Local::now().format("%F %T .%3f"));
}
```

> 运行结果(注意打印时间间隔为2秒):  
system here  
thread out block_on: 2022-07-11 12:40:54  
system ended: 2022-07-11 12:40:56 .478

- `block_on`是阻塞的，而`enter`是非阻塞的(注意查看以下四段代码的运行结果)。

```rust
fn main() {
  let rt1 = tokio::runtime::Runtime::new().unwrap();
  println!("system here");

  rt1.block_on(async{
    println!("system before: {}", Local::now().format("%F %T .%3f"));
    std::thread::sleep(Duration::from_secs(3)); // 会阻塞线程
    println!("system after: {}", Local::now().format("%F %T .%3f"));
  });

  println!("system end start");
  println!("system ending");
  println!("system ended: {}", Local::now().format("%F %T .%3f"));
}
```

> 运行结果:  
system here  
system before: 2022-07-11 11:37:12 .374  
system after: 2022-07-11 11:37:15 .377  
system end start  
system ending  
system ended: 2022-07-11 11:37:15 .380

```rust
fn main() {
  let rt1 = tokio::runtime::Runtime::new().unwrap();
  println!("system here");

  rt1.block_on(async{
    println!("tokio before: {}", Local::now().format("%F %T .%3f"));
    tokio::time::sleep(std::time::Duration::from_secs(3)).await; // 不会阻塞线程
    println!("tokio after: {}", Local::now().format("%F %T .%3f"));
  });

  println!("system end start");
  println!("system ending");
  println!("system ended: {}", Local::now().format("%F %T .%3f"));
}
```

> 运行结果  
system here  
tokio before: 2022-07-11 11:42:50 .247  
tokio after: 2022-07-11 11:42:53 .254  
system end start  
system ending  
system ended: 2022-07-11 11:42:53 .256

以下两个是非阻塞形式，但是由于`std::thread::sleep`会阻塞，所以线程任务可以完成，而`tokio::time::sleep`不会阻塞，想要完成线程任务必须让主线程等待相应的时长。

```rust
fn main() {
  let rt1 = tokio::runtime::Runtime::new().unwrap();
  println!("system here");

  // 使用enter语法进入runtime环境
  let guard = rt1.enter();

  tokio::spawn(async{
    println!("system before: {}", Local::now().format("%F %T .%3f"));
    std::thread::sleep(std::time::Duration::from_secs(3)); // 会阻塞线程
    println!("system after: {}", Local::now().format("%F %T .%3f"));
  });

  println!("system end start");
  // std::thread::sleep(std::time::Duration::from_secs(5));
  println!("system ending");

  drop(guard);

  println!("system ended: {}", Local::now().format("%F %T .%3f"));
}
```

> 运行结果  
system here  
system end start  
system ending  
system before: 2022-07-11 12:13:33 .299  
system ended: 2022-07-11 12:13:33 .300  
system after: 2022-07-11 12:13:36 .303

```rust
fn main() {
  let rt1 = tokio::runtime::Runtime::new().unwrap();
  println!("system here");

  // 使用enter语法进入runtime环境
  let guard = rt1.enter();

  tokio::spawn(async{
    println!("tokio before: {}", Local::now().format("%F %T .%3f"));
    tokio::time::sleep(std::time::Duration::from_secs(3)).await; // 不会阻塞线程
    println!("tokio after: {}", Local::now().format("%F %T .%3f"));
  });

  println!("system end start");
  std::thread::sleep(std::time::Duration::from_secs(5));
  println!("system ending");

  drop(guard);

  println!("system ended: {}", Local::now().format("%F %T .%3f"));
}
```

> 运行结果  
system here  
system end start  
tokio before: 2022-07-11 12:27:43 .195  
tokio after: 2022-07-11 12:27:46 .202  
system ending  
system ended: 2022-07-11 12:27:48 .206

- 最后，使用`block_on`，代码是顺序执行的，而使用`enter`顺序不能保证一致。
