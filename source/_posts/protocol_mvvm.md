---
title: 一种基于面相协议的MVVM设计思路
subtitle: "一种基于面相协议的MVVM设计思路"
keywords: "一种基于面相协议的MVVM设计思路 "
author:     "Scenery"
date: 2016-01-17
description: "其实关于iOS设计架构的文章最近已经很多了，我之前也曾经发表过一篇草稿基于面相协议MVP的软件设计。在这之前其实已经有不少的博主对于各种思路进行了大量的阐述，之所以写这遍文章的原因，是因为之前的一篇文章MVVM没你想象的那么的好,这遍文章从侧面描述了MVVM本质，但是不乏也缺少一些针对性的理解，在此我想真正的阐述一下自己的一些看法与见解，希望能与大家多多交流，一谈到MVVM可能好多人都认为又是一片关于架构的文章，也有一些人基本上就不厌其烦了(脱胎与MVC，将控制器剥离，轻量级控制器等)。"
tags:
    - iOS
    - MVVM
    - MVV
    - MVP
    - iOS开发框架
    - 电子商务
---

* 作者: sunnycn
* 原创: 一种基于面相协议的MVVM设计思路
* 出处: 风之痕的博客(中文原创精品)

### 导语

> 其实关于iOS设计架构的文章最近已经很多了，我之前也曾经发表过一篇草稿[基于面相协议MVP的软件设计](www.baidu.com)。在这之前其实已经有不少的博主对于各种思路进行了大量的阐述，之所以写这遍文章的原因，是因为之前的一篇文章[MVVM没你想象的那么的好](http://www.cocoachina.com/ios/20160112/14949.html),这遍文章从侧面描述了MVVM本质，但是不乏也缺少一些针对性的理解，在此我想真正的阐述一下自己的一些看法与见解，希望能与大家多多交流。

### 一、绪言

一谈到MVVM可能好多人都认为又是一片关于架构的文章，也有一些人基本上就不厌其烦了(脱胎与MVC，控制器剥离，轻量级控制器等)。在一大片的嘈杂声过后，不少人对于这个名词甚至都觉得危言耸听，至此有了文章说MVVM没你想象中的那么神往，更有甚至对其使用理解也是各有千秋。

一个成熟的设计思维是在提出思想以后，然后在经历不断的使用、操作并最终得到验证后从新升华出的新的思维总结。任何一套理论思维都是如此，不经历生产的实际验证，无法证明其可靠性，人类工业革命每次大规模的进步都是如此。MVVM这个思维其实不算是一个新的构思了，大量资料显示它出自C#,最终被应用到iOS。说到此处，我个人觉得MVVM可能在其他平台中的使用已经渐进成熟，但是在iOS中大家对于MVVM的理解运用确实各有异同。

谈到C#,其实我本人一直很钦佩这个平台的开创者。虽然说现有的service平台技术各有千秋，但是千遍一律，万变不离其宗。在服务端开发平台里，基础的三大平台的一些逻辑思维是值得每一个人去深思的。谈到处理业务逻辑服务端首档其冲。iOS之所以出现越来越多的设计思维方面的争议，究其原因无非是业务逻辑的处理越来越偏向于客户端、前端也需要担当一部分处理业务的责任，在这之前客户端是从不考虑逻辑处理的。我们在设计这一块区域的时间可以去借鉴一下服务端的一些基本的模式。无论是MVVM、MVP这些思维在上述平台中最早都曾出现过，学以致用，刨根揭底、物尽其用、极尽升华...

### 二、百家争鸣中MVVM

#### 2.1 ViewModel究其何物

MVVM新增了一个叫ViewModel的东东，其实最早看到iOS中使用MVVM，我自己也很模糊。ViewModel这个东西看起来很重，但是里面究竟要做什么、怎么做；它到底扮演了一个什么样的角色、到底该如何使用？这一点仍有争议。在不少文章以及使用中，可能最多看到的仍旧是在Bundle文件设计目录上一定要多出一个叫 *ViewModel的东西，然后将Controller中一部分逻辑直接玻璃出来放到这个class中，这样的文件结构决定了这是一种叫MVVM的设计架构。

曾经有一段关于ViewModel的解释：ViewModel是一种介于Model和Controller之间的一个抽象模型，他能帮助控制器起到一个协调Model的作用，从而将一部分逻辑从控制器中剥离进而达到解耦合目的。

可能就是这句解释最终产生了臆想不到的结果，从此以后ViewModel担起了处理数据显示、网络请求获取数据、表单验证更甚之可以放到这里面的任务全部放进来。ViewModel成了万金油，最终MVVM比起MVC只不过是多了一个class文件，然后拖进去一部分代码而已。

#### 2.2 ViewModel承担了更多的责任

ViewModel用于存放用户输入的逻辑、视图的表示逻辑、网络请求、以及其他不确定的各种代码逻辑。就是这样的一系列胶浊物，让人不禁对这种新的设计方式产生疑问。这样的结构能否满足并需求并达到彻底解耦合的目的，不同的逻辑分支的胶浊物从新被分配到一个新的类文件中是否真的解决了问题。太过于模糊的ViewModel是否真的可好；如果业务场景在增加一倍ViewModel中的逻辑是否仍旧清晰、是否会混淆等等。

ViewModel现有的这种理解并没有真的改变之前的一些问题，单独的从文件结构上来划分原有的逻辑，不禁让人产生深思。[MVVM没你想象的那么的好](http://www.cocoachina.com/ios／20160112/14949.html)中这遍文章的确也谈到了这一点。ViewModel最终的臃肿无法最终解决问题，这一点与Controller臃肿无非是五十步笑百步的问题。

### 三、个人理解的MVVM解耦

#### 3.1 传统业务模型的解决之道

谈到此处，我们暂时先抛开iOS 、抛开前端。让我们从新回到原始的软件设计模型中去...

最初的最初，我们开始使用文件来存储数据，之后慢慢有了数据库，到后来关系型数据库。我们开始存储时代的来临。大量的数据开始存储在数据库中。随着业务的持续增长，我们渐渐的逐步开始将一部分逻辑最终以抽象关系模型数据的方式存储在DB，从此以后渐渐诞生了业务模型这个概念。

随着业务模型的深入，我们不得不开始处理大量的逻辑，同时软件开发的最终的目的都是为了一个所谓的呈递、操作、处理业务的目的。我们将可操作、可使用的一个可执行程序呈递给用户，于是有了软件的使用、商业化软件市场的诞生。

随着业务模型的进一步拓展，业务愈加复杂，我们发现在开发、使用过程中难免遇见一些业务层次的变更，可能是存储层变更，会影响到我们的软件，逻辑层的变更、用户交互层的改变等都会影响到我们的软件的维护、升级。

于是我们在开发的模型中渐渐考虑到了软件的设计思维的剥离、抽取。这一点主要从两个方面来完成入手，一是从业务逻辑模型这条渠道进行分离(最终暂时理解为基本三层架构)；二是从软件操作界面上进行逻辑抽取玻璃(最终诞生了基本的MVC模型)，这些设计思维的前身都是从传统的模型中一步步走过来的。

随着时间的与日增场，为了满足业务对接式的需求。满满诞生了webservice、service这些服务型的应用来满足互联网网络传输过程的各种数据对接。

以上大概就是传统软件进阶之路...

#### 3.2 MVVM究竟该如何安置

MVVM无非是因为原有的MVC无法满足现有的开发以及维护产生的一个临时做法，我们只是想到抽离了这个东西，但是没有具体的定义该如何去使用。为此大量的文章并没有具体的谈到这一点。这一点还要归咎于app这个概念。如果我们暂且仍旧理解app只是一个展示性的界面，压根从不处理各种业务逻辑就不会有上述问题了。关键是事实并非如此，我们面临的关于处理业务逻辑的问题只能说是越来越多。

业务逻辑大量的在客户度中开始追加，我们暂且考虑ViewModel是一个业务处理模块，viewModel只处理业务数据逻辑，视图显示一概不要。ViewModel处理的数据究竟从哪里来；我们需要一个对应的数据获取层。ViewModel只有通过某种工具拿到数据才能对数据进行加工、逻辑处理，反过来如果说客户端要写入什么类型的数据，ViewMode作为数据逻辑处理工具需要提供对应的工具入口，数据有进有出，这才符合业务逻辑处理层次的概念。

ViewModel只负责数据处理，数据交付谁来处理。我们想到了数据传输层(DAL),ViewModel不会管理Http Request对象，它需要一个专门的工具来完成这个工作。我们开创了数据传输层，这一点根server端有区别。传统的DAL层负责支持从数据库端的增删改查操作，我们客户端的数据传输层（下文API层）则需要负责与网络service的访问对接，因为网络service才是客户度真正的数据存储源。

传统业务逻辑处理：

```
三层
    - Model    －－
    - DAL         ｜
    - BLL         ｜
    -             ｜
MVC :             ｜
    - Model    - -
    - View
    - Controller
    
```

谈到数据Model，此处我们仍旧坚持不会抛弃传统模式下的Model设计，只不过Model层我们做了一点点改动，对应的每个属性Model我们在此追加了协议层这一概念，暂时可以认为楼层中的每一个对象都是一个Model，我们针对这些数据设计了一个协议，Model具体由哪个对应的View显示绘制，由Model自身实现的协议决定。这一点大家可以参照我之前发表的文章[基于面向协议MVP模式下的软件设计－iOS篇
](http://www.cocoachina.com/ios/20151223/14768.html)
这样做的好处就是在控制器胶水层面，我们针对处理tableview各种代理的的时候，我们统一认为所有的数据模型都是id<xxxProtocol>类型统一去做粘合处理。此处的设计规范采用抽取公共属性，然后抽象到协议，再由具体的对象实现对应的协议去完成。成功的避开了继承，这一点与最近几天swift开发大会中喵神王巍提到的观念相吻合。

#### 3.3 页面层如何安置

解决了ViewModel的问题，我们来看页面展示层的概念，这一点我们要严格遵守MVC的通知界面、更新数据的策略。View永远不要直接通知Model，Model数据发生变化绝对禁止直接通知View进行显示。

谈到这一点可能ReactiveCocoa做的很好，但是其实一直以来好归好，我们本人不太喜欢整个工程完全抛弃Apple原有的代码编程规范，去采用一种新的编程模式。这一点我个人更青睐于Apple自身。

ReactiveCocoa在处理View－>Model，Model－>View已经封装慌的很好了；你可以直接在控制器内将需要通过信号型变量关联的Model和View链接起来，控制器仍旧起到链接Model和View的胶水作用，但是Model和View并没有直接交互，通过发送信号间接的进行交互。

在MVVM中，如有类似需求，我本人更倾向于KVO来完成,具体参照([Let’s discuss MVVM for iOS](https://medium.com/@ramshandilya/lets-discuss-mvvm-for-ios-a7960c2f04c7#.qkjcbjnpr))。在页面级别，仍旧采用控制器持有ViewModel的惯例，这一点根大家一观的做法相同。控制器的交互数据获取都从ViewModel来获取，我们暂时理解ViewModel就是一代工厂，既不拥有数据、也不具体使用数据，只负责加工处理数据。我们在控制器层可以针对数据来源的某些属性采用KVO增加观察者的形式做数据监听，当数据来源的这些属性发生变化时间，控制器能及时的收到通知并更新UI。这个过程中数据变化直接通知控制器然后由控制器直接通知View更新界面，完全符合MVC观念。

当UI界面上通过某个点击事件发生回传，我们仍旧采用事件回调首先传递回到控制器，然后由控制器拿到响应的数据源交付ViewModel去处理加工数据，最终拿到处理结果。此时KVO生效，控制器中直接感应到通知的回调去更新对应的UI界面。整个工作流程完全负责MVC的原始观念。

整个设计思路如下：
![MVVM](http://7xkmox.com1.z0.glb.clouddn.com/mvvm.png)
#### 3.4 购物车设计规范

一种基于面相协议的MVVM
```
- API                       与serve端API对接工具	(相当于客户端的数据访问层)
	- BaseAPI               提供基础的服务
	- CartAPI               基于购物车的API集合，提供相关的API服务
- Protocol                  基础协议层
	- CartProbeProtocol    探针器 主要为所有的cell提供计算cell高度、呈递数据、传入事件处理
	- CartRenderProtocol   渲染协议 主要为Model提供渲染目标视图
	- CartFloorProtocol    通用Model楼层协议，为大楼层的提供公共属性
- Model                    实体层 Entity  (数据实体层)
	- CartModel            购物车基础实体集合
	- CartSkuModel         商品Model
	- CartHeaderModel      分类title Model
- View                     基础UI层 主要提供基础的视图 (基础View层)
	- view	               自定义View
- Cell                     Cell (cell集合)
	- CartTableViewCell    公共集合
	- CartHeaderTableCell  头视图cell
- ViewModel                ViewModel 主要完成针对控制器的拆分 (相当于业务；逻辑层)
	- CartViewModel        内部封装了购物车相关的各种业务逻辑（数据拉取、数据更新、数据删除）
- Controller 		       控制器胶水层  (控制器层)
```
###### API层
客户端API层职能类似于传统的数据访问层，这里封装了基本的网络请求对象、service地址以及，回调处理等，等操作。API层主要用于ViewModel层的调用，关于客户端请求交互的模块全部都是由API层来完成，此处完主要成了业务模块针对网络请求层次的剥离抽取。

```
@class RequestModel;
@interface BaseAPI : NSObject
{
    AFHTTPSessionManager *_manager;
    RequestModel         *_requestModel;
}

@property (nonatomic,strong,readonly) RequestModel *requestModel;

- (NSString *)serverURL;
- (NSString *)functionID;
- (NSDictionary *)params;

- (void)startWithCompletionBlockWithSuccess:(void (^)(NSDictionary *content))success
                                    failure:(void (^)(NSError *error))failure;
- (void)stop;

@end

@interface RequestModel : NSObject

@property (nonatomic,strong) NSString *serverURL;
@property (nonatomic,strong) NSString *functionID;
@property (nonatomic,strong) NSDictionary *params;

@end

```

##### 基础协议层
这个地方主要提供了三个对应的协议，之所以使用抽象协议，此处省略了继承。

CartProbeProtocol   主要交付TableViewCell来实现
CartRenderProtocol  主要交付具体要绘制的Model来实现
CartFloorProtocol   主要交付大楼层符合Model来实现

此处使用协议的形式，主要是为了能够完成对象的协作性调用，在粘合剂控制器中，我们可以统一采用
id<Cart***Protocol>的形式来操作具体的对象，而无需再关心具体的Model类型以及cell类型。

##### Model 、Cell 、View层
这些层次与MVC类似

##### ViewModel层
ViewModel层是一个基于业务逻辑层面的剥离，主要提供一些增删改查的操作

##### Controller器层次
控制器主要起到胶水作用，控制器会组织View的与数据的融合。控制器中主要采用两种手段：
一种是基本的楼层我们采用id<Cart***Protocol>的形式来统一操作，这一点遵循了运行时语言的特性（运行时才决定具体的对象的类型）。
另一种是针对特定属性的功能，通过分类的形式来完成，尽最大限度的剥离控制器的逻辑。
备注：（如关于导航上相关的所有逻辑，我们统一将这块的UI、操作、衔接、回调统一放在一个当前控制器的一个分类文件去完成，这样从文件分割上进一步分割原有控制器）。

<<strong>创建分类的时间，最大限度的不要为系统级别的class创建分类，要尽量针对自己的某个自定义class创建分类，这一点尽量避免为系统级class创建分类引入多于的链接类型开销(有兴趣的可以研究下runtime相关东西，category的实现最后也是要讲分类方法加入method_list队列去的，如果分类给NSObject创建了分类，在世纪使用场景下，系统会给每个类都田间对应的方法，产生多于的函数链接、存储开销) </strong>>
如下：

```
//#pragma mark - UITableViewDelegate,UITableViewDataSource
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView 
{
    return [self.cartViewModel.list count];
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    id<CartFloorProtocol> floor = self.cartViewModel.list[section];
    return [floor numberOfModelInFloor];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    UITableViewCell *cell = nil;
    id<CartFloorProtocol> floor = self.cartViewModel.list[indexPath.section];
    cell = [tableView dequeueReusableCellProcessModel:floor indexPath:indexPath];
    [(id<CartProbeProtocol>)cell setDelegate:self];
    return cell;
}
- (UIView *)tableView:(UITableView *)tableView viewForHeaderInSection:(NSInteger)section
{
    UIView *view = [[UIView alloc] initWithFrame:CGRectMake(0, 0, SCREEN_WIDTH, 25)];
    view.backgroundColor = [UIColor lightGrayColor];
    return view;
}

- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    id<CartFloorProtocol> floor = self.cartViewModel.list[indexPath.section];
    id<CartRenderProtocol> renderModel = [floor cartModelForRowIndexPath:indexPath];
    Class<CartProbeProtocol> cellClass = NSClassFromString([renderModel cellIdentifier]);
    return [cellClass calculateSizeWithData:renderModel];
}

```

### 四、软件设计开发之展望

软件设计的思路千变万化，终归到底没有具体绝对的好与坏之分，真正的选择还需要根据的具体的业务场景来决定，MVVM脱胎于MVC，如果是展示型的复杂业务类型来讲我个人更偏重于MVP的模式来实现(具体参照[基于面向协议MVP模式下的软件设计－iOS篇
](http://www.cocoachina.com/ios/20151223/14768.html)，这一点针对模块化进行分割处理，严格的讲不通的逻辑快分割，提高了程序的健壮性)；
如果是针对具有复杂操作性的业务类型的场景，我个人偏重于MVVM，处理复杂交互逻辑。采用传统业务逻辑抽离的规则来处理客户端的逻辑，这一点绰绰有余。

任何一种软件构思都是在具体的实践中一步步的出的。纵观整个软件的发展，无一不是如此。无论是中文还是外文其实大家都有自己的见解，关于MVVM的文章其实的确有不少外文的，之前我也认真研读过一些。任何一种事务我们最终都是需要有自己独特一面见解的，只有不断的总结学习别人观点，不断的进行融汇升华，最终走出一条自己的路。这一点始终如此，也只有这样在某一点上我们才可能做到超越以往....