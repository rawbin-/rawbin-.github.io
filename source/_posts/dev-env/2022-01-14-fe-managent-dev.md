---
layout: post
title: "前端中后台管理系统选型和开发"
categories: [Web开发,前端开发]
tags: [管理系统, 自动化表单]
---

# 选型设计结论
## 诉求
- 对于后台管理系统
	- UI设计一次定型，符合业界设计风格，不需要太个性化的设计
	- 侧重业务流程、效率，不过分关注UI
	- 相似流程和交互占比大，有通用模式可以提取，在大量系统中可以大大提效
	- 表单、列表、通用流程展示、数据看板等都是最常用的通用业务模式

## 设计
### 对于管理系统模板/框架来说
- 具有通用的流程结构、登录注册，菜单、路由、权限校验等等
- 经过业界项目的洗礼，能够适用或者通用

### 对于通用业务模式
- 模式可以通过配置化生成
- 相似模式可以通过简单配置来实现，将代码量降到最低，同时会大大降低联调、测试等工作量
- 增加模式配置的灵活性
  - 支持各种布局方式
  - 支持详细的属性设置
  - 支持校验规则自定义
  - 支持元素之间联动
  - 支持事件的自定义

## 实现选型
### 后台管理系统模板/框架
#### React [ant-design](https://github.com/ant-design)/**[ant-design-pro](https://github.com/ant-design/ant-design-pro)**

#### Vue [vueComponent](https://github.com/vueComponent)/**[ant-design-vue-pro](https://github.com/vueComponent/ant-design-vue-pro)**

### 配置化表单 [alibaba/formily](https://github.com/alibaba/formily)
- 表单设计器这部分可以根据实现定位各取所长
	- 比如偏重开发的部分，可以有更多更详细的配置，可以通过写代码的方式来配置或者配置中可以写代码
	- 比如偏重运营的部分，那么[vform666](https://github.com/vform666)/[variant-form](https://github.com/vform666/variant-form)中的内置配置模板就值得参考采纳
-  实现思路上可以先偏重开发部分，暴露对应的接口，然后在此基础上偏重运营部分，让偏运营部分可以快速简单做绝大部分的事情，而偏开发部分可以作为增强的高级功能，让配置实现一切



# 现有的后台管理系统模板

## [[React]]系列
### [ant-design](https://github.com/ant-design)/**[ant-design-pro](https://github.com/ant-design/ant-design-pro)**
- Ant Design 中后台管理系统


### [yezihaohao](https://github.com/yezihaohao)/**[react-admin](https://github.com/yezihaohao/react-admin)**
- React + Antd
´
### [marmelab](https://github.com/marmelab)/**[react-admin](https://github.com/marmelab/react-admin)**
- React+Material UI

### [zuiidea](https://github.com/zuiidea)/**[antd-admin](https://github.com/zuiidea/antd-admin)**
- antd + umijs

### [NLRX-WJC](https://github.com/NLRX-WJC)/**[react-antd-admin-template](https://github.com/NLRX-WJC/react-antd-admin-template)**


## [[Vue]] 系列
### [iczer](https://github.com/iczer)/**[vue-antd-admin](https://github.com/iczer/vue-antd-admin)**
- Ant Design Pro的Vue实现版本


### [vueComponent](https://github.com/vueComponent)/**[ant-design-vue-pro](https://github.com/vueComponent/ant-design-vue-pro)**
- 基于Ant Design of Vue 的版本


### [chuzhixin/vue-admin-better](https://github.com/chuzhixin/vue-admin-better)
- 支持vue2 element-ui，vue2 element-plus
- 支持vue3 ant-design-vue
- 支持PC，平板，手机

### [hooray](https://github.com/hooray)/**[fantastic-admin](https://github.com/hooray/fantastic-admin)**
### [hooray](https://github.com/hooray)/**[one-step-admin](https://github.com/hooray/one-step-admin)**
- 支持Vue2 和 Vue3
- 支持PC端和移动端

### [PanJiaChen](https://github.com/PanJiaChen)/**[vue-element-admin](https://github.com/PanJiaChen/vue-element-admin)**
- Vue+ElementUI


### [vbenjs](https://github.com/vbenjs)/**[vue-vben-admin](https://github.com/vbenjs/vue-vben-admin)**
- Vue3+vite+TS

### [hooray](https://github.com/hooray)/**[fantastic-admin](https://github.com/hooray/fantastic-admin)**
- 支持Vue2 和 Vue3
- 支持PC和移动端

### [iview](https://github.com/iview)/**[iview-admin](https://github.com/iview/iview-admin)**

### [Armour](https://github.com/Armour)/**[vue-typescript-admin-template](https://github.com/Armour/vue-typescript-admin-template)**


# 配置化或动态化表单
##  业界的内容
- [动态表单引擎，向低代码迈出最关键的一步](https://www.tangshuang.net/8149.html)
- [动态表单](https://ngte-web.gitbook.io/i/gong-cheng-shi-jian/di-dai-ma-da-jian/dong-tai-biao-dan)

支持的语言表示  
- [hyperhype](https://github.com/hyperhype)/**[hyperscript](https://github.com/hyperhype/hyperscript)**
- [HyperJSON](https://www.tangshuang.net/8026.html)

## 切入点
- 通用的工具无法适配大部分业务场景，或者说配置量太大
- 需要针对业务场景进行专门的封装和定制，这样就可以业务人员快速开发和切入
- 前期对齐业务模型，后期只需要在业务模型的基础上进行配置即可
- [表单设计模式](https://fusion.design/pc/doc/design/%E8%AE%BE%E8%AE%A1%E6%A6%82%E8%A7%88/15?themeid=2)



## 业界已有实现
### 都支持系列
#### [alibaba/formily](https://github.com/alibaba/formily)
- [官网](https://formilyjs.org/zh-CN)
- 支持React/ReactNative/Vue2/Vue3/
- 支持[表单设计器](https://designable-antd.formilyjs.org/)，基于[alibaba/designable](https://github.com/alibaba/designable)
- 支持多种布局
- 支持PC和移动端
- 有详细的组件属性配置和校验规则配置入口

### [[React]] 系列
### [alibaba/formily](https://github.com/alibaba/formily)
- [官网](https://formilyjs.org/zh-CN)
- 支持React/ReactNative/Vue2/Vue3/
- 支持[表单设计器](https://designable-antd.formilyjs.org/)，基于[alibaba/designable](https://github.com/alibaba/designable)
- 支持多种布局
- 支持PC和移动端
- 有详细的组件属性配置和校验规则配置入口

#### [alibaba](https://github.com/alibaba)/**[x-render](https://github.com/alibaba/x-render)**
- 由原来的`form-render`升级而来
- 目前有`chart-render`,`form-render`,`table-render`
- [json-schema](https://json-schema.org/understanding-json-schema/)

#### [baidu](https://github.com/baidu)/**[amis](https://github.com/baidu/amis)**
- 支持原生JavaScript和[[React]]
- 通过JSON配置字段`type`映射到具体的组件上，剩下的都当做组件的属性传入
- 自定义组件支持函数，函数通过转成字符串传人



### [[Vue]] 系列
#### [alibaba/formily](https://github.com/alibaba/formily)
- [官网](https://formilyjs.org/zh-CN)
- 支持React/ReactNative/Vue2/Vue3/
- 支持[表单设计器](https://designable-antd.formilyjs.org/)，基于[alibaba/designable](https://github.com/alibaba/designable)
- 支持多种布局
- 支持PC和移动端
- 有详细的组件属性配置和校验规则配置入口

#### [vform666](https://github.com/vform666)/[variant-form](https://github.com/vform666/variant-form)
- 支持栅格布局
- 支持详细的配置，校验，事件属性配置（写代码）
- 有表单模板，可参考
- 支持PC，Pad和H5

#### [xaboy/form-create](https://github.com/xaboy/form-create)
- [官网](http://www.form-create.com/v2/)
- 支持vue2，vue3
- 支持 iview, view-design,elementUI, ant-design-vue
- 有表单设计器，代码 [xaboy](https://github.com/xaboy)/**[form-create-designer](https://github.com/xaboy/form-create-designer)**
- 支持栅格布局
- 支持PHP


#### [JakHuang](https://github.com/JakHuang)/**[form-generator](https://github.com/JakHuang/form-generator)**
- 有可视化设计器
- 可分组，无专门的布局
- 有较丰富的文档

#### [fuchengwei/vue-form-create](https://github.com/fuchengwei/vue-form-create)
- 有可视化设计器
- 支持栅格布局
- 可配置表单校验规则

#### [GavinZhuLei](https://github.com/GavinZhuLei)/**[vue-form-making](https://github.com/GavinZhuLei/vue-form-making)**
- 分为`MakingForm` 和 `GenerateForm` 
- 布局和控件相对较全，支持多端布局
- 可以为表单添加动作
- 二次开发需要商业授权，基础款的功能较少

####  [didi](https://github.com/didi)/**[epage](https://github.com/didi/epage)**
- 有可视化设计器
- 控件较少，布局只支持栅格
- 每个组件设置的内容很精细

#### [sscfaith](https://github.com/sscfaith)/**[avue-form-design](https://github.com/sscfaith/avue-form-design)**
- 支持的控件相对较多，布局可以分组，没有专门的布局
- 表单设计
- 流程设计

#### [lljj-x](https://github.com/lljj-x)/**[vue-json-schema-form](https://github.com/lljj-x/vue-json-schema-form)**
- 支持vue2 ElementUI, vue2 iView3
- 支持vue3 Element Plus, vue3 Antdv
- 有专门的活动页面编辑器 [lljj-x](https://github.com/lljj-x)/**[vjsf-demo-editor](https://github.com/lljj-x/vjsf-demo-editor)**
- 有可视化设计，布局限制较多

