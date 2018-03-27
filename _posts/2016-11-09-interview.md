---
layout: post
title: 面试题
description: 最近找工作，找了些面试题来做做功课
tags: starter
image:
  background: triangular.png
---

# 索引

### 1.风格纠错题

    typedef enum{
        UserSex_Man,
        UserSex_Woman
    }UserSex;
    
    @interface UserModel :NSObject
    
    
    @property(nonatomic, strong) NSString *name;
    @property (assign, nonatomic) int age;
    @property (nonatomic, assign) UserSex sex;
    
    -(id)initUserModelWithUserName: (NSString*)name withAge:(int)age;
    
    
    -(void)doLogIn;
    
    @end
    
这题考察的是编程规范问题，下面是优化部分：

1. enum建议使用 `NS_ENUM` 和 `NS_OPTIONS` 宏来定义枚举类型，参见[Adopting Modern Objective-C](https://developer.apple.com/library/ios/releasenotes/ObjectiveC/ModernizationObjC/AdoptingModernObjective-C/AdoptingModernObjective-C.html) 一文：

    //定义一个枚举
    typedef NS_ENUM(NSInteger, CYLGender) {
        CYLGenderMale,
        CYLGenderFemale
    };
   
2. age属性的类型：应避免使用基本类型，建议使用Foundation数据类型，对应关系如下：
    
    int -> NSInteger
    unsigned -> NSUInteger
    float -> CGFloat
    
3. doLogIn方法写在该类中不合适，登录操作属于业务逻辑，应当写在controller中，该类应该是model
4. doLogIn方法命名不规范：应为 `-login`
5. `-(id)initUserModelWithUserName: (NSString*)name withAge:(int)age` 方法中不要用 `with` 或者 `and` 来连接两个参数，改成 `- (id)initWithUserName:(NSString *)name age:(NSInter)age`
6. 由于字符串可能会改变 `strong ` 改成 `copy`
7. `UserModel` 改为 `User` 更加简洁，足够清晰
8. `UserSex` 改为 `Sex`

下面是必须改的编码规范

1. 在-和（void）之间应该有一个空格
2. enum中驼峰命名法和下划线命名法混用错误：枚举类型的命名规则和函数的命名规则相同：命名时使用驼峰命名法。
3. enum 左括号前加一个空格，或者将左括号换到下一行
4. enum 右括号后加一个空格
5. `UserModel :NSObject` 应为`UserModel : NSObject`，也就是`:`右侧少了一个空格。
6.  `@interface` 与 `@property` 属性声明中间应当间隔一行。
7. 两个方法定义之间不需要换行，有时为了区分方法的功能也可间隔一行，但示例代码中间隔了两行。
8. `-(id)initUserModelWithUserName: (NSString*)name withAge:(int)age;`方法中方法名与参数之间多了空格。而且 `-` 与 `(id)` 之间少了空格。
9. `-(id)initUserModelWithUserName: (NSString*)name withAge:(int)age;`方法中方法名与参数之间多了空格：`(NSString*)name` 前多了空格。
10. `-(id)initUserModelWithUserName: (NSString*)name withAge:(int)age;` 方法中 `(NSString*)name`,应为 `(NSString *)name`，少了空格。

### 2. 什么情况使用 weak 关键字，相比 assign 有什么不同？

1. 在ARC中，在有可能出现循环引用的时候，往往要通过让其中一端使用weak来解决，例如：delegate代理属性
2. 自身已经对它进行一次强引用，没有必要再强引用一次，此时也会使用weak，自定义IBOutet控件属性一般使用weak

不同点：

1. `weak` 此关键字表面该属性定义了一种“非拥有关系”。为这种属性设置新值时，设置方法既不保留新值，也不释放旧值。此特质同assign类似，然而在属性所指的对象遭到摧毁时，属性值也会清空（nil out）。而 `assign` “的设置方法”只会执行针对基础数据类型（CGFloat，NSInteger等）
2. `assign ` 可用于非OC对象，而 `weak` 必须用于OC对象

### 3. property都有哪些常见的字段，他们有什么不同

strong，weak，retain，copy，assign，nomatic，readonly

assign：简单赋值，不更改索引计数对基础数据类型

copy：建立一个索引计数为1的对象，然后释放旧对象。对NSString，NSArray等

retain：释放旧对象，将旧对象的值赋予输入对象，再提高输入对象的索引计数为1，对其他NSObject和其子类 

copy和retain：

copy其实是建立了一个相同的对象，而retain不是

copy是内容拷贝，对象像NSString的确是这样（深拷贝，重新开辟一个地址，旧地址的内容变了，不影响新地址内容），如果是NSArray这时只是copy了指向array中相对应元素的指针（浅复制），retain是指针拷贝

    - [immutableObject copy] // 不可变对象，浅复制
    - [immutableObject mutableCopy] //深复制
    - [mutableObject copy] //深复制
    - [mutableObject mutableCopy] //深复制

PS：这也是为什么NSString使用copy属性，而不用strong属性，strong相对应retain，指针复制，如果新值改变旧值会一起改变，因为是同一块内存区域  

weak和strong的区别：当一个对象不再有strong类型的指针指向它的时候它会被释放，即使还有weak型指针指向它。一旦最后一个strong型指针离去，这个对象将被释放，所有剩余的weak型指针都将被清除。

atomic是Objc使用的一种线程保护技术，基本上来讲，是防止在写未完成的时候被另外一个线程读取，造成数据错误。而这种机制是耗费系统资源的，所以在iPhone上，如果没有使用多线程间的通讯编程，那么nonatomic是一种非常好的选择。

### 4. 这个写法会有什么问题：
    @property (copy) NSmutableArray *array;
    
两个问题：1、添加,删除,修改数组内的元素的时候,程序会因为找不到对应的方法而崩溃.因为 copy 就是复制一个不可变 NSArray 的对象；2、使用了 atomic 属性会严重影响性能 ；

### 5. 如何让自己的类用copy修饰符？如何重写带copy关键字的setter？

1. 需要声明该类遵从NSCopting协议
2. 实现NSCopying协议。该协议只有一个方法：

        - (id)copyWithZone:(NSZone *)zone;

下面是例子：
    
    @interface Class : NSObject<NSCopying>
    
    @property (nonatomic, copy) NSString *string;
    
    @end
    
    //在实现方法中写
    - (id)copyWithZone:(NSZone *)zone {

      Class *class = [[Class alloc] init];
    
      class.string = self.string;
    
      return class;
    
    }
    
    //重写setter
    
    - (void)setString:(NSString *)string {

        _string = [string copy];
    }


###6. @synthesize和@dynamic分别有什么作用？
    
@synthesize自动生成getter和setter方法
@dynamic手动生成

###7. ARC下，不显式指定任何属性关键字时，默认的关键字都有哪些？

atomic,readwrite,assign,strong

###8. objc中向一个对象发送消息[obj foo]和`objc_msgSend()`函数之间有什么关系？

[obj foo]编译器编译之后就是`objc_msgSend()`

###9. 什么时候会报unrecognized selector的异常？

调用的方法不存在，消息传递机制，objc_msgSend(receiver, selector)。

###10. 一个objc对象的isa的指针指向什么？有什么作用？

指向他的类对象，从而可以找到对象上的方法

###11. 下面的代码输出什么？

 ```Objective-C
	@implementation Son : Father
	- (id)init
	{
	    self = [super init];
	    if (self) {
	        NSLog(@"%@", NSStringFromClass([self class]));
	        NSLog(@"%@", NSStringFromClass([super class]));
	    }
	    return self;
	}
	@end
 ```
 
 答案：Son，Son
 
super和self是指向的同一个消息接受者！他们两个的不同点在于：super 会告诉编译器，调用 class 这个方法时，要去父类的方法，而不是本类里的。

当使用 self 调用方法时，会从当前类的方法列表中开始找，如果没有，就从父类中再找；而当使用 super 时，则从父类的方法列表中开始找。然后调用父类的这个方法。


这也就是为什么说“不推荐在 init 方法中使用点语法”，如果想访问实例变量 iVar 应该使用下划线（ `_iVar` ），而非点语法（ `self.iVar` ）。点语法（ `self.iVar` ）的坏处就是子类有可能覆写 setter 。

###12. runtime如何通过selector找到对应的IMP地址？（分别考虑类方法和实例方法）

每一个类中都有一个方法列表，方法列表中记载着方法名称，方法的实现以及参数类型，其实selector的本质就是方法名称，通过这个名称就可以在方法列表中找到对应的实现方法。

###13. 使用runtime Associate方法关联的对象，需要在主对象dealloc的时候释放么？

不需要 
> [ runtime Associate方法关联对象 ](https://my.oschina.net/carson6931/blog/506050)
>

###14.@synthesize合成实例变量的规则是什么？假如property名为foo，存在一个名为_foo的实例变量，那么还会自动合成新变量么？

@synthesize会先生成一个实例变量，如果有则不生成，然后创建setter和getter方法

###15.objc中向一个nil对象发送消息将会发生什么？

会返回null，不会奔溃，因为isa指向的是0

###16.objc中向一个对象发送消息[obj foo]和objc_msgSend()函数之间有什么关系？

[obj foo]实际上调用的是objc_msgSend(obj,@selector(foo))

###17.什么时候会报unrecognized selector的异常？

在像一个不存在的方法发生消息时

###18.一个objc对象如何进行内存布局？（考虑有父类的情况）

1. 父类的实力对象和自己的实力对象都存储在该对象所对应的存储空间中
2. 每个实例对象都有个isa指针指向该对象的类对象，类对象中储存着实例对象的方法列表，属性列表，成员变量列表，类对象也有个isa指针指向元类，元类中储存这类对象的类方法列表，元类中还有一个superclass指针指向元类的父类，就这样一直指向NSObject类。

###19.一个objc对象的isa的指针指向什么？有什么作用？

答案同上

###20.runtime如何通过selector找到对应的IMP地址？（分别考虑类方法和实例方法）

对象发送消息时，会先去cache里面查找SEL，SEL其实是一个方法ID，通过该ID去找队友的IMP地址。如果cache里没有，这去isa指向的类对象的实例方法列表中去寻找，如果是类方法，则会去类对象的isa指针指向的元类去查找类方法列表。

###21._objc_msgForward函数是做什么的，直接调用它将会发生什么
消息重定向的，直接调用如果你本来就已经实现了某方法，但是这个方法也不会执行了。

###22.runtime如何实现weak变量的自动置nil？

runtime 对注册的类， 会进行布局，对于 weak 对象会放入一个 hash 表中。 用 weak 指向的对象内存地址作为 key，当此对象的引用计数为0的时候会 dealloc， 在这个 weak 表中搜索，找到所有以a为键的 weak 对象，从而设置为 nil。

###23.KVO KVC的实现原理

KVO:

在监听A类的name属性时，KVO机制会动态生产一个NSNotifying_A类继承A类，KVO在心类中会重写setter方法，并且在调用setter之前和之后，通知对象属性的更改情况。[参考链接](https://www.jianshu.com/p/0e75d99c3480)

KVC:

当一个对象调用setValue方法时，方法内部会做以下操作：

1. 检查是否存在相应key的set方法，如果存在，就调用set方法
2. 如果set方法不存在，就会查找与key相同名称并且带下划线的成员属性，如果有，则直接给成员属性赋值
3. 如果没有找到_key,就会查找相同名称的属性key，如果有就直接赋值
4. 如果还没找到，则调用valueForUndefinedKey:和setValue:forUndefinedKey:方法。这些方法的默认实现都是抛出异常，我们可以根据需要重写它们。

  
###24. objc中的类方法和实例方法有什么本质区别和联系？

类方法：

1. 类方法是属于类对象
2. 类方法只能通过类对象调用
3. 类方法中的self是类对象
4. 类方法中可以调用其他类方法
5. 类方法中不能访问成员变量
6. 类方法中不能直接调用对象方法

实例方法：

1. 实例方法是属于实例对象
2. 实例方法只能通过实例对象调用
3. 实例方法中的self是实例对象
4. 实例方法中可以访问成员变量
5. 实例方法中直接调用实例方法
6. 实例方法中也可以调用类方法

### 25. UIWindow和UIView和CALayer的联系和区别？

答：UIView是视图的基类，UIViewController是视图控制器的基类，UIResponder是表示一个可以在屏幕上响应触摸事件的对象；

UIWindow是UIView的子类，UIWindow的主要作用：1、是提供一个区域来显示UIView，而是将事件分发个UIView，一个应用基本上只有一个UIWindow

UIView是CALayer的管理器，UIView是CALayer的高度封装，UIView可以响应事件，CALayer不能

### 26. __block和__weak修饰符的区别

__block不管是ARC还是MRC模式下都可以使用，可以修饰对象，还可以修饰基本数据类型。

__weak只能在ARC模式下使用，也只能修饰对象，不能修饰基本数据类型

__block对象可以在block中被重新赋值，__weak不行。

###27. objc使用什么机制管理对象内存？

通过retaincount的机制来决定是否释放对象。
每次runloop的时候，都会检查对象retaincount，如果retaincount为0，说明该对象没有地方需要继续使用，可以释放掉了。

###28. 不手动指定autoreleasepool的前提下，一个autorealese对象在什么时刻释放？（比如在一个vc的viewDidLoad中创建）

如果在一个vc的viewDidLoad中创建一个 Autorelease对象，那么该对象会在 viewDidAppear 方法执行前就被销毁了。

###29. BAD_ACCESS在什么情况下出现？

访问了野指针，比如对一个已经释放的对象执行了release、访问已经释放的对象的成员变量或者发消息。
死循环。

###30. 使用block时什么情况会发生引用循环，如何解决？

一个对象中强引用了block，在block中又强引用了该对象。

解决方法是将该对象使用__weak或者在__block修饰符之后在block中使用。

 1. id weak weakSelf = self;
	或者 weak __typeof(&*self)weakSelf = self该方法可以设置宏
 2. id __block weakSelf = self;

或者将其中一方强制制空 `xxx = nil`。

检测代码中是否存在循环引用问题，可使用 Facebook 开源的一个检测工具  [***FBRetainCycleDetector***](https://github.com/facebook/FBRetainCycleDetector) 。

###31. 在block内如何修改block外部变量？

在block外部变量前加__block修饰符

 ```Objective-C
	__block int a = 0;
	void (^foo)(void) = ^{ 
	    a = 1; 
	};
	foo(); 
	//这里，a的值被修改为1
 ```
 
`__block` 所起到的作用就是只要观察到该变量被 block 所持有，就将“外部变量”在栈中的内存地址放到了堆中。进而在block内部也可以修改外部变量的值。

###32. 如何调试BAD_ACCESS错误

1. 重写object的respondsToSelector方法，现实出现EXEC_BAD_ACCESS前访问的最后一个object
 2. 通过 Zombie 
![enter image description here](http://i.stack.imgur.com/ZAdi0.png)

 3. 设置全局断点快速定位问题代码所在行
 
### 55. lldb（gdb）常用的调试命令？

 - breakpoint 设置断点定位到某一个函数
 - n 断点指针下一步
 - po打印对象

### 33. 写一个NSString类型的getter和setter方法

    @property (nonatomic, copy) NSString *name;
    - (NSString *)getterName {
      return [_name retain];
      }
  
    - (void)setterName:(NSString *)name {
    if(_name) {
        [_name release];
    }
    _name = [name retain];
    } 


### 34. 写一个冒泡排序

    NSMutableArray *nosort = [NSMutableArray arrayWithObjects:@3,@12,@34,@22,@56,@34,@23,@64,@36,@47,nil];
    for (int i = 0; i < nosort.count - 1; i++)
    {
        for (int j = 0; j < nosort.count - 1 - i; j++)
        {
            int a = [[nosort objectAtIndex:j] intValue];
            int b = [[nosort objectAtIndex:j + 1] intValue];
            if (a > b) {
                [nosort replaceObjectAtIndex:j withObject:[NSNumber numberWithInt:b]];
                [nosort replaceObjectAtIndex:j+1 withObject:[NSNumber numberWithInt:a]];
            }
        }
    }
    
    NSLog(@"%@",nosort);
    
### 35. `+ (void)load;` 和 `+ (void)initialize;` 有什么用

这两个方法都只被调用一次，load是在类被引用时调用一次，initialize是该类的第一个方法在执行前调用一次。

[详细说明](http://blog.iderzheng.com/objective-c-load-vs-initialize/)

### 36. loadView是干什么用的

loadView是设置自定义view的位置。

部分面试题已经答案来源于[微博@iOS程序犭袁](http://weibo.com/luohanchenyilong/) 

### 37. instancetype和id的异同

相同点：都可以作为方法的返回类型

不同点：

- instancetype可以返回和方法所在类相同类型的对象，id只能返回未知类型的对象；

- instancetype只能作为返回值，不能像id那样作为参数，比如下面的写法：

        - (void)setValue:(instancetype)value {
         }
         
### 38. Runloop和线程的关系

1. runloop和线程是一一对应的
2. runloop是用来管理线程的，当runloop被开启后，线程会在执行完后进入休眠状态，有了任务就会被唤醒执行任务
3. runloop在第一次获取是被创建，在线程结束时被销毁
4. 对于主线程来说，runloop在程序一启动就被创建好了
5. 对于子线程来说，runloop是懒加载，用到是才会创建

### 39. Block和函数指针的区别

相似点：

1. 函数指针和Block都可以实现回调的操作，声明上也很相似，实现上都可以看成是一个代码片段。
2. 函数指针类型和Block类型都可以作为变量和函数参数的类型。（typedef定义别名之后，这个别名就是一个类型）

不同点：

1. 函数指针只能指向预先定义好的函数代码块（可以是其他文件里面定义，通过函数参数动态传入的），函数地址是在编译链接时就已经确定好的。
2. Block本质是Objective-C对象，是NSObject的子类，可以接收消息。
3. 函数里面只能访问全局变量，而Block代码块不光能访问全局变量，还拥有当前栈内存和堆内存变量的可读性（当然通过__block访问指示符修饰的局部变量还可以在block代码块里面进行修改）。
4. 从内存的角度看，函数指针只不过是指向代码区的一段可执行代码，而block实际上是程序运行过程中在栈内存动态创建的对象，可以向其发送copy消息将block对象拷贝到堆内存，以延长其生命周期。