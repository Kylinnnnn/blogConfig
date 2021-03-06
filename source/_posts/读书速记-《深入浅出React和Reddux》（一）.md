---
title: 读书速记-《深入浅出React和Redux》（一）
date: 2022-03-07 20:50:17
tags:
    - React
    - 读书笔记
---

### 前言

近年来读书欲望偏低，记忆力也不如从前，究其原因，可能是在面对大量文字时不甚愿意花费心思和时间从中提取吸收知识。今逢有 React 学习需求，手边有恰有**《深入浅出 React 和 Redux》**这本书，因此在拜读学习同时略作笔记，以锻炼自己从文本中提取信息并加以分析的能力，同时以期能够再次爱上读书。

### 简介

本篇读书速记是在观读本书第一章内容后的笔记。在第一章中，作者旨在简要介绍 React，并对 React 一些突出的特点进行分析以及与其他框架进行对比，以便读者能够相对直观的简单认识 React。在本章中，首先是介绍了一个 React 应用的创建方式以及为何要使用这种方式。接着介绍了 React 的首要思想——组件化开发（这也是如今代码开发过程中的重要思想，当然各自框架对于组件化的理解会有所出入，但都大同小异）。紧接着就着组件化开发介绍了 React 的另一特色——JSX 语法。然后对脚手架搭建的项目结构进行简要解读。最后，对 React 的工作方式进行概况并与 JQuery 进行对比，在这一段中，React 的工作方式不仅仅是使用 React 的方式，也是以 React，Vue 这类框架为代表的初衷。
<!--more-->

### creat-react-app 脚手架

一个 React 应用的运作依赖于一个庞大的技术栈，其中需要进行一系列的配置，例如：转译 Js 代码需要 Babel，模块打包工具需要 Webpack，定制 build 过程需要 grunt 或者 gulp。

为了简化如此繁琐的配置过程，Facebook 提供了 creat-react-app 这个通过 npm 发布的安装包，由此脚手架的帮助，在命令行运行一行语句即可自动搭建项目。

### 组件化开发

组件是指一段功能高度集中以期能够重复使用的代码。React 中的组件是通过创建一个继承自 Component 的类来实现的（现在使用函数式组件偏多），其中使用 constructor 来实现对组件在数据层面上的从初始化构建，在 render 函数中书写 JSX 语法来实现组件 UI。此处列出一段书中的创建组件的代码。

```Javascript
import React,{Component} from 'react';

class ClickCounter extends Component{
    constructor(props){       //props是本组件从父组件中接受的参数
        super(props);         //super（）函数使得本类能够从父类中继承属性和方法
        this.onClickButton = this.onClickButton.bind(this);
        this.state={count:0};
    }

    onClickButton(){
        this.setState({count:this.state.count+1});
    }

    render(){
        return(<div>
        <button onClick={this.onClickButton}>Click Me</button>
        <div>Click Count:{this.state.count}</div>
        </div>)
    }
}

export default ClickCounter
```
在创建了组件之后就能使用import引入并通过<ClickCounter />使用。JSX能够通过标签的首字母是否大写来判断是自定义组件还是HTML标签。

另外在这一部分中，作者提到React出现之初使用的是React.createClass方式来创造组件，虽然网上仍有大量文章基于React.createClass来讲解React，但实际上这已经是过时的方法。令人莞尔的是，我在读到这本书时，React开始全面拥抱Hooks方法，函数式组件更为盛行。在莞尔一笑的同时，我们也要明白，前端的发展是快速而迅猛的，整个前端从最初的静态网页发展至今天具有各种各样展现形式与功能的前端页面也不过是近二十年的事情。所以，一定要保持一颗求学者的心。

### JSX语法
在上述render函数中，我们看到了React能够将HTML代码和JS代码写在一起，实际上不止如此，还可以定义一个变量在其中定义好css属性再赋值给标签的style属性，这样，就实现了HTML、JS、CSS全部都写在一个文件中。

另注一点，实际上我之前以为JSX是在HTML中书写JS代码，实则不然，JSX是在JS中书写其他代码，从其名字全称就可看出来（Javascript Extension)。

在这一段中，作者还作了一个对比，把JSX绑定onClick事件和HTML绑定onclick事件的过程进行对比来帮助读者分析JSX的出现是进步还是退步。

首先，虽然二者都是在标签上绑定事件，但实际上的过程是不一样的。
HTML绑定onclick事件是最为直接的，但这会带来诸多问题：
* onclick添加的事件处理函数是在全局环境下执行的，会污染全局环境（所谓污染全局环境是指可能对全局环境下的其他变量产生影响从而产生意料之外的结果）。
* 给很多DOM元素添加onclick事件，可能会影响网页的性能。
* 容易产生内存泄漏问题，比如动态地在DOM树中进行删除操作，需要注销对应的时间处理器，否则就可能造成内存泄漏。
  
相比之下，JSX的onClick并没有直接使用onclick，而是使用事件委托的形式处理。无论有多少个onClick，最后都只会在DOM的最顶层节点挂载一个onclick，所有的点击事件都由这个事件处理函数捕获，根据事件触发目标（e.target)调用对应的方法（详细涉及到事件委托的机制）。

由上可以看出，虽然形式回归了初始，但JSX的内容显然是更加先进高效的。正如辩证法的思想，事物的发展总是呈螺旋式上升、波浪式前进。很多事情都是这样，比如，学到后面的渲染方式时，会发现经历过服务端渲染、客户端渲染至今的同构渲染在形式上也很接近于最初的服务端渲染。

### 分解React应用
在前面提到，React应用的运作依赖于一个庞大的技术栈，这些技术栈为开发提供了更为便捷的方法。例如我们在使用`npm start`运行应用时，实际上运行的命令并不是这个，在package.json文件中我们可以看到对于脚本的定义。

作者在此处还提到了脚本中一个eject命令，eject能够将一系列的技术栈配置从node_modules包中弹出到项目顶层，即使用之后项目目录下会新增scripts和config两个目录，方便为满足项目需求进行定制化配置。

### React与jQuery
还是以上文中的ClickCount组件为例，如果用jQuery来是实现这个功能：
```
$(function(){
    $('#clickMe').click(function(){
        var clickCounter = $('#clickCount');
        var count = parseInt(clickCounter.text(),10);
        clickCounter.text(count+1);
    })
})
```
在jQuery实现的方法中，首先根据CSS规则找到了对应id的按钮，挂上一个匿名事件处理函数，然后在事件处理函数中获取需要被修改的DOM元素的文本值，修改后加以显示。很显然，在这个过程中会对DOM树进行直接操作，如果存在多个事件处理函数，就会频繁的操作DOM树，最为直观的危害就是会显著降低网页的性能，因为每次对DOM树的操作都需要网页重新渲染（详情见重绘与回流）。另外这样的模式会造成代码结构复杂，难以维护。

而React在处理这件事上，应用了Virtual DOM的理念。

我们先讲DOM（文档对象模型，Document Object Model），DOM树是对于HTML文档的抽象，将其层层递进的父子标签关系抽象成一棵树状结构。而Virtual DOM是对DOM的抽象，相当于将DOM拷贝了一份，每次在改动之后，React会先将所有改动应用在这棵Virtual DOM树上，最后根据Virtual DOM树一次性渲染出来。这样就能够显著减少对DOM树的操作。

另注，正如前面所说，前端技术的发展是非常迅猛的，我没有系统的接触过jQuery，不知道最新的jQuery是怎么样，有可能也以某种方式解决了这样的问题，这个对比重点在于介绍Virtual DOM树这个概念。

### 间记
第一章的速记就到这，在写完本文主要内容后我自己也回头看了看全文，有感到满意的地方，也察觉到有缺陷的地方。

满意的地方在于，我在记录时有自己的分析与思考，在看到作者的某一段文字时，我会去想作者这段话的目的是什么，是不是在为什么东西作铺垫。

而缺陷在于，一是我感觉本文的视角更偏向于作者，在本文的读者看来会觉得我好像对整体有比较清晰的了解，但实际上我也是在学习的过程中，也有可能会有理解上的错误。所以感觉如何找到一个既能与本文读者一起保持一个求知者的身份同时又能够充分分享自己的理解和看法是撰文的一大挑战。二来我感觉本文作为一篇读书速记篇幅有点略长，但又怕有疏忽的地方。如果能够再精简一点达到书题的深入浅出的高度就更好了。

### 参考资料
《深入浅出React和Redux》——程墨
[React官方文档](https://react.docschina.org/docs/getting-started.html)
[JavaScript 事件委托详解](https://zhuanlan.zhihu.com/p/26536815)
[浏览器的重绘和回流（Repaint & Reflow）](https://zhuanlan.zhihu.com/p/77520334)