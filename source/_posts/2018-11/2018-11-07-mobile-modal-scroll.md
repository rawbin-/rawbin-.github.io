# 2018-11-07-mobile-modal-scroll

---

layout: post
title: "移动端滚动穿透解决方案"
cagegories: [Web开发,前端开发,移动端]
tags: [滚动穿透,弹层滚动,禁止滚动]

---

# 需求场景描述
- 页面有弹层，弹层有滚动
- 要求弹层滚动时背景不滚动

# 解决办法
## 解决标准
- 很多方法都是要改原来的页面（也就是触发弹层的背景页面），给原来的页面加样式等操作，不符合本次解决的标准
- 本次解决的标准希望是在弹层中处理，把弹层当做一个业务无关的组件，不做关联依赖处理

## 小程序中的情景
### 模拟器（符合预期）
- 在弹层的滚动区域只会触发弹层的滚动，不会触发背景页面的滚动

### Android机（华为Mate30 5G, Android 10）
- 在弹层区域能滚动的时候只触发弹层的滚动
- 在弹层区域不能滚动的时候（比如滚到头或者滚到尾的时候）会触发背景页面的滚动

### iOS机（iPhone X ，iOS 13.4.1）
- 在弹层滚动条出现的时候正常，弹层滚动条不出现的时候滚动背景
- 不但有背景的本身的滚动，还有弹性滚动

## 小程序中解决办法
### 在modal 最外层加catchtouchmove="emptyFunction"（别给"true"这样会报警告的方法）
- 这样在模拟器上touchpad还可以滚动，真机和模拟器上都没法拖动
- 仅适合弹层没有滚动条的情况

### 在modal 最外层加catchtouchmove="emptyFunction"，弹层滚动区域用scroll-view，并设置scroll-y为true
- 只要有弹层，背景就不会滚动
- 弹层内部的滚动会正常的scroll-view滚动


# 参考资料
- [链接和代码](https://developers.weixin.qq.com/community/develop/article/doc/00062666530a300ed35ba552856413)