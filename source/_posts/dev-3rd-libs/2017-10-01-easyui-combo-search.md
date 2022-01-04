---
layout: post
title: "EasyUI 思想、用法解析"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript,EasyUI,jQuery]
---





### 思路

- 是用jQuery插件方法，在jQuery上扩展功能，在$.fn（prototype）上增加对象方法，让选择器结果对象可使用新增方法
- 通过继承合并的方式，让高层组件（combotree）拥有基层组件(combo,tree)所有的属性、方法和事件的并集



### 过程（以$.fn.myPlugin为例）

- 首先是注册myPlugin插件到jQuery原型上，其实就是一个返回jQuery对象的函数
- 当调用的时候，会判断当前DOM对象上是否有myPlugin 的data属性，没有就初始化，并写入这个data属性，有了的话就直接调用
- 初始化过程
  - 设置默认值，`$.fn.myPlugin.defaults`，这个我们可以在定制组件初始化之前改，来改变默认行为
  - 初始化配置，`$.fn.myPlugin.parseOptions(this) `，扫描一遍现有的元素属性配置，这也是为什么HTML里面配置了EasyUI的类，就能直接渲染对象的原因
- 我们配置的DOM元素没有直接被使用，而是被隐藏起来了，新的功能组件是按照对象属性配置生成的
  - 获取文本组件对应DOM元素的方法`$(selector).textbox('textbox')`
  - 获取下拉树种的文本框``$(selector).combotree('textbox')`
  - 获取下拉树种的树``$(selector).combotree('tree')`



### 实战（给EasyUI combotree 增加模糊查询 ）

- 参考资料里面给了一个实现的方法，写的代码量稍大，可以更简单
- 实现模糊查询
  - 首先是给文本框绑定input事件
  - 然后是让树形结构的展示通过文本框的过滤，tree对象默认有这个过滤 filter默认值
  - 对于多值combotree，需要切换fous时，保存并清空原值，blur时合并值并展示

```javascript
function enableComboTreeSearch(selector){
	var $combotree = $(selector);
	var $textbox = $combotree.combotree('textbox');
	var $tree = $combotree.combotree('tree');
	$textbox.removeAttr('readonly');  //让可输入
    $textbox.removeAttr('disabled');
	$textbox.on('input propertychange',function(){
		$tree.tree('doFilter',this.value); //利用默认的filter进行过滤
	});
}
```







### 参考资料

- [EasyUI组件解析实现思路](https://www.cnblogs.com/a-ray-of-sunshine/p/4658706.html)
- [combotree和combobox模糊查询](https://blog.csdn.net/dyx_nt88/article/details/44057315)