---
title: YAM项目功能点实现四
date: 2019-06-10 08:53:05
tags: 项目
categories: 前端项目
---

# 功能需求

点击头部结束文字弹出用户提示是否要退出

# 实现

使用`Alert`实现这个功能，参考<https://reactnative.cn/docs/alert.html#docsNav>，启动一个提示对话框，包含对应的标题和信息。本接口可以在 `iOS` 和 `Android` 上显示一个静态的提示框。如果要在显示提示框的同时接受用户输入一些信息，那你可能需要`AlertIOS`。

对应我们需求只需要一个静态的提示框就可以了。这个接口在 `Android` 上最多能指定三个按钮，这三个按钮分别具有“中间态”、“消极态”和“积极态”的概念：

如果你只指定一个按钮，则它具有“积极态”的属性（比如“确定”）；两个按钮，则分别是“消极态”和“积极态”（比如“取消”和“确定”）；三个按钮则意味着“中间态”、“消极态”和“积极态”（比如“稍候再说”，“取消”，“确定”）。我们这个只需要积极态和消极态就可以了。

```react
const { meetingId } = await retrieveUserLoginData();
if (meetingId) {
  Alert.alert(
    '提示',
    '是否确认退出当前视频会议',
    [
      { text: '取消', onPress: () => {} },
      { text: '确定', onPress: async () => {
        await this.yim.LeaveChannel(`${meetingId}`);// IM 退出房间
        await this.yim.logout();// IM 退出登录
        TalkService.leaveChannel();
        const { navigate } = this.context;
        navigate('JoinMeeting');
      }}
    ]
  );
}
```

