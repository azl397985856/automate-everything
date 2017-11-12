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

## 什么是组件化

提到组件化，不得不提web-component。有了web-component代码就可以这么写：

```
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

## 模块化和组件化的好处

## 怎么合理划分模块和组件
