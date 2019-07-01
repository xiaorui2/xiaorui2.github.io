---
title: YAM项目功能点实现一
date: 2019-05-27 22:01:13
tags: 项目
categories: 前端项目
---

# 项目运行步骤

先从git上把项目clone下来

```
git clone "项目地址的url"
```

利用`VSCode`打开项目后，配置好终端，默认的是`shell`，用`git bash`的会好用一点。

下载好`yarn`（一个资源管理器，可以很快导入执行包），在终端中运行命令就可以把项目跑起来了：

```
yarn
react-native run-android
```

# 功能需求

实现成员列表页面的图标渲染，并把这个组件进行封装。

![](1.png)

# 实现

首先，已经有一个读取好的`memberlist`和监听频道中人员变化的函数了。

那么我就需要把数据从这个`memberlist`中读取出来并显示。基于之前写的那个`ReactDemo`知道我们需要调用一个函数，并且使用`map`函数，在函数中把存储的数据显示：

```javascript
  function getListItem () {
    return memberList.map(member => (
      <ListItem key={member.userId}>
        <View style={{ flexDirection: 'row' }}>
          <View style={{flex: 1, flexDirection: 'row'}}>
            <Text style={{flex: 3}}> {member.userInfo && member.userInfo.nick_name}</Text>
            <Image source= {member.isMicrophoneOn ? require('./icons/Microphone.png') : require('./icons/Microphone1.png')} />
            <Image source= {member.isVideoOn ? require('./icons/Camera.png') : require('./icons/Camera1.png')} />
          </View>
        </View>
      </ListItem>
    ));
  }
  
<View>
    <List>
    	{getListItem () }
    </List>
</View>
```

这样的话我们就能够读取出信息并基于读取的信息来设定对应的图标。

然后要把这个`List`封装起来，就需要在我们的`components`组件文件下定义这个`Component`。基本的实现其实和我们写好的函数差不多，

```javascript
import React, {useState, useEffect} from 'react';
import PropTypes from 'prop-types';
import { View, Image } from 'react-native';
import { List, ListItem, Text } from 'native-base';

export default function EasyList (props){
  const { images } = props;
  const { Microphone, Microphone1, Camera, Camera1 } = images;

  function getListItem () {
    return props.memberList.map(member => (
      <ListItem key={member.userId}>
        <View style={{ flexDirection: 'row' }}>
          <View style={{flex: 1, flexDirection: 'row'}}>
            <Text style={{flex: 3}}> {member.userInfo && member.userInfo.nick_name}</Text>
            <Image source= {member.isMicrophoneOn ? Microphone : Microphone1} />
            <Image source= {member.isVideoOn ? Camera : Camera1}  />
          </View>
        </View>
      </ListItem>
    ));
  }

  return(
    <List>
      {getListItem()}
    </List>
  );
}

EasyList.propTypes = {
  memberList: PropTypes.array.isRequired,
  images: PropTypes.object.isRequired
};
```

我们要知道，组件中调用的资源都是从父组件中传递过来的，所以我们需要把之前的图标也传递过来。

在`Image`中，我们初步写的函数中调用资源的话是 

```
source= {...}
```

那么我们需要把`{...}`这些资源传递到组件中，因此可以定义一个常量进行传递：

```javascript
const images = {
  Microphone: require('./icons/Microphone.png'),
  Microphone1: require('./icons/Microphone1.png'),
  Camera: require('./icons/Camera.png'),
  Camera1: require('./icons/Camera1.png'),
};

<View>
	<EasyList memberList={memberList} images={images} />
</View>
```

至于如何读取，在上面的已经写过了，基本上思想是一样的就不重复的叙述了。