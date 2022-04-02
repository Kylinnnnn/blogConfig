---
title: 读书速记-《深入浅出React和Redux》（二）上
date: 2022-03-08 23:43:08
tags:
    - React
    - 读书笔记
---

### 简介

本篇读书速记的内容对应《深入浅出 React 和 Redux》这本书的第二章的前半，主要内容是 React 如何通过 state 和 prop 进行状态管理。

本章的内容是从组件化开发的基础上，介绍了 React 进行组件化开发的方式，其中尤其介绍了在 React 父子组件间的数据通信方式，即 state 与 prop 参数，以及组件的生命周期。

在 React 提供的组件开发方式中，我们可以一瞥 React 的设计思路，对后面理解 React 的一些实现原理以及开发过程中应当遵循的原则有所裨益。

<!--more-->

### 组件化开发

组件化开发的目的是为了代码的高复用性、可读性以及可维护性，为了达到这样的目的，组件化开发应当遵循一定的原则。

在软件工程这门学科中，将这高度概括为两点——高内聚和低耦合。

所谓内聚度，是指代码中实现同一功能的模块其内部代码的紧密程度。
至于耦合度，则是指不同模块之间互相依赖的程度。
所以“高内聚、低耦合”即是要求要将实现统一功能的代码尽可能封装在一起，而不同模块间等待依赖度要尽可能低，使得模块能够尽可能独立的实现功能。

### React 组件中的数据

React 组件中的数据分为两种，一种是组件间的数据，另一种则是组件内部的数据。

在 React 中，组件内部的数据称为状态（state），可以由 state 对内部数据进行控制。此外，React 组件可以通过 prop 参数来访问从外部获取的数据。

#### React 中的 prop

##### 为 prop 赋值

外界通过在 JSX 代码中使用类似于给标签属性赋值的方法向 prop 传入参数。

```Javascript
<SampleButton
id='sample'
borderWidth={2}
onClick={onButtonClick}
style={{color:'red'}}
/>
```

在上面样例代码中，就为 SampleButton 组件传入了 id、borderWidth、onClick、style 这几个属性的值。

另外，出于 React 单向数据流的思想，React 是不允许直接对变量直接进行改动的，React 的对于数据的改动只能通过 setState 去完成。其实是这样，在类组件中，直接对数据进行改动是可以的，数据也确实会改变，但是，这种方法做出的改动是不会触发 render 函数，所以数据的改动不会渲染到页面上；而在函数式组件中，如果尝试直接改动数据会直接报错，由此可以看出直接改动是不被推荐的。
但是，有的时候，组件不仅要从外部获取数据加以显示，还需要能够对数据进行改动并反馈到外部，这种时候，可以从外部将对应的 setState 方法也作为 prop 的参数传递进组件。

此外，书中在此处还举出了另一段样例代码：

```javascript
class ControlPanel extends Component {
    render() {
        return (
            <div>
                <Counter caption="First" initValue={0} />
                <Counter caption="Second" initValue={10} />
                <Counter caption="Third" initValue={20} />
            </div>
        );
    }
}
```

这是一个在 React 16 之前的问题（书中版本是 v15.4）。在上面的代码中我们可以看到，ControlPanel 组件为了返回三个同层级的 Counter 组件，不得不在在外部再包一层<div>标签，这是因为当时的 React 要求 render 函数只能返回一个元素。但是这多包的一层标签在某些情况下会对 CSS 产生影响，例如在表格中返回多个<td>标签时，就会导致样式失效。在 React 16 之后，React 提供了两种解决方案来应对需要返回多个元素的情况。

方法一是以数组的形式返回多个元素：

```javascript
class ControlPanel extends Component{
    this.setState({
        arrConfig: [
            {capthion:'First',initValue:0},
            {capthion:'Second',initValue:10},
            {capthion:'Third',initValue:20}
        ]
    })
    render (){
        this.state.arrConfig.map((item,i)=>{
            return [
                <Counter caption={item.caption} initValue={item.initValue />
            ]
        })
    }
}
```

方法二是 React 提供了片段 Fragment 来处理这种情况，即将需要返回的多个元素用<React.Fragment>标签来包裹住:

```javascript
class ControlPanel extends Component {
    render() {
        this.state.arrConfig.map((item, i) => {
            return (
                <React.Fragment>
                    <Counter caption="First" initValue={0} />
                    <Counter caption="Second" initValue={10} />
                    <Counter caption="Third" initValue={20} />
                </React.Fragment>
            );
        });
    }
}
```

在这种方法中，React 还提供一种缩略形式--用一个空标签把目标元素包裹起来。

```javascript
class ControlPanel extends Component {
    render() {
        this.state.arrConfig.map((item, i) => {
            return (
                <>
                    <Counter caption="First" initValue={0} />
                    <Counter caption="Second" initValue={10} />
                    <Counter caption="Third" initValue={20} />
                </>
            );
        });
    }
}
```

##### 读取 prop 中的值

传入 prop 的参数都会作为 props 的属性，以属性名的方式即可访问到对应的值。

```javascript
class Counter extends Components {
    constructor(props) {
        super(props);
        this.onClickIncrementButton = this.onClickIncrementButton.bind(this);
        this.onClickDecrementButton = this.onClickDecrementButton.bind(this);

        this.state = {
            count: props.initValue || 0,
        };
    }

    render() {
        const { caption } = this.props;

        rentrun(
            <div>
                <button
                    style={buttonStyle}
                    onClick={this.onClickIncrementButton}
                >
                    +
                </button>
                <button
                    style={buttonStyle}
                    onClick={this.onClickDecrementButton}
                >
                    -
                </button>
                <span>
                    {caption}count:{this.state.count}
                </span>
            </div>
        );
    }
}
```

在类组件中，首先需要调用 super 函数即父组件的构造函数，这样才能通过 props 访问父组件传入的参数。然后可以看到组件在接收函数的时候并不会自动把函数的 this 绑定到当前对象。
最后是一个代码编写的小技巧，在样例代码中紧跟在 render（）后的一行中，我们可以用 ES6 的新特性中的解构赋值快速地从 props 中将变量值提取出来。

##### props 的格式检查

在类组件中，我们可以使用 propType 来声明组件的接口规范，这个结构规范中应当包括两部分：一是本组件支持哪些 prop，二是每个 prop 应当是什么样的格式。
类组件中的格式声明方式如下：

```
Counter.propTypes={
    caption:PropType.string.isRequired,
    initValue:PropTypes.number
};
```

其中可以使用.isRequired 来表示该属性是必须的。

当使用函数式组件时，我们可以用如下方式来声明 prop 格式：

```javascript
interface Prop{
    caption: string
    initValue?:number
}

const Index = (props:Prop)=>{
    //xxxx
}
```

书中在最后提到，类型检查只能帮助检查不正确的 prop 使用方法，并不能提供实际约束。另外说，类型检查也会占用计算机资源，所以产品环境下一般不会保留类型检查，会在编译时通过自动化方法去掉。

#### React 的 state

##### state 的初始化

在类组件中，组件创建时一定会调用构造函数 constructor，所以一般在构造函数中进行 state 的初始化。另外类组件中可以通过 defaultProps 属性来进行组件状态默认值的设置。

```javascript
Counter.defaultProps = {
    initValue: 0,
};
```

而在函数式组件中，则是通过 useState 这个钩子函数去实现状态的声明和初始化。

##### state 的读取与更新

在类组件中，状态声明之后就可以通过 this.state.xxx 去获取对应的状态值

至于 state 的更新，其实上面有讲到过，类组件中虽然直接改变状态值也有效，但是不会触发 render 函数，所以不会把变动渲染到页面，因而主张使用 setState 函数。而在函数式组件中，则将直接改动状态值作为一种错误情况。

### 参考资料

《深入浅出React和Redux》——程墨
[React 官方文档-Fragments](https://react.docschina.org/docs/fragments.html)
[MDN 解构赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)
