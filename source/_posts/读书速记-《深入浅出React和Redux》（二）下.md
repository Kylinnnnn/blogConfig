---
title: 读书速记-《深入浅出React和Redux》（二）下
date: 2022-03-13 14:55:46
tags:
    - React
    - 读书笔记
---

### 简介

React 为了对类组件进行状态控制，在类组件生命周期的各个阶段提供了对应的生命周期函数；而在函数式组件中，则是用 hooks 来实现状态控制。本篇中以类组件为例，对各个生命周期函数进行初步解析。

<!--more-->

### 组件的生命周期

React 严格定义了组件的生命周期，组件的生命周期可能经过如下三个过程：

-   装载过程（Mount），也就是把组件第一次在 DOM 树中渲染的过程；
-   更新过程（Update），当组件被重新渲染的过程；
-   卸载过程（Unmount），组件从 DOM 中删除的过程。

#### 装载过程

在装载过程中依次调用如下函数：

-   construtor
-   getInitialState
-   getDefalultProps
-   componentWillMount
-   Render
-   componentDidMount

##### constructor

constructor 是类中的构造函数，是最先被调用的函数，construtor 的主要任务是：

-   初始化 state，因为其他任何部分都有可能需要调用 state，所以最先调用的 construtor 是初始化 state 的不二之选。
-   绑定成员函数的 this 指向，这个前面有说过，从父组件接受的函数是会保存 this 的指向。

##### getInitialState 和 getDefaultProps

getInitialState 的返回值会用来初始化组件的 state，getDefaultProps 的返回值可以作为 props 的初始值，但是这两个函数只有在使用 React.creatClass 方法创造的组件类才会用到。

##### render

一个组件中一定要实现 render 函数，因为所有类组件的父类 React.Component 类对除 render 以外的生命周期函数都有默认实现。

##### componentWillMount

在装载过程中，componentWillMout 会在调用 render 函数之前被调用。在这个函数被调用的时候，组件还没有被渲染出来，所以在函数内部调用 setState 也不会触发重新渲染。

##### componentDidMount

在 render 函数返回的内容已经触发渲染，组件已经挂载到 DOM 树之后，componentDidMount 才会被调用。

componentWillMount 和 componentDidMount 函数在多个组件渲染过程中的表现相对更复杂，需要注意。

无论是在父子组件的挂载过程还是在兄弟组件的挂载过程中，componentWillMount 会按顺序在各自 render 函数执行前执行，形如：

```
componentWillMount Parent
render Parent
componentWillMount Child1
render Child1
componentWillMount Child2
render Child2
```

对于 componentDidMount 来说比较特殊，componentDidMount 会在所有组件都挂载之后再按顺序执行。

此外，componentWillMount 和 componentDidMount 还有一个区别，componentWillMount 既可以在服务端被调用，也可以在浏览器端调用，而 componentDidMount 只能在浏览器端调用。也就是说在服务端渲染中不能使用 componentDidMount。究其原因，实际上是服务端渲染和客户端渲染的问题，因为服务端是没有 DOM 树，挂载到 DOM 这一动作必定是在浏览器端进行。

#### 更新过程

在更新过程中，会依次调用一下生命周期函数：

-   componentWillRecieveProps
-   shouldComponentUpdate
-   componentWillUpdate
-   render
-   componentDidUpdate

##### componentWillReceiveProps(nextProps)

当父组件的 render 函数被调用，render 函数里被渲染的子组件就会经历更新过程，不管父组件传给子组件的 props 有没有改变，都会触发子组件的 componentWillRecievePropshanshu 函数。另外为了避免循环调用，setState 方法触发的更新过程是不会调用这个函数的。

##### shouldComponentUpdate

shouldComponentUpdate 返回一个布尔值，标示着这个组件在这次更新中是否要继续。如果为 true，接下来就会调用 render 函数，如果为 false，那就会立即停止更新过程。

##### componentWillUpdate 和 componentDidUpdate

如果 shouldComponentUpdate 返回 true，后续就会依次执行 componentWillUpdate，render 和 componentDidUpdate。但是相对于 componentDidMount，componentDidUpdate 也能在服务端调用，但是一般不这样做，书中说，如果调用了说明程序有错误，需要改进。

#### 卸载过程

在卸载过程中，只有 componentWillUnmount 一个函数，一般用来处理清理计时器这类清理性的工作。

### React 组件中 state 和 props 的局限性

在本章中，最为主要的就是 React 中的状态管理。由于 React 的单向数据流，只能父组件通过 props 向子组件传递参数，然后我们通过将父组件控制状态的 setState 函数也通过 props 传递给子组件，从而实现子组件对状态变化的反馈。

但是，如果是在兄弟组件中，多个组件需要共享一个组件状态，就会比较麻烦。对于此种问题，一种解决问题的思路是指定一个组件的状态，然后其他组件跟这个组件的状态保持同步，但是如果其他组件也需要对状态进行改动就会比较困难。另一种解决方法是，将该状态提升一个层级，即相当于从这些组件的父组件或者是全局环境中获取该状态，让各组件的状态与父组件或者是全局保持一致。对比来看，后者实现起来明显要更加合理和顺畅一些，但是代价就是哪怕父组件并不会用到该状态，也需要为子组件维护该状态，抑或是污染了全局环境。

此外，在跨级传递参数时，例如子组件需要用到父组件的父组件的某一状态，虽然子组件的父组件并不会用到该状态，但仍需要接收和传递该状态。而在层级更深，或是子组件需要更改该状态时，状态管理就会更加复杂冗余。