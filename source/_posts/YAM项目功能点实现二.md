---
title: YAM项目功能点实现二
date: 2019-05-29 09:15:46
tags: 项目
categories: 前端学习
---

# 功能需求

在接收视频的时候，对于所占屏幕较大的采用高清流，所占屏幕较小的采用低清流。

# 实现

首先需要了解一下，这个视频是如何显示的，之前我是以为在这屏幕中，我需要把所有的`memberlist`的成员都渲染出来，对于渲染的那个`userid`就不能清楚的理解，后来我知道了，在这个屏幕的渲染中只会渲染两个成员，除了本身之外渲染`memberlist`的第一个成员。

这样的话，需要完成的点就比较清晰了。在之前已经有一个默认的把两个视频流都接收为低清流的函数。它是遍历了`memberlist`然后进行处理，那么我可以在`memberlist`里面多加一个`boolean`参数：`isBigStream`，当这个`boolean`值为`true`的时候我接收高清流，值为`false`的时候接收低清流。那么这个函数我就可以修改成（在之前的基础上修改，因为把对象弄错了，`memberlist`是一个容器，它是一个`map`，我们定义的`isBigStream`参数是对象上的）：

```javascript
_updateReceiveStream = ()=>{
    let streamInfos = [];
    for (const [k,v] of this.memberList) {
      if(v.isBigStream)
        streamInfos.push({userID:k, streamID: 0 });
      else
        streamInfos.push({userID:k, streamID: 1 });
    }
    if(streamInfos.length > 0)
      YoumeVideoEngine.setUsersVideoInfo(streamInfos);
}
```

第一个问题解决了，那么就是第二个问题我如何设置这个参数值，在这里面我了解到在渲染的时候有两个函数，`VideoBig`组件和``Videosmall`组件，它们分别的去渲染大窗口和小窗口，那么我需要在渲染大窗口的时候把这个`userid`传到函数里面，然后调用这个函数，把这个`userid`的`isBigStream`参数设置为`true`就可以了。所以是这样调用

`VideoBig.js`中：

```javascript
TalkService.setUserBigStreamStatus(userId);
```

那么在封装的`sdk`中，我就需要写这样的一个函数`setUserBigStreamStatus`，是这样：

```javascript
setUserBigStreamStatus = (userId) => {
    this._updateMemberList(userId, {
      isBigStream: true
    });
  }
```

但是也出现了一个问题，因为我渲染的是`memberlist`里面的除了自己的第一个成员，要是我这个渲染的成员离开了视频，那我是不是要把这个离开的成员的`userid`对应的`isBigStream`参数重新设置为`false`，然后把现在渲染的`userid`的`isBigStream`参数值设置为`true`，因此我又加了两个变量，存储之前渲染的`userid`和现在要渲染的`userid`，然后在`VideoBig.js`中调用函数的时候我们把这个参数进行重新赋值和`isBigStream`参数的设置，基本上是这样：

```javascript
//添加部分
// 存放状态为大流的 userId
this.prevBigStreamUserId = '';
this.currentBigStreamUserId = '';

//函数的重新修改
setUserBigStreamStatus = (userId) => {
    this.prevBigStreamUserId = this.currentBigStreamUserId;
    this.currentBigStreamUserId = userId;

    this._updateMemberList(this.prevBigStreamUserId, {
      isBigStream: false
    });

    this._updateMemberList(this.currentBigStreamUserId, {
      isBigStream: true
    });
}
```

至此我们修改的任务就完成了，虽然不是自己一个人完成的，但是自己一个人确定的具体的思路还是正确的，争取早日自己独立完成功能点的开发。