---
title: Vue的计算属性、侦听属性及函数方法的对比
date: 2022-01-13 18:55:05
tags: 
- Vue 
- 前端 
- Javascript
---

Vue 对于复杂逻辑的实现提供了计算属性`computed`、侦听属性`watch`、函数方法`methods`。本文主要是记录一下使用过程中需要注意的点以及在选择实现途径时的一点考量。

##### 计算属性`computed`

Vue 官方文档的例子

```HTML
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
```

```Javascript
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})
```

Vue 会根据代码中声明建立起响应式的依赖关系，比如上面代码中，就会将`message`与`reversedMessage`绑定在一起。并且，为了避免死循环的问题，计算属性的`getter`函数中是不允许对其依赖的属性进行改动，也就是没有副作用`side effect`。

综上所述，只有当`message`发生改变时，`reversedMessage`才会发生变动，所以多次访问`reversedMessage`时只会立即返回缓存的之前的值。

通常情况下，计算属性默认只有`getter`，但是也可以手动设置`setter`。

##### 侦听属性`watch`

Vue 官方文档的例子

```HTML
<div id="demo">{{ fullName }}</div>
```

```Javascript
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```
侦听属性的实现是回调式的,为避免无限的更新循环，也是不允许对被侦听的属性本身进行修改。

##### 方法`methods`
通过方法属性也能够实现复杂逻辑的计算，但是方法每次被调用时都会重新计算，因此每当页面重新渲染（发生回流）时，所有的方法都会被调用执行。

##### 计算属性、侦听属性与方法在选择上的考量
首先方法作为最普通的实现手段由于其一次性，在数据的实时显示上通常不如相对灵活的计算属性与侦听属性。

从上面的官方样例代码中我们可以看出，当存在多个属性需要监听，且均会对最后的值产生影响时。倘若采用`watch`，则每一个属性都需要一个回调函数，而若是使用`computed`，则只需要一个函数，Vue会自动建立起函数与各属性之间的依赖。

此外，官方文档中还给出了一个需要执行异步操作同时实现防抖和限流的样例。

在此类场景中，由于`watch`的侦听对象只有一个，所以即使异步操作后返回的结果需要再与其他数据进行操作，也能够只在异步操作涉及到的属性值发生变化时，才会重新进行异步操作。而若是采用`computed`，那么只要`computed`监听的整个过程中有数据发生变化，那么异步操作也会重新执行，即使发生改变的这个值并不参与异步操作。所以官方文档说当数据变化时执行异步或开销较大的操作时，`watch`是最有用的方法。

另外文档最后还提到了允许执行异步操作、限制操作频率、并在得到最终结果前设置一个中间状态这些都是`computed`无法做到的。细想一下我们可以发现，这些操作的过程中通常都包含着对其他数据值的修改，也就是说这些操作的实现会带有副作用`side effect`，因而采用`computed`是无法做到的。