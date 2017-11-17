# 页面记载性能优化
在互联网网站百花齐放的今天，网站响应速度是用户体验的第一要素，其重要性不言而喻，这里有几个关于响应时间的重要条件：

用户在浏览网页时，不会注意到少于0.1秒的延迟；

少于1秒的延迟不会中断用户的正常思维， 但是一些延迟会被用户注意到；

延迟时间少于10秒，用户会继续等待响应；

延迟时间超过10秒后，用户将会放弃并开始其他操作；

因此大家都开始注重性能优化，很多厂商都开始做一些性能优化。比较有名的是雅虎军规，不过随着浏览器和协议等的发展，有一些已经逐渐被淘汰了。因此建议大家以历史的目光看待它。比如.尽量减少HTTP请求数这一条，在HTTP2协议下就不管用了，因为HTTP2实现了HTTP复用，HTTP请求变少，反而降低性能。因此一定要结合历史环境看待具体的优化原则和手段，否则会适得其反。

> 雅虎军规中文版： http://www.cnblogs.com/xianyulaodi/p/5755079.html


## 一个经典问题
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

## 浏览器性能指标


### 几个关键的指标
1. 白屏时间

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

2. 首屏加载时间
3. 完全加载时间 

用户可以进行正常的事件输入交互操作

```js
domContentLoadedEventEnd - navigationStart
```

### Performance API
上面介绍了古老的方法测量关键指标，主要原理就是基于浏览器从上到下加载的原理。只是上面的方法比较麻烦，不适合实际项目中使用。 实际项目中还是采用打点的方式。 即在关键的地方埋点，然后根据需要将打点信息进行计算得到我们希望看到的各项指标，performance api 就是这样一个东西。

> The Performance interface provides access to performance-related information for the current page. It's part of the High Resolution Time API, but is enhanced by the Performance Timeline API, the Navigation Timing API, the User Timing API, and the Resource Timing API.          
> ------ 摘自MDN
  
  在浏览器console中输入performance.timing
  

![图4.2](https://github.com/azl397985856/automate-everything/blob/master/illustrations/%E5%9B%BE4.2.png)


返回的各字节跟下面的performance流程的各状态一一对应，并返回时间。


![图4.3](https://github.com/azl397985856/automate-everything/blob/master/illustrations/%E5%9B%BE4.3.png)

有了这个performance api 我们可以很方便的计算各项性能指标。并且我们可以自定义一些我们关心的指标，比如请求时间（成功和失败分开统计），较长js操作时间，或者比较重要的功能等。总之，只要我们想要统计的，我们都可以借助performance api 轻松实现。


> performance api 更多介绍请查看 https://developer.mozilla.org/en-US/docs/Web/API/Performance

## 性能监测的手段


## 性能优化的手段
