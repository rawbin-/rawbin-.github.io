---
layout: post
title: "fekit mock 内存泄露问题排查和解决"
categories: [开发技术,应用技术]
tags: [fekit,mock,内存泄露]
---

### 现象

Win10 NodeJS 0.12.12 使用fekit 最新版 0.2.141 本地开发

    fekit server -m package_b2c_admin/tests/mock.conf


fekit进程内存很快会涨到1G以上，基本上就是卡死状态，浏览器刷不出来静态资源

可以通过如上命令起本地server，多次刷新带较多静态资源的页面，同时监控进程占用的内存

### 解决办法

1. 切换NodeJS版本
   + 实测Node 0.10.42, Node 0.10.43, Node 5.9.0 无此问题
   + Node 4.3.0, 4,4.0, 0.12.12 有此问题
   + 推测，相关的大版本应该都有问题
2.  简单调整fekit mock.js代码
   + 修改FEKIT_HOME/lib/middleware/mock.js 将sandbox 的定义提到闭包外面来
3. 进一步优化fekit mock.js代码
   + 修改FEKIT_HOME/lib/middleware/mock.js 将mock_file()的结果缓存起来，如果被更新了才进行重新计算。

### 原因分析

原因这里就不分析了，[记一次 Node.js 应用内存暴涨分析](http://taobaofed.org/blog/2016/01/14/nodejs-memory-leak-analyze/)，这个小伙伴已经分析的很不错了。

### 排查过程

#### 不带mock信息
无此问题，内存在100M上下波动，正常

#### 与0.2.85 比较，发现代码几乎无差别，改动的代码对此几乎无影响

#### 在FEKIT_HOME/lib/command/server.js中进行中间件切除
发现去掉mock后 .use(middleware.mock(options)) 问题基本解决

#### 上面两条有点相互矛盾

#### 通过node-inspector 调试 

    node-debug "C:\Users\liao.zhang\AppData\Roaming\nvm\v4.3.0\node_modules\fekit\bin\fekit" server -m package_b2c_admin/tests/mock.conf


    node --debug-brk "C:\Users\liao.zhang\AppData\Roaming\nvm\v4.3.0\node_modules\fekit\bin\fekit" server -m package_b2c_admin/tests/mock.conf


查看profile数据，发现fekit占用的内容并不多


#### 不管上面的矛盾

看看这里面闭包比较多的地方，分片注释，可以定位到

      try {
        vm.runInNewContext(exjson(mock_file()), sandbox);
      } catch (err) {
        sandbox.module.exports = {};
        utils.logger.error("mock 配置文件出错 " + (err.toString()));
      }


JavaScript虚拟机内存没有被释放，调用的次数又太多（每一个请求都调用），所以内存爆了

#### 再回来
可以说已经不是fekit的问题了，85的代码和141的代码一样的时候，也会有这样的问题

#### NodeJS文档
+ [0.10.x](https://nodejs.org/docs/latest-v0.10.x/api/)
+ [0.12.x](https://nodejs.org/docs/latest-v0.12.x/api/)

大致测试，0.10.xx 应该没有这个问题；0.11.x,0.12.xx, 4.4.0 有这个问题，法不责众，我们回过头来看看fekit。

#### 换个姿势
上面说可以说是不是fekit的问题，因为NodeJS也有问题，再来看看fekit到底有没有问题

    module.exports = function(options) {
        var mock_file;
        if (!(options.mock && utils.path.exists(options.mock))) {
        return noop;
        }
        utils.logger.log("成功加载 mock 配置 " + options.mock);
        mock_file = utils.file.io.readbymtime(options.mock);
        return function(req, res, next) {
            var action, key, pattern, pieces, result, rule, rules, sandbox, url, _i, _len, _ref;
            sandbox = {
                module: {
                exports: {}
                }
            };
            try {
                vm.runInNewContext(exjson(mock_file()), sandbox);
            } catch (err) {
                sandbox.module.exports = {};
                utils.logger.error("mock 配置文件出错 " + (err.toString()));
            }
    
        //.......
    }    

上面的代码是fekit mock 中间件的主要代码，这片代码会在每个请求到达的时候被执行；可以看到在闭包里面申明了，一个沙盒变量，每次请求都会申请这么一片内存。

对于mock，在每次请求时内容不一样的情况是不可能事件，这里可以有两种解决办法：
1、将执行前的文件内容进行缓存，判断如果一样，就不执行了，执行了也没啥意义
2、直接申请同一片内存，后面的复写前面的，刷了就刷了

先用第二条改改试试效果。

    module.exports = function(options) {
        var mock_file,sandbox;
        if (!(options.mock && utils.path.exists(options.mock))) {
        return noop;
        }
        utils.logger.log("成功加载 mock 配置 " + options.mock);
        mock_file = utils.file.io.readbymtime(options.mock);
        sandbox = {
            module: {
            exports: {}
            }
        };
        return function(req, res, next) {
            var action, key, pattern, pieces, result, rule, rules, url, _i, _len, _ref;
    
            try {
                vm.runInNewContext(exjson(mock_file()), sandbox);
            } catch (err) {
                sandbox.module.exports = {};
                utils.logger.error("mock 配置文件出错 " + (err.toString()));
            }
    
        //.......
    } 

再来看看配合缓存结果，以空间换时间的方法：

    module.exports = function(options) {
        var mock_file,sandbox,lastMockConfig = "";
        if (!(options.mock && utils.path.exists(options.mock))) {
        return noop;
        }
        sandbox = {
            module: {
            exports: {}
            }
        };
        utils.logger.log("成功加载 mock 配置 " + options.mock);
        mock_file = utils.file.io.readbymtime(options.mock);
        return function(req, res, next) {
        var action, key, pattern, pieces, result, rule, rules, url, _i, _len, _ref;
        var thisMockConfig = mock_file();
        //读文件都有缓存，计算自然也可以有，重复计算也没有意义
        if(thisMockConfig !== lastMockConfig){
            lastMockConfig = thisMockConfig;
            try {
                vm.runInNewContext(exjson(thisMockConfig), sandbox);
            } catch (err) {
                sandbox.module.exports = {};
                utils.logger.error("mock 配置文件出错 " + (err.toString()));
            }
        }
        // ......
    }

于是这里的内存泄露问题得到解决。    



### 参考文档

1. [记一次 Node.js 应用内存暴涨分析](http://taobaofed.org/blog/2016/01/14/nodejs-memory-leak-analyze/)
2. [记一次 Node.js 应用内存暴涨分析](https://yq.aliyun.com/articles/4050?spm=5176.100239.yqblog1.6.9w1hrV)
3. [使用Node.js的VM执行脚本之后，沙箱所占的内存没有被清掉](http://nodejs.netease.com/topic/5201ba85d6bc618c60111b62)
4. [Node内存泄漏专题](http://cnodejs.org/topic/4fa94df3b92b05485007fd87)
5. [NodeJS内存泄露的几种情况及解决方案](http://www.nodejs.net/a/20141205/164524.html)
6. [Memory leak patterns in JavaScript](http://www.ibm.com/developerworks/web/library/wa-memleak/)
7. [浅谈V8引擎中的垃圾回收机制](http://www.html-js.com/blog/2514)
8. [深入理解Node.js中的垃圾回收和内存泄漏的捕获](http://www.csdn.net/article/1970-01-01/2826316)
9. [Node.js如何应对内存泄露？](http://www.zhihu.com/question/30289050)
10. [node-inspector](https://github.com/node-inspector/node-inspector)
11. [查看 Node.js 中的内存泄露](http://blog.jobbole.com/34262/)
12. [NodeJS调试GC及内存暴涨的分析](http://www.360doc.com/content/14/0903/22/11644963_406872841.shtml)
13. [使用Chrome+node-inspector查找NodeJS内存泄漏](http://www.cnblogs.com/ldlchina/p/4762036.html)
14. [The node.js Profiling Guide that Hasn’t Existed - Profiling node.js Applications](http://www.willvillanueva.com/the-node-js-profiling-guide-that-hasnt-existed-profiling-node-js-applications-part-1/)
15. [The node.js Profiling Guide that Hasn’t Existed - Finding a potential memory leak using memwatch](http://www.willvillanueva.com/the-node-js-profiling-guide-that-hasnt-existed-finding-a-potential-memory-leak-using-memwatch-part-2/)
16. [The node.js Profiling Guide that Hasn’t Existed - Finding The Cause of a Memory Leak Using Heap Snapshots](http://www.willvillanueva.com/the-node-js-profiling-guide-that-hasnt-existed-finding-the-cause-of-a-memory-leak-using-heap-snapshots-part-3/)