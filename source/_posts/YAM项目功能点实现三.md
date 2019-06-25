---
title: YAM项目功能点实现三
date: 2019-06-10 08:52:50
tags: 项目
categories: 前端学习
---

# 功能需求

实现`userId`和`View`的一对多，以及保证线程安全，在`ViewRenderManager`中修改`addRender`，`Remove`的实现修改一个人对应的`View`。

# 实现

首先之前的存储是利用`HashMap`存储，一个`userId`对应一个`View`：

```java
public class RenderInfo {
    public int rotation;
    public SurfaceViewRenderer view;
    public RenderInfo(int rotation, SurfaceViewRenderer view){
        this.rotation = rotation;
        this.view = view;
    }
}
private Map<String, RenderInfo> renderers = new HashMap<String, RenderInfo>();
```

那么我需要实现一个`userId`对应多个`View`，那么我肯定需要拿一个数据结构存储这多个`View`并且保证线程安全，可以选择`ConcurrentHashMap`代替`HashMap`，然后用`Vector`去存储这多个`View`。

```java
private Map<String, Vector> renderers = new ConcurrentHashMap<String, Vector>();
```

我们在`VideoRendererManager`里写增删，然后再外面封装一层`YoumeManager`实现增删改，最后在`YoumeViewManager`写业务逻辑时候再调用函数实现功能点。

那我们先修改`VideoRendererManager`层：

```java
/**
* 添加渲染源,添加的时候如果这个userId已经存在，就添加到对应的Vector里面，如果不存在
* 话就新生成一个Vector，然后put到ConcurrentHashMap里面就可以了。
* @param view
* @return
*/
public RenderInfo addRender(String userId, SurfaceViewRenderer view) {
   //int renderId = api.createRender(userId);
   RenderInfo info = new RenderInfo(0, view);
   Vector<RenderInfo> renderInfos = renderers.get(userId);
   if(renderInfos != null){
   		renderInfos.add(info);
   }
   else{
   		Vector<RenderInfo> renderInfos1 = new Vector<RenderInfo>();
        renderInfos1.add(info);
        renderers.put(userId,renderInfos1);
   }
   Log.d(TAG, "addRender userId:"+userId);
   return info;
}
/**
* get渲染源，以前的话只需要get出来那个userId，所以返回SurfaceViewRenderer就可以了
* 现在因为一个userId对应多个View，所以应该返回Vector<SurfaceViewRenderer>，然后
* 由于从ConcurrentHashMap里面取出来的是RenderInfo，所以应该把View从RenderInfo
* 取出来放到Vector中再返回
*/
public Vector<SurfaceViewRenderer> getRender(String userId) {
    Vector<RenderInfo> renderInfos = renderers.get(userId);
    Vector<SurfaceViewRenderer> surfaceViewRenderers = new 					Vector<SurfaceViewRenderer>();
    if(renderInfos != null){
      for(int i=0;i<renderInfos.size();i++){
        surfaceViewRenderers.add(renderInfos.get(i).view);
      }
      return surfaceViewRenderers;
    }
    else {
      return null;
    }
}
/**
* 删除的时候，因为每个人有多个View，所以删除的时候要删除对应的userId的对应的那个
* RenderInfo，当然如果这个userId对应的View要是为空的时候就应该把这个userId
* ConcurrentHashMap里面删除，
*/
public int deleteRender(String userId,RenderInfo renderInfo) {
    //int ret = api.deleteRender(renderId);
    if(renderInfo == null) {
      renderers.remove(userId);
    }
    else{
      Vector<RenderInfo> renderInfos = renderers.get(userId);
      if(renderInfos != null) {
        renderInfos.remove(renderInfo);
      }
    }
    return 0;
}
同理在渲染的时候之前是直接渲染对应的那个View，现在的话我们需要把这个userId对应的Vector取出来，然后渲染这个Vector。
```

这样的话最底层的数据修改就完成了，现在修改`YoumeManager`层：

```java
public VideoRendererManager.RenderInfo addRenderInfo(final String userid) {
    SurfaceViewRenderer sView = new SurfaceViewRenderer(context);
    return VideoRendererManager.getInstance().addRender(userid,sView);
}

public void deleteRenderInfo (String userid, VideoRendererManager.RenderInfo renderInfo){
    VideoRendererManager.getInstance().deleteRender(userid,renderInfo);
    if(renderInfo != null && renderInfo.view != null){
      renderInfo.view.release();
    }
}
```

再修改`YoumeViewManager`层：

```java
@ReactProp(name = "options")
public void setOptions(final YoumeVideoView videoView, final ReadableMap options) {
    String userID = options.hasKey("userID") ? options.getString("userID") : "";
    //停止在最后一帧画面
    boolean pause = options.hasKey("pause") ? options.getBoolean("pause") : false;
    //隐藏视频组件显示，如果全部都隐藏了，同时会屏蔽视频流接受
    boolean hide = options.hasKey("hide") ? options.getBoolean("hide") : false;
    //在组件销毁时自动屏蔽视频接受
    boolean autoMask = options.hasKey("autoMask") ? options.getBoolean("autoMask") : false;
    Log.d(REACT_COMPONENT_NAME, "setOptions:"+currentCount+ " " + userID + " pause:" + pause + " hide:" + hide);
    if (userID == null) return;
    /** 根据业务逻辑，当我们需要改变userId的时候有几种情况，第一个是change，第二个
    * 是add，add的话我们就直接调用YoumeManager层的addRenderInfo函数，然后把对应
    * 的View绑定到对应的videoView（正在渲染的View）上就可以了。change的话我们正常
	* 的逻辑就是我把这个对应的userId的对应的View删了，然后把新的View添加到这个
	* userId上去就可以了，当然还需要把之前的View从当前渲染的videoView上取消绑定以
	* 及把新的View绑定到哦当前渲染的videoView上。
    */
    if (videoView.surfaceView != null) {
      if (!userID.equals(videoView.userid)) {
        YoumeManager.getInstance().deleteRenderInfo(videoView.userid, videoView.renderInfo);
        videoView.removeView();
        videoView.renderInfo = YoumeManager.getInstance().addRenderInfo(userID);
        videoView.addView(videoView.renderInfo, userID);
        setZOrderMediaOverlay(videoView, videoView.zOrderMediaOverlay);
      }
    }
    else {
      videoView.renderInfo = YoumeManager.getInstance().addRenderInfo(userID);
      videoView.addView(videoView.renderInfo, userID);
    }
    videoView.setPause(pause);
    videoView.setHide(hide);
    api.maskVideoByUserId(userID, false);

    videoView.setAutoMask(autoMask);
    if(videoView.neetSetZOrder){
      videoView.neetSetZOrder = false;
      setZOrderMediaOverlay(videoView, videoView.zOrderMediaOverlay);
    }
}

@Override//销毁实例
public void onDropViewInstance(YoumeVideoView videoView) {
    Log.d(REACT_COMPONENT_NAME, "onDropViewInstance："+ currentCount + " "+ (videoView.userid != null ? videoView.userid:""));
    if(videoView.userid != null) {
      YoumeManager.getInstance().deleteRenderInfo(videoView.userid, videoView.renderInfo);

      Vector<SurfaceViewRenderer> renderInfos = VideoRendererManager.getInstance().getRender(videoView.userid);
      if(videoView.autoMask &&(renderInfos == null || renderInfos.size() == 0)) {
        api.maskVideoByUserId(videoView.userid, true);
      }
    }
    super.onDropViewInstance(videoView);
}
```

这样的话，我们的任务就解决了。

