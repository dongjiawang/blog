---
layout: post
title: switch case 的 expected expression
date: 2017-09-30 11:05:00
categories: ["iOS"]
tags:  ["iOS"]
mathjax: true
---

`Objective-c` 在使用复杂的 `switch case` 时候，如果在 `case` 内定义新的变量，就会报 `expected expression` 的错误，解决这种情况有两种方式。

**1、将变量定义在 switch 外面** 

**2、在 case 内使用 `{ }` 把对应的代码括起来**