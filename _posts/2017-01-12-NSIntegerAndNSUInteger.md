---
layout: post
title: swift perfect
description: 用swift开发服务器
tags: starter
image:
  background: triangular.png
---

### NSInteger 和 NSUInteger

昨天写代码遇到个问题

    NSInter index = -1;
    if (index > array.count)
    {
        NSLog("-1居然比正数还大");
    }
其实array.count是NSUInteger型的，这里会有个隐式转换，NSInteger在和NSUInteger做比较时，会自动转换成NSUInteger型，而NSUInteger型的是个很大的值。因为Objective-C 是ANSI C的超集，扩展了C 语言使它具备面向对象设计的能力，而隐式转换是C语言的特性，所以OC也具备这一特性。

[参考链接](http://blog.csdn.net/zhuimengzh/article/details/6728492)