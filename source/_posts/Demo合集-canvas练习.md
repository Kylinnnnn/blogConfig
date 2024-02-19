---
title: Demo合集-canvas练习
date: 2023-01-24 21:48:20
tags:
    - 前端
    - React
    - Canvas
---

### 前言

想实践练习一下 canvas，简单复现一下原神一个网页活动的加载页面的一些部分。本文主要是记录一下实现过程中的一些收获。
<!--more-->
### 正文

#### (一)、在 React 中使用 ref 获取 DOM 元素

在原生 js 中，要想对 canvas 进行操作，需要先使用`document.getElementById`等方法获取 canvas 的 DOM 元素从而进一步获取 canvas 的上下文。虽然这些原生的获取 DOM 元素的方法在 React 中也可行，但在 React 中也有 React 自己的做法。

在 React 的函数式组件中（在类组件中使用`createRef`，两者用法相似但不互通），可以通过`useRef`钩子创建一个变量。然后将这个变量赋予 canvas 标签的 ref 属性。这样，在组件挂载之后，就可以通过变量的`current`属性去获取 DOM 元素。

```javascript
import { useEffect, useRef } from "react";
const Index = () => {
    const canvasRef = useRef();
    useEffect(() => {
        //获取上下文
        const ctx = canvasRef.current.getContext("2d");

        //对canvas进行操作...
    }, []);
    return (
        <div>
            <canvas ref={canvasRef}></canvas>
        </div>
    );
};

export default Index;
```

#### (二)、使用 canvas 从一张 png 截取不同部分的素材

在使用大量图片的网页中，为了减少 Http 的请求量，会将许多素材放在一张 png 图片中。而当 canvas 使用这些素材时就需要进行截取。canvas 使用`drawImage`函数将图片绘制到画布上。

`drawImage`有三种传参方式：

1.`drawImage(image,dx,dy)`

-   `image`: 图片资源。
-   `dx`: 图片绘制在画布上的起始横轴位置(以左上角为源点，以右侧为正方向)。
-   `dy`: 图片绘制在画布上的起始纵轴位置(以下侧为正方向)。

    2.`drawImage(image,dx,dy,dWidth,dHeight)`

-   `dWidth`: 图片绘制在画布上的宽度。
-   `dHeight`: 图片绘制在画布上的高度.

    3.`drawImage(image,sx,sy,sWidth,sHeight,dx,dy,dWidth,dHeight)`

-   `sx`: 在图片上截取区域的起始横轴位置(以左上角为源点，以右侧为正方向)。
-   `sy`: 在图片上截取区域的起始纵轴位置(以下侧为正方向)。
-   `sWidth`: 在图片上截取区域的宽度。
-   `sHeight`: 在图片上截取区域的高度。

通过第三种传参方式，就可以实现在 png 图片上截取素材。

#### (三)、canvas 动画的启动、暂停与继续

我想用 web 动画的形式去代替进度条的读取过程(因为不知道怎么去实现进度条随文件读取状况试试变动，原页面好像是使用了一个专门的引擎)。

首先，动画的形式采用`requestAnimationFrame`函数来实现。向这个函数传递用于实现动画的函数名作为参数，然后这个函数能在下一帧调用一次实现动画的函数，通过不断地刷新页面，也就实现了动画的效果，从另一方面来讲，动画的帧率也与显示器帧率保持一致(不进行限制的话)。

至于动画的暂停，在调用`requestAnimationFrame`函数后，会返回一个 id 作为该回调函数在动画帧回调函数列表中位置的唯一标志。那么借由这个唯一标志，将其传进`cancelAnimationFrame`作为参数，即可暂停动画。

而动画的继续功能，只需再次调用`requestAnimationFrame`函数即可。因为动画的状态是由执行函数控制的,`requestAnimationFrame`只是负责每帧渲染，其变量状态也即保存了动画的状态，所以再次调用即可，另外注意每次调用函数后对唯一标志 id 的状态维护。

```javascript
import { useState, useEffect, useRef } from "react";
const Index = () => {
    const [handlerId, setHandlerId] = useState();
    const canvasRef = useRef();
    useEffect(() => {
        //开始动画
        requestAnimationFrame(animate);
        //3秒后停止动画
        setTimeOut(() => {
            cancelAnimationFrame(handlerId);
        }, 3000);
    }, []);
    const animate = () => {
        //动画执行逻辑
        //... ...
        //在结束前调用自身进行下一帧的绘制
        const id = requestAnimationFrame(animate);
        setHandlerId(id);
    };
    return (
        <div>
            <canvas ref={canvasRef}></canvas>
        </div>
    );
};
export default Index;
```

### 后记

这次的练习很基础，但对我这种菜鸡仍有不少可以学习实践的地方。然后以本文三个点为基础，还有些方面的内容想更深入些。有机会再接着写笔记吧。

另外附上 demo 的效果：
<video src="https://kylinnnnn.github.io/video/canvas-demo.mpeg" position= "absolute" width="100%" height="100%" controls="controls"></video>

### 参考文献
[Canvas API 中文网](https://www.canvasapi.cn/CanvasRenderingContext2D/drawImage)