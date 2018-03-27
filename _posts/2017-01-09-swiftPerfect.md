---
layout: post
title: swift perfect
description: 用swift开发服务器
tags: starter
image:
  background: triangular.png
---

### 安装swift perfect
工具：Xcode 8.0  swift 3.0

可以使用swift --version查看当前swift版本，打印信息应该如下：

    Apple Swift version 3.0-dev(LLVM 3863c393d9, Clang d03752fe45, Swift e996f0c248)
    Target: x86_64-apple-macosx10.9
    
### OSX
 
 先安装[Home Brew](http://brew.sh/index_zh-cn.html)，可以使用brew --version查看是否安装,brew是用来管理插件的,下面死安装命令：
 
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    
安装完成后，安装OpenSSL：

    brew install openssl
    brew link openssl --force
    
这里需要等待一段时间,完成后差不多是下面这个样子

![图片丢失]({{ site.url }}/images/homeBrew.png)

上面的配置工作完成后，下面编译入门项目：

    git clone https://github.com/PerfectlySoft/PerfectTemplate.git
    cd PerfectTemplate
    swift build   //这一步时间很长，耐心等待,
    //差不多等待1个小时会出现Linking ./.build/debug/PerfectTemplate
    .build/debug/PerfectTemplate
    
您应该可以在终端控制台中看到类似下面的内容：

    [INFO] Starting HTTP server localhost on 0.0.0.0:8181
    [INFO] Starting HTTP server localhost on 0.0.0.0:8080
    
![图片丢失]({{ site.url }}/images/PerfectTemplate.png)
    
服务器现在已经运行并等待连接。从浏览器打开[http://localhost:8181/](http://localhost:8181/) 可以看到欢迎信息(PS:其实就是Hello，world)。在终端控制台中输入组合键“control-c”可以随时终止服务器运行。

### Xcode

Swift软件包管理器（SPM）能够创建一个Xcode项目，并且能够运行PerfectTemplate模板服务器，还能为您的项目提供完全的源代码编辑和调试。在您的终端命令行内输入：

    swift package generate-xcodeproj
    
然后打开产生的文件“PerfectTemplate.xcodeproj”，确定选择了可执行的目标文件，并选择在“我的Mac”运行。现在您可以运行并调试服务器了。


[参考链接](http://perfect.org/downloads.html#download-perfect)
