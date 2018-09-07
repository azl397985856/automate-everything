# 2. 模块化和组件化

当你进行用户需求调研后，往往收集到的都是一个个的用户需求点，而一个软件需求分析员要做的是最终将这些需求实现为一个完整的业务系统。这里面就涉 及到业务模块的划分，模块间的分析，需求层面的复用能力分析，各种性能，可靠性，安全等非功能性需求。这些更加已经是一个完全的系统分析方面的内容，或者 说软件需求已经会兼顾部分软件架构设计的内容，因此作为一个软件需求人员更加需要去了解业务组件化，服务化，软件模块集成，复用等方面的技术内容。也需要 去了解涉及到交互设计方面的内容，这些都是形成一个高质量的软件业务系统的重要输入。

## 什么是模块化

![&#x56FE;2.1](https://raw.github.com/azl397985856/automate-everything/master/illustrations/图2.1.png)

模块化是个一般概念，这一概念也适用于软件开发，可以让软件按模块单独开发，各模块通常都用一个标准化的接口来进行通信。除了规模大小有区别外，面向对象语言中对象之间的关注点分离与模块化的概念基本一致。通常，把系统划分外多个模块有助于将耦合减至最低，让代码维护更加简单。任何一个类库实际上都是一个模块，无论其是Log4J、React还是Node。通常，开源和非开源的应用都会依赖于一个或多个外部类库，而这种依赖关系又有可能传递到其他类库上。 任何语言都有模块化的思想，比如java的 package， es6的 import/export 等，而js恰好经历了从无到有，而且js模块化规范比较多，AMD，CMD，UMD，以及es6官方的import/export，所以我以js为切入点，讲解模块化是什么。

js诞生的时候是没有模块化规范的，所有的代码都是在一个个script中，依赖管理也完全是全局变量+顺序加载的模式。 那时候代码大概是这样的：

```markup
<script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.3/umd/popper.min.js" integrity="sha384-vFJXuSJphROIrBnz7yo7oB41mKfc8JzQZiCq4NCceLEaO4IHwicKwpJf9c9IpFgh" crossorigin="anonymous"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.2/js/bootstrap.min.js" integrity="sha384-alpBpkh1PFOepccYVYDB4do5UnbKysX5WZXm3XxPqe5iKTfUKjNkCk9SaVuEZflJ" crossorigin="anonymous"></script>
```

最初js就是靠这种浏览器从上到下加载执行的这种特性完成依赖管理和模块化的。可以看出，如果项目依赖非常多，维护这种依赖关系将会非常痛苦。而且全局变量 增多也经常会碰到冲突。

随后各种模块化规范出现，比如commonjs，amd，umd。 又出现了很多包管理工具，比如bower，npm，browserify。前端模块化眼花缭乱， 随后tc39组织提出的esma推出了模块化标准import/export， 至此js正式有了模块化官方规范。

有了模块化，我们通常会这样组织代码：

![&#x56FE;2.2](https://raw.github.com/azl397985856/automate-everything/master/illustrations/图2.2.png)

我们上层模块总是复用一些小的模块，小的模块依赖更小的模块。构成了一个精妙的层级网络，这很美妙。让我们享受模块的好处吧。

## SOA和微服务

面向服务架构（Service-Oriented Architecture，SOA）又称“面向服务的体系结构”，是Gartner于2O世纪9O年代中期提出的面向服务架构的概念。 面向服务架构，从语义上说，它与面向过程、面向对象、面向组件一样，是一种软件组建及开发的方式。与以往的软件开发、架构模式一样，SOA只是一种体系、一种思想，而不是某种具体的软件产品。包括最近流行的micro service其实也是一种面向组件化和模块化的思想。

SOA和micro service 的基本理念是将应用程序的不同功能单元（称为服务）通过这些服务之间定义良好的接口和契约联系起来。接口是采用中立的方式进行定义的，它应该独立于实现服务的硬件平台、操作系统和编程语言。微服务通过将服务拆分成原子性，并通过服务治理完成系统的功能。它有很多好处，比如不限定语言和实现，大家可以选择合适的技术栈。比如简单性，通过这种拆解，系统从一个复杂系统，编程了若干简单的子系统，无疑降低了系统的复杂度。但是同样有很多缺点，但这并不是本篇文章的重点。 一个典型的微服务系统大概是这样的：

![&#x56FE;2.3](https://raw.github.com/azl397985856/automate-everything/master/illustrations/图2.3.png)

## 什么是组件化

组件的概念在前端用的比较大多。组件和模块表达的意思比较相近。 我这里讲的组件，是比较狭隘的组件，专指前端中构建页面的基本组成单位。组件是对业务逻辑的封装，一个页面由多个组件组成，组件又可以由其他组件组成。 因此对组件进行分类很有必要。一般而言，组件分为下面四种类型：

1. 展示型组件

   给定的输入，输出一定。没有交互逻辑

2. 容器型组件

   通常是作为数据容器，并将数据分发到子组件。

3. 交互性组件

   大多数组件都是交互组件，满足用户的交互需求。 比如输入框，下拉框等。

4. 功能性组件

   不在页面中展示，不与用户直接接触。 通常以高阶组件的形式存在，给现有的组件注入功能。比如我需要实现一个可以拖动的功能，这个东西是抽象的，不是具体的组件。 大概是这样的用法:

```jsx
<Drag >
<Modal />
</Drag>
```

一个典型的系统是这样的：

![&#x56FE;2.4](https://raw.github.com/azl397985856/automate-everything/master/illustrations/图2.4.png)

组件化就是基于可重用的目的，将一个大的软件系统按照分离关注点的形式，拆分成多个独立的组件，主要目的就是减少耦合。一个独立的组件可以是一个软件包、web服务、web资源或者是封装了一些函数的模块。这样，独立出来的组件可以单独维护和升级而不会影响到其他的组件。 提到组件化，不得不提web-component。有了web-component代码就可以这么写：

```markup
<link rel="import" href="banner.html">
<link rel="import" href="body.html">
<link rel="import" href="footer.html">
<template name="t-list">
    <t-banner></t-banner>
    <t-body></t-body>
    <t-footer></t-footer>
</template>
```

这样就可以通过分工提高开发效率，同时可以复用代码。代码也非常清爽。但是web-component 是一个新的标准，目前还没有很好的实现，浏览器支持性并不是很好。 然后像react和 vue 这种 组件化思想的库可以实现类似web-compoennt的效果，尤其是vuejs。

组件化的开发方式可以给我们**显著减少**开发时间，我们可以根据自己的业务场景沉淀一些基础组件和业务组件。使用者不必关心组件内部的实现，只需要关心组件对外包喽的接口即可，可以说将开发者的关注点集中在业务逻辑本身。这也就是像ant-design，elementUI等组件库火爆的原因。同时公司内部应该沉淀一些业务组件，供各个子系统使用。这样如果组件需要修改，只需要修改一处，其他系统只需要更改依赖版本即可。在这里要感谢各大公司提供的开源组件库，比如阿里的antd，滴滴的魔方，有赞的zent，饿了么的elementUI，它们提供了数十种的基础组件，几乎覆盖了所有的非复杂业务场景，不仅如此，它们还提供了诸多行业解决方案，帮助大家更快更好的搭建系统。同时它们也有很多业务组件，比如滴滴会有呼叫车辆的组件，有赞有sku组件等等，这些都是项目业务场景沉淀下来的，虽然不具有广泛适用性，但是其独特的业务特点，导致其稀缺性。如果小公司自己开发一套这样的系统的化，可以说是难度非常大。因为一套被广泛的系统不仅要组件丰富，文档清晰，还要质量高（就是要经过充分测试，并且有足够高的单元测试覆盖率），同时各大公司的组件库通常都是和设计师反复沟通产出的，其设计质量也是蛮高的。

我们已经知道了模块化的概念和模块的好处，那么如何划分模块层次就是一个很重要的问题。毫不夸张的说，如果组件划分不得当， 模块之间职责重叠或者覆盖不均匀会给使用者带来不便，给系统带啦额外负担，甚至会误导使用者，造成潜在的问题。 那么如何划分模块和组件系统呢？下一节为大家揭晓。

## 怎么合理划分模块和组件

模块和组件的划分小到目录结构大到数据流动，状态管理，大大小小，内容繁杂。

> 什么叫架构？揭开架构神秘的面纱，无非就是：分层+模块化。任意复杂的架构，你也会发现架构师也就做了这两件事。

### 前端架构演变

前端的工作其实就是和DOM打交道的工作，只不过是直接和间接的关系。为什么这么说呢？我们先来看下前端架构的发展。

就前端架构发展角度来看，前端发展大概经历了四个阶段。

1. 直接操作DOM年代。

在这个年代，也就是原生操作DOM和Jquery等DOM操作封装库的时代，所有的效果都是直接操作DOM（前端直接操作或者框架封装了操作DOM的过程）。 这个时代，业务逻辑通常比较简单，对于比较复杂的逻辑通常都是落地到后端。

1. MVC模式

这个时代，并没有本质的差别，只不过将操作DOM的代码换了位置而已。

1. MV\*

这个年代可以说是真正的将前端从直接和DOM打交道中解脱了出来。MV\* 包括 react + 类flux架构， 都将DOM做了一层抽象，并不是简单的封装。 它们的理念是数据驱动DOM，同时借助一定的手段（可以是虚拟DOM）将DOM操作最小化（DOM操作非常昂贵）。

> 为什么DOM操作比较昂贵？ 一方面DOM API 比较多，大家可以看下DOM的API就知道了，非常的多。另一方面，DOM操作通常伴随这浏览器的重排和重绘。大家可以这么理解，操作DOM就好像计算机操作硬盘。 执行JS就好像计算机操作内存。

拿react来举例，react的核心思路可以用下面的伪代码表示：

```jsx
    const oldTree = render(oldState, oldProps);
    const newTree = render(newState, newProps);

    const patches = diff(oldTree, newTree) // 比较两棵树，计算patches

    doPatch(patches) // 根据patches应用补丁，将dom以”最小化“更新
```

可以看出react 封装了 dom操作， 将dom 创建（render）和 dom修改（doPactches）封装了起来，对开发者来说是透明的。 react 的思想是给定的state 和 props 渲染出相同或者相似的dom。 所以react 开发者的核心关注点从DOM 移到了 状态， 也因此诞生了很多状态管理框架， 比如官方的flux，以及类flux 框架， reflux 和redux。 以及vuejs 配套的vuex。这也是众多数据管理框架越来越火爆的原因，比如mobx（MobX 是一个经过战火洗礼的库，它通过透明的函数响应式编程\(transparently applying functional reactive programming - TFRP\)使得状态管理变得简单和可扩展）和 rxjs（RxJS 是使用 Observables 的响应式编程的库，它使编写异步或基于回调的代码更容易）

> jquery 将 开发者从DOM 复杂的API 和浏览器兼容性泥潭中拉了出来。 而数据管理框架将开发者彻底从DOM 关注 点中解放出来了。

1. MNV\*

前面说了，前端的工作就是和DOM打交道。 这一说法在MNV _面前显得不是那么确切。MNV_真正将开发者脱离DOM，从令人诟病的DOM操作中完全解脱，并且不借助DOM实现原生的体验。目前很多大公司的核心APP 都是基于这种混合式开发完成，比如支付宝，微信，钉钉等等。这种开发方式的好处结合了H5开发的速度和原生开发的性能的优势。 这种开发模式的原理很简单，在原生APP中嵌入webview并基于一定的协议，完成客户端和H5页面的双向通信。做过flex的人可能知道，flex可以将flex内部应用通过接口暴漏给window对象，js通过window对象访问flex中的方法。同时flex可以调用window上的方法，从而实现flex和js的双向绑定。而MVN\*的交互模式有点类似，H5和Native直接的交互基于的是名字叫JSBridge的东西。

记得在15年本科没毕业的时候，我的讲师跟我们介绍过一个名字叫PhoneGap的东西，其实它就是在web基础上包了一层Native，然后通过Bridge技术使得js可以调用视频，音频，GPS，拍照等原生的功能。要进一步了解JSBridge，需要大家懂一点IOS和Android的相关知识。我给大家普及一点基础的IOS和Android的知识。

IOS有一个叫UIWebView的东西，这个东西就像是一个浏览器一样，有浏览器的基本功能，同时还可以调用一些原生的API，比如GPS定位。需要特别注意的是Safari浏览器使用的浏览器的控件和UIwebView并不是同一个，两者在性能上有一定的差距。IOS调用webviewjs代码是通过这样实现的：

```javascript
webview.stringByEvaluatingJavaScriptFromString
```

可以看出它和flex如出一辙，都是通过暴漏在window上的方法操作webview。 实际项目中通常将window上挂在一个对象专门存在此类操作，比如window.webview

上面讲了Native调用js，那么js如何调用Native呢？其实js调用Native是基于一个事件代理。 webview中所有的请求都会经过native中一个代理类，代理类进行拦截，如果是约定的格式（如调用原生方法）就解析，并调用原生方法。如果是其他的请求则放行。通常是下面的格式：

```javascript
jsbridge://class?callback/method?param1=value1&param2=value2
```

比如H5要实现一个原生的toast功能，可以这么去写：

```text
var url = 'jsbridge://feedback?onSuccess=xxx&onFail=xxxx/doToast?icon=图标&title=文字';  
var iframe = document.createElement('iframe');  
iframe.style.width = '0px';  
iframe.style.height = '0px';  
iframe.style.display = 'none';  
iframe.src = url;  
document.body.appendChild(iframe);  
setTimeout(function() {  
    iframe.remove();
}, 0);
```

安卓的交互模式大同小异。知道了交互的具体原理，那么对于不同端（IOS，Android）需要不同的方法。那么这时候去建立一个中间层做统一，通常是前端的js-api形式存在。比如钉钉的做法是引入 dingtalk-promise-lwp.js 将native 调用方法封装成一个简单的类库，并且屏蔽了不同端的差异。

还是以toast为例,代码如下：

```javascript
dd.device.notification.toast({
    type: "information", //toast的类型 alert, success, error, warning, information, confirm
    text: '这里是个toast', //提示信息
    duration: 3, //显示持续时间，单位秒，最短2秒，最长5秒
    delay: 0, //延迟显示，单位秒，默认0, 最大限制为10
    onSuccess : function(result) {
        /*{}*/
    },
    onFail : function(err) {}
})
```

1. Native

上面讲述了hybrid app实现了js调用原生模块的功能。本质上H5应用还是运行在webview中，通常一些模块还是基于DOM的，即使是原生模块也是基于代理的方式将js和 native联系在一起。 而Native 的方式则大又不同，它本质上就是Native，因此性能上又比hybrid好很多。比如广为人知的react native（以下简称RN） 和 weex，RN本质上是以React的方式书写代码，然后通过RN这一个中间层，将React转化并调用为原生Native代码，反之亦然。当然RN也可以退化到hybrid的实现方式，即以webview作为桥接层，很多为了兼容性都是这么做的。

RN 将原生模块通过导出供React使用,以下是RN官网示例代码：

```java
@Override
  public List<NativeModule> createNativeModules(
                              ReactApplicationContext reactContext) {
    List<NativeModule> modules = new ArrayList<>();

    modules.add(new ToastModule(reactContext));

    return modules;
  }
```

```javascript
import { NativeModules } from 'react-native';
module.exports = NativeModules.ToastExample;
```

```text
import ToastExample from './ToastExample';

ToastExample.show('Awesome', ToastExample.SHORT);
```

### 技术架构演进给我们的启示

技术架构在不断的演进，我们的目录结构，代码层次，公共逻辑抽离都不一样，我们看待问题的方式就要不断演进，架构也要不断演进。 那么架构发展中不变量是什么？如何根据不变量来规划软件架构？要回答这个问题，首先要明白软件开发的核心关注点，这个问题比较宽泛。那么 我以前端开发的角度来描述下，作为前端开发的核心关注点有哪些。

1. 页面结构

如何划分结构，如何组织页面。前端发展前期，页面结构划分的方式就是div，”前端“将一个个组件通过div构建出来，复用性几乎不存在。 随着技术进一步发展，组件的思想深入人心，人们通过组件描述应用的结构，将程序看作是一个个组件组成，进而有了一大批关于组件化开发的best pratice。 web-components就是组件化思想的官方表现。

1. 页面样式

如何实现设计稿的效果。 早期大家通过css2实现基本的网站布局。随着网站发展，交互越来越复杂，动画的要求越来越高。诸如css3， canvas动画技术浮出水面，这些技术都是为了更好的实现网站的样式。为了提高书写效率有了一批css预处理和后处理引擎，这些东西的出现都是为了提高样式产出的效率。为了解决css全局覆盖的问题，又出现了css module的方案。 为了使组件和样式更加复用，又出现了style compoent技术等等，不胜枚举。

1. 页面行为交互

如何完成产品的交互需求。网站早期页面的交互就是简单的登陆和提交留言等最基础的功能。现在网页做的越来越像一个app。复杂性可想而至，同一时刻，存在十几种状态更是家常便饭（已经请求了A资源，已经做了实名认证，已经通过了测验，已经提交了留言等等等，如果我愿意，我可以一直说下去）。那么如何维持越来越复杂的状态呢？由于状态之间不是独立存在的，某些状态可以依赖于别的状态，各种状态交织在一起，令人头疼。 因此为了解决这个问题，出现了诸如redux， vuex， mobx等数据管理软件，它们的目标就是做状态管理。

除此之外，还有很多关注点，但是最基本的就是这三个。这三点也是大多数前端必须要关注的事情。大家往往需要根据自己项目的实际情况对三者进行某种程度的取舍。对于其他的岗位，比如后端工程师，一样需要将后端的核心关注点找到，然后根据自己项目实际情况对关注点的实践进行一定的取舍。

### 模块和组件的划分依据

#### 根据业务划分

首先要明白，技术是服务业务的，业务依托于技术而存在。 任何脱离业务的技术都是意淫。 所以模块和组件的划分首先要站在业务角度划分，其次才是技术角度。 复杂的系统，最好先按业务领域横向拆分成可独立部署的子系统，每个子系统内部再按技术（主要是业务层和Web层）纵向拆分成不同的模块。

举个简单电商的例子, 可能这样划分模块：订单、会员、商品、优惠、支付。 每个模块都只负责自己的一块业务，同时对其他模块开放必要的接口。 这种情况下，哪个模块有变动，只要接口不变完全不影响其他应用。而商品上架就不适合划分为模块， 因为其本身属于不基础服务，不是给系统使用的，而是面向用户的，面向用户的就会有频繁的变动，就会不停匹配用户的需求。 因此一个原则就是根据业务划分出模块， 是**面向系统的，还是面向用户**的。

#### 根据技术划分

我们已经根据业务划分了模块，这时候需要将这些模块组合起来，对外提供服务。 因为最终产出的是产品，是面向用户的。如果组织模块，又落地到了技术问题。一个好的思路是分层，将基础服务下沉，将业务服务上浮。 实现了分层，但是模块间通信又是问题，目前业界比较好的解决方案是通过MQ 和 配置中心解耦。 模块之间 并不知道自己依赖的系统或者被谁依赖，所有需要的东西动过MQ注册，并通过配置中心管理，解决了模块之间的通信问题。因此模块划分的另一个原则是单一职能原则。

#### 总结

其他的划分原则还有高内聚低偶合，模块大小规模适当，模块的依赖关系适当等。又一个物理学名词熵，是一种测量在动力学方面不能做功的能量总数，也就是当总体的熵增加，其做功能力也下降，熵的量度正是能量退化的指标。熵亦被用于计算一个系统中的失序现象，也就是计算该系统混乱的程度。 软件开发本质上是处理复杂度的过程，软件总是趋向无序混乱发展。 但是架构得当，模块划分合适，可以将软件腐败速度降低。

### 模块和组件编写的技巧

前面讲述了模块和组件划分的依据。 假设我们已经正确合理地划分了模块。 是否就认为我们的代码结构就足够好，足够复用，足够方便调试呢？也不一定！ 原因在于组件内部通常也是有很多细小的逻辑的，由于这些逻辑没有必要（或者我们认为没有必要）抽离出来，在加上模块和组件内部状态复杂多变，各自牵扯就会造成模块内部代码难以复用和修改。

#### 抽象

[为什么我们的代码难以维护](https://my.oschina.net/wanjubang/blog/1580050)这边文章讲述了为什么我们的代码难以维护，其中讲了一个重要原因就是 系统抽象级别不够。那么如何编写抽象级别合适的代码呢？其中一个原则就是我们的代码描述的应该是一般逻辑，而将硬编码和分支策略做成可配置。其中硬编码部分和分支策略可以称之为程序的元数据。

> 为一般逻辑编码，为特殊逻辑配置。

我们平时工作中会使用一些设计模式，这些设计模式就是对具体业务逻辑进行抽象和提炼的结果。比如我们经常碰到同一时刻，同类型的Modal只能出现一个的需求。我们对这样的需求进行提炼，发现还有很多类似的逻辑。虽然它们本身所处的环境各不相同，但是我们可以将其中共性提炼出来，这就形成了单例模式。再比如我们现在要做 一个社保计算的功能，每一个省份的计算方式都不同，我们如何设计这个系统 ？ 先看下一般的写法：

```javascript
function getHenanSocialInsurance(salary){
    return salary * .4;
}

function getHebeiSocialInsurance(salary){
     return salary * .2;
}
const calSocialInsurance = (province, salary) => {
   let socialInsurance = 0;
   if (province === 'henan') {
    socialInsurance = getHenanSocialInsurance(salary);
   } else if (province === 'hebei') {
    // do something
    socialInsurance = getHebeiSocialInsurance(salary);
   }
   // ....

   return socialInsurance;
}
```

再来看下策略模式：

```javascript
// 封装的策略类
var strategies={
    "henan":function (salary) {
        return salary * .4;
    },
    "hebei":function (salary) {
        return salary * .2;
    },
};


// 调用方
var calSocialInsurance= (province, salary) => {
    return strategies[province](salary);
};
```

其实生活中还有很多类似的场景，我们对这样的场景进一步抽象，提炼出策略模式。为通用过程先编码，具体算法设计不同的实现，由调用方决定使用哪种策略（算法）。还有很多设计模式就不列举了，大家可以私底下自己去看。其实抽象是很重要的一个概念，linux操作系统将一切都抽象为文件，而文件又是对字节流的抽象。

> cat /dev/urandom &gt; /dev/dsp

包括很多的程序设计方法，比如面向对象编程，是将生活中的物体抽象成拥有属性和方法的对象，并通过封装继承等完成复杂的逻辑。 函数式编程是对代码逻辑运用数学的方法进行抽象，进而可以将数学中的定律（结合律，交换律，集合论等）运用到代码中。正是因为抽象，我们才能一步步构建出坚实的上层系统。

#### 保持模块和组件的纯粹性

**纯函数**

提到纯粹性，不提到另一个重要的概念-纯函数。 纯函数其实就是数学中的函数。

> 函数是不同数值之间的特殊关系：每一个输入值返回且只返回一个输出值。

它有一个重要的特征就是给定输入，输出是一定的。前面这句话可以从两方面理解，一是给定输入，输出一定，一对一。 另一点是没有副作用（side effect），副作用指的是函数经过运算不会对外界产生影响，当然也不依赖外界。 其实上面的两点是一点，没有副作用正是给定输入输出一定的前提。

换句话说，函数只是两种数值之间的关系：输入和输出。尽管每个输入都只会有一个输出，但不同的输入却可以有相同的输出。下图展示了一个合法的从 x 到 y 的函数关系；

![&#x56FE;2.5](https://raw.github.com/azl397985856/automate-everything/master/illustrations/图2.5.png)

（[http://www.mathsisfun.com/sets/function.html）](http://www.mathsisfun.com/sets/function.html）)

这也就要求我们的代码不依赖外部环境且不会对外界有影响，并且给定输入，输出一定（比如不可以是Math.random 这样的逻辑）。

**单一职责**

纯粹性除了上面的特征之外，还有一个重要特征是单一职责。 即一个功能函数只完成原子性功能，不要让函数做非常多的事情，保证其功能的纯粹。软件设计原则中有一个单一职责原则，描述的是如果一个模块承担的职责过多，就等于把这些职责耦合在一起了。一个职责的变化可能会削弱或者抑制这个类完成其他职责的能力。这种耦合会导致脆弱的设计，当发生变化时，设计会遭受到意想不到的破坏。而如果想要避免这种现象的发生，就要尽可能的遵守单一职责原则。此原则的核心就是解耦和增强内聚性。只有模块和组件满足单一职责原则，我们的代码才能够更好地被修改，扩展，才能真正地拥抱变化。

**总结**

通过上面方法，我们将模块和组件代码切分成满足单一职责的单元，并且每一个单元都尽可能地纯粹。通过这样的模块和组件构建出来的系统才能保证稳定性和健壮性

> 底层基础决定上层建筑

