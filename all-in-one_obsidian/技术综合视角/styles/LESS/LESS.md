# 变量
## 定义
- @var-name
- 重复定义以最后一个为准，跟CSS的层级和优先级类似
- 也可以使用属性名作为变量，如果当前作用域里面能找得到属性名的话

## 使用
- 在选择器名称 @{var-name}
- 属性名称 @{property-var}.  background-@{property-var}
- URLs url("@{var-name}/xx.png")
- @import 声明

## 双重变量
- @@var-name  以 @var-name 的值为变量名

# 选择器
## 父级选择器
- & 表示父级选择器名称，一直到top
- 可以多次连续出现
- 可以方便地用来给当前父选择器添加标识
- 也可以用来作为多个父级选择器的组合 比如
```
p, a, ul, li { border-top: 2px dotted #366; & + & { border-top: 0; } }

```

# 扩展
- extend 函数
- 可以扩展一个和多个（逗号分隔）
- @media中的扩展只会匹配当前所在的@media内的选择器
## 语法
```
.a:extend(.b) {}

// the above block does the same thing as the below block
.a {
  &:extend(.b);
}
```
```
.c:extend(.d all) {
  // extends all instances of ".d" e.g. ".x.d" or ".d.x"
}
.c:extend(.d) {
  // extends only instances where the selector will be output as just ".d"
}
```

# 混入
- .mixin 混入函数
- 为了避免无意地混入，需要在混入属性上明确加上+（逗号混入）或者+_（空格混入）
- 可以将混入赋值给变量(要带圆括号，有点先函数调用后的值，但还可以当函数调用)，然后通过变量调用来使用，为了区分到底是用名称还是用值
```
.mixin() {
  box-shadow+: inset 0 0 10px #555;
}
.myclass {
  .mixin();
  box-shadow+: 0 0 20px black;
}

```

```
.mixin() {
  transform+_: scale(2);
}
.myclass {
  .mixin();
  transform+_: rotate(15deg);
}
```

```
#theme.dark.navbar {
  .colors(light) {
    primary: purple;
  }
  .colors(dark) {
    primary: black;
    secondary: grey;
  }
}

.navbar {
  @colors: #theme.dark.navbar.colors(dark);
  background: @colors[primary];
  border: 1px solid @colors[secondary];
}
#library() {
  .colors() {
    background: green;
  }
}
.box {
  @alias: #library.colors();
  @alias();
}
.box {
  @alias: #library.colors;
  @{alias} {
    a: b;
  }
}

```

## 属性混入
- 可以使用class选择器和id选择器作为混入函数执行
```
.a, #b {
  color: red;
}
.mixin-class {
  .a();
}
.mixin-id {
  #b();
}
```
## 带括号的混入
- 使用带括号的混入，可以让此混入不出现在产物中

## 混入中的选择器
- 混入中的父选择器 & 是指调用混入函数的父选择器

## 名称空间
- #NAME{} #NAME(){} 来定义名称空间，可以作为分组使用
- 条件名称空间
```
#namespace when (@mode = huge) {
  .mixin() { /* */ }
}

#namespace {
  .mixin() when (@mode = huge) { /* */ }
}
```

## !important 混入
- 带 !important修饰符的混入，会给每个混入属性都加 !important 


## 带参数的混入
- 逗号分隔参数
- 分号分隔多个逗号分隔的参数值
- 冒号分隔默认值或者多个逗号分隔的默认值
- LESS 4.0 参数可以使用括号~() 跟 ~"quote"类似
- 混入可以多参数重载
- 参数是通过名称引用的，就可以跟位置无关
- @arguments 可以获取空格分隔的所有参数
- @rest 可以获取形参... 代表的所有参数
```
.border-radius(@radius: 5px) {
  -webkit-border-radius: @radius;
     -moz-border-radius: @radius;
          border-radius: @radius;
}
.mixin(@color: black; @margin: 10px; @padding: 20px) {
  color: @color;
  margin: @margin;
  padding: @padding;
}
.class1 {
  .mixin(@margin: 20px; @color: #33acfe);
}
.class2 {
  .mixin(#efca44; @padding: 40px);
}
```

## 模式匹配
- 可以定义不同参数让混入走不同的行为
```
.mixin(dark, @color) {
  color: darken(@color, 10%);
}
.mixin(light, @color) {
  color: lighten(@color, 10%);
}
.mixin(@_, @color) {
  display: block;
}
```
## 作为函数使用混入
- 可以用中括号取函数返回结果的属性，不给属性名的话就拿到最后一个值
- 多个同名的规则会合并，就像CSS一样，可以重写相关的值
- mixin和变量可以被释放到调用处，如果有同名属性则只用变量
```
.average(@x, @y) {
  @result: ((@x + @y) / 2);
}

div {
  // call a mixin and look up its "@result" value
  padding: .average(16px, 50px)[@result];
}

.mixin() {
  @width:  100%;
  @height: 200px;
}

.caller {
  .mixin();
  width:  @width;
  height: @height;
}

```

## 递归mixin
- 加了条件判断和模式匹配的时候
```
.loop(@counter) when (@counter > 0) {
  .loop((@counter - 1));    // next iteration
  width: (10px * @counter); // code for each iteration
}

div {
  .loop(5); // launch the loop
}
```

## 混入守卫
- 比较符号 `>`, `>=`, `=`, `=<`, `<`.
- and not，逗号表示or，多个条件可以不用括号
- 只有为true 的值才是true
```
.mixin(@a) when (lightness(@a) >= 50%) {
  background-color: black;
}
.mixin(@a) when (lightness(@a) < 50%) {
  background-color: white;
}
.mixin(@a) {
  color: @a;
}

.truth(@a) when (@a) { ... }
.truth(@a) when (@a = true) { ... }

@media: mobile;

.mixin(@a) when (@media = mobile) { ... }
.mixin(@a) when (@media = desktop) { ... }

.max(@a; @b) when (@a > @b) { width: @a }
.max(@a; @b) when (@a < @b) { width: @b }
.mixin(@a) when (@a > 10), (@a < -10) { ... }

```
## 类型检查
- iscolor
- isnumber
- isstring
- iskeyword
- isurl
- ispixel
- ispercentage
- isem
- isunit

# CSS守卫
- 可以使用立即执行混入的方式来写
```.my-optional-style() when (@my-option = true) {
  button {
    color: white;
  }
}
.my-optional-style();
```
- 可以直接给样式加条件
```
button when (@my-option = true) {
  color: white;
}
```
- 可以跟&结合when来写
```
& when (@my-option = true) {
  button {
    color: white;
  }
  a {
    color: blue;
  }
}
```
- 可以直接用if函数来写
```
@dr: if(@my-option = true, {
  button {
    color: white;
  }
  a {
    color: blue;
  }
});
@dr();
```

# 抽离的规则集
- 需要强制使用圆括号调用
```
// declare detached ruleset
@detached-ruleset: { background: red; }; // semi-colon is optional in 3.5.0+

// use detached ruleset
.top {
    @detached-ruleset(); 
}

.desktop-and-old-ie(@rules) {
  @media screen and (min-width: 1200px) { @rules(); }
  html.lt-ie9 &                         { @rules(); }
}

header {
  background-color: blue;

  .desktop-and-old-ie({
    background-color: red;
  });
}
```

- 可以把规则集直接赋值给变量，这个例子里面会把media合并
```
@my-ruleset: {
    .my-selector {
      @media tv {
        background-color: black;
      }
    }
  };
@media (orientation:portrait) {
    @my-ruleset();
}
```
- 抽离的规则集不返回变量
```

```
- 作用域
```
@detached-ruleset: {
  caller-variable: @caller-variable; // variable is undefined here
  .caller-mixin(); // mixin is undefined here
};

selector {
  // use detached ruleset
  @detached-ruleset(); 

  // define variable and mixin needed inside the detached ruleset
  @caller-variable: value;
  .caller-mixin() {
    variable: declaration;
  }
}
```

- 可见性
```
@variable: global;
@detached-ruleset: {
  // will use global variable, because it is accessible
  // from detached-ruleset definition
  variable: @variable; 
};

selector {
  @detached-ruleset();
  @variable: value; // variable defined in caller - will be ignored
}
```

# @import 规则
- LESS中@import位置没有关系，标准CSS要放在开头
- 如果后缀是.css 的就当做css文件
- 如果后缀不是css 也不是less 或者没有后缀的，都当做是less文件
```
@import "foo";      // foo.less is imported
@import "foo.less"; // foo.less is imported
@import "foo.php";  // foo.php imported as a Less file
@import "foo.css";  // statement left in place, as-is
```
- @import (keyword) "filename" 多个关键字用逗号分隔
	- reference 用less文件不输出
	- inline 包含文件但不处理
	- less 当做less文件处理不管后缀是啥
	- css 当做css文件不管后缀是啥
	- once 只包含文件一次（默认行为）
	- multiple 包含文件多次
	- optional 找不到文件的时候也继续编译


# @plugin 规则
- 跟@import类似
- 插件是js文件，通过install:function(){} 注册，并在functions上扩展
```
registerPlugin({
    install: function(less, pluginManager, functions) {
        functions.add('pi', function() {
            return Math.PI;
        });
    }
})
module.exports = {
    install: function(less, pluginManager, functions) {
        functions.add('pi', function() {
            return Math.PI;
        });
    }
};
```
## 插件结构
```
{
    /* Called immediately after the plugin is 
     * first imported, only once. */
    install: function(less, pluginManager, functions) { },

    /* Called for each instance of your @plugin. */
    use: function(context) { },

    /* Called for each instance of your @plugin, 
     * when rules are being evaluated.
     * It's just later in the evaluation lifecycle */
    eval: function(context) { },

    /* Passes an arbitrary string to your plugin 
     * e.g. @plugin (args) "file";
     * This string is not parsed for you, 
     * so it can contain (almost) anything */
    setOptions: function(argumentString) { },

    /* Set a minimum Less compatibility string
     * You can also use an array, as in [3, 0] */
    minVersion: ['3.0'],

    /* Used for lessc only, to explain 
     * options in a Terminal */
    printUsage: function() { },

}
```

# 映射（Maps）
- 使用[] 方括号，可以将规则集或者mixin转换为映射
```
@sizes: {
  mobile: 320px;
  tablet: 768px;
  desktop: 1024px;
}

.navbar {
  display: block;

  @media (min-width: @sizes[tablet]) {
    display: inline-block;
  }
}

#library() {
  .colors() {
    primary: green;
    secondary: blue;
  }
}

#library() {
  .colors() { primary: grey; }
}

.button {
  color: #library.colors[primary];
  border-color: #library.colors[secondary];
}
```

- 可以定义中转变量
```
.button {
  @colors: #library.colors();
  color: @colors[primary];
  border-color: @colors[secondary];
}
```

- 可以多层级取值
```
@config: {
  @options: {
    library-on: true
  }
}

& when (@config[@options][library-on] = true) {
  .produce-ruleset {
    prop: val;
  }
}
```

## 使用二级变量
```
.foods() {
  @dessert: ice cream;
}

@key-to-lookup: dessert;

.lunch {
  treat: .foods[@@key-to-lookup];
}
```