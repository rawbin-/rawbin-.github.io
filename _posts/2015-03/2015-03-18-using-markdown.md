---
layout: post
title:  "Markdown 语法简要说明"
categories: [编程语言]
tags: [Markdown]
---

## Markdown简介
Markdown是一种纯文本的标记方式，的目标是实现易读易写的纯文本编辑，使作者能更专注于文档写作，弱化文档排版。

Markdown兼容HTML，适合于Web发布，能解决PDF，Word等文档需要特定的阅读工具才能阅读的问题，只需要手边的浏览器和简单的文本编辑器就可以进行阅读和编辑。

每个关键符号之后需要有空格或者空行才能进行正确的解析，比如> , + 等；当然这些内容基本都是可以嵌套的，嵌套的时候增加相应的缩进即可。

### 标题
标题通过在行首输入#来表示，#的个数（1-6）表示1级至6级标题，
字号从1级到6级依次缩小（分别对应HTML的H1至H6标签），如

>      #一级标题   
>      ######六级标题  

显示为

> #一级标题
> ######六级标题

### 列表
无序列表使用*，+，-来表示（别忘了其后跟空格），对应HTML的li，如

>      + 项目一  
>      + 项目二  
>      + 项目三  

显示为  

>+ 项目一
>+ 项目二
>+ 项目三

有序列表只需要把上面的符号换成数字即可

###粗体和斜体
Markdown 用*和_表示,左右两个星号或者两个下划线表示加粗，左右一个星号或者一个下划线表示斜体,如：
>     **加粗** *斜体*  __加粗__ _斜体_

显示为

>**加粗** *斜体*  __加粗__ _斜体_

###区块引用
Markdown用`>`来表示信息的引用,如：

>       > 这是引用块
>       > 这也是引用块

显示为

>> 这是引用块
>> 
>> 这也是引用块

###代码块
使用反引号 `(ESC下边的符号)，来标记代码块，代码块中的&, <, >等符号都会被转换成HTML的信息&amp; &lt; &gt;

>     这是代码段信息, &, <, >等特殊字符

如果要输出格式化好的代码，则需要每行缩进4个空格，或者一个Tab
如：

>     def func_demo:
>          print 'Hello world'

显示为

>     def func_demo:
>         print 'Hello world'
		


###超链接
Markdown行内方式为**`[链接文字](链接URL，"链接标题")`**,链接标题在鼠标放上去时显示，如

>      [显示的文字](http://github.com "URL标题")

显示为

> [显示的文字](http://github.com "URL标题")

Markdown作为参考链接的书写方式为 **`[链接文字][参考链接ID]`**, 可以在任何地方定义参考的链接;
链接的定义方式为： **`[lind_id]: link_url "link_title"`**
如：

>     [github主站][github]
>     [github]: http://github.com "github title“

显示为

> [github主站][github]
[github]: http://github.com "github title"

还可以隐式链接的方式来写**`[隐式链接显示文字][]`**，然后同样定义一个链接，如
**`[隐式链接显示的文字]: http://github.com`**
如:

>
>     [懒人链接][]
>     [懒人链接]: http://github.com

显示为

> [懒人链接][]
[懒人链接]: http://github.com


###图片
Markdown行内图片的定义方式为：**`![图片描述](图片路径, "图片标题")`**
如:

>     ![演示图片](/abspath/for/image, "图片标题")

显示为

> ![演示图片](/abspath/for/image, "图片标题")

当然也可以像超链接那样写成参考材料的方式,如

>     ![演示图片][demo_img]
>     [demo_img]: /abspath/for/image "图片标题"

显示为

> ![演示图片][demo_img]
[demo_img]: /abspath/for/image "图片标题"


###转义

上面我们看到比如说. * _ + - !等，如果需要原样显示的话，需要用反斜杠(\)进行相应的转义。

###分割线

三个以上的星号或者减号一起就能表示分割线，星号之间可以用空格，但行内不能有其他内容,如：

>      * * *   
>      - --   

显示为

> ** *   
> - --   

###相关的编辑器

大致可以参考：
[小众软件](http://www.appinn.com/markdown-tools/)，
[segmentdefault](http://segmentfault.com/a/1190000000506986)，
[open-oen](http://www.open-open.com/news/view/1be6464)等对相关工具的介绍。


###参考文档:

0. [Markdown快速入门][1]
0. [Markdown完整语法][2]
0. [Markdown语法说明][3]

[1]: http://wowubuntu.com/markdown/basic.html  "Markdown快速入门" 
[2]: http://wowubuntu.com/markdown/  "Markdown完整语法" 
[3]: http://www.markdown.cn/ "Markdown语法说明"

