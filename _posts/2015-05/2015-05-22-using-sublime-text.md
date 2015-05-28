---
layout: post
title: "Sublime Text 使用和配置"
categories: [开发环境]
tags: [Sublime Text 2, 快捷键, 插件推荐]

---


## 快捷键        

### 查找
+ 文件名过滤   Ctrl + P  可以@和：符号与Ctrl+P和Ctrl+G配合使用
+ 符号过滤     Ctrl + R
+ 跳到指定行    Ctrl + G
+ 符号查找	   Ctrl + F
+ 文件中查找	   Ctrl + Shift + F
+ 代码范围查找	Ctrl + Shift + Space, Ctrl + Shift + A, Ctrl + Shift + A,多次按可扩大范围
+ 括号范围      Ctrl + M, Ctrl + Shift + M,
+ 单词选中  Ctrl + D，多次按可以扩大选中范围
+ 行选中	Ctrl + L

### 编辑
+ 列编辑   Shift + 鼠标右键，鼠标中键 ，Ctrl+Alt+上下方向键, Ctrl + Shift + L
+ 代码折叠  Ctrl+Shift+[,Ctrl+Shift+]
+ 删除单词  Ctrl + Delete，Ctrl+ Backspace
+ 删除当前行  Ctrl + Shift + K
+ 复制当前行  Ctrl + Shift + D
+ 移动行     Ctrl + Shift + 上下方向键
+ 注释行		Ctrl + /
+ 多行合并 		Ctrl + J
+ 缩进			Ctrl + [, Ctrl + ],Tab, Shift + Tab
+ 格式化粘贴	Ctrl + Shift + V
+ 替换			Ctrl + H， Ctrl + Shift + H

## 插件

### 安装方法
参考[Package Control](https://packagecontrol.io/installation#st2), 基本方式是拷贝一段安装的Python 代码,在Sublime Text 的控制台（View->Console），回车执行。

装好包管理器之后，就有就可以在(Ctrl + Shift + P)中调出相应命令，比如输入 Install Package， 就可以安装插件包了（第一次稍慢，看状态栏的状态）。

### 使用方法
有些插件是自动加载的，比如代码提示；有些是直接在需要使用的时候调用相关的命令的，比如JSON格式化，在使用的时候，Ctrl + Shift + P 调出控制台，然后输入相应的命令，这个比较保险，可以避免快捷键冲突；当然也可以直接使用快捷键 Ctrl + Alt + J,如果没有快捷键冲突的话。

### 插件列表
+ IMESupport
在Sublime中更好地支持中文输入法；在Sublime中输入中文的时候，会出现输入法不跟随光标的情况，出现要么输入法在当前屏幕的左上角，要么在另外一个屏幕上，这个插件很好的解决了这个问题。
+ sublime-enhanced
sublime增强工具集合，把sublime向ide打造的好帮手

#### 格式化插件
+ CoolFormat
支持多种语言的代码格式化插件，相关的有一个叫CodeFormatter的支持的语言较少，关键是还基本不起作用，这个就可以不用尝试了。
+ Pretty JSON
JSON格式化插件
+ HTML-CSS-JS Prettify
看名字就可以知道了，做这三种代码的格式化的插件，直接在Package Setting中使用，快捷键Ctrl + Shift + H
+ JsFormat
JS格式化插件
+ 其他
在安装列表里面可以搜具体的格式化插件，比如CSS Formatter等，功能都差不多，有一个全功能的基本就OK了

#### 代码自动补全
Sublime默认是从当前文件中提取符号作为代码补全提示。
+ SublimeCodeIntel
这个是从一个IDE里面移植到Sublime中的插件，功能较全面，支持的语言较多。
+ AllAutoComplete
这个是从所有打开的文件中提取符号作为提示信息的，可以作为一个选项，不建议用多个，因为这个是自动启用并执行的，你懂的。
+ Autocompletion​Fuzzy
这个一直在更新，属于[sublime-enhanced][12] 的一部分,比All autocomplete实用
+ Better Completion
对默认sublime text自动补全的修正和增强
+ 其他
你可以安装其他开发库的代码自动补全，不如说jQuery等。

#### 代码自动生成
+ Emmet
Zen Coding, Emmet的支持，高效快速的生成静态HTML代码
+ Sublime Prefixr
自动生成兼容各种浏览器的CSS3样式私有属性

+ Sublime Linter
自动代码语法检查
+ Git、Gitgutter 、Modific
Git命令相关工具，支持Sublime中执行Git相关命令
+ BracketHighlighter
匹配的括号，标签，引号等的高亮显示
+ SublimeREPL
提供多种语言的交互式命令行，比如Node，Python等。
+ DocBlockr
在函数附近写注释的时候，自动生成格式良好的返回值，参数等信息。
+ AutoFileName
文件路径中文件名和自动补全，比如在引用静态文件的时候。
+ Sublime V8
在Sublime中显示JavaScript控制台，跟浏览器控制台类似，有JSlint功能
+ Filter Lines
进行行过滤，类似于Linux中的Grep，方便对数据进行处理
+ FileDiff
文件比较结果，跟Git的diff类似

### 问题
上面介绍的插件已经很多了，如果因为装了各种各样的插件，导致Sublime不那么灵活了，还会报类似下面的错误：
A plugin (SublimeCodeIntel) may be making Sublime Text unresponsive by taking too long (0.020000s) in its on_modified callback.
This message can be disabled via the detect_slow_plugins setting
解决办法为：
Preferences > Settings - User
Add the following: "detect_slow_plugins": false
添加不检测的配置，不过这个只是隐藏了提示，确实是让Sublime没有那么快了，所以安装插件时需要节制。不过不管怎样，打造一个轻量级的IDE，占用内存一两百兆，总比占用八九百兆要好得多。


### 参考文档
0. [Sublime Text 2 快捷键][5]
1. [实用的sublime插件集合 – sublime推荐必备插件][8]
0. [Package Control Package 库][1]
1. [Sublime Text最佳插件列表][2]
2. [一些必不可上的Sublime插件][3]
3. [Sublime Text2 的使用方法和插件推荐][4]
4. [前端开发者需要的10个Sublime插件][6]
5. [Sublime常用插件集锦][7]
6. [20 个强大的 Sublime Text 插件][8]
7. [15个针对开发人员的最好Sublime Text插件][9]
8. [编码神器 Sublime Text 包管理工具及扩展大全][10]

[1]: https://packagecontrol.io/ "Package Control Package 库"
[2]: http://blog.jobbole.com/79326/ "Sublime Text最佳插件列表"
[3]: http://www.qianduan.net/essential-to-sublime-the-text-2-plugins/ "一些必不可上的Sublime插件"
[4]: http://www.aimks.com/sublime-text2-method-plug.html "Sublime Text2 的使用方法和插件推荐"
[5]: http://blog.csdn.net/fovwin/article/details/9102731 "Sublime Text 2 快捷键"
[6]: http://developer.51cto.com/art/201503/467605_all.htm "前端开发者需要的10个Sublime插件"
[7]: http://www.zuojj.com/archives/566.html "Sublime常用插件集锦"
[8]: http://www.xuanfengge.com/practical-collection-of-sublime-plug-in.html "实用的sublime插件集合 – sublime推荐必备插件"
[9]: http://www.open-open.com/news/view/26d731 "20 个强大的 Sublime Text 插件"
[10]: http://www.open-open.com/news/view/f130bd "15个针对开发人员的最好Sublime Text插件"
[11]: http://www.open-open.com/news/view/181c7a5 "编码神器 Sublime Text 包管理工具及扩展大全"
[12]: https://github.com/shagabutdinov/sublime-enhanced "Sublime enhanced"