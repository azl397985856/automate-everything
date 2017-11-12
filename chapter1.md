# 前后端分离
 
 ## 前后端分离的历史
软件开发早期是没有前后端分离的概念的，为什么？因为当时压根儿就没有前端工程师，专门的前端工程师大约是在2005年。在此之前前端是不受重视的。这其实和软件的发展有关，说到这里又不得不提到js的发展历史。JavaScript诞生于1995年。起初它的主要目的是处理以前由服务器端负责的一些表单验证。后来大家对页面的要求越来越高，js又给web多了一些动态功能。大家对前端的需求就是展示静态内容或者简单的动态内容（比如CGI返回数据拼接输出“动态”内容）

## 前后端分离
1998年ajax技术的出现，允许客户端脚本发送HTTP请求（XMLHTTP），并且局部刷新页面，这种突破性的创新使得web高速发展，推动了web的发展。随着HTML5，CSS3，ES6（简称356）的出现，web正以前所未有的速度前进，web工程师从无到有，再到现在web工程师被赋予了很多花环，机遇和挑战。也因此前后端不得不逐渐的分离。
## 前后端合并
正所谓合久必分，分久必合。 前几年被热炒的全栈工程师，以及前后端同构技术都反映了前端端在分离之后又逐渐合并的迹象。为什么会这样呢？前后端分离虽然减少了后端开发的工作（最初前端都是后端写的，比如.net 的 aspx  java的jsp等）。但前后端分离地不干净导致一些沟通问题反而不如一个人来做。为了解决这个问题，主要又两种方式：
* 全栈工程师。 将所有工作交给一个人，或者有着前后端知识的一群人，这样沟通起来成本比较低。
* 稍后讲。
## 那还又必要分离吗？
上面讲述了前后端合并，那么还有必要分离吗？ 非常又必要！！！

刚才说为了解决前后端沟通问题主要又两种方式，下面说下第二种。

* 建立中间层。 前后端问题产生的原因主要是两个，一个是知识背景，技术栈不同，难以互相理解。二是 前后端是一个依赖的关系，前端需要依赖后端的数据接口，因此存在工作上的先后关系。 建立中间层可以有效减少上述的问题。 目前淘宝，挖财，51，有赞，二维火都在尝试这种方式。这也是我将会在后面重点描述的方式。

最近出现的docker容器技术等有效地减少了后端的工作量，让后端更加专注业务逻辑的编写。我觉得node的出现也大概如此吧，它的出现不是用来替代apache成为下一个web容器，我觉得他更是扩展了前端的领域，使得可以将一部分后端无法做（比如ssr）或者不好做（不愿意做）的东西（比如接口聚合），移交给前端。


> 我不喜欢被冠以前端工程师，后端工程师的帽子。 我喜欢工程师这个称呼，我觉得我是以工程化的方式解决问题的角色，而不应该限定于某一部分职责。这个我主张前后端分离不矛盾，每个角色都自己的分工，都有自己精细化的一面。我们不试图取代后端，我们只是专注做好自己罢了。

# 前后端耦合
我有幸在我的职业生涯中经历了前后端耦合，前后端开发分离，部署分离的“分离阶段”， 以及大厂（alibaba）在这些方面的解决方案，所以非常荣幸能够将自己的经验和心得与大家分享。

最初在百世做前端的时候，我经历了前后端极度耦合的方式前后端虽然由不同的人来书写，那恰好也是我的领导首次尝试前后端分离模式，在此之前前后端通常都是一名后端人员来写的。这种模式虽然将前后端工作进行了切分，但是其耦合的成分还是非常大。

首先我们来看下我们的具体做法。
1. 前后端约定接口。
2. 前端根据后端接口进行开发。
3. 后端进行开发。（2与3基本并行）
4. 前端打包代码，并将代码发送给后端。后端放到war包下。

tomcat/conf/web.xml配置如下：

```xml
<welcome-file-list>
<welcome-file>index.html</welcome-file>
<welcome-file>index.htm</welcome-file>
<welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```

将war文件直接复制到tomcat/webapps下

我们后端使用的是java，具体做法：

* 如果是Intellij Idea,在导入前端项目之后，右键项目 add framework support --> web application，这时将会把前端项目转换为一个javaweb项目，然后将静态资源放在生成的web目录下即可。
* 如果是eclipse，可以新建一个javaweb项目然后将静态资源放入web或者webcontent目录下，或者直接先导入前端项目，然后通过 project facts 将项目转换为dynamic web项目并勾选 js等相关配置。
然后，运行项目时把后端的war包和前端的war包一同添加到 deployment中运行即可

这一阶段我们实现了初步的前后端分离。 但是上述做法有两个问题。
1. 由于上述的做法存在严重的问题在于前端对于发布控制力明显不足，比如版本控制不好做。 另外由于发布通常需要两个编译环境，即jdk编译后端代码，node环境编译前端代码。前端通常需要安装后端环境如jdk，后端也需要安装前端环境如node。不管是学习成本还是沟通成本都是一个问题。

2. 前端通常需要等待后端给出测试数据后才能开工。所以我上面说“基本并行”

由于存在上面的两个问题，我们进一步探索出了下面的前后端”分离“模式。

## 前后端“分离”
这一阶段我们通过nginx将前后端部署分离，通过mock服务器将前后端时间上的耦合给减少了。
下面是我们的具体做法：
1. 前后端约定接口
2. 前端开发
3. 后端开发（2和3完全并行）
4. 前端打包，并将代码通过ftp上传到文件服务器。
5. 配置nginx将静态请求代理到上面发布文件的文件服务器，后台接口代理到apache web server。

nginx 配置 通常是这样的：

```xml
server {
        listen       80;
        server_name  example.com;

        charset utf-8;

        #access_log  logs/host.access.log  main;
        
        location / {
              proxy_pass http://tomcat_pool;
              proxy_redirect off;  
              proxy_set_header HOST $host;  
              proxy_set_header X-Real-IP $remote_addr;  
              proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  
              client_max_body_size 10m;  
              client_body_buffer_size 128k;  
              proxy_connect_timeout 90;  
              proxy_send_timeout 90;  
              proxy_read_timeout 90;  
              proxy_buffer_size 4k;  
              proxy_buffers 4 32k;  
              proxy_busy_buffers_size 64k;  
              proxy_temp_file_write_size 64k;  
        }
        
        location ~ .*\.(html|htm|gif|jpg|jpeg|bmp|png|ico|txt|js|css|woff|woff2|ttf|eot|map)$  {     
             root D:\Workspaces\front-end;
             index index.html;
        }
```

## 大厂的方案
这里主要介绍下阿里巴巴-钉钉的前后端分离解决方案。阿里这样的大厂通常有着自己内部的成熟系统支撑业务，比如阿里的持续集成系统aone，配置服务diamond，分布式服务系统hsf等等。 小公司通常没有经济实力去搭建自己的内部系统，我有幸接触到了这些业界较为顶级的系统，对它们的运作方式有了一定的了解。这里简单介绍下阿里巴巴-钉钉的前后端分离解决方案。
钉钉的具体做法如下：

1. 前后端约定接口
2. 约定接口上dip（一个mock服务器）
3. 前端开发
4. 后端开发（2和3完全并行）
5. 前端打包，push触发测试环境和预发环境的云构建，
打tag触发线上环境云构建自动发布静态资源到cdn。
6. 需要发布只需要负责人修改diamond 配置即可，版本控制变得简单。

可以发现这种方式其实就是比上面多了两个点，一是云构建自动化，二是diamond配置。 云构建其实属于自动化，我将在流程自动化里面详细描述，云构建本身和
前后端分离没有关系。 其实两者的区别在于diamond配置。那么为什么配置diamond是前后端分离的工作呢？作为一个配置服务，diamond将专业性比较强的东西抽离出来，将包括但不限于版本管理的内容可以交给没有任何前端背景的人来做。
## 前后端分离的理想方式
上面的这种方法是非常高效的一种方式，但仍然不是我认为的理想的前后端分离方式。
我认为理想的前后端分离方式是后端提供纯粹的接口，**只需要提供数据**-系统的数据或者根据根据二方库获取数据返回前端，剩下的逻辑前端做。
这样由于后端提供元数据，前端只需要组合，前后端在逻辑和时间上没有了耦合。先来一张图来描述下：
![图1.1](https://github.com/azl397985856/automate-everything/blob/master/illustrations/%E5%9B%BE1.1.png)

如图，后端只是提供原子数据，保证数据稳定输出就可以了，事实上保证系统稳定很多已经是运维再做的事了。前端需要根据需要进行接口整合，服务端渲染，mock数据等工作。
那么整个流程具体是怎么工作的呢？ 可以下面这张图：
![图1.2](https://github.com/azl397985856/automate-everything/blob/master/illustrations/%E5%9B%BE1.2.png)

可以看出请求首先留到ngxin（反向代理），nginx判断是否是静态请求（html），如果是则转发到node服务器，node服务器会判断是否需要进行ssr，如果需要则调用后台接口拼装html，将**html和应用状态**一起返回给前端。 否过不需要ssr，则直接返回静态资源，并设置缓存信息。
如果不是静态资源，判断头部信息（比如有一个自定义字段reselect: 'node' | ''），是否需要请求合并，如果需要则请求到node端，如果不需要直接转发给后端服务器。
ngxin配置大概是这样：

```xml
map $reselect/node $reselect {
  default "";
  "node" "reselect/node";
}

server {
  listen                    80;
  server_name               demo.io;

  charset                   utf-8;
  autoindex                 off;

  location / {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-Port $server_port;
    proxy_set_header X-Forwarded-Ssl on;

    if ($reselect="reselect/node"){
      proxy_pass      http://node-demo.io;
      break;
    }

    proxy_pass      http://java-demo.io;
  }
  location ~ .*\.(html|htm|gif|jpg|jpeg|bmp|png|ico|txt|js|css|woff|woff2|ttf|eot|map)$  {     
       root D:\Workspaces\front-end;
       index index.html;
  }
}

```

## 总结
本章介绍了四种前后端分离的方式和阶段，这里需要强调的是并不是越往后的方式越好，问题的关键点还是选择合适的
方式，根据当前所处阶段选择合适的分离方式，提高单体和整体作战效率才是明智之举。


