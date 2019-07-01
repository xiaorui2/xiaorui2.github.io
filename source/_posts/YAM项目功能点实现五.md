---
title: YAM项目功能点实现五
date: 2019-06-10 10:40:54
tags: 项目
categories: 前端项目
---

# 功能需求

现在情形是每当有人进频道的时候，这个人的视频数据和`video`数据都会被接受，现在想做成我只接收我现在当前屏幕的显示的视频的数据流，频道里其他人的话我只接收声音就可以了。

# 实现

首先我么要知道什么时候我们需要这个视频流量屏蔽的功能，根据需求我们可以了解到是否绑定视图来决定是否要屏蔽流量，所以我们需要再三个地方进行修改。

在`YoumeModule`添加对应的事情监听：

```java
case YouMeConst.YouMeEvent.YOUME_EVENT_OTHERS_VIDEO_ON:{
    Log.d(TAG, "video _on: "+param.toString());
    TimerTask task = new TimerTask() {
      @Override
      public void run() {
        Vector<SurfaceViewRenderer> renderInfos = VideoRendererManager.getInstance().getRender(param.toString());
        if(renderInfos == null || renderInfos.size() == 0) {
          Log.d(TAG, "run: "+param.toString());
          api.maskVideoByUserId(param.toString(), true);
        }
      }
    };
    Timer timer = new Timer();
    timer.schedule(task, 150);
}
```

然后就是对应的在`delete`的时候肯定是屏蔽流量的，在对应的`add`或者`change`的时候肯定是不屏蔽的所以在`YoumeViewManager`中加以下的内容：

```java
//添加或者改变的时候是在当前的videoview上修改，所以不屏蔽
api.maskVideoByUserId(userID, false);
//delete的时候需要屏蔽
if(videoView.autoMask &&(renderInfos == null || renderInfos.size() == 0)){
	api.maskVideoByUserId(videoView.userid, true);
}
```

