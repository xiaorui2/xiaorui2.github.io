---
title: HOOK介绍及理解
date: 2019-06-08 13:10:16
tags: HOOK
categories: 前端学习
---

# 什么是HOOK，什么时候用？

`Hook`是一个特殊的函数，它可以让你“钩入” `React` 的特性。它允许让你在不编写 `class` 的情况下使用 `state` 以及其他的 `React` 特性。`Hook` 不能在 `class` 组件中使用 —— 这使得你不使用 `class` 也能使用 `React`。

React 内置了一些像 `useState`，`useEffect`这样的 `Hook`。你也可以创建你自己的 `Hook` 来复用不同组件之间的状态逻辑。

如果你在编写函数组件并意识到需要向其添加一些 state，以前的做法是必须将其它转化为 `class`。现在你可以在现有的函数组件中使用 `Hook`。

# State Hook

看实例：

```react
import React, { useState } from 'react';
function Example() {
  // 声明一个叫 “count” 的 state 变量。
  const [count, setCount] = useState(0);
  // 声明多个 state 变量！
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

`useState` 就是一个 `Hook`。通过在函数组件里调用它来给组件添加一些内部 `state`。`React` 会在重复渲染时保留这个 `state`。`useState` 会返回一对值：当前状态和一个让你更新它的函数，你可以在事件处理函数中或其他一些地方调用这个函数。它类似 `class` 组件的 `this.setState`。这里用到了数组解构的语法。

`useState` 唯一的参数就是初始 `state`，不同于 `this.state`，这里的 `state` 不一定要是一个对象。

我们声明了一个叫 `count` 的 `state` 变量，然后把它设为 `0`。`React` 会在重复渲染时记住它当前的值，并且提供最新的值给我们的函数。我们可以通过调用 `setCount` 来更新当前的 `count`。

这样的话，我们在引用这个`state`变量的时候和更新`state`的时候就比之前方便很多，如：

```react
//在函数中，我们可以直接用 count:
<p>You clicked {count} times</p>
//更新的时候
<button onClick={() => setCount(count + 1)}>
    Click me
</button>
```

# Effect HOOK

`Effect Hook`可以让你在函数组件中执行副作用操作，使用`useEffect`来实现。我们可以把 `useEffect` `Hook` 看做 `componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个函数的组合，在 React 组件中有两种常见副作用操作：需要清除的和不需要清除的。我们来更仔细地看一下他们之间的区别。感觉就是你需不需要返回一个清除的函数。

我们知道在 `React` 的 `class` 组件中，`render` 函数是不应该有任何副作用的。一般来说，在这里执行操作太早了，我们基本上都希望在 `React` 更新 `DOM` 之后才执行我们的操作。

## 不需要清除的Effect HOOK

看下实例：

```react
import React, { useState, useEffect } from 'react';
function Example() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

通过使用这个 `Hook`，会告诉 `React` 组件需要在渲染后执行某些操作。`React` 会保存你传递的函数（我们将它称之为 “`effect`”），并且在执行 `DOM` 更新之后调用它。在这个 `effect` 中，我们设置了 `document` 的 `title` 属性。

### 为什么在组件内部调用 useEffect？

将 `useEffect` 放在组件内部让我们可以在 effect 中直接访问 `count` `state` 变量（或其他 `props`）。

### useEffect 会在每次渲染后都执行吗？

默认情况下，它在第一次渲染之后和每次更新之后都会执行。你可能会更容易接受 `effect` 发生在“渲染之后”这种概念，不用再去考虑“挂载”还是“更新”。`React` 保证了每次运行 `effect` 的时，`DOM` 都已经更新完毕。

大多数情况下，`effect` 不需要同步地执行。在个别情况下（例如测量布局），有单独的 `useLayoutEffectHook` 供你使用

## 需要清除的Effect HOOK

就是我们添加了监听事件或者一些事需要在之后进行清除操作防止引起内存泄露，那么我们就应该返回一个函数，其实是对应着之前说过的组件的生命周期`componentWillUnmount`不使用`Hook`的时候是需要在这个函数中调用清操作的。看实例：

```react
import React, { useState, useEffect } from 'react';
function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // Specify how to clean up after this effect:
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

### React 何时清除 effect？

`React` 会在组件卸载的时候执行清除操作。正如之前学到的，`effect` 在每次渲染的时候都会执行。这就是为什么 `React` 会在执行当前 `effect`之前对上一个 `effect` 进行清除。

### 通过跳过Effect进行性能优化

在某些情况下，每次渲染后都执行清理或者执行 `effect` 可能会导致性能问题。在 `class` 组件中，我们可以通过在 `componentDidUpdate` 中添加对 `prevProps` 或 `prevState` 的比较逻辑解决：

```react
componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) {
    document.title = `You clicked ${this.state.count} times`;
  }
}
```

在`Hook`中，如果某些特定值在两次重渲染之间没有发生变化，你可以通知 `React` 跳过对 `effect` 的调用，只要传递数组作为 `useEffect` 的第二个可选参数即可：

```react
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新
```

但是注意使用这种优化的情况是：你这个 `userEffect` 依赖了 `props` 或者`state`的属性时，由于不想每次组件渲染的时候都执行这个 `useEffect`，因此可以在数组里面添加 `props.name`，每次组件渲染的时候，通过看一下 `props.name` 这个属性的值有没有变化，如果没有变化，就不执行这个 `useEffect`。这样就减少了这个 `useEffect` 的执行次数，从而达到了优化性能的作用。

就像官网上说的：如果你要使用此优化方式，请确保数组中包含所有外部作用域中会随时间变化并且在 effect 中使用的变量，否则你的代码会引用到先前渲染中的旧变量

# Hook使用的一些规则

`Hook` 本质就是`JavaScript` 函数，但是在使用它时需要遵循两条规则：

## 只在最顶层使用 Hook

不要在循环，条件或嵌套函数中调用 `Hook`，确保总是在你的 `React` 函数的最顶层调用他们。遵守这条规则，你就能确保 `Hook` 在每一次渲染中都按照同样的顺序被调用。这让 `React` 能够在多次的 `useState` 和 `useEffect` 调用之间保持 `hook` 状态的正确。

如果需要调用条件循环的话，可以把判断放到`Hook`的内部：

```react
useEffect(function persistForm() {
    // 👍 将条件判断放置在 effect 中
    if (name !== '') {
      localStorage.setItem('formData', name);
    }
  });
```

## 只在 React 函数中调用 Hook

不要在普通的 `JavaScript` 函数中调用 `Hook`，可以在`React`的函数组件中调用`Hook`，或者在自定义`Hook`中调用其他的`Hook`。

我们可以在单个组件中使用多个 `State Hook` 或 `Effect Hook`。