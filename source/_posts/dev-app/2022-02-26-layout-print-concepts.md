---
layout: post
title: "页面排版布局印刷相关概念实践理解"
sticky: 100
categroies: [Web开发,前端开发]
tags: [页面布局,打印,印刷]
---

# 基础概念

## 点（Dot,Point）

### Dot 

- 从英语词源的角度，这是早期的词，更侧重具体的事物

- 常用于具体的事物（如打印机）及相关物件（比如打印物，印刷稿等）的描述单位
- 比如用 DPI (Dot Per Inch)每英寸点数来表示打印机或者实体设备的工艺水平或者印刷质量。
- 可以是打印机给打印物喷的墨点，可以是扫描仪采样的点位，可以是图像像素的小方格（块），也可以是屏幕的显示小方格（块）等

### Point

- 更抽象的点，比如观点，或者我说的这一点
- 是一个逻辑上的概念，可大可小，比如iOS中用来表达的尺寸`320x480pt`是初代iPhone和iPhone4的尺寸，都是3.5寸，但iPhone 4是2倍屏像素是`640x960px`可以理解为同样的`1pt`包含了更多的像素（更小的点（Point）），1像素又可以包含多个颜色点（更小的电子元件亮点，比如RGB色点）

### 像点

- 一个像素的单色分量，比如一个像素由三原色（RGB）分量组成，而像点就是红色或者绿色或者蓝色分量。



##  像素（Pixel）

- 不同应用场景、不同描述对象表达的含义不一样，有时候是物理概念，有时候是逻辑概念。

### 物理像素

- 显示屏上面可以变色的最小的亮点，也是点阵里面的最小单位（一个点，也就是一个小方格），我们常说的分辨率 `1024 x 768`,`2560 x 1600` [13寸 MacBook Pro](https://www.apple.com.cn/macbook-pro-13/specs/) 说的就是这个显示屏上最大可以显示的横纵像素数，是一个能力指标，跟屏幕的尺寸有关系，同样的电子工艺（物理像素点密度）下，当然是屏幕尺寸大，像素点就多，分辨率也就越高。

### 逻辑像素

- 这是我们广泛意义上说的像素，他是一个相对值，在不同的设备不同场景下渲染的结果不一样。
- 在计算机发展早期，一个逻辑像素就是一个物理像素一一对应，随着微电子工艺的发展，物理像素密度越来越高，多倍屏已经是用多个物理像素点来表达一个逻辑像素点，这个时候如果还用一一对应的关系来渲染的话，同样的数字媒体渲染出来的尺寸只有原来的1/4，1/9 等等，可能小的没法看。比如初代iPhone和iPhone 4物理尺寸完全一样3.5寸，但iPhone 4的物理像素密度是初代iPhone的2倍，如果还按照原来的方式渲染，那么原来全屏的图在iPhone4屏幕里面只能显示1/4屏，这样的效果肯定没法玩，用户开发人员都崩溃了。
- 同时随着行业的发展，各种各样的不同分辨率不同工艺的屏幕都出现了，为了让同样的应用同样的媒体在新旧设备上都能正常的渲染，需要有一个适配层来做相关的事情，

#### 参考像素

- 参考像素是从一个胳膊远的距离在像素密度为96DPI的设备上看1像素的结果，具体点是：

  - 在`71cm`(`28 in`)距离看到 `0.26mm`的结果

  - 在`3.5m`(`138 in`)的距离看到`1.3mm`的结果 


- 在低分辨率的设备（显示器、液晶大屏等）上，1 px 对应一个点，高分辨率的设备（高分屏、打印机等）1px 对应多个点。高分辨率的点的密度更高，点更小，点间距也更小，显示观感也更加平滑细腻。

#### iOS 逻辑像素 pt(Point)

- 参考[Points vs. Pixels](https://developer.apple.com/library/archive/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/GraphicsDrawingOverview/GraphicsDrawingOverview.html) [iOS设备适配](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/adaptivity-and-layout/) [图像适配](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
- iOS 用pt来表示逻辑像素，它的基准是 `163dpi`（初代iPhone），初代iPhone和iPhone 4的尺寸都是`320x480 pt`,但物理像素分别为`320x480 640x960`
- 对于不同dpi的设备，最终的像素数`px = pt * dpi / 163`

#### Android 逻辑像素 dp/dip

- dp/dip(Device-Independent Pixels) 
- sp/sip(Scale-Independent Pixels)
- 参考[不同像素密度适配](https://developer.android.com/training/multiscreen/screendensities)
- Android中以 `160dpi`像素密度为基准，也就是 `1 dp = 1 px`(`160 dpi`的屏幕)，如果屏幕的DPI不是`160`会根据这个比例进行缩放。最终渲染的像素数`px = dp * (dpi / 160)`,从而保证渲染的体验一致。
- dp是DPI相对单位，sp是给字体的相对单位，相对于用户的首选字体大小。默认`1 dp = 1 sp`，同时它俩是相互独立变化的。
- 点阵图的缩放可能会导致图像失真，这也就是我们需要在多倍屏上提供并渲染多倍图的原因。

#### 微信小程序 rpx

- [Responsive Pixel](https://developers.weixin.qq.com/miniprogram/dev/framework/view/wxss.html)
- 以屏幕宽度为适配（相对）基准，规定屏幕宽度为 `750rpx` 其他屏幕 `1 rpx = 屏幕宽度 / 750 px`，对于iPhone 6来说，`1 rpx = 375（逻辑像素） / 750 px = 0.5px`，反过来此时 `1px = 2rpx`，实现了2倍屏的适配。

#### 鸿蒙 Harmony OS vp/fp

- vp(Virtual Pixels)
- fp(Font Pixels)
- 参考[鸿蒙设计基础概念](https://developer.harmonyos.com/cn/docs/design/des-guides/basic-0000001055539104)
- vp可以类别Android 的dp，sp可以类比Android的fp，默认情况下`1 vp = 1 fp`,同时它俩是相互独立变化的。

#### CSS像素

- 在页面布局中使用到的 `px`为单位的数值。

## 分辨率/解析度（Resolution）

- 分辨率用来表达设备或者数字媒体的分辨能力，分辨率越高越清晰、细腻、精细。
- DPI(Dots Per Inch) 点密度，用来表示印刷相关设备的分辨率，比如打印、印刷、扫描等分辨率
- PPI(Pixels Per Inch)像素密度，用来表示显示、数字媒体等的分辨率，如屏幕分辨率，图像分辨率等
- 因为点(Dot)和像素(Pixel)本身含义比较广泛，所以DPI和PPI也经常混用。

### 屏幕分辨率

- 严格来说它是屏幕的PPI或者DPI，是一个比率是一个密度。我们常说的是屏幕上所能表达的最大物理像素（就像太阳东升西落一样），比如分辨率 `1024 x 768`,`2560 x 1600` ，它只能表达屏幕的物理像素数，不能直接表达清晰度或者精细程度。
- 相同尺寸的屏幕下，分辨率越高，屏幕的清晰度越高，显示的内容也越精细。比如在同一个设备上调整不同的分辨率。
- 不同尺寸的屏幕，单从分辨率不能判断清晰度高低，还需要通过尺寸来计算，相同PPI下，尺寸大的分辨率大，但清晰程度是一样的（PPI一样）。

### 图像分辨率

- 跟屏幕一样，严格来说是图像的PPI或者DPI，是一个比率是一个密度；而我们同样常用横纵像素数来衡量，比如 `300x300`,`800x800`等等的图像，在电子设备上显示图像几乎看不出差异，除了不同分辨率的图像大小不一样之外。但对于打印稿，来说就需要像屏幕一样，通过PPI来比较图像的清晰度了。相同像素数`300x300`的图像，不同的PPI下打印效果差异比较大，因为PPI直接决定了打印设备的点密度（也就是打印物的精细程度）
- 在Photoshop中，照片、打印稿、图稿和插图等都默认 `300ppi`，而Web设计稿、移动设计稿、胶片和视频等都默认 `72ppi`，为什么是300和72呢？后面答疑解惑有讲到。
- 这个就涉及到相机相关的像素和图像质量的关系了，相机的像素就是产出照片的像素数的最高值，这个值越大，图像包含的像素数越多，相同尺寸下，也就越清晰。

### 打印/扫描分辨率

- 这个就涉及到DPI和PPI的数值了，同样的`300x300`的图像，如果PPI是`72`和PPI是`300`的情况下，打印、冲印的图像精细程度或者是前者的四倍多，也就是同样的尺寸下会用四倍多的点去渲染，得到的结果自然更精细。
- 这也就是为什么一般打印稿要设计更高的PPI（300+），而电子文稿一般72就足够了。
- 扫描跟拍照类似，是打印的逆过程，扫描DPI就是用多少个感光元件去参与扫描过程，自然也是DPI越高，扫描得到的结果越精细。 

### 显示分辨率

- 这里表达的是肉眼对显示内容的观感体验，人眼分辨的范围有限（感受光的范围，以及感官精细下限），就像照相机或者扫描仪一样，有一个阈值范围。
- 显示不需要最求多高的分辨率，只需要让肉眼分辨不出来差别，观感舒适就OK，再高也体会不到，反而会增加系统性能负担。这也是Photoshop在屏幕显示的设计稿默认`72ppi`的一方面原因。
- 所以屏幕最终的显示分辨率不是设备能提供的能力，而是参考像素得到的值，有一种说法Window典型值为`96dpi` ，macOS上典型值为`72dpi`。为什么是`72ppi`和`96ppi`呢？后面答疑解惑有讲到。同时我们也可以看到 `72ppi`其实是Photoshop的典型默认值，而现在的Mac上已经不是`72ppi`了，而是`96ppi`。 
- 比如我用的`13.3英寸(2560 × 1600)`这台设备，实际分辨率是 `1440x900`(测量方式后文有)，这个过程就不是简单的直接渲染了，有多重因素在里面：
  - 显示屏作为一个硬件，在操作系统管理起来之后，就按操作系统的模式来运作，可能就不是技术参数本身的体现了。
  - 操作系统作为一个产品，面向的是用户的使用和体验，这里面为了更好的体验效果，可能有很多不同的策略，比如倍屏渲染，比如缩放等等，这些都可以在操作系统的显示设置界面来调整和查看。




# 计量单位

## 单位计算和换算

### DPI/PPI 计算

DPI顾名思义是个密度，由于像素点是均匀的，所以我们用横边、纵边和对角线计算出来的效果都是一样的，而由于显示器经常是横纵表示像素，而对角线表示尺寸，所以就有了下面的计算方法：

- 用勾股定理算出对角线像素
- 用对角线像素除以对角线长（显示器尺寸单位为英寸，就是我们经常说的多少寸的电视或者显示器）
- 定义a为宽像素值，b为高像素值，c为屏幕尺寸（对角线长，多少寸），那么有屏幕DPI计算公式 $dpi = \frac{\sqrt{a^2+b^2}}{c} $

比如如下计算结果：

- 我的小MacBook显示器`13.3英寸(2560 × 1600)`，$dpi = \frac{\sqrt{2560^2+1600^2}}{6.62} = 226.98  $
- 我的小Android机 `6.62英寸（2340 x 1080）` $dpi = \frac{\sqrt{2340^2+1080^2}}{13.3} = 389.3  $
- 初代iPhone  `320x480 3.5寸`,$dpi = \frac{\sqrt{320^2 + 480^2}}{3.5} = 164.8$ 跟`163`有点出入原因是3.5取的不够精确
- iPhone4 `640x960 3.5寸`,$dpi = \frac{\sqrt{640^2 + 960^2}}{3.5} = 329.65$, 同样物理尺寸的情况下，由于电子元件工艺发展带来的精细程度翻了一番。



### 标准换算关系

`1 in = 96 px = 2.54 cm = 25.4 mm = 101.6 Q = 6 pc = 72 pt` （DPI为 96的设备）



### 打印/印刷物尺寸计算

- 前面说到的DPI/PPI，在电脑上或者Web上显示几乎没什么用，都参照系统默认的PPI（现在一般`96ppi`左右），但一到打印或者印刷的时候，这个参数就有用了，直接决定了印刷或者打印的质量

#### 如何确认打印/印刷质量

- 不同应用配置方式不一样:
  - 比如macOS Chrome就是打印预览界面=>更多设置=>打印质量 `600ppi`
  - 比如macOS WPS在打印配置界面，有一个下拉框，里面选择【打印机特性】然后功能设定选【打印质量】分辨率`600dpi`
- 印刷一般是CMYK四色，打印可以是RGB三色

#### 如何确认打印机分辨率

- 打印机技术参数上面可以看到

- `300DPI`是一个中等质量的打印效果，现在的打印机基本都是`600DPI` `1200DPI`

- 当然不同印刷物对设计稿PPI要求不一样，跟最终的打印物尺寸、观看距离等有关系，距离远PPI可以低，距离近需要更高，有如下可参考

  - 海报、喷绘等宽幅面打印一般采用 75-150DPI/PPI，或者更小，因为如果面积大，分辨率又高，设计稿文件尺寸太大。
  - 报纸一般 125-170DPI/PPI，为LPI（Line Per Inch）的2倍
  - 杂志、宣传品一般 300DPI/PPI，
  - 精美画册、日历、高品质书籍一般 350-400DPI/PPI

  

#### A4纸尺寸打印物计算

- 尺寸可以参考 [a4-size.com](https://www.a4-size.com/) 和 [papersizes.org](https://www.papersizes.org/)
- 常见换算 `1 A4 = 21.0 x 29.7 cm` = ` 210 x 297 mm = 8.268 x 11.693 in`
- 打印物相关尺寸计算，对应层次分明的数字媒体比如图片，需要限制最小的PPI/DPI以保证最终打印物的质量

##### Chrome导出A4 PDF报表尺寸计算

- 根据屏幕`96ppi`的值，可以得出`1 A4 = (21 / 2.54 * 96) x (29.7 / 2.54 * 96) =793.7 x 1122.52 px     `，按照这样的像素得到的是一个屏幕上的A4纸的像素尺寸。



## 开发相关单位

### CSS 单位

#### 相对长度单位

- `em` 元素的字体大小
- `rem` 根元素 比如`html`的字体大小
- `ex` 元素字体下`x`字母的高度，将近一半的高度
- `rex` 根元素字体下`x`字母的高度，将近一半的高度
- `cap` 元素字体下大写字母的高度
- `rcap` 根元素字体下大写字母的高度
- `ch `元素字体下数字`0`的尺寸
- `rch `  根元素字体下数字`0`的尺寸
- `ic `元素字体下`水`字的尺寸
- `ric ` 根元素字体下`水`字的尺寸
- `lh` 元素的行高
- `rlh` 根元素比如`html`的行高
- `vw` 视窗 `1%`的宽度
- `vh `视窗`1%`的高度
- `vi ` 根元素文字方向上视窗 `1%`的尺寸
- `vb ` 根元素文字垂直方向上视窗`1%`的尺寸
- `vmin` 视窗短尺寸的 `1%`
- `vmax` 视窗长尺寸的`1%`

#### 绝对长度单位

- `cm`  厘米  `1 cm =96px / 2.54 `   (DPI 为96的设备)
- `mm` 毫米 `1 mm = 1/10 cm`
- `Q` 四分之一毫米 `1 Q = 1/40 cm`
- `in` 英寸 `1 in = 2.54 cm = 96px` (DPI为 96的设备)
- `pc` 派卡 `1 pc = 1/6 in`
- `pt`  点 `1 pt = 1/72 in`
- `px` 像素 `1 px = 1/96 in `

换算关系：

`1 in = 96 px = 2.54 cm = 25.4 mm = 101.6 Q = 6 pc = 72 pt` （DPI为 96的设备）

#### 角度单位

- `deg` 度 `1/360圆`
- `grad` `1/400圆` `9/10度`
- `rad` 弧度  `1/(2π)圆`
- `turn` 一圈

#### 时间单位

- `s` 秒
- `ms` 毫秒

#### 频率单位

- `Hz`赫兹
- `kHz` 千赫兹

#### 分辨率单位

- `dpi` (Dots Per Inch）每英寸点数
- `dpcm`（Dots Per Centimeter）每厘米点数
- `dppx/x` (Dots Per `px`) 每像素点数



# 应用实践拓展

## 答疑解惑

### 为什么有说 Mac显示器72ppi，Windows 显示器96ppi？

#### Mac上72ppi的参考

- [The 72 PPI Web Resolution Myth](https://www.photoshopessentials.com/essentials/the-72-ppi-web-resolution-myth/)，这个最全了，同第一个参考，当时Apple 显示器为72ppi，同时也为了适配`144ppi`的打印机
- 第一个参考世界上第一台家用Mac `Macintosh 128K` ，当时是`128K`内存，9英寸CRT显示器，分辨率`512x342 px`( 72ppi)
- 第二个参考早期Mac开发图形界面，当`ppi为72时`，有`1 in = 2.54 cm = 72 pt = 72 px = 6pc`，当设置为`72ppi`时，`1pt 等于 1px` 字体和界面单位不用换算了
- 第三个参考早期Mac显示器为14寸（这个可能是后来发展的阶段了），分辨率最高也就`800x600`，根据 $ ppi = \frac{\sqrt{800^2 + 600^2}}{14} = 71.43 $约为`72`

- Windows为了增加 1/3的阅读距离，将ppi从 `72ppi`增加到了`96ppi` [参考](https://docs.microsoft.com/zh-cn/archive/blogs/fontblog/where-does-96-dpi-come-from-in-windows)

### 为什么Photoshop里面默认的是`72ppi`,`300ppi`?

- 当前最新2022 macOS版本的Photoshop对新建文件进行了分类，照片、打印、图稿和插图默认的是`300ppi`，而Web、移动设备、胶片和视频则默认`72ppi`
- 从 [Photoshop发展历史](https://baijiahao.baidu.com/s?id=1613753449976626590&wfr=spider&for=pc) 里面可见一斑，PS是从Mac上面发展起来的，72ppi也有些因果。
- `300ppi`是一个打印、印刷的临界经验值，在300ppi左右，人眼难以明显看出差异，再高观感差异也不会太大，低于这个值太大就能明显看出差异。[参考](https://www.printivity.com/insights/2019/07/03/resolution-matters-design-files-at-300-pixels-per-inch/)，当然更高的ppi有更好的印刷效果，比如Chrome 98当前默认的PDF打印是 `600ppi` 和 `1200ppi`
- `300ppi`可能是一个过去经验值了，现在可能不一样了，[参考](Your 300dpi images may be wrong for two reasons!@#)

### 为什么在开发过程中需要使用多倍图？

- 对于一个`100x100 px`的图像，在`1倍屏`下刚好是`100x100 px`能够正常显示，如果是`2倍屏`下显示有两种方式：
  - 原样显示只能显示为原图大小的1/4（横纵都是一半），因为2倍屏物理像素密度大2倍，只需要一半的物理面积就能显示原来的图
  - 放大显示，为了显示跟原来一样的尺寸，就需要把原图的像素数横纵分别放大2倍，位图图像放大会失真，相当于将一个点稀释到了四个点里面，显示的结果就会模糊，点与点之间衔接不够细腻平滑了。
  - 这两种方式都不是我们想要的，所以为了高质量显示对应的图像，就需要在多倍屏里面提供多倍图。而多倍图在单倍屏下会被等比缩小，等比缩小不会损失清晰度（但流量会增加，因为图片大小增加了）。

### 如何查看当前屏幕是几倍屏

- 直接看技术参数相关文档，比如iOS相关设备很容易找到 @2x @3x等等参数
- 通过浏览器控制台`window.devicePixelRatio` 取值查看，这个值可能不那么准确，原因如下：
  - Windows中（实测Windows 10），这个值是显示设置里面的缩放比例，比如 `125%`的放大，这里就是`1.25`
  - 浏览器中这个值不是只读值，可以改动，`window.devicePixelRatio = 5`最后读出来的也就是5了，所以要取未改动的做参考

## 理论和实际的差异

前面我们提到了，我的设备们的参数

- 我的小MacBook显示器`13.3英寸(2560 × 1600)`，$dpi = \frac{\sqrt{2560^2+1600^2}}{13.3} = 226.98  $
- 我的小Android机 `6.62英寸（2340 x 1080）` $dpi = \frac{\sqrt{2340^2+1080^2}}{13.3} = 389.3  $

然而实际上真正在屏幕上显示的结果(设定1in的页面元素，然后测量实际屏幕显示的尺寸)却不是这样的：

- 理论上 `1 in = 2.54 cm`，但在我的小MacBook上`1 in`显示为约`2 cm`,而在小Android机上`1 in`显示约`1.5 cm`
- 所以是大了折扣的，也就是没有给那么多的像素点去渲染这个元素，也就是有一定比例的缩放，但显示效果相对大小都是一致的，同时能在屏幕上显示更多的元素。

### 如何获取当前实际分辨率?

#### 方法一：直接从系统配置获取

- macOS、Windows都有设置显示分辨率的地方，在这里就可以看到当前的配置，有可能就跟屏幕技术参数的分辨率不一样了
- 比如我的小MacBook显示器是`13.3英寸(2560 × 1600)`,但实际分辨率是 `1440x900`

#### 方法二：通过screen对象获取

- 打开Chrome开发者工具控制台，输入`screen`并回车，得到的结果中的`width`和`height`就是屏幕实际分辨率对应的宽高像素值

#### 方法三：全屏截图看像素值

- 随便找个截图工具，点选截个屏幕全图，基本拿到的就是当前分辨率，比如我用iShot拿到的结果是`1443x911` 跟`1440x900`近似。

### 如何获取当前屏幕的实际的PPI？

- 前面我们已经知道了如何计算屏幕理论上的PPI/DPI，是单位英尺的像素数，不管是用横边、纵边还是对角线计算都可以，因为本身电子元件密度比较高，在一英寸 (`1 inch = 2.54 cm`)的距离下横纵边和斜边距离差别不大。比如我的小MacBook显示器`13.3英寸(2560 × 1600)`，$dpi = \frac{\sqrt{2560^2+1600^2}}{6.62} = 226.98  $

- 那么实际屏幕也是这样的结果么？我们可以简单编程算一下: （我这边得到的结果是 `96px`  在结合尺寸 ` 1 in` ，所以结果是 `96ppi`）

  - 完整代码

    ```html
    <!DOCTYPE html>
    <html>
        <head>
            <title>test real screen ppi</title>
            <style type="text/css">
                .the-inch-block{
                    box-sizing:border-box;
                    width:1in;
                    height:1in;
                    background:green;
                }
                </style>
        </head>
    <body>
    <div class="the-inch-block"></div>
    <script>
        window.addEventListener('load',function(){
            const domEl = document.querySelector('.the-inch-block');
            if(domEl){
                alert(`width: ${domEl.offsetWidth} px,height: ${domEl.offsetHeight} px`)
            }
        });
    </script>
    </body>
    </html>
    ```

  - Console执行代码

    ```JavaScript
    const domEl=document.createElement('div');
    domEl.style.cssText="width:1in;height:1in;";
    document.body.appendChild(domEl);
    console.log(`width: ${domEl.offsetWidth} px,height: ${domEl.offsetHeight} px`);
    ```

  - 参考[这个](https://www.infobyip.com/detectmonitordpi.php) 现成的工具的计算。





# 参考资料

0. [W3C CSS单位](https://www.w3.org/TR/css-values-4/#lengths)
0. [MDN length](https://developer.mozilla.org/zh-CN/docs/Web/CSS/length)
1. [2022 年移动端适配方案指南 — 全网最新最全](https://juejin.cn/post/7046169975706353701)
2. [前端基础知识概述 -- 移动端开发的屏幕、图像、字体与布局的兼容适配](https://juejin.cn/post/6844903935568789517)
2. [移动端web布局：像素与成像的基本原理 ](https://www.cnblogs.com/ZheOneAndOnly/p/13723402.html)
2. [DPI 和 PPI 的区别是什么？](https://www.zhihu.com/question/23770739?_t_t_t=0.17883179074636324)
2. [对屏幕的理解---分辨率，dpi，ppi，屏幕尺寸，像素 等](https://www.cnblogs.com/icmzn/p/7295343.html)
2. [十分钟快速理解DPI和PPI，不再傻傻分不清！](http://www.kamilet.cn/dpi-and-ppi-ultimate/)
2. [DPI和PPI的计算公式](https://www.kamilet.cn/dpi-ppi-math/)
2. [Macbook 官网标注像素、参数和实际分辨率为什么不一样？](https://www.zhihu.com/question/501962846)
2. [The 72 PPI Web Resolution Myth](https://www.photoshopessentials.com/essentials/the-72-ppi-web-resolution-myth/)
2. [Image Resolution, Pixel Dimensions and Document Size in Photoshop](https://www.photoshopessentials.com/essentials/image-resolution/)
2. [Where does 96 DPI come from in Windows?](https://docs.microsoft.com/zh-cn/archive/blogs/fontblog/where-does-96-dpi-come-from-in-windows)