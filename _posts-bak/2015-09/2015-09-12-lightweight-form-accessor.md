---
layout: post
title: "一个轻量级的通用表单值存取方案"
categories: [Web开发,前端开发,JavaScript]
tags: [面向对象,JavaScript,表单值存取]

---

## 由来

一个数据list，展示数据概要，然后通过点击编辑弹层展示数据详情的表单，是web开发中一个很常见的交互场景。

在这个场景中，涉及到整个表单值的存和整个表单值的取，当然这只是一个聚合结果，更细一步说，其实就是每个表单元素的值的存取。

## 实现方式

### 基本类结构

于是我们可以定义如下一个类：
    
    function BaseFormDataAccessor(config){
        this.init(config)
    }
    
    BaseFormDataAccessor.prototype = {
        init:function(config){
           // 
        },
        getData:function(){
            return {}
        },
        setData:function(data){
            //
        },
        getElementData:function(el){
            return {}
        },
        setElementData:function(el,data){
            //
        }
    }

这就是一个大体的结构。

在此基础上，我们只需要在具体的form实现类中，继承这个基类，传入响应的表单配置，就实现了一个表单值的存取。

### 实现类

一个实现类的代码：

    function UserInfoForm(container,config) {
        this.elsConfig = config.elsConfig || [];
        BaseFormDataClass.call(this, container);
    }
    UserInfoForm.prototype = new BaseFormDataClass();

### 类的使用

一个使用类的代码：

    var userInfoForm = new UserInfoForm('.js-user-info-container'，{
        elsConfig: [{
            dataKey: 'loginname',   //提供给后台的数据key，不用写数据适配了，直接改这儿就行
            selector: '.js-login-name' //表单不用写name了，直接配一个当前container内唯一选择器就行
        }, {
            dataKey: 'nickname',       
            selector: '.js-nick-name'
        }, {
            dataKey: 'portrait',
            selector: '.js-portrait-container',  //简单数据处理什么都不需要配置，
            dataGetter:function(){               //可以根据需要灵活自定义数据处理方式
                var $container = this.$el;
                return $container.children('img').attr('src');
            },
            dataSetter:function(imgUrl){
                var $container = this.$el;
                var $img = $container.children('img'); 
                if($img.length === 0){
                    $container.html('<img src="' + imgUrl +'">');
                }else{
                    $img.attr('src') = imgUrl;
                }    
            }    
        }]
    });

### 基类

基类代码如下：
    
    function BaseFormDataAccessor(container){
        if(container){
            this.init(container);
        }
    }

    BaseFormDataAccessor.prototype = {
        elsConfig:[],
        keysMap:{},
        init:function(container){
            if (container) {
                this.$container = $(container);
            }

            if (!this.$container) {
                return;
            } else {
                var cfgList = this.elsConfig;
                var keysMap = this.keysMap || {};
                var tmpConfig;
                for(var i = cfgList.length; i--;){
                    tmpConfig = cfgList[i];
                    keysMap[tmpConfig.dataKey] = tmpConfig;
                    tmpConfig['$el'] = $(tmpConfig.selector);
                }
            }

        },
        isInitialized: function(){
            return this.$container && this.$container.length > 0;
        },
        checkForm:function(){
            if (!this.isInitialized()) {
                throw new Error('请先初始化表单上下文');
            }
        },
        getData:function(keyList,targetKeyList){

            this.checkForm();

            keyList = keyList || Object.keys(this.keysMap);
            var formData = {};
            var key,targetKey;

            if(!targetKeyList){
                for (var i = keyList.length; i--;) {
                    key = keyList[i];
                    formData[key] = this.getElementData(this.keysMap[key]);
                }
            }else{
                for (var i = keyList.length; i--;) {
                    key = keyList[i];
                    targetKey = targetKeyList[i];
                    formData[targetKey] = this.getElementData(this.keysMap[key]);
                }
            }

            return formData;
        },
        setData: function (data, isFull) {

            this.checkForm();

            var key;
            var keyList = isFull?Object.keys(this.keysMap): Object.keys(data);
            for (var i = keyList.length; i--;) {
                key = keyList[i];
                if(key in this.keysMap){
                    this.setElementData(this.keysMap[key],data[key]);
                }else{
                    //throw new Error('指定的配置不存在')
                    //pass
                }
            }
        },
        getElementData:function(config){
            if(config.dataGetter && $.isFunction(config.dataGetter)){
                return config.dataGetter.call(config);
            }else{
                return config.$el.val()
            }
        },
        setElementData:function(config,value){
            value = this.serializeValue(value);
            if(config.dataSetter && $.isFunction(config.dataSetter)){
                config.dataSetter.call(config,value);
            }else{
                config.$el.val(value);
            }
        },
        serializeValue:function(value){
            if(!value){
                return ''
            }

            if(typeof value === 'object'){
                try{
                    return JSON.stringify(value)
                }catch(e){
                    return value.toString();
                }
            }else if(typeof value === 'string' || typeof value === 'number'){
                return value
            }else{
                return '';
            }
        },
        getElement:function(key){
            var config = this.keysMap[key];
            return config?config.$el:null;
        }
    }
    
