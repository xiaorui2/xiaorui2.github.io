---
title: React学习二（知识点总结）
date: 2019-06-27 17:39:48
tags: React
categories: 前端学习
---

# React组件&props

`React`编写组件主要是有两种方式：函数组件和`class`组件。一般声明组件的时候都会申明为首字母大写。

定义组件最简单的方式就是编写`JavaScript`函数：

```react
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

还可以使用`ES6`的 `class`来定义组件：

```react
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

注意有一点：`React`是一个直接操作数据的声名式框架，它不会直接操作`DOM`，它是通过虚拟`DOM`来实现最后的渲染的，会帮我们节约掉大量操作`DOM`的代码。

## 子组件和父组件的通信方式

父组件想传递信息到子组件是利用`props`，`state`属性来实现

子组件想传递给父组件信息的话是通过调用父组件传递给子组件的方法间接的传递信息

## 子组件和父组件之间通信是单向数据流

这个也就是为什么组件无论是使用函数声明还是通过`class`声明，都决不能修改自身的`props`。因为这个属性是父组件传递给子组件的。这是为了保证当数据出现错误的时候能很容易的找到出错的地方在哪。

# React虚拟DOM

在`React`中，`render`执行的结果得到的并不是真正的`DOM`节点，结果仅仅是轻量级的`JavaScript`对象，我们称之为`virtual DOM`。 虚拟`DOM`是`React`的一大亮点，具有`batching`(批处理)和高效的`Diff`算法。

这个批处理优化基于：`setState`是异步的，当多个变化很相近的时候就放到一起来去生成虚拟`DOM`。

它也是使得跨端应用得到实现的重要原因。

## React修改数据之后的流程

- `state`数据
- `JSX`模板
- 数据+模板生成虚拟`DOM`（损耗了性能）：`['div',{id: 'abc'},'hello world!']`
- 用虚拟DOM的结构生成真实DOM来显示：`<div id='abc'>'hello world!'</div>`
- `state`发生改变
- 数据+模板生成新的虚拟`DOM`
- 比较原始虚拟`DOM`和新的虚拟`DOM`的区别
- 直接操作`DOM`，更改内容

`JSX`模板变成真实`DOM`的过程：`JSX` -> `React.createElement` -> `js`对象 -> 真实的`DOM`。

## DIFF算法

 两个树的完全的`diff`算法是一个时间复杂度为 `O(n3)`的问题。 但是在前端中，你会很少跨层地移动`DOM`元素，所以真实的`DOM`算法会对同一个层级的元素进行对比。

![](1.png)

`div`只会和同一层级的`div`对比，第二层级的只会和第二层级对比。 这样算法复杂度就可以达到`O(n)`.

利用深度优先遍历，记录差异然后把差异引用到真正的`DOM`树上。

# React的生命周期函数

React的生命周期分为三个阶段：

- 挂载阶段
- 更新阶段
- 卸载阶段

## constructor（组件构造函数）

如果没有显示定义它，我们会拥有一个默认的构造函数。在构造函数里面我们一般会做两件事：

- 初始化`state`对象
- 给自定义方法绑定`this`

## render

`React`中最核心的方法，一个组件中必须要有这个方法

返回的类型有以下几种：

- 原生的`DOM`，如`div`
- `React`组件
- `Fragment`（片段）
- `Portals`（插槽）
- 字符串和数字，被渲染成`text`节点
- `Boolean`和`null`，不会渲染任何东西

## componentDidMount

组件装载之后调用，此时我们可以获取到DOM节点并操作，，比如对`canvas`，`svg`的操作，服务器请求，订阅都可以写在这个里面，但是记得在`componentWillUnmount`中取消订阅

## shouldComponentUpdate

函数原型`shouldComponentUpdate(nextProps, nextState)`，有两个参数`nextProps`和`nextState`，表示新的属性和变化之后的`state`，返回一个布尔值，`true`表示会触发重新渲染，`false`表示不会触发重新渲染，默认返回`true`。

## componentDidUpdate

组件改变之后调用，有三个参数`prevProps`，`prevState`，`snapshot`，表示之前的`props`，之前的`state`，和`snapshot`。第三个参数是`getSnapshotBeforeUpdate`返回的。在这个函数里我们可以操作`DOM`，和发起服务器请求，还可以`setState`，但是注意一定要用if语句控制，否则会导致无限循环

## componentWillUnmount

当我们的组件被卸载或者销毁了就会调用，我们可以在这个函数里去清除一些定时器，取消网络请求，清理无效的`DOM`元素等垃圾清理工作

注意不要在这个函数里去调用`setState`，因为组件不会重新渲染了。

之前还有`componentWillMount()`和`componentDidMount()`是组件即将被渲染到页面之前触发和组件已经被渲染到页面中后触发，这个在现在的版本已经被去除了。使用`HOOK`能很方便的代替生命周期函数。

