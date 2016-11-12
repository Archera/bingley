---
layout: post
title: 脸书开源框架ARC（ReactiveCocoa）初体验
description: 
tags: starter
image:
  background: triangular.png
---

最近研究了下ARC这个框架，怕以后怠慢学习，决定把所学记录下来，鼓励自己继续学习。

在开发hiStarter的时候，我发现viewController力的内容的确太多了，多的我不想看了，真的像网上的一些前辈说的哪样，MVC模型的一大弊端就是viewController太臃肿了，又要构建页面，又要传递消息，数据，各种交互，有时候懒一点把该抽出去的私有方法留在里面，VC就更不能看了，正当我苦不堪言又无所突破的时候我发现了网上开始流行了MVVC这一设计模型，而与这一模型完美结合使用的便是RAC了。MVC与MVVC直接的各种优劣势就不多说了，主要说一下MVVC的一大劣势就是如果程序出现BUG，将很难定位到BUG的位置，因为是消息是响应式传递，传递链越长BUG隐藏的约深。当然这也是网上说的，我还没遇到。不过仔细想想这的确是一大硬伤，毕竟不同于MVC中控件触发时间，能清楚的知道信号的位置，MVVC中消息发送后，可以说就与控件毫无关系了，消息在RAC中传递，还一下真不知道传到哪里去了。但是这并不影响我学习RAC的动力，我相信随着我学习经验的积累，真的在项目中出现这种BUG，也会并我的积累解决掉。对，我就是这么自信。好了废话了半天，现在开始记录学习的成功。

这次目标，hiStarter项目的登录注册部分使用MVVC修改。项目框架结构，以tabbar模块划分，目前做的是login模块，login模块里面在分MV（ModleView）V（view）C（viewController）。

V（view）：个人的开发观点，view只是显示控件，页面只有创建控件以及属性的代码，没有任何的逻辑交互。相当于在使用xib的时候把控件拖到xib上，然后各个控件的位置设置好，就OK。这个页面暂时需要3个属性账号，密码，登录按钮用于跟后面的MV信号模型绑定。

MV（ModleView）：公开属性

    NSString *userName;//用来传递账号 
    NSString *password;//用来传递密码 
    RACSubject *successObject;//登录按钮时间成功后返回信息 
    RACSubject * failureObject;//登录按钮时间失败后返回信息
    RACSubject *errorObject;//登录按钮时间错误后返回信息
    
私有属性

    RACSignal *userNameSignal;//绑定userName
    RACSignal *passwordSignal;//绑定password
    NSDictionary *requestData;//登录请求所需要的数据

公开方法
 
    - (id) buttonIsValid;//合并两个输入框信号，并返回按钮bool类型的值，合并成功与否是更具编写的私有方法来判断账号密码是否合法
    - (void)login;//开始登录

C（viewController）： 私有属性

view和ModleView两个实例对象给view.loginBtn添加信号点击事件

私有方法 

    - (void)bindLoginVModel {

    //ModleView. userName和view.accountTfd.rac_textSignal,把账号输入内容和信号绑定；
    
    //把密码输入内容和信号绑定;
    
    //调用buttonIsValid返回结果赋给登录按钮信号，判断登录按钮是否满足被点击的条件
    
    }

程序按这样分层后边得果然简洁易懂，我还把之前的网络层（请求网络并返回的方法）抽出来单独成ModleView的父类，返回自然由successObject，failureObject，errorObject这3个绑定信号来传递。这样做了之后更加坚信了我要把项目重构一次的信念，持续重构，持续记录，待续。

[项目下载地址](https://github.com/Archera/ReactiveCocoa-study.git)