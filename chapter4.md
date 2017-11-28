# 页面加载性能优化
在互联网网站百花齐放的今天，网站响应速度是用户体验的第一要素，其重要性不言而喻，这里有几个关于响应时间的重要条件：

用户在浏览网页时，不会注意到少于0.1秒的延迟；

少于1秒的延迟不会中断用户的正常思维， 但是一些延迟会被用户注意到；

延迟时间少于10秒，用户会继续等待响应；

延迟时间超过10秒后，用户将会放弃并开始其他操作；

因此大家都开始注重性能优化，很多厂商都开始做一些性能优化。比较有名的是雅虎军规，不过随着浏览器和协议等的发展，有一些已经逐渐被淘汰了。因此建议大家以历史的目光看待它。比如.尽量减少HTTP请求数这一条，在HTTP2协议下就不管用了，因为HTTP2实现了HTTP复用，HTTP请求变少，反而降低性能。因此一定要结合历史环境看待具体的优化原则和手段，否则会适得其反。

> 雅虎军规中文版： http://www.cnblogs.com/xianyulaodi/p/5755079.html


## 一个经典问题

让我们回忆一下浏览器从加载url开始到页面展示出来，经过了哪些步骤：

1. 浏览器调用loadUrl解析url
2. 浏览器调用DNS模块，如果本地游dns缓存则直接返回IP。 否则查询本机DNS，并逐层往上查找。返回IP则将其存到DNS缓存并设置过期时间。

> chrome://dns/ 可以查看chrome缓存的dns记录

3. 浏览器调用网络模块。 网络模块和目标IP 建立TCP连接，途中经过三次握手。

4. 浏览器发送http请求，请求格式如下：

```js
header
(空行)
body
```

5. 请求到达目标机器，并通过端口与目标web server 建立连接。

6. web server 获取到请求流，并对请求流进行解析，最终返回响应流到前端。

7. 前端根据相应的内容，下载文档（content download），并对文档进行解析。解析的过程如下所示：

![图4.1](https://github.com/azl397985856/automate-everything/blob/master/illustrations/%E5%9B%BE4.1.png)

知道了浏览器加载网页的步骤，我们就可以从上面每一个环节采取”相对合适“的策略优化加载速度。 比如上面第二步骤会进行dns查找，那么dns查找是需要时间的，如果提前将dns解析并进行缓存，就可以减少这部分性能损失。在比如建立TCP连接之后，保持长连接的情况下可以**串行**发送请求。熟悉异步的朋友肯定知道串行的损耗是很大的，它的加载时间取决于资源加载时间的和。而采取**并行**的方式是所有加载时间中最长的。这个时候我们可以考虑减少http 请求或者使用支持并行方式的协议（比如HTT2协议）。如果大家熟悉浏览器的原理或者仔细观察网络加载图的化，会发现同时加载的资源有一个上限（根据浏览器不同而不同），这是浏览器对于单个域名最大建立连接个数的限制，所以可以考虑增加多个domain来进行优化。类似的还有很多，留给大家思考。

## 浏览器性能指标


### 几个关键的指标
#### 白屏时间

用户从打开页面开始到有页面开始呈现为止。白屏时间长是无法忍受的，因此有了很多的缩短白屏时间的方法。 比如减少首屏加载内容，首屏内容渐出等。白屏的测量方法最古老的方法是这样的：

```html
<head>
<script>
var t = new Date().getTime();
</script>
<link src="">
<link src="">
<link src="">

<script>
tNow = new Date().getTime() - t;
</script>
</head> 
```

> 但是上面这种只能测量首屏有html内容的情况，比如像react这样客户端渲染的方式就不行了。如果采用客户端渲染的方式，就需要在首屏接口返回，
并渲染页面的地方打点记录。 

通过类似的方法我们还可以查看图片等其他资源的加载时间，以图片为例：

```js
<img src="xx" id="test" />
<script>
var startLoad = new Date().getTime()
document.getElementById('test').addEventListener('load', function(){
var duration = new Date().getTime() - startLoad();
}, false)
</script>

```

通过这种方法未免太麻烦，还在浏览器performance api 提供了很多有用的接口，方便大家计算各种性能指标。下面performance api 会详细讲解。

#### 首屏加载时间
我们所说的首屏时间，就是指用户在没有滚动时候看到的内容渲染完成并且可以交互的时间。至于加载时间，则是整个页面滚动到底部，所有内容加载完毕并可交互的时间。用户可以进行正常的事件输入交互操作。

```js

firstscreenready - navigationStart

```

这个时间就是用户实际感知的网站快慢的时间。firstscreenready 没有这个 performance api， 而且不同的渲染手段（服务端渲染和客户端渲染计算方式也不同），不能一概而论。具体计算方案，这边文章写得挺详细的。[首屏时间计算](http://www.alloyteam.com/2016/01/points-about-resource-loading/)

#### 完全加载时间 

通常网页以两个事件的触发时间来确定页面的加载时间.

1. DOMContentLoaded 事件，表示直接书写在HTML页面中的内容但不包括外部资源被加载完成的时间，其中外部资源指的是css、js、图片、flash等需要产生额外HTTP请求的内容。

2. onload 事件，表示连同外部资源被加载完成的时间。

```

domComplete - domLoading

```

### Performance API
上面介绍了古老的方法测量关键指标，主要原理就是基于浏览器从上到下加载的原理。只是上面的方法比较麻烦，不适合实际项目中使用。 实际项目中还是采用打点的方式。 即在关键的地方埋点，然后根据需要将打点信息进行计算得到我们希望看到的各项指标，performance api 就是这样一个东西。

> The Performance interface provides access to performance-related information for the current page. It's part of the High Resolution Time API, but is enhanced by the Performance Timeline API, the Navigation Timing API, the User Timing API, and the Resource Timing API.          
> ------ 摘自MDN
  
  在浏览器console中输入performance.timing
  

![图4.2](https://github.com/azl397985856/automate-everything/blob/master/illustrations/%E5%9B%BE4.2.png)


返回的各字节跟下面的performance流程的各状态一一对应，并返回时间。


![图4.3](https://github.com/azl397985856/automate-everything/blob/master/illustrations/%E5%9B%BE4.3.png)

有了这个performance api 我们可以很方便的计算各项性能指标。如果performamce api ”埋的点“不够我们用，我们还可以自定义一些我们关心的指标，比如请求时间（成功和失败分开统计），较长js操作时间，或者比较重要的功能等。总之，只要我们想要统计的，我们都可以借助performance api 轻松实现。


> performance api 更多介绍请查看 https://developer.mozilla.org/en-US/docs/Web/API/Performance

## 性能监测的手段

### 日志

> 监控是基于日志的

一个格式良好，内容全面的日志是实现监控的先觉条件，可以说基础决定上层建筑。
良好的的日志系统通常有以下几个部分构成：

1. 接入层

日志由产生到进入日志系统的过程。 比如rsyslog生成的日志，通过logstash（transport and process your logs, events, or other data）接入到日志系统。这种比较简单，由于是基于原生linux的日志系统，学习使用成本也比较低。公司不同系统如果需要介入日志系统，只需要将日志写入log目录，通过logstash等采集就可以了。

2. 日志	处理层

这部分是日志系统的核心，处理层可以将接入层产生的日志进行分析。过滤日志发送到监控中心（监控系统状态）和存储中心（数据汇总，查询等）

3. 日志存储层

将日志入库，根据业务情况，建立索引。这部分通常还可以入elastic search库，提供日志的查询，上面的logstash 就是elastic家族的。

除了上面核心的几层，通常还有其他层完成更为细化的工作。

通常来说，一个公司的日志有以下几个方面

1. 性能日志

记录一些关键指标，具体的关键指标可以参阅“浏览器性能指标”一节。

2. 错误日志

记录后端的服务器错误（500，502等），前端的脚本错误（script error）。

3. 硬件资源日志

记录硬件资源的使用率，比如内存，网络带宽和硬盘等。

4. 业务日志

记录业务方比较关心的用户的操作。方便根据用户报的异常，定位问题。

5. 统计日志 

统计日志通常是基于存储日志的内容进行统计。统计日志有点像数据库视图的感觉，通过视图屏蔽了数据库的结构信息，将数据库一部分内容透出到用户。用户行为分析等通常都是基于统计日志分析的。有的公司甚至介入了可视化的日志统计系统（比如Kibana）。随着人工智能的崛起，人工智能+日志是一个方向。

### 性能日志
#### 性能日志的产出
除了上面介绍的关键指标记录。我们通常还比较关心接口的响应速度。这时候我们可以通过打点的方式记录。
#### 性能日志的消费
我们已经产出了日志，有了日志数据源。那么如何消费数据呢？ 现在普遍的做法是服务端将收集的日志进行转储，并通过可视化手段（图标等）展示给管理员。还有一种是用户自己消费，即自产自销。用户产生数据，同时自己消费，提供更加的用户体验。 详情查阅 [locus](https://github.com/azl397985856/locus)
### 性能监测平台
监控平台大公司基本都有自己的系统。比如有赞的Hawk，阿里的SunFire。小公司通常都是使用开源的监控系统或者干脆没有。 我之前的公司就没有什么监控平台，最多只是阿里元提供的监控数据而已。所以我在这一方面做了一定的探索。并开始开发[朱雀平台](https://github.com/azl397985856/zhuque)，但是限于精力有限，该计划最后没有付出实践，还是蛮可惜的。性能监测的本质是基于监测的数据，提供方便的查询和可视化的统计。并对超过临界值（通常还有持续时长限制）发出警告。
上一节介绍了性能监控平台，提到了性能监控平台的两个组成部分，一个是生产者一个是消费者。 这节介绍如何搭建一个监控平台。那么我先来看下整体的架构

![图4.4](https://github.com/azl397985856/automate-everything/blob/master/illustrations/%E5%9B%BE4.4.png)

为了方便讲解，这里只实现一个最简化的模型，读者可以在此基础上进一步划分子系统，比如接入SSO，存储展示分离等。

#### 客户端
客户端一方面上报埋点信息，另一方面上报轨迹信息。 

##### 上报埋点信息

这一部分主要借助一些手段，比如performance api 将网页相关加载时间信息上报到后端。

```js
performance.getEntriesByType("resource").forEach(function(r) {
    console.log(r.name + ": " + r.duration)
})

```

另一方面对特定的异步请求接口，打点。对用户所有的交互操作打点（点击，hover等）

```js

const startTime = new Date().getTime();

fetch(url)
.then(res => {
   const endTime = new Date().getTime();
   report(url , 'success', endTime - startTime);
})
.catch(err => {
	const endTime = new Date().getTime();
	report(url, 'failure', endTime - startTime);
})

```

##### 上报轨迹信息

上传轨迹信息就简单了。如果是页面粒度，直接在页面上报就可以了。如果使用了前端路由，还可以在路由的钩子函数中进行上报。


```js
pageA.js

// 上报轨迹
report('pageA',{userId: '876521', meta: {}})

```

这样我们就有了数据源了。

#### 服务端
服务端已经有了数据，后端需要将数据进行格式化，并输出。

##### locus server

##### zhuque server
## 性能优化的手段
要做性能优化，首先要对系统运行的过程有一个完整的理解，然后从各个环节分析，找到系统瓶颈，从而进行优化。在这里我不罗列性能优化的各种手段，而是从前端三层角度逐个描述下性能优化的常见优化方向和手段。如果大家希望有一个完整的优化清单， 这里有一份比较完整的[Front-End-Checklist](https://github.com/thedaviddias/Front-End-Checklist)，对于性能优化，有一定的借鉴意义。

很多人都知道，前端将应用分为三层，分别是结构层，表现层和行为层。我们就从三层角度讲一下性能优化的方向。

### 结构层
结构层指的是DOM结构，而DOM结构**通常**是由HTML结构决定的,因此我们主要分析下HTML结构的性能优化点。
我们知道DOM操作是非常昂贵的，这在前面讲述前端发展历史的时候也提到了。如何减少DOM数量，减少DOM操作是优化需要
重点关注的地方。
#### AMP HTML
说到HTML优化，不得不提AMP HTML。
AMP的核心思想是提供移动端更佳的用户体验，。由AMP HTML， AMP JS 和 AMP Cache 三个核心部分组成。

> AMP HTML is HTML with some restrictions for reliable performance.

下面是典型的AMP HTML

```html
<!doctype html>
<html ⚡>
 <head>
   <meta charset="utf-8">
   <link rel="canonical" href="hello-world.html">
   <meta name="viewport" content="width=device-width,minimum-scale=1,initial-scale=1">
   <style amp-boilerplate>body{-webkit-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-moz-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-ms-animation:-amp-start 8s steps(1,end) 0s 1 normal both;animation:-amp-start 8s steps(1,end) 0s 1 normal both}@-webkit-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-moz-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-ms-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-o-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}</style><noscript><style amp-boilerplate>body{-webkit-animation:none;-moz-animation:none;-ms-animation:none;animation:none}</style></noscript>
   <script async src="https://cdn.ampproject.org/v0.js"></script>
 </head>
 <body>Hello World!</body>
</html>
```
可以看出AMP HTML由普通的HTML标签和amp标签组成。amp标签是做什么呢？且听我跟你说，DOM虽然操作比较昂贵，但是不同的DOM效率也是不意义的。比如渲染一个a标签和渲染一个img或者table时间肯定不是一样的。我们称a标签这样渲染较快的元素为轻元素，称table，img这样的元素为重元素。那么我们就应该尽量避免重元素的出现，比如table 可以采用ul li 实现。
img之所以比较慢的原因是图片下载虽然是异步的，但是会占用网络线程，同时会多发一个请求（浏览器并发请求数是有限制的），因此可以进一步封装称轻元素（比如x-image，组件内部可以延迟发送图片请求，等待主结构渲染完毕再发图片请求）。 可以考虑将其封装为web-component或者其他组件形式（如react组件）

回到刚才AMP HTML， 其实在amp 中 有一个amp-image这样的接口，大概可以根据需要自己实现，上面我们说的x-image 其实就是实现了amp接口规范的组件。

#### 减少没有必要的嵌套
前面说到了尽可能使用轻元素。那么除了使用轻元素，还有一点也很重要，就是减少DOM数量。减少DOM数量的一个重要的途径就是减少冗余标签。比如我们通过新增加一个元素清除浮动。

```html
<div class="clear"></div>
```

不过目前都是采用伪元素实现了。另一个途径是减少嵌套层次。很多人都会在`<form>`或者`<ul>`外边包上`<div>`标签，为什么加上一个你根本不需要的`<div>`标签呢？实际上你完全可以用CSS selector，实现同样的效果。 重要的是，这种代码我见过很多。
	
```html
<div class="form">
	<form>
	...
	</form>
</div>
```

完全可以这样写:

```html
<form class="form">
	...
</form>

```

### 表现层

### 行为层
行为层指的是用户交互方面的内容，在前端主要通过JavaScript实现。目前JavaScript 规范已经到es2017。 前端印象较为深刻的是
ES6（也就是ES2015），因为ES5是2009年发布的，之后过了6年，也就是2015年ES6才正式发布，其中增加了许多激动人心的新特性，
被广大前端所熟悉。甚至曾一度称目前前端状态是536（HTML5，CSS3，ES6），可见其影响力。

毫不夸张的说，目前前端项目绝大多数代码都是javascript。既然js用的这么多，为什么很少有人谈js性能优化呢？ 一是因为现在工业技术的发展，硬件设备的性能提升，导致前端计算性能通常不认为是一个系统的性能瓶颈。二是随着V8引擎的发布，js执行速度得到了很大的提升。三是因为计算性能是本地CPU和内存的工作，其相对于网路IO根本不是一个数量级，因此人们更多关注的是IO方面的优化。那么为什么还要将js性能优化呢？一方面是目前前端会通过node做一些中间层，甚至是后端，因此需要重点关注内存使用情况，这和浏览器是大相径庭的。另一方面是因为前端有时候也会写一个复杂计算，也会有性能问题。 最后一点是我们是否可以通过JS去优化网络IO的性能呢，比如使用JS API 操作 webWorker 或者使用localStorage缓存。

#### 计算缓存
前端偶尔也会有一些数据比较大的计算。 对于一些复杂运算，通常我们会将计算结果进行缓存，以供下次使用。前面提到了纯函数的概念，要想使用计算缓存，就要求函数要是纯函数。一个简单的缓存函数代码如下：

```js

// 定义
function memoize(func) {
  var cache = {};
  var slice = Array.prototype.slice;

  return function() {
    var args = slice.call(arguments);

    if (args in cache)
      return cache[args];
    else
      return (cache[args] = func.apply(this, args));

  }
}
// 使用
function cal() {}
const memoizeCal = memoize(cal);

memoizeCal(1) // 计算，并将结果缓存
memoizeCal(1) // 直接返回

```

#### 网络IO缓存
前面讲了计算方面的优化，它的优化范围是比较小的。因为并不是所有系统都会有复杂计算。但是网络IO是所有系统都存在的，而且网络IO是不稳定的。网络IO的
速度和本地计算根本不是一个数量级，好在我们的浏览器处理网络请求是异步的（当然可以代码控制成同步的）。一种方式就是通过本地缓存，将网络请求结果存放到本地，在下次请求的时候直接读取，不需要重复发送请求。一个简单的实现方法是：

```js
function cachedFetch(url, options) {
  const cache = {};
  if (cache[url]) return cache[url];
  else {
   return fetch(url, options).then(res) {
      cache[url] = res
      return res;
   }
  }
}

```

当然上面的粗暴实现有很多问题，比如没有缓存失效策略（比如可以采用LRU策略或者通过TTL），但是基本思想是这样的。
这种方式的优点很明显，就是**显著**减少了系统反馈时间，当然缺点也同样明显。由于使用了缓存，当数据更新的时候，就要考虑缓存更新同步的问题，否则会造成数据不一致，造成不好的用户体验。
#### 数据结构，算法优化
数据结构和算法的优化是前端接触比较少的。但是如果碰到计算量比较大的运算，除了运用缓存之外，还要借助一定的数据结构优化和算法优化。
比如现在有50，000条订单数据。
```js
const orders = [{name: 'john', price: 20}, {name: 'john', price: 10}, ....]

```
我需要频繁地查找其中某个人某天的订单信息。 我们可以采取如下的数据结构：

```js
 const mapper = {
   'john|2015-09-12': []
 }
```
这样我们查找某个人某天的订单信息速度就会变成O(1)，也就是常数时间。

#### 内存泄漏
