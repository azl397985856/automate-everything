# 模块化和组件化
## 什么是模块化
![图2.1](https://github.com/azl397985856/automate-everything/blob/master/illustrations/%E5%9B%BE2.1.png)

模块化是个一般概念，这一概念也适用于软件开发，可以让软件按模块单独开发，各模块通常都用一个标准化的接口来进行通信。除了规模大小有区别外，面向对象语言中对象之间的关注点分离与模块化的概念基本一致。通常，把系统划分外多个模块有助于将耦合减至最低，让代码维护更加简单。任何一个类库实际上都是一个模块，无论其是Log4J、React还是Node。通常，开源和非开源的应用都会依赖于一个或多个外部类库，而这种依赖关系又有可能传递到其他类库上。
任何语言都有模块化的思想，比如java的 package， es6的 import/export 等，而js恰好经历了从无到有，而且js模块化规范比较多，AMD，CMD，UMD，以及es6官方的import/export，所以我以js为切入点，讲解模块化是什么。

js诞生的时候是没有模块化规范的，所有的代码都是在一个个script中，依赖管理也完全是全局变量+顺序加载的模式。
那时候代码大概是这样的：

```html
<script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.3/umd/popper.min.js" integrity="sha384-vFJXuSJphROIrBnz7yo7oB41mKfc8JzQZiCq4NCceLEaO4IHwicKwpJf9c9IpFgh" crossorigin="anonymous"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.2/js/bootstrap.min.js" integrity="sha384-alpBpkh1PFOepccYVYDB4do5UnbKysX5WZXm3XxPqe5iKTfUKjNkCk9SaVuEZflJ" crossorigin="anonymous"></script>
```

最初js就是靠这种浏览器从上到下加载执行的这种特性完成依赖管理和模块化的。可以看出，如果项目依赖非常多，维护这种依赖关系将会非常痛苦。而且全局变量
增多也经常会碰到冲突。

随后各种模块化规范出现，比如commonjs，amd，umd。 又出现了很多包管理工具，比如bower，npm，browserify。前端模块化眼花缭乱，
随后官方esma推出了模块化标准import/export， 至此js正式有了模块化官方规范。

有了模块化，我们通常会这样组织代码：
![图2.2](https://github.com/azl397985856/automate-everything/blob/master/illustrations/%E5%9B%BE2.2.png)

我们上层模块总是复用一些小的模块，小的模块依赖更小的模块。构成了一个精妙的层级网络，这很美妙。让我们享受模块的好处吧。

## SOA和微服务
面向服务架构（Service-Oriented Architecture，SOA）又称“面向服务的体系结构”，是Gartner于2O世纪9O年代中期提出的面向服务架构的概念。
面向服务架构，从语义上说，它与面向过程、面向对象、面向组件一样，是一种软件组建及开发的方式。与以往的软件开发、架构模式一样，SOA只是一种体系、一种思想，而不是某种具体的软件产品。包括最近流行的micro service其实也是一种面向组件化和模块化的思想。

SOA和micro service 的基本理念是将应用程序的不同功能单元（称为服务）通过这些服务之间定义良好的接口和契约联系起来。接口是采用中立的方式进行定义的，它应该独立于实现服务的硬件平台、操作系统和编程语言。微服务通过将服务拆分成原子性，并通过服务治理完成系统的功能。它有很多好处，比如不限定语言和实现，大家可以选择合适的技术栈。比如简单性，通过这种拆解，系统从一个复杂系统，编程了若干简单的子系统，无疑降低了系统的复杂度。但是同样有很多缺点，但这并不是本篇文章的重点。  一个典型的微服务系统大概是这样的：

![图2.3](https://github.com/azl397985856/automate-everything/blob/master/illustrations/%E5%9B%BE2.3.png)

## 什么是组件化
组件化就是基于可重用的目的，将一个大的软件系统按照分离关注点的形式，拆分成多个独立的组件，主要目的就是减少耦合。一个独立的组件可以是一个软件包、web服务、web资源或者是封装了一些函数的模块。这样，独立出来的组件可以单独维护和升级而不会影响到其他的组件。
提到组件化，不得不提web-component。有了web-component代码就可以这么写：

```html
<link rel="import" href="banner.html">
<link rel="import" href="body.html">
<link rel="import" href="footer.html">
<template name="t-list">
    <t-banner></t-banner>
    <t-body></t-body>
    <t-footer></t-footer>
</template>

```
这样就可以通过分工提高开发效率，同时可以复用代码。代码也非常清爽。但是web-component 是一个新的标准，目前还没有很好的实现，浏览器支持性并不是很好。
然后像react和 vue 这种 组件化思想的库可以实现类似web-compoennt的效果，尤其是vuejs。

组件化的开发方式可以给我们**显著减少**开发时间，我们可以根据自己的业务场景沉淀一些基础组件和业务组件。使用者不必关心组件内部的实现，只需要关心组件对外包喽的接口即可，可以说将开发者的关注点集中在业务逻辑本身。这也就是像ant-design，elementUI等组件库火爆的原因。同时公司内部应该沉淀一些业务组件，供各个子系统使用。这样如果组件需要修改，只需要修改一处，其他系统只需要更改依赖版本即可。在这里要感谢各大公司提供的开源组件库，比如阿里的antd，滴滴的魔方，有赞的zent，饿了么的elementUI，它们提供了数十种的基础组件，几乎覆盖了所有的非复杂业务场景，不仅如此，它们还提供了诸多行业解决方案，帮助大家更快更好的搭建系统。同时它们也有很多业务组件，比如滴滴会有呼叫车辆的组件，有赞有sku组件等等，这些都是项目业务场景沉淀下来的，虽然不具有广泛适用性，但是其独特的业务特点，导致其稀缺性。如果小公司自己开发一套这样的系统的化，可以说是难度非常大。因为一套被广泛的系统不仅要组件丰富，文档清晰，还要质量高（就是要经过充分测试，并且有足够高的单元测试覆盖率），同时各大公司的组件库通常都是和设计师反复沟通产出的，其设计质量也是蛮高的。

我们已经知道了模块化的概念和模块的好处，那么如何划分模块层次就是一个很重要的问题。毫不夸张的说，如果组件划分不得当，
模块之间职责重叠或者覆盖不均匀会给使用者带来不便，给系统带啦额外负担，甚至会误导使用者，造成潜在的问题。
那么如何划分模块和组件系统呢？下一节为大家揭晓。

## 怎么合理划分模块和组件
模块和组件的划分小到目录结构大到数据流动，状态管理，大大小小，内容繁杂。

> 什么叫架构？揭开架构神秘的面纱，无非就是：分层+模块化。任意复杂的架构，你也会发现架构师也就做了这两件事。

### 前端架构演变

前端的工作其实就是和DOM打交道的工作，只不过是直接和间接的关系。为什么这么说呢？我们先来看下前端架构的发展。

就前端架构发展角度来看，前端发展大概经历了四个阶段。

1. 直接操作DOM年代。

在这个年代，也就是原生操作DOM和Jquery等DOM操作封装库的时代，所有的效果都是直接操作DOM（前端直接操作或者框架封装了操作DOM的过程）。
这个时代，业务逻辑通常比较简单，对于比较复杂的逻辑通常都是落地到后端。
2. MVC模式

这个时代，并没有本质的差别，只不过将操作DOM的代码换了位置而已。
3. MV*

这个年代可以说是真正的将前端从直接和DOM打交道中解脱了出来。MV* 包括 react + 类flux架构， 都将DOM做了一层抽象，并不是简单的封装。
它们的理念是数据驱动DOM，同时借助一定的手段（可以是虚拟DOM）将DOM操作最小化（DOM操作非常昂贵）。 

> 为什么DOM操作比较昂贵？ 大家可以看下DOM的API就知道了，非常的多。大家可以这么理解，操作DOM就好像计算机操作硬盘。
执行JS就好像计算机操作内存。

4. MNV*

### 模块和组件的划分依据
