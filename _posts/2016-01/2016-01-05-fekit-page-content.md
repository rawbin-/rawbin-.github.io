---

layout: post
title: "fekit 带中文文件的目录 页面内容渲染不全"
categories: [前端开发,Web开发]
tags: [node-inspector,fekit,javascript]

---

## 问题
+ 随便一个目录，用fekit 起一个server
+ 浏览器打开目录
+ 在这个目录中添加一个中文文件
+ 刷新刚打开的目录页面
+ 某些目录或者文件未被渲染出来

## 排查 
fekit 使用了connect框架来做静态资源的server 

connect目录在$FEKIT_HOME\node-modules\connect\中

查看connect框架中处理静态目录的处理：$FEKIT_HOME\node-modules\connect\lib\middleware\directory.js，

其中静态处理的代码如下:

    exports.html = function(req, res, files, next, dir, showUp, icons){
    fs.readFile(__dirname + '/../public/directory.html', 'utf8', function(err, str){
        if (err) return next(err);
        fs.readFile(__dirname + '/../public/style.css', 'utf8', function(err, style){
        if (err) return next(err);
        if (showUp) files.unshift('..');
        str = str
            .replace('{style}', style)
            .replace('{files}', html(files, dir, icons))
            .replace('{directory}', dir)
            .replace('{linked-path}', htmlPath(dir));
        res.setHeader('Content-Type', 'text/html');
        //res.setHeader('Content-Length', str.length);
        res.setHeader('Content-Length', new Buffer(str,'utf-8').length);
        res.end(str);
        });
    });
    };

基本流程是读取一个静态模板，然后填充相应的内容。
如果文件名存在中文，在计算相应长度的时候会存在问题（算少了），所以会出现页面看不到某些靠后的内容一样。

原因是编码不一样，计算结果不一致，[参考](http://www.ruanyifeng.com/blog/2014/12/unicode.html)