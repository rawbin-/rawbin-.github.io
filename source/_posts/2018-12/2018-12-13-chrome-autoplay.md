---
layout: post
title: "chrome浏览器下音视频autoplay的情况记录"
sticky: 100
categories: [Web开发,前端开发]
tags: [音视频,自动播放]
---



常见的几个错误在[chromium源码](https://github.com/chromium/chromium/blob/main/third_party/blink/renderer/core/html/media/autoplay_policy.cc)中列出来了

```
const char kWarningUnmuteFailed[] =
    "Unmuting failed and the element was paused instead because the user "
    "didn't interact with the document before. https://goo.gl/xX8pDD";
const char kErrorAutoplayFuncUnified[] =
    "play() failed because the user didn't interact with the document first. "
    "https://goo.gl/xX8pDD";
const char kErrorAutoplayFuncMobile[] =
    "play() can only be initiated by a user gesture.";
```



[文档](https://developer.chrome.com/blog/autoplay/) 写的相对比较清楚，三个条件

- 静音的自动播放都可以
- 带声音的播放有条件
  - 用户在当前域名有交互（点击、触摸等等）的可以自动播
  - 在桌面端的Chrome中，符合用户偏好历史的可以自动播，在 [chrome://media-engagement/](chrome://media-engagement/) 可以看
  - 移动端用户加入首屏的站点或者桌面端已安装的PWA可以自动播
- 顶层的Frame可以允许子级iframe的带声音自动播放，用`iframe`的 `allow="autoplay"`属性，前提是顶层frame满足带声音播放的条件



### 检测方法

可参考 [这个项目](https://github.com/video-dev/can-autoplay)（不是很新的） 的[DemoPage](https://video-dev.github.io/can-autoplay/)
