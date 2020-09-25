---
layout: post
title: "fekit 性能优化 "
categories: [前端开发,应用技术,开发环境]
tags: [fekit, 前端工程]

---

## 1 重要的写前面

### 1.1 解决办法
修改$FEKIT_HOME/lib/util.js,性能能提升N倍。

    exists: function(path) {
      //return _fileExistsWithCaseSync(path);
      
      if (fs.existsSync) {
        return fs.existsSync(path);
      }
      if (syspath.existsSync) {
        return syspath.existsSync(path);
      }
      
    }

### 1.2 效果

pack、min 过程减少N分钟


### 1.3 原因分析
说实话没看懂到底要干啥，github上抠代码，发现 是2014年12月份提交的， 当时的提交日志是“开发时require区分大小写”。

发布时间是v0.2.88之后 0.2.99之前

看完又是懵逼了，代码里面没有看到大小写，整个工程中也就这一个文件用到了这个方法。

技术原因倒也简单，递归的效率太低（指数增长），这里一次能搞定的东东，搞了很多次，自然慢了。

找一个工程 打印exists调用次数 618958,写一段代码测试下 1000次执行的差异，可以看出这个工程可以提升近十分钟：

    ################
    修改后: 50
    ################
    修改前N级目录: 607
    ################
    修改前N+2级目录: 785
    ################
    修改前N+4级目录: 976

代码如下：

    var fs = require("fs");
    var syspath = require('path');
    
    var _fileExistsWithCaseSync = function (filepath) {
        //console.log("pid:", process.pid, '--', filepath, + new Date())
        var dir, filenames;
        if (!fs.existsSync(filepath)) {
            return false;
        }
        dir = syspath.dirname(filepath);
        if (dir === '/' || dir === '.' || (/^\w:\\$/.test(dir))) {
            //console.log("pid:", process.pid, '--', filepath, + new Date())
            return true;
        }
        filenames = fs.readdirSync(dir);
        if (filenames.indexOf(syspath.basename(filepath)) === -1) {
            return false;
        }
        return _fileExistsWithCaseSync(dir);
    };
    
    var exists = function (path) {
        //  return _fileExistsWithCaseSync(path);
        //console.log("pid:", process.pid, '--', path, + new Date())  
        if (fs.existsSync) {
            //console.log("pid:", process.pid, '--', path, + new Date())
            return fs.existsSync(path);
        }
        if (syspath.existsSync) {
            //console.log("pid:", process.pid, '--', path, + new Date())
            return syspath.existsSync(path);
        }


    }
    
    var counter = 1000;
    
    console.log("################")
    var startTime = new Date();
    for(var i = 0;i < counter;i++){
        exists("D:/OpenSource/fekit/lib/commands/pack.js")
    }
    console.log("修改后:",new Date() - startTime);
    
    console.log("################")
    startTime = new Date();
    for(var i = 0; i < counter; i++){
    _fileExistsWithCaseSync("D:/OpenSource/fekit/lib/commands/pack.js") 
    }
    console.log("修改前N级目录:",new Date() - startTime);


    console.log("################")
    startTime = new Date();
    for(var i = 0; i < counter; i++){
    _fileExistsWithCaseSync("D:/OpenSource/fekit/lib/commands/test/longer/pack.js") 
    }
    console.log("修改前N+2级目录:",new Date() - startTime);	
    
    console.log("################")
    startTime = new Date();
    for(var i = 0; i < counter; i++){
    _fileExistsWithCaseSync("D:/OpenSource/fekit/lib/commands/test/longer/test/longer/pack.js") 
    }
    console.log("修改前N+4级目录:",new Date() - startTime);	


## 2 问题由来

一开始一直使用的fekit 的一个比较老旧的版本，fekit@0.2.85；后来需要做一段touch开发，工程需要支持sass，于是升级了fekit至最新版。

也就是噩梦的开始，期间换个多个版本，效果都是一样。一个字，太慢了。刷一个页面要而二三十秒。

也尝试过去解决，希望可以找到一个临界版本，然后进行目录比较得到结果。事情没有能够顺利的进行，因为中间有好多版本没法使用（在windows上），所以根本没法这样获得结果。

也跟维护者进行过咨询和讨论，也没有得到一个较好的解决办法。

于是使用fekit 的-b -c 各种参数来进行性能提升，慢慢的强迫症有些消退，也适应了这种老牛拉破车的节奏。

## 3 转折点

然而，虽然我个人慢慢的适应了这个状态，但心里始终有个心结。后来其他部门的小伙伴，其他职位的小伙伴也慢慢的反映出此问题来了。

当邮件组再次出现这样的问题的时候，我也没有像一开始那么激动地要上去煽一把火，期待火大点总能燃起来。 只是默默的想着，我要有点时间，必搞搞此问题。

恰好来了个周末。

## 4 问题解决过程

### 4.1 搭环境 
+ 使用nvm搞了两个node 环境，每一个node装一个fekit，一个最新版，一个0.2.85
+ 用同一个项目（自然是同一台机子），进行对比测试（还是老样子）
+ 然后是打log，尝试了node-inspector, 报错
+ 对比fekit pack，fekit min同理

### 4.2 代码对比分析 差异太大
+ 0.2.85是单进程，最新版是多进程
+ 各种模块有差异
+ 先改了改安装后的源代码，嵌入了一段代码，记录所有打包文件耗时的top5
+ 然后根据这个top5 分析代码特征
+ 发现都是一大片的require
+ 于是查看fekit 的CHANGE LOG，发现跟require相关的差异比较大的sass模块，替换上，没多大影响
+ 于是就懵逼了

### 4.3 目录对比，还是目录对比
+ 前面说到了，分析不下去了
+ 到这个份上了，只能硬比，硬不起来也要比
+ 生物学实验的氛围立马营造了起来
+ 0.2.85是单进程，最新版是多进程，首先把多进程搞成单进程
+ 尽量把最新版的模块都替换成参照物的0.2.85的模块
+ 然后是各种匹配，替换，各种失败
+ 不说了，都是泪
+ 看着看着发现某些代码差异比较大，看起来还io，满以为到头了，结果还是。。。
+ 大块的都搞完了，开始搞小块的了，代码已经被替换的不堪入目了
+ 然后是一个判断环境的一些，直接return一个值，没啥鸟用
+ 最后到了判断路径存在的，居然还是一个递归。。。。，替换到这儿，撞上了。

### 4.4 全是代码

#### 4.4.1 一个记录TopN的小工具

    topNManager = {
        n:5,
        maxN:[],
        infoMap:{},
        raceTop:function(timeDelta,path){
            var self = this;
            if(self.maxN.length < self.n){
                self.maxN.push(timeDelta);
                self.infoMap[timeDelta] = path;
            }else{
                var min = Math.min.apply(null,self.maxN);
                if(min < timeDelta){
                    self.maxN.splice(self.maxN.indexOf(min),1,timeDelta);
                    self.infoMap[timeDelta] = path;
                }
            }
        },
        print:function(){
            var self = this;
            utils.logger.log("耗时Top",self.n);
            self.maxN.sort(function(a,b){return b - a})
            self.maxN.forEach(function(value,index){
                utils.logger.log("耗时",value,", 路径:",self.infoMap[value]);
            })
        }
    }


#### 4.4.2 瓶颈点的代码

    exists: function(path) {
      //return _fileExistsWithCaseSync(path);
      
      if (fs.existsSync) {
        return fs.existsSync(path);
      }
      if (syspath.existsSync) {
        return syspath.existsSync(path);
      }
      
    },
    
      _fileExistsWithCaseSync = function(filepath) {
        console.log("pid:",process.pid,'--',filepath, + new Date())
        var dir, filenames;
        if (!fs.existsSync(filepath)) {
        return false;
        }
        dir = syspath.dirname(filepath);
        if (dir === '/' || dir === '.' || (/^\w:\\$/.test(dir))) {
        return true;
        }
        filenames = fs.readdirSync(dir);
        if (filenames.indexOf(syspath.basename(filepath)) === -1) {
        return false;
        }
        return _fileExistsWithCaseSync(dir);
    };
    
    说实话没看懂到底要干啥。

### 4.5 原因分析

说实话没看懂到底要干啥，github上抠代码，发现当时的提交日志是“开发时require区分大小写”。

看完又是懵逼了，代码里面没有看到大小写，整个工程中也就这一个文件用到了这个方法。

技术原因倒也简单，同步递归的效率太低，目录层级成了递归深度，同步递归的时候，这里用了多进程，CPU会都占满掉，然后会不断激活失活，其他进程时间片很少，机子也会出现卡死的状态。


​    
​    
​    
​    