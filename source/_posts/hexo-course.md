---
title: Hexo教程GitCafe部署搭建博客
subtitle: "GitCafe下使用Hexo搭建博客"
keywords: "Hexo教程 GitCafe下搭建博客 "
author:     "Scenery"
date: 2016-01-10
description: "GitCafe下使用Hexo搭建博客,一直以来都是喜欢在CSDN、cnblogs上分享一些自己的想法，渐渐的发觉其实这些平台有时间并不能满足自己的某些需求。对于从事技术行业以来，有自己的博客相信意义就不再多说了。不说好多大牛们都有自己的站点，作为一个大牛路上的前行者，心动不如行动，动起手来...一开始的时间选择了wordexpress、EMlog这些模版(基于PHP)，在某宝上买了基础版的主机，部署后总觉得怪怪的。说真的选用第三方的虚拟主机，出了每年要对应的开销外，经常到了期需要从心付费，如果忘记了可能会被释放掉。平时做完代码每次需要部署都的必须上传挺麻烦的。更让人受不了的是公司的网络禁止一切FTP上传工具."
tags:
	- iOS
	- Hexo教程
	- GitCafe下搭建博客
	- 电子商务
---


## 一、绪言 
一直以来都是喜欢在CSDN、cnblogs上分享一些自己的想法，渐渐的发觉其实这些平台有时间并不能满足自己的某些需求。对于从事技术行业以来，有自己的博客相信意义就不再多说了。不说好多大牛们都有自己的站点，作为一个大牛路上的前行者，心动不如行动，动起手来...

一开始的时间选择了wordexpress、EMlog这些模版(基于PHP)，在某宝上买了基础版的主机，部署后总觉得怪怪的。说真的选用第三方的虚拟主机，出了每年要对应的开销外，经常到了期需要从心付费，如果忘记了可能会被释放掉。平时做完代码每次需要部署都的必须上传挺麻烦的。更让人受不了的是公司的网络禁止一切FTP上传工具。

后来想到了阿里云，基础版的云主机一年大概500大洋的样子，配置足够用了(如果想用学生证买的话一年100大洋)，中间曾经买过一小段的时间，在自己的机器上部署了node、mysql、nginx，同时部署了git，每次做完修改直接push到远端的目录。这样坚持了一段时间，慢慢的又不习惯了，可能好长时间不远程连接一次。并且云主机也有到期后忘记续费被释放的危险，随着在主机上放的东西越来越多，越发的觉得不是那么安全。

偶然的事件看到了Jekyll这个东西，可以在本地进行开发然后部署在在Github上。Jekyll也是一个基于node的框架，他的优势在于可以在本地完成自己的开发，然后直接push到Github，Github自身是支持Jekyll部署的，只需要设置好自己的page页面就可以了。但是问题又来了，生活在天朝一切总是那么不尽人意，Github这个东西大多时间访问速率极慢，甚至有时间经常打不开。更可杯具的是，Github自身屏蔽了百度爬虫的抓取，对于生活在天朝不能google的人来说这的确是一场的最大的无奈了。

GitCafe是个好东西啊，这个东西就是中国版的Github，无论是访问还是爬虫抓取都是不受任何限制的。废话少说 直接迁移。就这样blog从此之后就迁移到了新家。之所以没有选择Jekyll而选择Hexo,Hexo 可以暂时理解为Jekyll＋,Hexo 是一个台湾的大学生在Jekyll的基础上脱胎而来的，我本人更喜欢后者。

相对前者而言部署时间包过大，每次clone、上传都需要下载很大的一个工程包。Hexo就不同了，他的配置是在本地的，需要发布的时间配置好发布地址，hexo会自动将本地的网页全部生成对应的静态网页，之后将生成的网页存储在public文件夹下，最终hexo只会讲public文件下的内容上传到对应的部署地址，并且public文件下的内容是由deploy_git来管理的，换句话说就是每次修改发布都只是上传发生改变的文件，这一点本人特别喜欢。

## 二、Hexo环境搭建
hexo 是一个基于node的框架工具，首先我们需要在本地安装nodejs的环境，还需要安装git.有了node的环境我可以在本地进行hexo的开发调试，然后通过git直接发布并且部署到Github上。

### Node 安装
到Node.js[官网](https://nodejs.org/)下载相应平台的最新版本，一路安装即可。我用的是node-v4.2.4.pkg(Mac平台),下载以后直接安装就可以了.安装好以后,打开Terminal 输入: node -v

```
ccguodeMacBook-Pro:sunny-blog ccguo$ node -v
v5.3.0
ccguodeMacBook-Pro:sunny-blog ccguo$ npm -v
3.3.12
ccguodeMacBook-Pro:sunny-blog ccguo$ 

```

出现版本号以后，node安装成功。

Mac平台下面的git此处就略过了，不在一一解释了(mac平台自带的git工具)；

关于window平台Git的客户端很多，我用的是[msysgit](http://code.google.com/p/msysgit)，喜欢用绿色版本[Portable application for official Git for Windows 1.8.4](http://code.google.com/p/msysgit/downloads/detail?name=PortableGit-1.8.4-preview20130916.7z)，下载下来设置一下环境变量即可，Git_HOME，%Git_HOME%\bin之类的，不多说。

 安装sublime工具，Mac下面我个人还是比较喜欢[sublime3](http://www.sublimetext.com), 小巧轻便不说，插件一大堆，想怎么用怎么用就。在这里仅仅作为一个文本编辑器使用，支持各种编程语言和文件格式，当然也支持Markdown语法，实在是个不可多得的练码奇才。

### hexo安装
node 安装好以后，我们看到npm也被安装好了，npm可以暂时理解成nodejs的一个包管理工具，
有了它我们可以通过它来将我们需要使用的node包安装到本地(例如hexo就是一个node的包)，如下
使用npm 安装hexo，打开终端输入: npm install -g hexo

```
npm install -g hexo

```
紧接着终端会进入下载模式，一大堆进度下载完毕以后，hexo安装成功，在终端输入: hexo -v

```
ccguodeMacBook-Pro:sunny-blog ccguo$ hexo -v
hexo: 3.1.1
os: Darwin 14.5.0 darwin x64
http_parser: 2.6.0
node: 5.3.0
v8: 4.6.85.31
uv: 1.8.0
zlib: 1.2.8
ares: 1.10.1-DEV
icu: 56.1
modules: 47
openssl: 1.0.2e
ccguodeMacBook-Pro:sunny-blog ccguo$ 
```
OK ,此时hexo已经成功安装，到此本地开发环境部署安装完毕，接下来进行开发...

## 三、Hexo开发
hexo只是一个node下的框架，通过它我们可以创建自己的应用程序，然后运行自己的程序。hexo工程的源码也是基于node的，我们可以对此进行二次开发，根据自己的需求，可以改变博客的主题，也可以修改node的源码。

整体来说hexo就是一个可以帮助你搭建一个不需要数据库的网站，我们可以随心所欲的修改他的源码，hexo是支持markdown 的，我们所有的内容都是以.md文件的形式存储在工程里面。hexo发布工程的时间，hexo会将所有的内容打包成一个对应的静态html页面，然后生成到一个public的文件下，之后将这个public文件发布到线上。

### hexo 创建工程
cd到当前硬盘的某个目录下，在终端中输入: hexo init myblog

 ```
    hexo init myblog
 ```

成初始化成功以后，我们可以在对应的文件夹下看到一恶果工程目录:

```
 - Desktop
    - myblog
        - _config.yml
        - package.json
        - scaffolds
        - source
        - theme
```

cd到myblog目录，在该目录下面输入指令: npm install

```
    //安装依赖包
    npm install
    //安装成功以后发现myblog下面多出了几个文件
    - myblog
        - node_modules
        - db.json
```

此时，如果没有异常，我们的blog工程就已经创建完毕了，在终端输入 : hexo s
```
ccguodeMacBook-Pro:myblog ccguo$ hexo s
[Error: Module version mismatch. Expected 47, got 14.]
INFO  Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.

```

此时在浏览器中输入 http://0.0.0.0:4000 ，就能看到我们的博客了.

在终端中输入 hexo g z 则会进行博客的生成工作，工程目录下会多出一个public的文件夹，里面是整个博客网站的所有静态页面。
在终端中输入hexo d 则可以进行blog的发布，具体发布流程如下...

### hexo二次开发
工程创建完毕以后，通过hexo s已经将博客运行起来了，相信看到这一面心里面已经感受到hexo的魅力了。
将myblog文件夹拖进sublime，此时我们看到如下的工程目录：
```
- myblog
    - .deploy_git    //git发布管理目录，每次发布新的内容由git进行版本管理
    - node_modules   //node 所有的依赖包
    - public         //发布目录
    - scaffolds
    - source         //博文内容文件目录
        - _post
    - themes         //主题文件目录
    - .gitignore     
    - _config.yml    //博客的配置文件
    - db.json
    - npm-debug.log  //日志文件
    - package.json   //包依赖文件
```

到了这一步，我们需要做的首先是如何使用hexo,如何自己手动来完成并创建自己的博文。一般情况下，无论是我们创建自己的博文，还是针对hexo进行二次开发，我们需要关心的目录只有source目录和theme目录，source->_post目录下面是存放.md文件的，我们的每一篇博客都是以markdown文件的形式存放在_post目录下的，之后hexo s发布的时间，hexo会将所有的.md文件都生成一个静态html页面，之后发布到public目录，最终hexo d 发布到目标地址去。

### 创建第一篇博文
在source -> _post目录下，我们创建一个新的文件hello-world.md，然后编辑文件内容如下:
```
 Hello World , This is my Hexo Blog Sunnycn..
 Welcome to [Hexo](http://hexo.io/)! This is your very first post. Check [documentation](http://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](http://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).
```

编辑完成后保存，在终端下从新执行hexo s,然后在浏览器中访问：http://0.0.0.0:4000/，就能看到刚才我们创建的博客了，hexo就是折磨简单...

### 替换自己喜欢的主题
theme文件夹，主要是主题文件，你可以去Github上挑选自己的喜欢的主题，然后放进theme目录，之后修改配置文件_config.yml 的theme选项，就能看到对应的效果了。

```
# Extensions
## Plugins: http://hexo.io/plugins/
## Themes: http://hexo.io/themes/
theme: huno //使用主题
```

#### 发布博客
hexo发布博客，主要是将本地已经开发好，并且已经生成的静态网页发布到一个对应的远端。例如Github，我们可以将这一系列静态文件发布到Github的某个responsity下，之后我们就可以通过一个链接去真正的访问我们的博客。
首先，我们需要在自己的Github下面创建一个和用户名一模一样的一个工程([具体教程](http://ibruce.info/2013/11/22/hexo-your-blog/)), 创建完毕，验证ssh公钥以后，我们需要的是修改博客的本地配置文件，如下：

修改配置文件 _config.yml 
```

# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git  //hexo 3.0以后此处填写git
  repository: https://gitcafe.com/sunnycn/sunnycn.git  //刚才创建的与Github账户名相同的工程的远端地址
  branch: master //gitcafe-pages  //所在的分支

```

在博客的配置文件中，我们配置好要发布到远端的路径、版本工具类型、分支名，然后在终端下依次执行
hexo g , hexo d 
之后看到如下：
```
ccguodeMacBook-Pro:sunny-blog ccguo$ hexo d
[Error: Module version mismatch. Expected 47, got 14.]
INFO  Deploying: git
INFO  Clearing .deploy folder...
INFO  Copying files from public folder...
[master a06e618] Site updated: 2016-01-17 22:35:19
 44 files changed, 752 insertions(+), 174 deletions(-)
 create mode 100644 2016/01/17/protocol_mvvm/index.html
 rewrite fonts/foundation-icons/foundation-icons.woff (98%)
 create mode 100644 images/2015-12-16/hexo/hexo.png
^CINFO  Catch you later
INFO  Deploy done: git

```
 OK 博客发布成功，之后在浏览器中输入Github下的对应的地址就能访问我们的博客了...
 至此，hexo的教程完毕....