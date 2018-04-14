---
layout: post
title: "Vue从入门到上手"
categories: [Web开发,前端开发,JavaScript,HTML5]
tags: [JavaScript,Vue]
---

### 1 Vue 学习的内容

0. [风格指南](https://cn.vuejs.org/v2/style-guide/)
1. [组件](https://cn.vuejs.org/v2/guide/components.html)
2. [单文件组件](https://cn.vuejs.org/v2/guide/single-file-components.html)
3. [vue-loader](https://vue-loader.vuejs.org/zh-cn/)
4. [vue-router](https://router.vuejs.org/zh-cn/)
5. [vuex](https://vuex.vuejs.org/zh-cn/)





# 组件

## 组件结构

```
{
        el:'#app',
        //组件模板
        template:'',
        //vm的原始数据
        data:{
            
        },
        //计算属性，所有原始数据的变形，每个属性可以有getter(get)何setter(set)
        computed:{

        },
        //组件的操作方法
        methods:{

        },
        components:{
          
        }
}
```



## 组件生命周期

### 2 beforeCreated

- 在实例初始化完成之后，数据监测和事件监听配置之前调用

### 3 created

- 在实例创建完成后被立即调用，此时已经完成数据监测和事件监听配置，挂载还未完成，`$el` 还不存在

### 4 beforeMount

- 在DOM挂载之前被调用，相关的 render 函数手册被调用

### 5 mounted

- 在`el` 被新创建的`vm.$el`替换，并挂载到了实例上后调用，此时子组件不一定都挂载完成，要全部DOM Ready，需要在下一个事件循环`vm.$nextTick`中处理

### 6 beforeUpdate

- 在虚拟DOM重新渲染和更新之前调用，此时更改数据会被合并到一次更新中

### 7 updated

- 在本次虚拟DOM重新渲染和更新之后调用，避免在此回调中更新数据（可能导致一直渲染），可以考虑使用计算属性和 `watcher` 取代这里面的数据更新需求，此时子组件不一定都更新完成，要全部组件都更新完成的状态，需要在下一个事件循环 `vm.$nextTick` 中处理

### 8 beforeDestory

- 实例销毁之前调用，这里实例仍然可用

### 9 destroyed

- Vue实例销毁后调用，这个时候实例及其相关的属性监听器，子组件，事件监听器等都已被销毁





## 组件定义
```
    var TestComponent = Vue.extend({
        template:''，
        //定义组件时必须是函数
        data:function(){

        }
    })

```

## 组件注册
### 10 局部注册
```
    var TestComponent = Vue.extend({})
    var Wrapper = Vue.extend({
        template:`
            <sub-component></sub-component>
        `,
        components:{
            'sub-component':TestComponent
        }
    })

```

### 11 全局注册
```
    var TestComponent = Vue.extend({})
    Vue.component('test-component',TestComponent)

```

## 组件接收外部数据
### 12 字符串类型
```
    <sub-component data-from-prop='dataObj'></sub-component>

    var TestComponent = Vue.extend({
        props:['dataFromProp'],
        template:'outer data:{{ dataFromProp }}'
    });

```

```
    <sub-component data-from-prop='dataObj'></sub-component>

    var TestComponent = Vue.extend({
        props:{
            dataFromProp:{
                type:String, //[String,Number]
                required:true,
                default:function(){
                    return '' //{}
                }
            }
        },
        template:'outer data:{{ dataFromProp }}'
    });

```

## 组件通信
### 13 避免紧耦合
- 需要访问父组件和根组件的内容通过props传进来
- 使用自定义事件 $on,$emit
- .sync 绑定，$emit('update:xxx')
- $ref属性访问 ref属性绑定的子组件

### 14 占位子组件
- slot标签

## 组件实例
### 15 组件实例结构
```
    var viewModel = new Vue({
        el:'#app',
        //vm的原始数据
        data:{
            
        },
        //计算属性，所有原始数据的变形，每个属性可以有getter(get)何setter(set)
        computed:{

        },
        //监听自定义事件
        events:{

        },
        //组件的操作方法
        methods:{

        }
    });

```

### 16 事件操作
#### 16.1 事件绑定
- events属性
- this.$on 方法

```
    var viewModel = new Vue({
        el:'#app',
        //监听自定义事件
        events:{

        },
        //组件的操作方法
        methods:{

        }
    });

```
#### 16.2 事件触发
- this.$emit
- this.$dispatch
- this.$broadcast

## 组件状态管理
- store          初始化并存储所有的状态
- actions        所有的操作定义和分发，调用mutation函数（传入参数）来更新store
- mutations      所有的状态转换

### 17 组件状态过滤
```
    {
        vuex:{
            getters:{
                stateA:function(state){
                    return state
                }
            },
            actions:{
                
            }
        }
    }
```

### 18 严格模式
```
    new Vuex.Store({
        strict:true //process.NODE_ENV !== 'production'
    });
```

### 19 中间件
```
    new Vuex.Store({
        middlewares:[testMiddleware]
    });

```

## 组件生命周期
  初始化事件和生命周期

- init

  在实例开始初始化的时候调用，此时数据监测，事件绑定和Watcher都尚未初始化。


- beforeCreate

  在实例初始化之后，数据监测和事件配置之前调用

  初始化注入和反射

- created

  在实例创建之后调用，此时实例已经结束解析选项，这意味着已建立：数据绑定、计算属性、方法、Watcher\事件回调。此时还没有开始DOM编译， $el还不存在。 

  判断有没有el选项，再判断有没有template选项；有el直接再判断有没有template选项，没el等vm.$mount(el)调用后再判断；有template直接编译作为模板，没有就用el的outerHTML作为模板

- beforeMount

  在挂载开始之前被调用 ：相关的render函数首次被调用

- mounted

  `el` 被新创建的 `vm.$el` 替换，并挂载到实例上去之后调用该钩子。如果 root 实例挂载了一个文档内元素，当 `mounted` 被调用时 `vm.$el` 也在文档内。

  注意 `mounted` **不会**承诺所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以用 [vm.$nextTick](https://cn.vuejs.org/v2/api/#vm-nextTick) 替换掉 `mounted`

 - beforeUpdate

   数据更新时调用，发生在虚拟 DOM 重新渲染和打补丁之前。

   你可以在这个钩子中进一步地更改状态，这不会触发附加的重渲染过程。

 - updated

   由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。

   当这个钩子被调用时，组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 的操作。然而在大多数情况下，你应该避免在此期间更改状态。如果要相应状态改变，通常最好使用[计算属性](https://cn.vuejs.org/v2/api/#computed)或 [watcher](https://cn.vuejs.org/v2/api/#watch) 取而代之。

   注意 `updated` **不会**承诺所有的子组件也都一起被重绘。如果你希望等到整个视图都重绘完毕，可以用 [vm.$nextTick](https://cn.vuejs.org/v2/api/#vm-nextTick) 替换掉 `updated`

- beforeDestory

  实例销毁之前调用。在这一步，实例仍然完全可用

- destoryed 

  Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁

- 


# 工程化
## Webpack配置模板
- webpack             基于Webpack和vue-loader的目录结构和配置，支持热部署、代码检查、测试和CSS提取 
- webpack-simple      基于Webpack和vue-loader的目录结构和配置 
- browserify          基于Browserify和vueify的结构，支持热部署、代码检查及单元测试
- browserify-simple   基于Browserify和vueify的结构
- simple              单个引入Vue.js的index.html页面

### 20 webpack 配置模板目录结构
- build webpack相关配置和脚本
- config 配置文件区分开发、测试和线上环境
- src 源码和资源文件
- static 不需要webpack处理的静态资源
- test 存放测试文件

### 21 webpack 配置代理
```
    proxyTable:{
        '/api':{
            target:'http://demo.baiwang.com',
            changeOrigin:true,
            pathRewrite:{
                '^/api':'/api'
            }
        }
    }

```



# 问题解决
## vue npm install 安装不上的问题
将如下内容放入到 `.bashrc`中
```
export NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node/
export NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node/
export CHROMEDRIVER_CDNURL=https://npm.taobao.org/mirrors/chromedriver
export SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass
```




### 22 参考资料

- [Vuex源码分析](http://web.jobbole.com/91187/)
- [探索Vue高阶组件](http://hcysun.me/2018/01/05/%E6%8E%A2%E7%B4%A2Vue%E9%AB%98%E9%98%B6%E7%BB%84%E4%BB%B6/)