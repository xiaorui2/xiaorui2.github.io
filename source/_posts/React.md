---
title: React学习一
date: 2019-05-25 17:35:39
tags: React
categories: 前端学习
---

# React是什么？

`React` 是一个声明式，高效且灵活的用于构建用户界面的 `JavaScript` 库。使用 `React` 可以将一些简短、独立的代码片段组合成复杂的 `UI` 界面，这些代码片段被称作“组件”。`React` 中拥有多种不同类型的组件。

```javascript
class Square extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
          value: null,
        };
    }
    render() {
      return (
        <div className="shopping-list">
        <h1>Shopping List for {this.props.name}</h1>
 		<ul>
          <li>Instagram</li>
        </ul>
      	</div>
      );
    }
}
// 用法示例: <Square name="Talk" />
```

`render` 方法的返回值描述了你希望在屏幕上看到的内容。`render` 返回了一个 `React` 元素，这是一种对渲染内容的轻量级描述。一个组件接收一些参数，我们把这些参数叫做 `props`，然后通过 `render` 方法返回需要展示在屏幕上的视图的层次结构。如上面的代码中 `Square` 组件只会渲染一些内置的 `DOM` 组件，如`<div />`、`<li />`等，但是你也可以组合和渲染自定义的 `React` 组件。

# 第一个Reactdemo

我们要实现一个列表，列表有`Name`和`Job`两列，我们能进行`delete`操作和`submit`操作。

## 安装环境要求以及运行

环境至少要求：`Node.js5.2`版本以上

安装`React`

```
npm install -g create-react-app
```

创建一个对应的目录存放文件，利用`cmd`命令行`cd`到对应的目录，执行以下命令：

```
npx create-react-app react-tutorial
cd react-tutorial
npm start
```

运行完之后将`localhost:3000`使用新的React应用程序弹出一个新窗口如下：

![](1.png)

这个时候你去对应的`react-tutorial`文件夹下就能看到对应的文件了，这个时候你就可以打开`/src`目录，对里面的文件进行操作了，在你打开对应的`localhost:3000`的时候，在对应的编辑器里面进行操作保存的话对应的网站内容就会即时的进行更新，报错的信息也能很快地看到。

## 第一次修改尝试

我们将里面的对应的`App.js`文件进行修改

```
import React, { Component } from 'react'
class App extends Component {
  render() {
    return (
      <div className="App">
        <h1>Hello, React!</h1>
      </div>
    )
  }
}
export default App;
```

运行之后的效果图

![](2.png)

我们将组件导出为`App`并加载`index.js`。将组件分离到文件中并不是强制性的，但如果不这样做，应用程序将开始变得笨拙和失控，就是对应的功能做到对应的文件里面，维护一个较好的功能树。

## 创建Table组件，实现放置列表

首先创建一个`Table.js`文件。创建一个组件的方式有两种，一个是类组件，用类组件创建这个`Table`组件：

```javascript
import React, { Component } from 'react'
class Table extends Component {
  render() {
    return (
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Job</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>Charlie</td>
            <td>Janitor</td>
          </tr>
          <tr>
            <td>Mac</td>
            <td>Bouncer</td>
          </tr>
          <tr>
            <td>Dee</td>
            <td>Aspiring actress</td>
          </tr>
          <tr>
            <td>Dennis</td>
            <td>Bartender</td>
          </tr>
        </tbody>
      </table>
    )
  }
}
export default Table;
```

我们创建的这个组件是一个自定义类组件。我们将自定义组件大写，以区别于常规HTML元素。返回`App.js`，我们可以在表中加载，首先导入它：

```
import Table from './Table'
```

加载了之后我们才能在`App.js`文件中使用这个组件，不然是会报错的。那么加载了之后修改对应的`render()`函数内容

```
return (
  <div className="container">
    <Table />
  </div>
)
```

效果图如下：

![](3.png)

另一种创建组件的方式是使用`ES6`箭头函数来创建这些简单的组件。它是一个函数。此组件不使用`class`关键字。我们修改一下刚才创建`Table`组件的方式：

```javascript
const TableHeader = () => {
  return (
    <thead>
      <tr>
        <th>Name</th>
        <th>Job</th>
      </tr>
    </thead>
  )
}
const TableBody = () => {
  return (
    <tbody>
      <tr>
        <td>Charlie</td>
        <td>Janitor</td>
      </tr>
      <tr>
        <td>Mac</td>
        <td>Bouncer</td>
      </tr>
      <tr>
        <td>Dee</td>
        <td>Aspiring actress</td>
      </tr>
      <tr>
        <td>Dennis</td>
        <td>Bartender</td>
      </tr>
    </tbody>
  )
}
//修改对应的render()函数
class Table extends Component {
  render() {
    return (
      <table>
        <TableHeader />
        <TableBody />
      </table>
    )
  }
}
```

刷新一下结果还是和之前的一样。

## 利用props和state进行数据的传输

我们现在已经有了一个`Table`组件，但是数据是硬编码的，`React`的一个很好的点就是数据传输非常的方便，它使用`props`和`state`来实现。我们先使用`props`来进行数据传输。

### props

首先删除`TableBody`组件中的所有数据

```
const TableBody = () => {
  return <tbody />
}
```

然后我们在它的父组件里面加入数据，并进行数据传输，我们进行数据传输的方式就是使用属性的方式传递给子组件：

```javascript
class App extends Component {
  render() {
    const characters = [
      {
        name: 'Charlie',
        job: 'Janitor',
      },
      {
        name: 'Mac',
        job: 'Bouncer',
      },
      {
        name: 'Dee',
        job: 'Aspring actress',
      },
      {
        name: 'Dennis',
        job: 'Bartender',
      },
    ]
    return (
      <div className="container">
        <Table characterData={characters}/>
      </div>
    )
  }
}
```

传递到了`Table`组件之后，我们需要把数据从这个组件内拿出来：

```javascript
//拿出来的方式通过数组映射返回数组每个对象，将其映射在rows变量中并返回
const TableBody = props => {
  const rows = props.characterData.map((row, index) => {
    return (
      <tr key={index}>
        <td>{row.name}</td>
        <td>{row.job}</td>
      </tr>
    )
  })
  return <tbody>{rows}</tbody>
}
class Table extends Component {
  render() {
    const { characterData } = this.props
    return (
      <table>
        <TableHeader />
        <TableBody characterData={characterData} />
      </table>
    )
  }
}
```

这样的话数据传输就做完了，但是`props`呢，它只能进行读取并不能对于组件的信息进行改动，如果需要改动的话就需要`state`了，也就是实现我们的`delete`操作。

### state

首先需要在父组件里面创建一个`state`对象。存放我们之前的数据，为了更新状态，我们将使用`this.setState()`一种内置的方法来操作状态

```javascript
state = {
    characters: [
      {
        name: 'Charlie',
        // the rest of the data
      },
    ],
  }
```

我们需要通过一个函数来处理这件事情，`filter`就是之前在学习`JavaScript`的一个高阶函数

```javascript
removeCharacter = (index) => {
  const { characters } = this.state
  this.setState({
    characters: characters.filter((character, i) => {
      return i !== index
    }),
  })
}
```

同时呢我们也需要把这个函数传给下面对应的组件，在`App.js`中进行更新：

```javascript
return (
  <div className="container">
    <Table characterData={characters} removeCharacter={this.removeCharacter} />
  </div>
)
```

在`Table`组件中也需要进行相应的更新操作：首先要提取出正确的数据，然后继续传递给下面的组件`TableBody`

```javascript
render() {
    const { characterData, removeCharacter } = this.props
    return (
      <table>
        <TableHeader />
        <TableBody characterData={characterData} removeCharacter={removeCharacter} />
      </table>
    )
}
```

在`TableBody`中就需要添加对应的组件并对点击时间进行`removeCharacter()`方法的处理：

```javascript
<tr key={index}>
  <td>{row.name}</td>
  <td>{row.job}</td>
  <td>
    <button onClick={() => props.removeCharacter(index)}>Delete</button>
  </td>
</tr>
```

该`onClick`函数必须通过一个返回该`removeCharacter()`方法的函数就实现了我们的需求。对应的效果图如下并且删除了一个`Mac`

![](4.png)

至此初始化和删除操作就搞定了。

## 表单的提交

通过表单的提交我们可以添加对应的数据，首先我们删除之前的数据，使它为空，然后我们创建一个`From.js`的文件，设置初始状态为`Form`具有一些空属性的对象，并将该初始状态指定给`this.state`。

```javascript
import React, { Component } from 'react'
class Form extends Component {
  constructor(props) {
    super(props)
    this.initialState = {
      name: '',
      job: '',
    }
    this.state = this.initialState
  }
}
```

我们对此表单的想法是每次在表单中更改字段的状态，并且当我们提交时，所有数据将传递到`App`状态，然后将更新状态`Table`。

首先我们定义一个函数`handleChange` ，在`Input`中添加`Onchange`属性调用改函数，使得它能获得`Input`组件输入的数据。

```javascript
handleChange = (event) => {
        const { name, value } = event.target
      
        this.setState({
          [name]: value,
        })
    }
render() {
  const { name, job } = this.state;

  return (
    <form>
      <label>Name</label>
      <input
        type="text"
        name="name"
        value={name}
        onChange={this.handleChange} />
      <label>Job</label>
      <input
        type="text"
        name="job"
        value={job}
        onChange={this.handleChange} />
    </form>
  );
}
```

这个时候，这个表单已经能够成功的提交了。但是还不能渲染到父组件，我们需要做的最后一步就是在`App.js`文件创建一个名为`handleSubmit()` 的函数，传递到`From.js`组件，然后在该组件中调用这个函数，使得我们在`App.js`中能够成功的实际提交该数据并更新父状态。

`App.js`

```javascript
//我们实际提交该数据并更新父状态
handleSubmit = character => {
  this.setState({ characters: [...this.state.characters, character] })
}
render() {
    const { characters } = this.state;
    return (
      //使用属性将数据传递给子组件
      <div className="container">
        <Table characterData={characters} removeCharacter={this.removeCharacter}/>
        <Form handleSubmit={this.handleSubmit} />
      </div>
    )
  }
```

`From.js`

```javascript
onFormSubmit = (event) => {
        event.preventDefault();
        this.props.handleSubmit(this.state);
        this.setState(this.initialState);
    }
<button type="submit" value="Submit" onClick={this.onFormSubmit} >
                Submit
</button>
```

差不多这样整个`demo`就跑通了。

流程是这样的，可能叙述的不是很清楚，大家可以去`github`上看下源码就能理解了，还是很基础的`demo`。

<https://github.com/xiaorui2/FrontLearn/tree/master/react-tutorial/src>