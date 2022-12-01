---
title: "Rust学习之基础部分1"
date: 2022-12-01T12:35:02+08:00
description: "Rust基础学习"
categories: ["Rust"]
tags: ["基础"]
thumbnail: "/blog-post.jpg"
draft: true
---

Rust查找candidate receiver type的顺序：  
>|
T  
&T  
&mut T  
*T  
&*T  
&mut *T  
coercion to U  
&U  
&mut U  
