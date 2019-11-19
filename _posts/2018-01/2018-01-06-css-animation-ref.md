---
layout: post
title: "CSS动画简明参考手册"
categories: [前端开发,Web开发,CSS]
tags: [CSS,动画]
---



#### 过渡相关属性

- `transition-property`
  - 属性名称|all（默认）|none
- `transition-duration`
  - 时间(默认0)
- `transition-timing-function`
  - ease(默认)|linear|ease-in|ease-in-out|step-start|step-end|steps|cubic-bezier(#,#,#,#)
- `transition-delay`
  - 时间(默认0)

#### 变换属性

- `transform`
  - 变换函数(s)|none(默认)
    - rotate, rotateX, rotateY, rotate3d, totateZ
    - scale, scaleX, scaleY, scale3d, scaleZ
    - translate, translateX, translateY, translate3d, translateZ
    - skew, skewX, skewY
- `transform-origin`
  - 百分比|长度|left|center|right|top|bottom
  - 默认 50% 50%

#### 关键帧动画

 ```
@keyframes animation-name{ 
	keyframe {property:value}
	keyframe {property:value}
}
 ```

- `animation-name`
- `animation-duration`
- `animation-timing-function`
- `animation-delay`
- `animation-iteration-count`
- `animation-direction`
- `animation-play-state`
- `animation-fill-mode`
- `backface-visibility`
- `perspective`
- `perspective-origin`