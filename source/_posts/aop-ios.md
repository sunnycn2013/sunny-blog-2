---
layout:     post
title:      "漫谈iOS AOP编程之路 "
subtitle:   "漫谈iOS AOP编程之路"
date:       2015-10-24
author:     "Scenery"
logo: "avatar.png"
keywords: "iOS AOP iOS切面编程 spect Oriented Programming 反射 runtime reflact "
description: "　面向切面编程(也叫面向方面)：Aspect Oriented Programming(AOP),是目前软件开发中的一个热点。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。　　AOP是OOP的延续，是（Aspect Oriented Programming）的缩写，意思是面向切面（方面）编程。　　主要的功能是：日志记录，性能统计，安全控制，事务处理，异常处理等等。　　主要的意图是：将日志记录，性能统计，安全控制，事务处理，异常处理等代码从业务逻辑代码中划分出来，通过对这些行为的分离，我们希望可以将它们独立到非指导业务逻辑的方法中，进而改  变这些行为的时候不影响业务逻辑的代码。"
tags:
    - iOS
    - AOP
    - 电子商务
---



## 1. AOP简介

AOP: Aspect Oriented Programming 面向切面编程。

　　面向切面编程(也叫面向方面)：Aspect Oriented Programming(AOP),是目前软件开发中的一个热点。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

　　AOP是OOP的延续，是（Aspect Oriented Programming）的缩写，意思是面向切面（方面）编程。

　　主要的功能是：日志记录，性能统计，安全控制，事务处理，异常处理等等。

　　主要的意图是：将日志记录，性能统计，安全控制，事务处理，异常处理等代码从业务逻辑代码中划分出来，通过对这些行为的分离，我们希望可以将它们独立到非指导业务逻辑的方法中，进而改  变这些行为的时候不影响业务逻辑的代码。


　　可以通过预编译方式和运行期动态代理实现在不修改源代码的情况下给程序动态统一添加功能的一种技术。AOP实际是GoF设计模式的延续，设计模式孜孜不倦追求的是调用者和被调用者之间的解耦，AOP可以说也是这种目标的一种实现。


假设把应用程序想成一个立体结构的话，OOP的利刃是纵向切入系统，把系统划分为很多个模块（如：用户模块，文章模块等等），而AOP的利刃是横向切入系统，提取各个模块可能都要重复操作的部分（如：权限检查，日志记录等等）。由此可见，AOP是OOP的一个有效补充。

注意：AOP不是一种技术，实际上是编程思想。凡是符合AOP思想的技术，都可以看成是AOP的实现

## 2. iOS中的AOP

利用 Objective-C 的 Runtime 特性，我们可以给语言做扩展，帮助解决项目开发中的一些设计和技术问题。这一篇，我们来探索一些利用 Objective-C Runtime 的黑色技巧。这些技巧中最具争议的或许就是 Method Swizzling 。

其次，用不用就看项目规模和团队规模。有些业务确实非常适合使用AOP,比如log，AOP还可以用来debug

**AOP的优势：**

1. 减少切面业务的开发量，“一次开发终生使用”，比如日志
2. 减少代码耦合，方便复用。切面业务的代码可以独立出来，方便其他应用使用
3. 提高代码review的质量，比如我可以规定某些类的某些方法才用特定的命名规范，这样review的时候就可以发现一些问题

**AOP的弊端：**
1. 它破坏了代码的干净整洁。
（因为 Logging 的代码本身并不属于 ViewController 里的主要逻辑。随着项目扩大、代码量增加，你的 ViewController 里会到处散布着 Logging 的代码。这时，要找到一段事件记录的代码会变得困难，也很容易忘记添加事件记录的代码）

## 3. iOS AOP实战

**玩转 Method Swizzling**

#### 1.事务拦截，安全可变容器
 iOS中有各类容器的概念，容器分可变容器和非可变容器，可变容器一般内部在实现上是一个链表，在进行各类（insert 、remove、 delete、 update ）难免有空操作、指针越界的问题。
最粗暴的方式就是在使用可变容器的时间，每次操作都必须手动做空判断、索引比较这些操作:
```

 NSMutableDictionary *dic = [[NSMutableDictionary alloc] init];
    if (obj) {
        [dic setObject:obj forKey:@"key"];
    }

 NSMutableArray *array = [[NSMutableArray alloc] init];
    if (index < array.count) {
        NSLog(@"%@",[array objectAtIndex:index]);
    }
```

在代码中大量的使用这鞋操作实在是太过于繁琐了，试想如果可变容器自身如何能做这些兼容岂不是更好。可能会想到继承的方法来解决，但是项目中尽可能的避免过多的派生（至于派生的弊端这里就不多说了）；或者想到分类，这里也不尽人意。

Method Swizzling 移花接木

runtime 这里就不多多说了（swift里面已经对这个概念的说法从心转变成了 Reflection<反射>），objective c中每个方法的名字(SEL)跟函数的实现(IMP)是一一对应的，Swizzle的原理只是在这个地方做下手脚，将原来方法名与实现的指向交叉处理，就能达到一个新的效果。

废话少说，直接上代码：

这里使用NSMutableArray 做实例，为NSMutableArray追加一个新的方法


```
@implementation NSMutableArray (safe)

+ (void)load
{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        id obj = [[self alloc] init];
        [obj swizzleMethod:@selector(addObject:) withMethod:@selector(safeAddObject:)];
        [obj swizzleMethod:@selector(objectAtIndex:) withMethod:@selector(safeObjectAtIndex:)];
        [obj swizzleMethod:@selector(insertObject:atIndex:) withMethod:@selector(safeInsertObject:atIndex:)];
        [obj swizzleMethod:@selector(removeObjectAtIndex:) withMethod:@selector(safeRemoveObjectAtIndex:)];
        [obj swizzleMethod:@selector(replaceObjectAtIndex:withObject:) withMethod:@selector(safeReplaceObjectAtIndex:withObject:)];
    });
}

- (void)safeAddObject:(id)anObject
{
    if (anObject) {
        [self safeAddObject:anObject];
    }else{
        NSLog(@"obj is nil");
        
    }
}

- (id)safeObjectAtIndex:(NSInteger)index
{
    if(index<[self count]){
        return [self safeObjectAtIndex:index];
    }else{
        NSLog(@"index is beyond bounds ");
    }
    return nil;
}

```

```
- (void)swizzleMethod:(SEL)origSelector withMethod:(SEL)newSelector
{
    Class class = [self class];
    
    Method originalMethod = class_getInstanceMethod(class, origSelector);
    Method swizzledMethod = class_getInstanceMethod(class, newSelector);
    
    BOOL didAddMethod = class_addMethod(class,
                                        origSelector,
                                        method_getImplementation(swizzledMethod),
                                        method_getTypeEncoding(swizzledMethod));
    if (didAddMethod) {
        class_replaceMethod(class,
                            newSelector,
                            method_getImplementation(originalMethod),
                            method_getTypeEncoding(originalMethod));
    } else {
        method_exchangeImplementations(originalMethod, swizzledMethod);
    }
}
```

这里唯一可能需要解释的是 class_addMethod 。要先尝试添加原 selector 是为了做一层保护，因为如果这个类没有实现 originalSelector ，但其父类实现了，那 class_getInstanceMethod 会返回父类的方法。这样 method_exchangeImplementations 替换的是父类的那个方法，这当然不是你想要的。所以我们先尝试添加 orginalSelector ，如果已经存在，再用 method_exchangeImplementations 把原方法的实现跟新的方法实现给交换掉。

safeAddObject 代码看起来可能有点奇怪，像递归不是么。当然不会是递归，因为在 runtime 的时候，函数实现已经被交换了。调用 objectAtIndex: 会调用你实现的 safeObjectAtIndex:，而在 NSMutableArray: 里调用 safeObjectAtIndex: 实际上调用的是原来的 objectAtIndex: 。

如此以来，一直担心的问题就迎刃而解了，不仅在可变数组、可变字典等容器内都可以做自己想做的事情。

[Demo](http://https://github.com/ccguo/1-SwizzlingMethod-.git)
#### 2. Aspects 一个基于Objective-c的AOP开发框架
###### 业务埋点、日志打印分离

相信大多童鞋们在重构代码的时间经常会从一些问题入手，例如轻量级controller、MVVM等，这些无非是对原有逻辑进一步抽象、区分、分离，重新抽象数据模型、viewmodel；相关代码放入分类；考虑业务层次抽取剥离父类；mananger、factory等。经历一大翻工作controller 中代码终于减少了，但是仍旧留下一堆的埋点、日志log的相关代码。

Aspects是一个很不错的 AOP 库，封装了 Runtime ， Method Swizzling 这些黑色技巧，只提供两个简单的API：

```
+ (id<aspecttoken>)aspect_hookSelector:(SEL)selector
                         withOptions:(AspectOptions)options
                      usingBlock:(id)block
                           error:(NSError **)error;
- (id<aspecttoken>)aspect_hookSelector:(SEL)selector
                     withOptions:(AspectOptions)options
                      usingBlock:(id)block
                           error:(NSError **)error;

//使用 Aspects 提供的 API，我们之前的例子会进化成这个样子

@implementation UIViewController (Logging)+ (void)load
{
   [UIViewController aspect_hookSelector:@selector(viewDidAppear:)
                             withOptions:AspectPositionAfter
                              usingBlock:^(id<aspectinfo> aspectInfo) {        NSString *className = NSStringFromClass([[aspectInfo instance] class]);
       [Logging logWithEventName:className];
                              } error:NULL];
}
```
相对来说如果想要捕捉到viewDidAppear 的log打印，或者是页面PV的统计上报，我们从原有的业务中将这部分代码剥离出来，掉换IMP指向以后我们可以在usingBlock 内做自己想做的事情， 这样就能达到上述想要的目的了。

Aspects扩展使用：

页面的PV统计，事件点击统计，可以事先写在配置文件里面：

```
{        @"MainViewController": @{
           GLLoggingPageImpression: @"page imp - main page",
           GLLoggingTrackedEvents: @[
               @{
                   GLLoggingEventName: @"button one clicked",
                   GLLoggingEventSelectorName: @"buttonOneClicked:",
                   GLLoggingEventHandlerBlock: ^(id<aspectinfo> aspectInfo) {
                       [Logging logWithEventName:@"button one clicked"];
                   },
               },
               @{
                   GLLoggingEventName: @"button two clicked",
                   GLLoggingEventSelectorName: @"buttonTwoClicked:",
                   GLLoggingEventHandlerBlock: ^(id<aspectinfo> aspectInfo) {
                       [Logging logWithEventName:@"button two clicked"];
                   },
               },
          ],
       },        @"DetailViewController": @{
           GLLoggingPageImpression: @"page imp - detail page",
       }

```

```
@implementation AppDelegate (Logging)
+ (void)setupLogging{
     [AppDelegate setupWithConfiguration:config];
}

+ (void)setupWithConfiguration:(NSDictionary *)configs
{    // Hook Page Impression
   [UIViewController aspect_hookSelector:@selector(viewDidAppear:)
                             withOptions:AspectPositionAfter
                              usingBlock:^(id<aspectinfo> aspectInfo) {                                       NSString *className = NSStringFromClass([[aspectInfo instance] class]);
                                   [Logging logWithEventName:className];
                              } error:NULL];    // Hook Events
   for (NSString *className in configs) {
       Class clazz = NSClassFromString(className);        NSDictionary *config = configs[className];        if (config[GLLoggingTrackedEvents]) {            for (NSDictionary *event in config[GLLoggingTrackedEvents]) {
               SEL selekor = NSSelectorFromString(event[GLLoggingEventSelectorName]);
               AspectHandlerBlock block = event[GLLoggingEventHandlerBlock];
               [clazz aspect_hookSelector:selekor
                              withOptions:AspectPositionAfter
                               usingBlock:^(id<aspectinfo> aspectInfo) {
                                   block(aspectInfo);
                               } error:NULL];
           }
       }
   }
}

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {    // Override point for customization after application launch.
   [self setupLogging];    return YES;
}

```

[Demo](https://github.com/okcomp/AspectsDemo)
#### 3. NSProxy 实现技术



[method-swizzling](http://nshipster.com/method-swizzling/)

[method replacement for fun and profit](https://www.mikeash.com/pyblog/friday-qa-2010-01-29-method-replacement-for-fun-and-profit.html)

[Aspects](https://github.com/steipete/Aspects)