---
layout: post
title: "jekyll serve 无法本地预览 中文路径问题 解决"
categories: [开发技术]
tags: [jekyll,中文路径,中文路径,ruby,webrick,本地预览]
---

## 问题描述
使用bootstrap jekyll 搭建github blog，想使用`jekyll serve`实现本地预览
然而预览过程中在浏览器中输入`http://localhost:4000/计算机基础/`,得到的结果是`Sorry this page does not exist =( `,然而输入 `http://localhost:4000/编程语言/`,却能得到正确的file index页面。

## 解决过程
在ruby 安装目录下 webrick\httpservlet\filehandler.rb 这个文件中:

调整整个URL Path部分的编码：

      def prevent_directory_traversal(req, res)
        # Preventing directory traversal on Windows platforms;
        # Backslashes (0x5c) in path_info are not interpreted as special
        # character in URI notation. So the value of path_info should be
        # normalize before accessing to the filesystem.
    
        # dirty hack for filesystem encoding; in nature, File.expand_path
        # should not be used for path normalization.  [Bug #3345]
        path = req.path_info.dup.force_encoding(Encoding.find("filesystem"))
        puts "path:#{path}---#{path.encoding}"
        #change the path encoding interpreter
        path.force_encoding('UTF-8')
    
        puts "path:#{path}---#{path.encoding}"
        if trailing_pathsep?(req.path_info)


有一个查找目录的地方，函数名set_filename中对路径进行遍历拼接，改成下面的样子。

      def set_filename(req, res)
        puts "system encoding: #{Encoding.find("filesystem")}"
        res.filename = @root.dup
        puts "res.filename encoding:#{res.filename.encoding}"
        puts "req.path_info.encoding:#{req.path_info.encoding}"
        path_info = req.path_info.scan(%r|/[^/]*|)
        path_info.unshift("")  # dummy for checking @root dir
        puts "#{path_info}----#{res.filename}"
        while base = path_info.first
          break if base == "/"     
          # change the encoding interpreter for each path element
          base.force_encoding("UTF-8")   


改上这两行就ok了，先将整个Path的编码解释为UTF-8，再将每个分隔的路径元素解释为UTF-8。





这里面有两个问题还没解决，为什么`编程语言`的URL是OK的，需要解释清楚。

同时`开发环境` 和`应用技术`等还是有些问题，也需要解释清楚。



上面这两个问题，在新版的Ruby和jekyll 中是好使的，可以确定是ruby本身的移植性问题



另外，还有一个因此引出的问题，irb中输入中文，会有问题，比如输入`中文`变成`治`。

待续...

## 参考资料
1. [Jekyll编译中文文件名的网页的本地预览问题](http://www.oschina.net/question/1396651_132154)
2. [在Windows的CMD中如何设置支持UTF8编码?](http://blog.useasp.net/archive/2012/04/24/how_to_use_UTF8_encoding_in_Windows_CMD.aspx)
3. [文件编码转换与中文路径的那些事儿](https://ruby-china.org/topics/101)
4. [Ruby中文目录的问题](http://www.oschina.net/question/8695_177432)
5. [关于ruby的文件/目录名编码错误问题](http://www.canaansky.com/blog/78/)
6. [UTF-8和GBK等中文字符编码格式介绍及相互转换](http://www.cnblogs.com/lizhenghn/p/3690406.html?utm_source=tuicool)
7. [Encoding::UndefinedConversionError: "\xE5" from ASCII-8BIT to UTF-8](http://runupwind.iteye.com/blog/1129605)
8. [Ruby 对多语言的支持](http://about.ac/2012/06/understanding-m17n.html)
9. [ruby编码说明](http://blog.csdn.net/five3/article/details/8966280)
10. [Ruby 1.9+ 的字符编码](https://ruby-china.org/topics/16856)
11. [Ruby：字符集和编码学习总结](http://www.cnblogs.com/happyframework/p/3275367.html)