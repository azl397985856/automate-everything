# 前言

这是我准备写的第一本书，其实早些时候已经打算开始写书了，只是苦于没有写书经验，无从下手。写书不同于博客，写书需要将知识，经验等系统化地讲述出来，而我现在恰巧缺乏这种表现能力。因此我决定在这里将项目中零散的东西记录下来，然后后期润色一下，写成一本书。

软件工程之所以叫工程是因为其中运用了很多工程化的方法，早期的各种瀑布模型，UML建模语言，以及近期的SCRUM，他们中充斥着各种工程化的工具和方法。 知乎是这么描述软件工程的：软件工程 (Software Engineering) 是一门研究和应用如何以系统性的、规范化的、可定量的过程化方法去开发和维护软件，以及如何把经过时间考验而证明正确的管理技术和当前能够得到的最好的技术方法结合起来的学科。

本书主要分为四个部分来讲述软件开发的工程化。

* 第一部分讲述前后端如何分离，旨在通过任务分工，拆解，提高单个成员的开发效率，并通过减少前后端沟通进而提高项目整体成员的开发效率。这一章主要讲述为什么要前后端分离的历史，为什么要前后端分离，如何做到前后端分离，以及前后端分离能够带来哪些好处。
* 第二部分讲述模块化与组件化。高级语言有很多模块化的思想。比如高级语言中的类和实例，各种第三方库，前端的web-components等等。可以说模块化和组件化无处不在，那为什么还要花时间去讲述这些东西呢？记得大学时候学习C#的时候老师讲过面向对象的四个基本特性是抽象，继承，封装和多态。其实面向对象正是软件开发早期对模块化和组件化的一种探索，是人类智慧的结晶。其实模块化和组件化是一种**将复杂系统分解为更好的可管理模块**的方式。这一章主要讲述模块化的发展，模块化在不同领域的表现，模块化给我们带来了什么，以及如何实现模块化。
* 第三部分将流程自动化。自动化的流程无疑可以减少人力成本，可以提高效率，降低失误率。自动化可以将人们从繁琐的无味的重复劳动中解脱出来。有人说程序员是懒惰的，因为它们经常编写一些可以减少日常工作的工具，从而达到”偷懒“ 的目的。这一章主要讲述流程自动化是一种什么样的感受，如何实现自动化，这一章主要分享我在实际工作中遇到的问题，以及自己在自动化方面作出的思考。
* 第四部门主要讲述页面加载性能优化。 性能优化一直是一个非常古老，并且非常深奥的问题。说他古老是因为，我们的产品是让用户使用的，良好的用户体验是抓住用户内心的根本，因此性能优化方面，业界做了非常多的探索。说他非常深奥是因为没有任何一种优化手段可以cover 全部， 这就需要你能够靠你的经验和眼界分辨现实情况，采取合适的优化方法和时机（不要过早优化）。这部门主要讲述web性能优化，从用户输入URL到页面加载出来，用户进行交互着手分析可以优化的点，并讲述如何发现**系统性能的短板。** 建立性能监控平台也是非常重要的一环，本章也会带你一步步拆解，讲述如何设计并开发一个性能监控平台。

![https://github.com/azl397985856/automate-everything/blob/master/chapter1.md](第一章 前后端分离)
![https://github.com/azl397985856/automate-everything/blob/master/chapter2.md](第二章 模块化与组件化)
![https://github.com/azl397985856/automate-everything/blob/master/chapter3.md](第三章 流程自动化)
![https://github.com/azl397985856/automate-everything/blob/master/chapter4.md](第四章 页面加载性能优化)

