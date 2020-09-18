---
layout: post
title: "纯CSS实现markdown标题自动编号"
categories: [前端开发,Web开发,JavaScript,代码规范]
tags: [css,markdown,W3C,代码规范]
---



# 问题的由来
第一次关注这个标题编号的问题应该回溯到本科毕业论文的时候了，当时还单独涉猎过这个主题，`Word` 有个很好的特性`级联标题`，一次设置好之后，后续只要设置标题样式就能按照设置的标题编号方式自动编号，我们要做的只是将对应的标题设置成对应基本的标题样式就好了，这个方法让我爱不释手，多年来一直沿用。完全解决了中途插入一章，一节等等导致的章节编号都需要人肉调整的问题，当然还有图片的编号命名什么的，都是类似的。
直到后面开始用`markdown` 由于各个编辑器的切换，一直没有一个好用的替代方案，所以几年前我写了一个小工具用命令行来做这事`rawbin-/markdown-clear`，这个工具解决了在`github`写博客的问题，同时在解决博客的问题的基础上解决了在各个平台发文的问题，因为编号是用脚本写上去的，所以用`markdown here`在各个平台发文也就顺理成章的转成html就行了，也解决了这个阶段的问题。
前两天把拖欠几个月的`全面认知`的总结写了，突然不想用这个脚本来编号了，产生一个想法：能不能不人肉编号，自动编上？然后就有了下面的内容。

# 先搞起来解决问题

>>> 以下操作案例都是在macOS中产出，其他平台可能有些许差别，换汤不换药。

## 在`typora`中写`markdown`自动编号

- 打开`typora`【偏好设置】
- 找到【外观】=>【主题】=>【打开主题文件夹】
- 将如下代码加入到打开目录的`base.user.css` 中
```css
#writer {
    counter-reset: h1
}

h1 {
    counter-reset: h2
}

h2 {
    counter-reset: h3
}

h3 {
    counter-reset: h4
}

h4 {
    counter-reset: h5
}

h5 {
    counter-reset: h6
}

#writer h1:before {
    counter-increment: h1;
    content: counter(h1) ". "
}

#writer h2:before {
    counter-increment: h2;
    content: counter(h1) "." counter(h2) ". "
}

#writer h3:before {
    counter-increment: h3;
    content: counter(h1) "." counter(h2) "." counter(h3) ". "
}

#writer h4:before {
    counter-increment: h4;
    content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) ". "
}

#writer h5:before {
    counter-increment: h5;
    content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) "." counter(h5) ". "
}

#writer h6:before{
    counter-increment: h6;
    content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) "." counter(h5) "." counter(h6) ". "
}
```

### 讲道理

- 打开`typora`【偏好设置】
- 找到【通用】=>【高级 】=>【开启调试模式】=>勾选
- 然后在非源码模式下=>【右键】=>【检查元素】，就可以看到为什么是`#write`了
- 这个后面还有用



## 在`github pages` 写`markdown`博客自动编号

我用的是`jekyllbootstrap.com`的模板，比较简单

- 打开任意一篇`rawbin-.github.io`中的文章，然后【右键】=>【检查】

- 可以拿到两个内容

  - 容器类为 `.content` ，严格点为`#wrap .content`
  - 样式文件在`assets/themes/bootstrap3`，可以修改其下的`css/style.css`

- 将如下内容改到源代码的`assets/themes/bootstrap3/css/style.css`中

```css
.content {
    counter-reset: h1
}

h1 {
    counter-reset: h2
}

h2 {
    counter-reset: h3
}

h3 {
    counter-reset: h4
}

h4 {
    counter-reset: h5
}

h5 {
    counter-reset: h6
}

.content h1:before {
    counter-increment: h1;
    content: counter(h1) ". "
}

.content h2:before {
    counter-increment: h2;
    content: counter(h1) "." counter(h2) ". "
}

.content h3:before {
    counter-increment: h3;
    content: counter(h1) "." counter(h2) "." counter(h3) ". "
}

.content h4:before {
    counter-increment: h4;
    content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) ". "
}

.content h5:before {
    counter-increment: h5;
    content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) "." counter(h5) ". "
}

.content h6:before{
    counter-increment: h6;
    content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) "." counter(h5) "." counter(h6) ". "
}
```



## 在其他网页编辑中自动编码

比如各个博客平台，各个自媒体平台等，像我们常用的写文档的语雀都可以的。

这里面涉及到一款浏览器插件`markdown here`，可以在页面富文本编辑器中将`markdown` 自动转换为网页，这也是我前面说到的这几年在各个平台发文的套路，写好编号好标题`markdown`往编辑器里面一贴，然后一点 ，搞定。

### 简单尝试

- `markdown here` 有一个配置页面，可以配置和调整css，并能预览效果
- 简单看了下是用`js`把类转成了`style`属性，并且不支持伪类

### 修改源码

- 到`adam-p/markdown-here` 看到，已经两年没动代码了

- 不管三七二十三先 `fork`一把到`rawbin-/markdown-here`，然后把代码拉下来

- 先把css文件建起来`src/common/auto-number-title`，找容器类可以在`markdown here`的选项页面找到`.markdown-here-wrapper`

```css
.markdown-here-wrapper {
    counter-reset: h1
}

.markdown-here-wrapper h1 {
    counter-reset: h2
}

.markdown-here-wrapper h2 {
    counter-reset: h3
}

.markdown-here-wrapper h3 {
    counter-reset: h4
}

.markdown-here-wrapper h4 {
    counter-reset: h5
}

.markdown-here-wrapper h5 {
    counter-reset: h6
}

.markdown-here-wrapper h1:before {
    counter-increment: h1;
    content: counter(h1) ". "
}

.markdown-here-wrapper h2:before {
    counter-increment: h2;
    content: counter(h1) "." counter(h2) ". "
}

.markdown-here-wrapper h3:before {
    counter-increment: h3;
    content: counter(h1) "." counter(h2) "." counter(h3) ". "
}

.markdown-here-wrapper h4:before {
    counter-increment: h4;
    content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) ". "
}

.markdown-here-wrapper h5:before {
    counter-increment: h5;
    content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) "." counter(h5) ". "
}

.markdown-here-wrapper h6:before{
    counter-increment: h6;
    content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) "." counter(h5) "." counter(h6) ". "
}

```
- 然后修改一下注入配置，允许加载这个样式文件，并引入这个样式问题
- 剩下的有错改错就好了

### 最终产出和应用

- 克隆`rawbin-/markdown-here`

- 打开Chrome 设置三个点=>【更多工具】=>【扩展程序】

- 打开【开发者模式】

- 选择【加载已解压的扩展程序】=>选择克隆代码下的`src`目录即可安装并加载插件

- 将插件固定在插件栏方便使用

- `auto-number-title.scss`内容如下

```scss
.markdown-here-wrapper {
    counter-reset: h1;
    h1 {
        counter-reset: h2;
        &:before {
            counter-increment: h1;
            content: counter(h1) ". ";
        }
    }
    h2 {
        counter-reset: h3;
        &:before {
            counter-increment: h2;
            content: counter(h1) "." counter(h2) ". "
        }
    }
    h3 {
        counter-reset: h4;
        &:before {
            counter-increment: h3;
            content: counter(h1) "." counter(h2) "." counter(h3) ". "
        }
    }
    h4 {
        counter-reset: h5;
        &:before {
            counter-increment: h4;
            content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) ". "
        }
    }
    h5 {
        counter-reset: h6;
        &:before {
            counter-increment: h5;
            content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) "." counter(h5) ". "
        }
    }
    h6:before{
        counter-increment: h6;
        content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) "." counter(h5) "." counter(h6) ". "
    }
}

```



# 再来简单讲一下道理

## CSS 自动编号

- 不是一个新特性，或者说是一个老特性了，出现在`CSS 2.1`中，搜索`site:w3.org css automatic numbering` 可以找到，当然截止今天后来的版本(`CSS 3`, `CSS 2.2`)都有这个特性，从`caniuse`上可以看到，`IE8`及以上兼容，很棒吧
- 简单说明
  - `counter-reset` 重置
  - `counter-increment` ++
  - `counter()` 取值
  - 配合`before`和`after`来做
  - 还有更多的玩法，参见 `CSS The Defiiniitiive Guide 4th` ，这里有翻译`gdut-yy/CSS-The-Definitive-Guide-4th-zh`

## Chrome插件或扩展开发

- 这个 我也没实际搞过，原来看了看书
- 可参考的资料
  - 官方文档
  - `sxei/chrome-plugin-demo` 或者搜索`Chrome插件 全攻略`
  - 《Chrome扩展及应用开发》，这个就是我原来看的那本老书



# 还是有些问题没解决

- 上面的操作方式必须要`h1`到`h6`依次排开，不然会很好看
- 如果标题本身就编号了的，就有点糟糕了
- 这俩问题在我`github`的博客里面都能看到，解决办法可以是运行下``

## 顺便探索下`CSS`其他可变的内容

### CSS变量或者说自定义属性

- 这个IE不兼容，其他浏览器高版本兼容

```
:root{
	--var-test:xxx
}
.body{
	--var-test:ooo;
	prop-test:var(--var-test)
}
```

### attr()

- 这个`caniuse`也有些说不清楚，主体兼容也是从`IE8`开始的，需要自己总结
- 强大的地方是可以读取属性值，赋给另外的属性，也就是可以来个属性联动

## 看起来纯CSS的解决方案就到此告一段落了

- 如果能有脚本参与，就自由了
- `attr()` 配合伪类来做展示，是一个JS和CSS通信的一个比较好的方式