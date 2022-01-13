---
title: CSS隐藏元素的方法
date: 2022-01-02 17:17:58
tags: 前端 CSS
---
学习vue时思考了下v-if是怎样隐藏元素的，由此想到了经典面试题之display:none与visibility:hidden的区别，随即将CSS隐藏元素的方法整理一下。
<!--more-->
##### 方法一：display属性
通过将display属性值设置为none可以实现将元素隐藏。

在浏览器解析html与css文件生成渲染树时，diplay属性被设置为none的节点会被除去。
也就是说，display属性值为none的元素在页面上不会占有空间，因而将原本显示的元素设置display:none时，页面的布局会因为该元素的消失发生变化。

##### 方法二：visibility属性
通过将visibility属性设置为hidden可以实现元素隐藏。

visibility属性值被设置为hidden的元素，仅仅只是元素不可见。在布局上，其仍占据着原本的位置，这是visibility相对于display的区别。
此外，由visibility实现隐藏的元素也不再响应用户的交互。

以上即使display与visibility实现元素隐藏的特点。除了这两种方法外，还有两种在我个人看来略显邪道（非贬义）的方法。

##### 方法三：opacity属性
opacity属性原本控制的是元素的透明度，在将透明度设置为0时即变向实现了元素的隐藏。

这种方法的特点也很明显。由于opacity控制的是透明度，所以虽然元素不可见，但是元素在布局上并无变化，并且元素的功能也不受影响，因而元素仍能正常与用户交互。

##### 方法四：position属性
通过设置position属性将元素移出网页的可视区域即算是实现了元素的隐藏。

例如：
```
.hide {
   position: absolute;
   top: -9999px;
   left: -9999px;
}
```