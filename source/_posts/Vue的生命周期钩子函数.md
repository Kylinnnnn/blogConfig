---
title: Vue的生命周期钩子函数
date: 2022-01-10 18:04:30
tags: Vue 前端 Javascript
---
看文档想到Vue的8个生命周期钩子函数除了文档中的介绍外还有些值得一记的点。
<!--more-->
##### 生命周期钩子函数简介
首先是文档中列举出的八个生命周期钩子函数:
* beforeCreate
* created
* beforeMount
* mounted
* beforeUpdate
* updated
* beforeDestroy
* destroyed

除此以外还有三个较为特殊的钩子函数，一是keep-alive缓存组件才会有的生命周期钩子函数：
* activated
* deactivated,
从名字就能猜到二者分别是缓存组件被显示和缓存组件被隐藏时触发；二是档子孙组件出错时，会调用名为errorCaptured的钩子函数。但是此三者不甚了解，于此不谈，详细可见文后参考链接。
##### 生命周期
对于生命周期函数来说，最为重要的莫过于其在生命周期中触发的时刻与顺序。
![](https://kylinnnnn.github.io/img/20220110-Vue的生命周期钩子函数01.png)
根据Vue文档的上图，可以作出一些解读。

/*TODO*/
各钩子函数之间，Vue实例具体发生了哪些变化。
##### 拓展
/*TODO*/
之前见过的一些面试题及思考的点。
* 浏览器页面的生命周期钩子函数与Vue的生命周期钩子函数（window.onload和DOMContentLoaded)