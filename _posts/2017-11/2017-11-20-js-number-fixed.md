---
layout: post
title: "JavaScript中的金额（钱）数据处理，始终保留两位小数"
categories: [前端开发,Web开发]
tags: [JavaScript,四舍五入]
---



### JavaScript保留两位有效数字

#### 问题一 舍入规则不统一

```
const floatNumbers = [0.015,0.025,0.035,0.045,0.055,0.065,0.075,0.085,0.095];
const addOne = item => item + 1;
const printToFixed2 = item => {
    console.log(item,'.toFixed(2) //',item.toFixed(2))
};

floatNumbers.map(printToFixed2);
floatNumbers.map(addOne).map(printToFixed2)
```

- 在Chrome 71 中运行的结果 和 Firefox 63 中结果一样，如下

```
0.015.toFixed(2) // "0.01"
0.025.toFixed(2) // "0.03"
0.035.toFixed(2) // "0.04"
0.045.toFixed(2) // "0.04"
0.055.toFixed(2) // "0.06"
0.065.toFixed(2) // "0.07"
0.075.toFixed(2) // "0.07"
0.085.toFixed(2) // "0.09"
0.095.toFixed(2) // "0.10"
1.015.toFixed(2) // "1.01"
1.025.toFixed(2) // "1.02"
1.035.toFixed(2) // "1.03"
1.045.toFixed(2) // "1.04"
1.055.toFixed(2) // "1.05"
1.065.toFixed(2) // "1.06"
1.075.toFixed(2) // "1.07"
1.085.toFixed(2) // "1.08"
1.095.toFixed(2) // "1.09"
```

- 这个结果跟咱们平常理解的四舍五入不一样（0.015，0.025的舍入方法不一样），跟银行家舍入法也不一样(0.035，0.045的舍入值一样)

#### 问题二 二进制小数本身就是一个坑（特性）

- 计算 `28908.75 * 2.82`
  - 列竖式算 得到的结果是 `81522.6750`，四舍五入保留两位小数得到 `81522.68`
  - 用程序算( `python`,` node`,`js`) 得到的结果是 `81522.67499999999`, 四舍五入保留两位小数得到 `81522.67`



#### 寻求解决办法

- 片面的办法很容易出来，比如[这个](https://www.sitepoint.com/number-tofixed-rounding-errors-broken-but-fixable/), 一顿牛逼的操作，把问题一中的问题直接片杀，但问题二却无动于衷
- 也可以暴力直接按照我们想要的结果直接重写
- 亦或是直接通过放大的方式转成整数来处理，规避这个问题
- 但我们终究还是想前前后后说个清楚，知其所以然



### 追本溯源

#### 必要的内容	

- 先上点理论
  - [IEEE 754](https://zh.wikipedia.org/zh-cn/IEEE_754)
  -  [图解：JavaScript中Number的一些表示上/下限](https://segmentfault.com/a/1190000000407658)
  - [Number.prototype.toFixed](https://tc39.github.io/ecma262/#sec-number.prototype.tofixed)
  - [Math.round](https://tc39.github.io/ecma262/#sec-math.random)
- 再上个工具 [VisualNumeric64](http://alvarto.github.io/VisualNumeric64/#1)
- 参考 《深入理解计算机系统》第三版 关于信息的表示和处理

#### 浮点数理论

- 跟浮点相对应的事定点，定点表示的小数点固定在最低位的右边
- 浮点数：二进制小数点不固定的表达数的方式
- 对实数采用规格化形式的标准科学计数法优点：简化了浮点数的数据交换；简化了浮点算术算法；提高了用一个字存储的数的精度，因为无用的前导零可能占用的位被二进制小数点右边的有效位替代了
- 浮点数内容
  - 符号位 放在最高位，便于比较大小，0正、1负
  - 尾数 位于浮点数的尾数字段，其值在0到1之间
  - 指数 位于浮点数的指数字段，表示小数点的位置；指数的底或者基是隐含的
  - 特殊的位模式表示的特殊值，运算过程加减法比乘除法更复杂《计算机组成与体系结构性能设计》 
  - 32位表示的范围-(2-2^-23)* 2^128 和 -2^-127之间的负数, 2^-127和(2-2^-23)*2^128之间的正数
  - 不是补码的形式？
  - 浮点表示的数不想定点那样沿数轴均匀分布，浮点数越靠近原点数越密集，越远离原点，数越稀疏？
- 浮点数表示的问题
  - 溢出
    - 又分为阶值上下溢和有效值上下溢
    - 溢出（上溢）数值太大，正的指数太大而导致指数域表示不了的情况
    - 溢出（下溢）数值太小，负的指数太大而导致指数域表示不了的情况
    - 为了解决浮点数的溢出问题，采用更大的指数表示方式
      - 单精度 浮点数由一个32位的字表示
      - 双精度 浮点数由两个32位的字表示
  - 不够精确
    - 整数可以精确表示，浮点数只能表示无法表示的小数的近似值
    - 为了让表示的值最接近，中间结果的计算增加了两位，需要硬件支持
      - 保护位 在浮点数中间计算中，在右边多保留的两位中的首位；用于提高舍入精度
      - 舍入位 在浮点数中间计算中，在游标多保留的两位中的第二位；使浮点中间结果满足浮点格式得到最接近的数
      - 粘贴位 用来区分舍入位右边的数据是否非零，如果非零，这一位为1
      - 算术逻辑单元的寄存器的位数宽度是包含了保护位的宽度的，溢出的位数也能有地方放
    - IEEE 754提供了几种舍入模型
      - 总是向上舍入
      - 总是向下舍入
      - 截断舍入
      - 向最近的偶数舍入（粘贴位）
        - 给出了当数值在中间的时候的舍入方式，美国国税局（IRS）总是将0.5向上舍入，也就是我们的四舍五入。比四舍五入更公平的舍入方式是当值为0.5的时候，有一半的时间向上舍入，一半的时间向下舍入。IEEE 754中如果最后一位是奇数就加1，如果偶数就截去。
        - 比如超出可保存23位的多余位为10010，则多余位的值超过了最低可表示位值的一半，这种情况下，正确的答案是最低可表示位加1，即入到可表示的数；现在考虑多余位是01111，这种情况多余位的值小于最低可表示位值的一半，正确的答案是简单去掉多余位（截断）。对于多余位10000这种特殊情况，此时结果位于两个可表示数值的严格中点，一种可选的办法是截断，这样简单；但截断的问题是给一个计算序列带来小的但可累计的偏差效应；另一种可选的办法是基于一个随机数来决定是舍还是入，于是平均而言无偏差累积效应，但无法产生一个可预期的计算结果；IEEE采取的方法是强迫结果为偶数，若计算结果是严格位于两个可表示的数的正中间，则当结果的最低可表示位是1时，结果向上入，当最低可表示位为0时，结果向下舍；
  - 同一个浮点数用科学计数的方式有很多种表达
    - 规格化，让浮点数位数第一位始终是1，这样同一个数只有唯一一个表示了，同时这个1和小数点也能隐含省略掉了，规格化的过程就是将非规格化的数字中的小数点移动到最左一个1右边。
- IEEE 754
  - 隐藏了规格化二进制数的前导位1，增加了1位精度
  - 规定指数带偏阶（偏移量）`2^k-1`(k为指数的位数)，单精度偏阶127，双精度偏阶1023，这样能扩大数值表示范围，比如单精度的0-128，进行移位之后能表示-127到128之间的数，
  - IEEE 754-1985规定了浮点数的表示，IEEE 754-2008，在原来的基础上增加了 半精度（16位）和四精度（128位）
  - 单精度格式（32位）1位符号位，8位指数位，23位有效值；双精度格式（64位）1位符号位，11位指数位，52位有效值
  - 规定了舍入模型
  - 规定了特殊情况及相应的处理方法，以使浮点运算能产生一致的、可预期的结果
  - IEEE 754是从1976年跟 8087协处理器设计同时进行的由Inter赞助的



### 参考资料

0. 《计算机组成与设计 硬件软件接口》第五版
1. 《计算机组成与体系结构性能设计》第八版
2. 《深入理解计算机系统》第三版
3. [为什么(2.55).toFixed(1)等于2.5？](https://zhuanlan.zhihu.com/p/31202697)
4. [为什么0.1 + 0.2不等于0.3？](https://fed.renren.com/2017/05/13/float-number/)
5. [从标准原理出发理解 JavaScript 数值精度](https://juejin.im/post/5c3db8b7e51d45515817bdeb?utm_source=gold_browser_extension)
6. [Number().toFixed() Rounding Errors: Broken But Fixable](https://www.sitepoint.com/number-tofixed-rounding-errors-broken-but-fixable/)
7. [Rounding Numbers in JavaScript](https://www.kirupa.com/html5/rounding_numbers_in_javascript.htm)
8. [JavaScript 浮点数运算的精度问题](https://www.css88.com/archives/7340)
9. [ROUNDING AND TRUNCATING NUMBERS IN JAVASCRIPT](https://pawelgrzybek.com/rounding-and-truncating-numbers-in-javascript/)
10. [JavaScript Number](https://javascript.info/number)
11. [Rounding Decimals in JavaScript](http://www.jacklmoore.com/notes/rounding-in-javascript/)
12. [Round to at most 2 decimal places (only if necessary)](https://code.i-harness.com/en/q/b48e52)
13. [如何避开JavaScript浮点数计算精度问题（如0.1+0.2!==0.3）](https://blog.csdn.net/u013347241/article/details/79210840)
14. [JavaScript 格式化数字、金额、千分位、保留几位小数、舍入舍去](https://www.css88.com/archives/7324)
15. [javaScript 中 toFixed() 精度问题及解决方案](https://www.jianshu.com/p/c2eca2b1be3c?fromApp=1)
16. [toFixed计算错误(依赖银行家舍入法的缺陷)解决方法](http://www.chengfeilong.com/toFixed)
17. [Javascript中toFixed计算错误(依赖银行家舍入法的缺陷)解决方法](https://www.zhangshengrong.com/p/RmNPnqLP1k/)
18. [JavaScript 浮点数精度之谜](https://mp.weixin.qq.com/s?__biz=MzAwNTAzMjcxNg==&mid=2651424976&idx=1&sn=7c0d3e315ceb25b22dd3eca42798a6b7&chksm=80dff6b3b7a87fa58a79591819af0d78ee3190d1153c73d5530eb196ad12b43c9be4a7237b09&mpshare=1&scene=1&srcid=0802C7xoLq9bLQXMEViEHTSM&key=92e1ed454942560da716c8641291a5f89ff92ec74c4a1d7e26aa3b115d4f07370a00d2c8399463eda46c2e11e4fe48c8c10cfae499e900311b9a85d2538acdd7eccfbe8de145175cc8c131b27e697381&ascene=0&uin=MjEzMTAwMzgyNQ%3D%3D&devicetype=iMac+MacBookAir7%2C1+OSX+OSX+10.12.6+build(16G29)&version=12020)
19. [js浮点数精度问题的前世今生？](https://www.zhihu.com/question/26806477)
20. [JS的浮点数计算精度丢失问题解决方案](https://blog.csdn.net/u012143360/article/details/53148591)
21. [JavaScript 浮点数陷阱及解法](https://www.cnblogs.com/sunshq/p/7682109.html)
22. [JS四舍五入](https://www.jianshu.com/p/4ac81311ae6e)
23. [javascript中toFixed()方法详解](https://www.waitig.com/javascript%e4%b8%adtofixed%e6%96%b9%e6%b3%95%e8%af%a6%e8%a7%a3.html)

