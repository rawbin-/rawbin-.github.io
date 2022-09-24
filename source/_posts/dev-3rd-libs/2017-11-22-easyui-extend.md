---
layout: post
title: "EasyUI 扩展实战"
categories: [Web开发,前端开发,JavaScript开发]
tags: [JavaScript,EasyUI,jQuery,Combobox,Datagrid]
---

### 基本概念

#### 对于每一个插件 xxx 来说，有如下几部分内容，见[文档](http://www.jeasyui.com/documentation/index.php)

- 属性 定义在每个插件的默认值对象里面 `jQuery.fn.xxx.defaults`
- 方法 定义在每个插件的方法对象里面 `jQuery.fn.xxx.methods`, 调用方式为`$("selector").xxx('methodName',methodParams)`，每个方法两个参数，第一个为jQuery对象（必传），第二个为传入的参数
- 事件 定义在每个插件的默认值对象里面 `jQuery.fn.xxx.defaults`



### 实战预热

- 有了上面的基础之后，有扩展就覆盖原有配置属性，随时可以定义自己的扩展
- 先 [下载](http://www.jeasyui.com/download/index.php) EasyUI源码，以便后续看具体的内容
- EasyUI 可以免费用，但不开源，部分代码是压缩的，但不影响代码分析
  - 未压缩的版本再`src`目录里面（内容不全）
  - 所有内容再 `plugins` 里面（带压缩的，内容是全的）
- 翻看`src`下的各个插件的代码（都是插件，jQuery插件）
  - 共有的特征
    - `$.fn.xxx` 以jQuery插件的方式定义
    - `$.fn.xxx.defaults` 定义插件默认属性值
    - `$.fn.xxx.methods` 定义插件方法集合
    - `$.fn.xxx.parseOptions` 定义解析插件配置的方法
    - `$.fn.xxx.parseData` 部分插件才有的解析数据的方法



### 参考资料

0. [EasyUI思想、用法解析](https://rawbin-.github.io/web%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/javascript/2017/10/01/easyui-combo-search/)
1. [EasyUI doc](http://www.jeasyui.com/documentation/index.php)
2. [Datagrid doc](http://www.jeasyui.com/documentation/datagrid.php)
3. [EasyUI 代码下载](http://www.jeasyui.com/download/index.php)
4. [EasyUI Datagrid 列拖拽](https://www.cnblogs.com/wangjiahong/p/4564174.html)
5. [动态的改变列显示的顺序](https://www.cnblogs.com/wangjiahong/p/4564174.html)
6. [Easyui-Datagrid—表头灵活显示](https://blog.csdn.net/u010293698/article/details/47956865)
7. [Easyui-Datagrid—表头灵活拖动](https://blog.csdn.net/u010293698/article/details/48438277)