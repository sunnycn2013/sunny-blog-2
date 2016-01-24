---
layout:     post
title:      "Xcode 7智能测试化工具XCTest学习"
subtitle:   "Xcode 7智能测试化工具XCTest学习"
date:       2015-11-29
header-img: "img/post-bg-alitrip.jpg"
description: "单元测试在程序里面可以理解一个模块一个方法，在每个可能存在的模块都进行测试，确保每个模块都没有问题，从而提高整体程序的质量
谈的单元测试此处不免要聊起一个新的概念，代码覆盖率，最早最一次听到这个词的时间觉得很可笑(难道这个也需要统计)，故名思义 代码覆盖率 ＝ 实际执行的代码行数 / 整个工程总代码行数，直白来讲就是这样一个数值，上述谈过，单元测试的目的除了讲程序分成各个最小的单元独立去测试确保正确以外，还有一个就是代码覆盖率问题，如果说发到线上的产品有相当一部分代码从来都没有执行过，这个问题是相当危险的下面说下Xcode中代码覆盖率的问题，Xcode7以前代码覆盖率统计比较麻烦，Xcode7以后Apple推出了更为人性化的工具"
tags:
    - iOS
    - 电子商务
    - XCTestCase
---



## 1. 单元测试简介

####  1.1、Unit Testing: 单元测试

 测试这个词很容易理解，那么什么是单元(Unit)呢？一个单元指的就是应用程序中可以测试的最小单元。一组源代码可以测试，一般要求有明确的输入与输出。因此一般来说源代码中明确的包含输入输出的每一个方法被认为一个测试的单元(一个case)。注意，这里的输出并不局限于方法的返回值对输入参数的改变，也包括方法在执行过程中改变的任何数据。

单元测试在程序里面可以理解一个模块一个方法，在每个可能存在的模块都进行测试，确保每个模块都没有问题，从而提高整体程序的质量。

####  1.2、单元测试的目的
单元测试的目的是将程序中所有的源代码，隔离成最小的可测试单元，以确保每个单元的正确性，如果每个单元都能保证正确，就能保证应用程序整体相当程度的正确性。另一方面测试脚本本身就是被测试代码的实际使用代码，这对于开发者理解被测试单元的使用是用帮助的。

测试是分黑盒测试和白盒测试(概念此处不在解释)，单元测试其实就是一种白盒测试，开发者对现有已经实现的模块自己写对应测试脚本进行测试，这中间还包含测试用例的设计。相对来说还是由开发者自己来完成白盒测试，然后在交由测试团队进行黑盒测试，这样也更加有助于提升测试流程的完整性，最终提高产品的质量。

 单元测试的内容：
 
> * 单元测试的测试目的
> * 模块接口测试
> * 局部数据结构测试
> * 路径测试
> * 错误处理测试
> * 边界测试

在现有的开发工作中，我们一般都会忽略掉单元测试的重要性，功能开发完成以后开发者拿到现有的测试用例，直接针对每条用例进行手工的测试，测试通过就进行提测，之后测试人员还是重复手工测试的流程、数据的mock、专项测试等，这样以来白盒测试的流程有时间份量会变的很低，开发人员不知道自己模块代码的覆盖路问题，更多的时间可能某些代码一直到到上线都从来没有跑过，以至于到了真实环境下会产生一些意想不到的问题，这样以来风险极高，整体来说单元测试还是至关重要的。

下面介绍一下Xcode7 中现有的一些测试工具：


## 2. Xcode7中的UnitTest

####  2.1、XCTest介绍

本文主要基本Xcode7来讲解，至于xcode 新功能的历史各位自己去趴去，此处只讲解如何使用，废话少说，下面直接入正题。

最新的Xcode7中是包含了UITest  UnitTest工具的，这个可以在你创建工程的时间勾选对应的选项，也可以直接通过addTarget的形式来完成，

#####  2.1、XCTestCase简介
如果项目创建的时间勾选了UnitTest(从名字上看就是Apple提供的官方的一个单元测试工具) ，我们可以看到工程里面是多了一个目录，默认多了一个类， 如图：

![选择XCTest](/images/2015-12-16/post-xctest-case/xctest-case-createpro.png)


XCTest时Apple官方提供一个测试工具，一个内置的测试框架，从工程里面可以看到，一个“应用名称”的group，我们直接可以使用commond+R 来远行，一个测试的target我们可以使用commond+U来远行测试target,在测试target的目录下会有一个默认的“应用名称”＋Test的类，这个类只有.m没有.h,继承于XCTestCase，使用commond+U即可运行。

默认测试类里面有以下方法：

```
//方法在XCTestCase的测试方法调用之前调用，可以在测试之前创建在test case方法中需要用到的一些对象等
- (void)setUp ;
//当测试全部结束之后调用tearDown方法，法则在全部的test case执行结束之后清理测试现场，释放资源删除不用的对象等
- (void)tearDown ;
//测试代码执行性能
- (void)testPerformanceExample 

```

####  2.2、XCTestCase使用

XCTestCase的初始化不是用户控制的,开发者无需手动针对XCTestCase的subclass进行 alloc 、init或者调用静态方法初始化的操作，针对一个功能块的单元测试(针对某个class)，只需要单独给为这个类创建一个继承于XCTestCase，在这个文件内实现上述基本函数以后(一半系统会默认创建这三个函数)，其实的逻辑只需要开发者自行定义以“test"开头的函数，然后在内部实现自己针对某个函数、返回数值结果、操作等的测试脚本即可，commond+U执行的时间，单元测试会自动执行这些test打头的函数，当函数头上出现蓝色的标记则表明测试通过，否则直接报红色错误。

XCTest测试范畴：

> * 基本逻辑测试处理测试
> * 异步加载数据测试
> * 数据mock测试

##### XCTest常用基本测试工具
XCTest常用的一些判断工具都是以XCT开头的，如：

```
  //断言,最基本的测试，如果expression为true则通过，否则打印后面格式化字符串
  XCTAssert(expression, format...)
  
  //Bool测试:  
  XCTAssertTrue(expression, format...)
  XCTAssertFalse(expression, format...)
  
  //相等测试
  XCTAssertEqual(expression1, expression2, format...)
  XCTAssertNotEqual(expression1, expression2, format...)
  
  //double float 对比数据测试使用
  XCTAssertEqualWithAccuracy(expression1, expression2, accuracy, format...)
  XCTAssertNotEqualWithAccuracy(expression1, expression2, accuracy, format...)
  
  //Nil测试，XCTAssert[Not]Nil断言判断给定的表达式值是否为nil
  XCTAssertNil(expression, format...)
  XCTAssertNotNil(expression, format...)
  
  //失败断言     
  XCTFail(format...)
  
```

##### XCTest异步测试

Xcode单元测试中加入的最令人兴奋的功能也许就是类XCTestExpression类带入的异步测试了。现在测试可以等待指定长度的时间，一直到某些条件符合的时候在开始测试。而不用再写很多的GCD代码控制。

要使用异步测试，首先用方法expectationWithDescription创建一个expection

```
let expectation = expectationWithDescription("...")
```

之后，在方法的最后添加方法waitForExpectationsWithTimeout，指定等待超时的时间和指定时间内条件无法满足时执行的closure。

```
waitForExpectationsWithTimeout(10) { (error) in
    // ...
}
```

剩下的就是在异步测试剩下的回调函数中告诉expectation条件已经满足。

expectation.fulfill()
如果在测试中有多个expectation，则每个expectation都必须fulfill，否则测试不通过。

```
- (void)testFetchRequestWithMockedManagedObjectContext
{
    MockNSManagedObjectContext *mockContext = [[MockNSManagedObjectContext alloc] initWithConcurrencyType:0x00];
    
    let mockContext = MockNSManagedObjectContext()
    NSFetchRequest * fetchRequest = [[NSFetchRequest alloc] initWithEntityName:@"User"];
    let fetchRequest = NSFetchRequest(entityName: "User")
    fetchRequest.predicate = [NSPredicate predicateWithFormat:@"email ENDSWITH[cd] apple.com"];
    fetchRequest.predicate = NSPredicate(format: "email ENDSWITH[cd] %@", "apple.com")
    fetchRequest.resultType = NSDictionaryResultType;
    fetchRequest.resultType = NSFetchRequestResultType.DictionaryResultType
    var error: NSError?
    NSError *error = nil;
    NSArray *results = [mockContext executeFetchRequest:fetchRequest error:&error];
    let results = mockContext.executeFetchRequest(fetchRequest, error: &error)
    XCTAssertNil(error, @"error应该为nil");
    XCTAssertEqual(results.count, 2, @"fetch request应该只返回一个结构");
    NSDictionary * result = results[0];
    XCTAssertEqual(result[@"name"], @"张三", @"name应该是张三");
    NSLog(@"email : %@",result[@"email"]);
    XCTAssertEqual(result[@"email"], @"zhangsaan@apple.com", @"email应该是zhangsan@apple.com");
    
}


```

##### XCTest Mock
数据mock

####  2.3、Xcode7 Code Coverage介绍

谈的单元测试此处不免要聊起一个新的概念，代码覆盖率，最早最一次听到这个词的时间觉得很可笑(难道这个也需要统计)，故名思义 代码覆盖率 ＝ 实际执行的代码行数 / 整个工程总代码行数，直白来讲就是这样一个数值，上述谈过，单元测试的目的除了讲程序分成各个最小的单元独立去测试确保正确以外，还有一个就是代码覆盖率问题，如果说发到线上的产品有相当一部分代码从来都没有执行过，这个问题是相当危险的(问题大家可以各自猜测，相信这个问题不是很陌生)。

##### Code Coverage工具使用
下面说下Xcode中代码覆盖率的问题，Xcode7以前代码覆盖率统计比较麻烦，Xcode7以后Apple推出了更为人性化的工具，既然学习就学习最为流行的，过去的麻烦就当随之过去吧，何必留在痛苦回忆里(有兴趣的可以参考 [Apple DOC](https://developer.apple.com/library/ios/qa/qa1514/_index.html)  [Xcode代码覆盖率测试工具](http://www.cocoachina.com/ios/20150915/13163.html))


**注： 传统统计覆盖率的方法会做一部分Xcode配置，最终打出一个叫“插庄包”的包，这个包仅为做统计使用，如果要上生产环境，切忌将配置一定要关掉在从新打包上传，否则后患无穷...**

Xcode7 提供了一个内置的Code Coverage工具组件，废话不说，下面看使用方法：

1、首先需要在product->scheme->Edit Scheme里面将Code Coverage模式打开，选中为debug模式，如图:

![Edit Scheme](/images/2015-12-16/post-xctest-case/xctest-case-selectschem.png)

![Edit Scheme](/images/2015-12-16/post-xctest-case/xctest-case-editschem.png)

2、打开Code Coverage模式之后，打开某个测试类，commond+U 运行，如果测试通过，测试脚本的函数头上会出现一个绿色的标志(相反如何哪一个方法测试没有通过，则会提示一个红色错误)，如下:

![Edit Scheme](/images/2015-12-16/post-xctest-case/xctest-case-testsucc.png)

3、打开Xcode左边窗口的Report Navigator，找到 Project Log,选择最近一次的log选项，最近一次是刚才的一个Test Log,选择中这个Log实例，可以看到一下界面,
如图:

![Edit Scheme](/images/2015-12-16/post-xctest-case/xctest-case-testlog.png)

然后在tab中选中 Coverage，此时你可以看到大致的代码执行覆盖情况，如果指示条是满的则代表该类代码全部跑过一遍。

![xctest-case-coverage.png](/images/2015-12-16/post-xctest-case/xctest-case-coverage.png)

4、双击你想要查看的类，此处选择查看UATrackDao，打开后既可以看到刚刚的测试中有哪些代码是执行过的，那些代码时未执行的，橘黄色的代表还未执行的，执行过的每一行后面会有一个序号代表这行代码在刚才的测试过程中执行的次数。如果有未执行的，可根据具体的情况调整对应的测试脚本，继续测试，最终确保每一行代码都能正确执行,如图:

![Edit Scheme](http://ccguo.gitcafe.io/blog/2015-12-16/post-xctest-case/xctest-case-coverageItem.png)

![Edit Scheme](/images/2015-12-16/post-xctest-case/xctest-case-trackDao.png)

## 3. Xcode7中的UITest

#### UnitTest简介

任何软件开发中，自动化UI测试都是很重要的(UI自动化测试的好处此处就不再多说了),iOS平台在以往是通过UIAutomation来完成自动化UI测试的，它的测试用例是javascript写的(Instruments中提供了该功能)，这个过程深奥繁琐，需要自行编写对应的测试脚本，速度慢，学习成本高(关于Automation自动化测试概念大家可以查看相关的资料，Automation自动化测试在各大平台都有应用，在大型的软件开发测试过程的确的确可以节省大量的手工测试人员，大大提高软件测试的成本与效率，在最新Xcode7本文推荐使用Apple提供的最新的工具UITest)。

Apple在Xcode 6中又新增了UnitTest之外，到了Xcode 7 Apple从新提供了一个新的框架UITest，这个主要是用来测试UI的，UnitTest涌来测试功能逻辑代码，UITest专门用来测试UI。

Xcode 7已经集成了UITest，UITest允许你找到UI元素并与之交户，还能检查UI的属性和状态，并且UITest也已经集成在Xcode 的测试报告了，可以和单元测试一起执行，和UnitTest一样我们可以在检查UI的时间执行断言。

创建UITest target，同样会生成一个“项目名称”＋UITest的group，UITest target可以在创建工程的时间勾选，也可以在工程中手动添加，在 “项目名称”＋UITest 分组下，我们可以看到系统会帮我们默认生成一个UI测试类，这个类也同样是继承于XCTestCase的。由此可见，在iOS中无论是单元测试还是UI测试，都是基于XCTestCase的。

#### UnitTest UI测试

创建模态视图，我们选择从第一个VC通过点击按钮的形式push到第二个VC

![Edit Scheme](/images/2015-12-16/post-xctest-case/xctest-case-UItestIB.jpeg)

创建UITest target，我们对上述UI进行测试 如图选项:

![Edit Scheme](/images/2015-12-16/post-xctest-case/xctest-case-createUITestSelectItem.jpeg)

![Edit Scheme](/images/2015-12-16/post-xctest-case/xctest-case-createUITestPro.jpeg)


打开UATrackDemoUiTest.m,创建 - (void)testUI,同时将光标留在函数内

![Edit Scheme](/images/2015-12-16/post-xctest-case/xctest-case-uitestStart.jpeg)

点击下面的红色按钮，开始recorder操作，程序运行起来后，点击界面上的按钮，程序会push到一个新的页面，这个时间会看到到刚才的鼠标光标处自动生成了一部分代码，重复操作，每次都会生成新的代码,如图：

![Edit Scheme](/images/2015-12-16/post-xctest-case/xctest-case-uitestcode.jpeg)


从新点击小红点按钮，此时结束recorder操作，commond+U 运行测试，此时刚才的一连串动作会一步一步连续执行下来:

![Edit Scheme](/images/2015-12-16/post-xctest-case/end.gif)

此处声明: 第一次点击红色的recorder按钮，然后手动操作会自动生成测试脚本，第二次commond+U是进行测试UI

##### UnitTest工具拓展

XCTest一共提供了三种UI测试对象

> * XCUIApplication   当前测试应用target
> * XCUIElementQuery   定位查询当前UI中xctuielement的一个类
> * XCUIElement   UI测试中任何一个item项都被抽象成一个XCUIElement类型

当我们获取了录制生成的代码以后，根据UITest提供的三种对象，我可以在此来对测试代码进行修改，调试
UITest中同样适用以下断言等：

```

  XCTAssert(expression, format...)
  
  //Bool测试:  
  XCTAssertTrue(expression, format...)
  XCTAssertFalse(expression, format...)
  
  //相等测试
  XCTAssertEqual(expression1, expression2, format...)
  XCTAssertNotEqual(expression1, expression2, format...)
  
  //double float 对比数据测试使用
  XCTAssertEqualWithAccuracy(expression1, expression2, accuracy, format...)
  XCTAssertNotEqualWithAccuracy(expression1, expression2, accuracy, format...)
  
  //Nil测试，XCTAssert[Not]Nil断言判断给定的表达式值是否为nil
  XCTAssertNil(expression, format...)
  XCTAssertNotNil(expression, format...)
  
  //失败断言     
  XCTFail(format...)
.....


```

关于Xcode 7 UnitTest的问题就讲到此处，希望有兴趣的同学大家共同交流...


## 4. 小结

#### 1.总结现有问题，分享心得
Xcode6的内置工具终于足够的好了。也就是说即使是很大的APP也没有必要为了单元测试的代码覆盖率而排斥Xcode内置的测试工具。无论什么样的测试，XCTest的各种断言、expectation和性能测试都足够应对。但是无论多好的工具，都需要用好才行。

如果你在测试iOS或者OS X的APP，开始为自动添加的测试类添加一些断言并按下Command+U。你一定会发现感觉这些工具让你的测试方便不少 。





[WWDC2015 What's New in Xcode ](https://developer.apple.com/videos/play/wwdc2015-104/)
[WWDC2015 UI Testing in Xcode ](https://developer.apple.com/videos/play/wwdc2015-406/)
[WWDC2015 Testing in Xcode 6 ](https://developer.apple.com/videos/play/wwdc2014-414/)
[Mattt Thompson Blog](http://nshipster.com/xctestcase)
[使用Xcode自带的单元测试](http://www.mincoder.com/article/3650.shtml)
[method-swizzling](http://nshipster.com/method-swizzling/)
[Parse 开源代码](https://github.com/ParsePlatform/Parse-SDK-iOS-OSX)