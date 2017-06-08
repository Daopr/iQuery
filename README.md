- [APQuery](#APQuery)
    - [入坑](#入坑)
    - [安装](#安装)
    - [设计前提](#设计前提)
    - [基本功能及其实现原理介绍](#基本功能及其实现原理介绍)
        - [属性和方法  册机制](#属性和方法--册机制)
        - [属性选择器](#属性选择器)
        - [属性获取](#属性获取)
        - [属性设置](#属性设置)
    - [扩展功能介绍](#扩展功能介绍)
        - [业务设计模式](#业务设计模式)
            - [MBC](#MBC)
            - [其他类](#其他类)
    - [不足](#不足)
        - [胶水代码还是过多](#胶水代码还是过多)
        - [有调用的安全隐患](#有调用的安全隐患)


# APQuery

我最近偶然机会写了三四个月的Web前端，在这过程中，对大前端有了更多了解。iOS 在很多处理方式上可以借鉴Web 前端，比如jQuery。

在Web上jQuery 的表现非常优秀，这种id选择器的设计模式，其实在iOS 上面对于方法的应用已经很成熟，但是却没有应用到属性上。

我在长期编写iOS代码时，对于传值调用深感不便，尤其是跨页面调用，需要很多中间代码，索性决定撸一个框架，来实现基于属性选择器的开发架构，叫Apple Query 好了，两个字缩写，APQuery。

## 入坑

养了Swift好几年了，前几天看了WWDC以后，感觉是时候入坑了，于是下了Xcode 9，投入了Swift的怀抱，一写起来就不想Objective-C了。Swift像极了脚本语言js的语法，很有函数式语言的风格。

最让我感到欣慰的是

*！一个类可以不用写两个文件了！导致文件管理很清爽，导航很清晰！

*不用导入头文件！不用看着满屏幕的import发呆！不用处理因为交叉包含头文件导致的错误！

*swift原生支持点语法调用函数！告别了坑爹的OC里用Block实现点语法！

我本来已经用OC写好了基础选择器的代码，效果也很好，但是写了Swift以后，我决定还是用Swift3来实现我要的功能。

## 安装

Objective-C语言需要桥接swift，在这里不过多赘述

Swift则直接使用

## 设计前提

APQuery 的设计前提是你的代码只有你在修改。而实际上会有各种技术来修改你的代码，代码最终是运行在你不受控的设备上，你永远不知道其他人会对你的代码干些什么。

## 基本功能及其实现原理介绍

### 属性和方法  册机制

### 属性选择器

### 属性获取

### 属性设置

## 扩展功能介绍

### 业务设计模式

#### MBC

我在应用层的设计模式是一个以业务为中心的设计模式，从根本上讲，我们撸的所有代码都是面向业务的，所以做系统设计时我们直接就面向业务来设计。

以下为我的设计中完成一个具体业务所需要的类群

1. 首先是业务类

2. 然后是数据类

3. 接着是请求类

4. 另外是视图类

5. 还有控制者类

6. 最后是插件类

7. 以及模型类



模型类必不可少，无需赘言。其中业务类是核心类，由控制器类持有，所有业务由控制者交给业务来处理，再由业务调度数据类、请求类、视图类来完成，由名字可以看出它们各自的分工

得益于Swift没有头文件的机制，对于每一个业务都实现这些类群，并不是一件复杂的事情，可以根据业务大小决定是否拆分成几个文件。对于不大的业务，在一个文件里完成结构还是比较清晰的

最后要说的是插件类，这个类的功能是基于方法选择器实现的, 每个类会注册好它有的方法，然后在运行时APQuery会在所有方法运行之前运行统计代码，这个时候匹配我们的插件类，如果满足插件类的指定运行条件，则会优先调用插件类。

插件类可以返回加工过的值，如果返回特定值可以中断原来函数的调用。

我们可以用插件类来从指定类的外部做格式控制、内容校验、倒计时及其恢复等弱业务相关性的代码。这样不会打扰主要业务的开展，会有一个良好的阅读性，以及顺便实现了所谓的低耦合度，其实这类代码本来与实际业务关联就不大，我们这样做比较符合我们的心理预期。

插件类还有一个用途就是用于小粒度的热修复，只要让我们的插件管理者载入你的代码就可以。实现方法可以是预留framework下载和加载插件接口，然后动态下载含有插件类的framework导入插件管理者的列表里，不知道现在Apple 这方面力度加大后，这种方式还可不可行，如果不可行也不影响，毕竟不是我们框架的主要应用领域。

我其实研究设计模式还是有这么久了，但是匹配业务的设计模式还没有遇到，不知道是不是藏的太深，也或许是我活得还不够久。

一般的设计模式要么是太复杂，不利于小型开发，代表是MVP; 要么是不够复杂，粒度太大，各种类别的代码不加区揉成一团，代表是MVC;或者就是折中，但是需要手写许多胶水代码，尤其是业务不够明确，让人把主要精力都用在了处理代码结构上，, 而不是我们最初期望的业务，代表是MVVM。

MBC，我实在不知道叫什么名字好，从结构上看，其实是面向业务(Business Oriented )的MVC，因为其中主要角色就是模型、业务、控制者，所以也可以叫做BOMVV。不过暂且就叫MBC吧，如果有谁有更好的名字或是设计模式改进可以联系我讨论。

#### 其他类

作为一个成熟的应用系统，当然不可能只有应用层的代码，而其他比如文件管理、应用生命周期监控、用户管理、用户提示、路由、网络、存储、数据库、安全、时间管理、统计、日志、版本控制、热修复(小粒度非jsPatch)、跨应用协同工作、本地化、测试框架以及扩展工具类等等

## 不足

### 胶水代码还是过多

其中还是有很多胶水代码，我正在致力于减少前台可见的胶水代码，将这些代码做成开发者透明的，以提升开发体验

### 有调用的安全隐患

这种url的调用方式有很大的安全隐患，因为模式异常清晰，外部观察者如果熟悉模式将会很轻易地找到他需要修改的界面元素、数据元素、方法元素，和在开发普通h5页面时候我们暴露出来的东西相当，所以我们在开始做出了，你的代码只有你来修改这种假设。

由于当前人手不足，框架由我独立开发，按步骤我是首先撸功能类代码，当我完成一定功能的时候，尤其是需要发布正式版本的时候才会考虑加入安全类，在这方面有独到见解的撸友可以留言我们讨论




