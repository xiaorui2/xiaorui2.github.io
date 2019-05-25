---
title: ReactNative（一）
date: 2019-05-22 11:34:24
tags: ReactNative
categories: 前端学习
---

# 搭建第一个`ReactNative`项目

## 环境要求

需要`Node`，`Python2`，`Jdk`，`Android Studio`，`Android SDK`。

注：学会通过[Chocolatey](https://chocolatey.org/)（一种流行的Windows程序包管理器）安装`Node`和`Python2`以及`Jdk`。

## 配置环境变量

添加`ANDROID_HOME`

在Windows控制面板中的“ **系统和安全”**下打开“系统”窗格，然后单击“ **更改设置...”**。打开“ **高级”**选项卡，然后单击“ **环境变量...”**。单击**New ...**以创建`ANDROID_HOME`指向`Android SDK`路径的新用户变量

![](1.png)

将平台工具添加到`Path`

在Windows控制面板中的“ **系统和安全”**下打开“系统”窗格，然后单击“ **更改设置...”**。打开“ **高级”**选项卡，然后单击“ **环境变量...”**。选择**Path**变量，然后单击“ **编辑”**。单击“ **新建”**，将平台工具的路径添加到列表中。

此文件夹的默认位置是：

```
c:\Users\YOUR_USERNAME\AppData\Local\Android\Sdk\platform-tools
```

## 跑Demo

利用`cmd`命令行界面生成一个名为`AwesomeProject`的新`React Native`项目

```
react-native init AwesomeProject
```

利用`Android Studio`打开这个项目，并跑好虚拟机。

然后在命令行运行

```
cd AwesomeProject
react-native run-android
```

如果设置都是正确的话，你就能得到以下界面：（这样的话第一个`demo`就跑通了，要修改的话就在`App.js`文件修改即可，刷新的话使用`ctrl+m`或者双击`RR`

![](2.png)

# 基础知识

## 简单的组件介绍

学习过`Html`的话会熟悉很多，ReactNative提供很多的内置组件像`<Text>`，`<View>`，`<Button>`这些，当然你也可以自己定义自己的组件，如下，这样的话我们就定义好了自己的组件`Greeting`。

```javascript
class Greeting extends Component{
  render(){
    return (
      <View style={styles.instructions}>
        <Text> Hello {this.props.name}!</Text>
      </View>
    );
  }
}
```

大多数组件在创建时可以使用不同的参数进行自定义。调用这些创建参数`props`。

比方说利用组件`Image`创建图像，可以使用命名的道具`source`来控制它显示的图像。

```javascript
import React, {Component} from 'react';
import {FlatList, ScrollView, Platform, StyleSheet, Text, View, AppRegistry, Image, TextInput, Button, Alert, TouchableHighlight, TouchableOpacity, TouchableNativeFeedback, TouchableWithoutFeedback} from 'react-native';
export default class App extends Component {
  render() {
    let pic={
      uri : 'https://upload.wikimedia.org/wikipedia/commons/d/de/Bananavarieties.jpg'
    };
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>Hello World!</Text>
        <Image source={pic} style={styles.iamge1}/>
      </View>
    );
  }
}
AppRegistry.registerComponent('AwesomeProject', () => App);
```

这个是内置组件自带的道具，那么要是我们自己定义的组件的话想用`props`也是可以的。如下

自己定义的组件，通过在下面组件的调用的时候传入参数，利用this.props来调用显示。道具的概念就是对于组件使用不同的参数进行自定义的操作。像正常组件Text调用的style，Image调用source的这些都是道具也就是props。（个人感觉把道具和`props`连在一起理解会好一些）

```javascript
import React, {Component} from 'react';
import {FlatList, ScrollView, Platform, StyleSheet, Text, View, AppRegistry, Image, TextInput, Button, Alert, TouchableHighlight, TouchableOpacity, TouchableNativeFeedback, TouchableWithoutFeedback} from 'react-native';
class Greeting extends Component{
  render(){
    return (
      <View style={styles.instructions}>
        <Text> Hello {this.props.name}!</Text>
      </View>
    );
  }
}
export default class App extends Component {
	render() {
    return (
      <View style={{alignItems: 'center', top: 50}}>
        <Greeting name='Rexxar' />
        <Greeting name='Jaina' />
        <Greeting name='Valeera' />
      </View>
    );
  }
}
AppRegistry.registerComponent('AwesomeProject', () => App);
```

对于每个组件的话是有两个控制组件的参数：`props`和`state`。`props`由父项设置，它们在组件的整个生命周期内都是固定的。对于即将发生变化的数据，我们必须使用`state`，利用`setstate`来重新渲染。假设我们想要制作一直闪烁的文本的话代码如下：

```javascript
import React, {Component} from 'react';
import {FlatList, ScrollView, Platform, StyleSheet, Text, View, AppRegistry, Image, TextInput, Button, Alert, TouchableHighlight, TouchableOpacity, TouchableNativeFeedback, TouchableWithoutFeedback} from 'react-native';
class Greeting extends Component{
  constructor(props){
    super(props);
    this.state= {isShowingText :true};
    setInterval(() => (
      this.setState(previousState => (
        {isShowingText: !previousState.isShowingText}
      ))
    ),1000);
  }
  render(){
    if(!this.state.isShowingText){
      return null;
    }
    return(
      <Text>{this.props.name}</Text>
    );
  }
}
export default class App extends Component {
	render() {
    return (
      <View style={{alignItems: 'center', top: 50}}>
        <Greeting name='Rexxar' />
        <Greeting name='Jaina' />
        <Greeting name='Valeera' />
      </View>
    );
  }
}
AppRegistry.registerComponent('AwesomeProject', () => App);
```

但是在实际应用中，会调用Redux或者Mobx等状态容器来控制数据流。（还没用过，口嗨一下）

## 样式和组件大小

所有核心组件都接受名为的道具`style`，就是当一个组件需要写的道具过多的时候，就用样式来代替它。如下：（复制下教程的代码，自己写的因为堆积内容过多）

```javascript
import React, { Component } from 'react';
import { AppRegistry, StyleSheet, Text, View } from 'react-native';
const styles = StyleSheet.create({
  bigBlue: {
    color: 'blue',
    fontWeight: 'bold',
    fontSize: 30,
  },
  red: {
    color: 'red',
  },
});
export default class LotsOfStyles extends Component {
  render() {
    return (
      <View>
        <Text style={styles.red}>just red</Text>
        <Text style={styles.bigBlue}>just bigBlue</Text>
        <Text style={[styles.bigBlue, styles.red]}>bigBlue, then red</Text>
        <Text style={[styles.red, styles.bigBlue]}>red, then bigBlue</Text>
      </View>
    );
  }
}
AppRegistry.registerComponent('AwesomeProject', () => LotsOfStyles);

```

组件大小的话是用`width`和`height`样式来控制的

```javascript
import React, { Component } from 'react';
import { AppRegistry, View } from 'react-native';
export default class FixedDimensionsBasics extends Component {
  render() {
    return (
      <View>
        <View style={{width: 50, height: 50, backgroundColor: 'powderblue'}} />
        <View style={{width: 100, height: 100, backgroundColor: 'skyblue'}} />
        <View style={{width: 150, height: 150, backgroundColor: 'steelblue'}} />
      </View>
    );
  }
}
AppRegistry.registerComponent('AwesomeProject', () => FixedDimensionsBasics);
```

## 利用Flexbox进行布局和Flex尺寸

组件可以使用`Flexbox`算法指定其子项的布局，常用`flexDirection`，`alignItems`以及`justifyContent`。不同的又有一些对应的参数值

```javascript
import React, {Component} from 'react';
import {FlatList, ScrollView, Platform, StyleSheet, Text, View, AppRegistry, Image, TextInput, Button, Alert, TouchableHighlight, TouchableOpacity, TouchableNativeFeedback, TouchableWithoutFeedback} from 'react-native';
export default class App extends Component {
	render(){
    return(
      <View style={{flex: 1,flexDirection: 'column',justifyContent: 'flex-end'}}>
        <View style={{flex: 1, backgroundColor: 'powderblue'}} />
        <View style={{flex: 2, height: 50, backgroundColor: 'skyblue'}} />
        <View style={{flex: 3, height: 50, backgroundColor: 'steelblue'}} />
      </View>
    );
  }
}
AppRegistry.registerComponent('AwesomeProject', () => App);
```

我们看到第一个View有个flex的参数设置为1，它告诉组件填充所有可用空间，在具有相同父级的其他组件之间平均共享。孩子所占用的分量根据孩子的flex值进行相比分配，但是注意如果组件的父级具有大于0的维度，则组件只能展开以填充可用空间。如果父组件没有固定的`width`和`height`或`flex`，则父组件的维度为0，`flex`子组件将不可见。也就是如果父组件的flex为0了，或者只给了width或者heigth它的子组件是没办法显示的。

## 处理文本输入和处理接触

文本输入利用`TextInput`组件，它是一个允许用户输入文本的基本组件，它有`onChangeText`和`onSubmitEditing`这两个props。假设用户键入时，您将其单词翻译成其他语言。在这种新语言中，每个单词用`$$$`代替。（还不是很理解和这个函数的写法，后续学习补一下）

```javascript
import React, {Component} from 'react';
import {FlatList, ScrollView, Platform, StyleSheet, Text, View, AppRegistry, Image, TextInput, Button, Alert, TouchableHighlight, TouchableOpacity, TouchableNativeFeedback, TouchableWithoutFeedback} from 'react-native';
export default class App extends Component {
	constructor(props){
    super(props);
    this.state= {text: ''};
  }
  render(){
    return(
      <View style={{padding:10}}>
        <TextInput 
        style={{height: 40}}
        placeholder="Type here to translate!"
        onChangeText={(text) => this.setState({text})}
        />
        <Text style={{padding: 10,fontSize: 42}}>
          {this.state.text.split(' ').map((word) => word && '$$$').join(' ')}
        </Text>
      </View>
    );
  }
}
AppRegistry.registerComponent('AwesomeProject', () => App);
```

处理接触的话是利用`Button`这个组件还有一些处理常见的手势组件（没看），简单看一下接触的处理。可触摸的组件有多种返回类型。

```javascript
import React, {Component} from 'react';
import {FlatList, ScrollView, Platform, StyleSheet, Text, View, AppRegistry, Image, TextInput, Button, Alert, TouchableHighlight, TouchableOpacity, TouchableNativeFeedback, TouchableWithoutFeedback} from 'react-native';
export default class App extends Component {
	onPressButton(){
    	Alert.alert('you tapped the button');
 	 }
  	onLongPresssButton(){
   	 	Alert.alert('you press the button longer');
 	 }
     render(){
    return(
      <View style={styles.container}>
        //当用户按下按钮时，视图的背景将变暗。
        <TouchableHighlight onPress={this.onPressButton} underlayColor='white'>
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableHighlight</Text>
          </View>
        </TouchableHighlight>
        //通过降低按钮的不透明度来提供反馈，从而允许在用户按下时看到背景
        <TouchableOpacity onPress={this.onPressButton}>
            <View style={styles.button}>
              <Text style={styles.buttonText}>TouchableOpacity</Text>
            </View>
        </TouchableOpacity>
        //显示响应用户触摸的墨水表面反应涟漪
        <TouchableNativeFeedback
            onPress={this.onPressButton}
            background={Platform.OS === 'android' ? TouchableNativeFeedback.SelectableBackground() : ''}>
            <View style={styles.button}>
              <Text style={styles.buttonText}>TouchableNativeFeedback</Text>
            </View>
        </TouchableNativeFeedback>
        //处理点击手势但不希望显示任何反馈
        <TouchableWithoutFeedback onPress={this.onPressButton}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableWithoutFeedback</Text>
          </View>
        </TouchableWithoutFeedback>
        //在之前的视图变暗的基础上加上了一个长按的操作
        <TouchableHighlight onPress={this.onPressButton} onLongPress={this.onLongPresssButton} underlayColor="white">
          <View style={styles.button}>
            <Text style={styles.buttonText}>Touchable with Long Press</Text>
          </View>
        </TouchableHighlight>
      </View>
    );
  }
}
AppRegistry.registerComponent('AwesomeProject', () => App);
```

## 滚动条和列表视图

`ScrollView`一个通用的滚动容器，可以放置多个`component`或者 `views`，通过参数控制是横着还是竖着。

```javascript
import React, {Component} from 'react';
import {FlatList, ScrollView, Platform, StyleSheet, Text, View, AppRegistry, Image, TextInput, Button, Alert, TouchableHighlight, TouchableOpacity, TouchableNativeFeedback, TouchableWithoutFeedback} from 'react-native';
export default class App extends Component {
	//实现一个界面放两个滚动条
 	render() {
    render() {
    return (
      <View>
        <ScrollView horizontal>
          <Text style={{fontSize:96}}>Scroll me plz</Text> 
        </ScrollView>

        <ScrollView>
          <Text style={{fontSize:96}}>If you like</Text>
          <Text style={{fontSize:96}}>Scrolling down</Text>
          <Text style={{fontSize:96}}>What's the best</Text>
          <Text style={{fontSize:96}}>Framework around?</Text>
          <Text style={{fontSize:80}}>React Native</Text>
        </ScrollView>
      </View>
    );
  }
}
AppRegistry.registerComponent('AwesomeProject', () => App);
```

`React Native`提供了一套用于显示数据列表的组件：`FlatList`和`SectionList`。列表视图最常见的用途之一是显示从服务器获取的数据。

`FlatList`组件显示一个滚动列表，它唯一呈现当前在屏幕上显示的元素，而不是一次显示所有元素。它有两个道具：`data`和`renderItem`。`data`是列表的信息来源。`renderItem`从源中获取一个项目并返回要呈现的格式化组件。

```javascript
import React, {Component} from 'react';
import {FlatList, ScrollView, Platform, StyleSheet, Text, View, AppRegistry, Image, TextInput, Button, Alert, TouchableHighlight, TouchableOpacity, TouchableNativeFeedback, TouchableWithoutFeedback} from 'react-native';
export default class App extends Component {
	render(){
    return(
      <View style={styles.container}>
        <FlatList 
          data={[
            {key: 'Devin'},
            {key: 'Jackson'},
            {key: 'James'},
            {key: 'Joel'},
            {key: 'John'},
            {key: 'Jillian'},
            {key: 'Jimmy'},
            {key: 'Julie'},
          ]}
		//renderItem这个不是很懂，看懂了再解释
          renderItem={({item}) => <Text style={styles.item}>{item.key}</Text>}
          />
      </View>
    );
  }
}
AppRegistry.registerComponent('AwesomeProject', () => App);
```

如果将一组数据分解为逻辑部分，可能使用部分标题，那么就使用`SectionList`

```javascript
import React, { Component } from 'react';
import { AppRegistry, SectionList, StyleSheet, Text, View } from 'react-native';
export default class SectionListBasics extends Component {
  render() {
    return (
      <View style={styles.container}>
        <SectionList
          sections={[
            {title: 'D', data: ['Devin']},
            {title: 'J', data: ['Jackson', 'James', 'Jillian', 'Jimmy', 'Joel', 'John', 'Julie']},
          ]}
          renderItem={({item}) => <Text style={styles.item}>{item}</Text>}
          renderSectionHeader={({section}) => <Text style={styles.sectionHeader}>{section.title}</Text>}
          keyExtractor={(item, index) => index}
        />
      </View>
    );
  }
}
const styles = StyleSheet.create({
  container: {
   flex: 1,
   paddingTop: 22
  },
  sectionHeader: {
    paddingTop: 2,
    paddingLeft: 10,
    paddingRight: 10,
    paddingBottom: 2,
    fontSize: 14,
    fontWeight: 'bold',
    backgroundColor: 'rgba(247,247,247,1.0)',
  },
  item: {
    padding: 10,
    fontSize: 18,
    height: 44,
  },
})

AppRegistry.registerComponent('AwesomeProject', () => SectionListBasics);

```

