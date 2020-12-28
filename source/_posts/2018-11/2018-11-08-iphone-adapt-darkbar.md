# 2018-11-08-iphone-adapt-darkbar
---

layout: post
title: "iPhonee 安全区适配，小黑条适配标准解决方案"
cagegories: [Web开发,前端开发,移动端]
tags: [iPhone X适配,小黑条适配,安全区适配]

---

# 需求场景
- 自从有了iPhone底部小黑条之后，页面底部就有各种适配
- 小黑条还不止iPhone X这一种机型，后续新出的还要不要改代码？
- 可以通过js来匹配机型，可以通过媒体查询来匹配机型，新的机型如何适配？

# 解决办法
## 解决标准
- 希望尽可能少的改动，不需要js里面设置，然后模板里面再判断，然后再加个对应的样式啥的，太麻烦
- 希望尽可能通用，不能加了iPhoneX，又加iPhone XI，又加iPhone XII，没完没了
- 希望可以适配未来的类似的机型

## 解决思路
- 找到这个小黑条的尺寸获取方式，
    - 在iPhone上可以通过如下方式获取（兼容写法）
       ```
            height:50px;
            height: calc(50px + constant(safe-area-inset-bottom)); /* ios < 11.2*/
            height: calc(50px + env(safe-area-inset-bottom)); /* ios >= 11.2*/
       ```
- 在我们页面样式对应的地方加上这个尺寸，需要就加上，不需要就不加
- 加的方式可以根据情况来选择
    - 直接加在页面底部，padding
    - 如果是吸底元素，要加元素的padding，通过加height高的方式也行
    - 如果元素浮空，需要加margin的方式来设置，或者加底边距bottom的方式
    - 当然内部加margin也行，需要注意背景颜色是否一致
    - 有些地方需要全量的，有些地方可能需要高度折半，请自行选择
- 如果碰到一些特殊的情况，上面方式处理不了的，比如听说某些安卓机（我目前还没遇到），还可以回归原始的处理办法
    - 媒体查询
    - 配合JS通过getSystemInfo直接处理
        - `screenHeight - safeArea.bottom` 即是底部小黑条影响的范围，也就是上面方法求得的值

### 小程序中的解决办法
- 直接使用上面的兼容写法的适配方案即可
- 当前（20201128）最新的微信开发者工具开发版本上可以直接验证了，h5部分目前还不支持，开发哥哥么已经在搞了

### H5中的解决办法
- 在html模板里面加如下meta标签,如果存在就合并一下，
```
 <meta name="viewport" content="viewport-fit=cover">
```
- 然后用上面的兼容写法处理

### 手边没有设备怎么办？
#### 模拟器
- XCode(XCode 14)
- New =>iOS=>App类型的Application
- 接下来 Interface 选StoryBoard, Language选Swift
- 创建工程完成
- 把`ViewController.swift`代码改成如下:
    ```
    import UIKit
    import WebKit

    class ViewController: UIViewController {
        var webView: WKWebView!

        override func loadView() {
            webView = WKWebView()
            view = webView
        }

        override func viewDidLoad() {
            super.viewDidLoad()
            // Do any additional setup after loading the view.
            let url = URL(string: "http://127.0.0.1:8080/iphone-style-adapt.html")!
            webView.load(URLRequest(url: url))
            webView.allowsBackForwardNavigationGestures = true
        }
    }

    ```
- 在`Info.plist`中加入如下内容并合并<dict>标签 ，也就是把中间两行加入到dict标签中，（通过文本编辑器打开，或者XCode中以源码方式打开都行）
```
    <dict>
        <key>NSAllowsArbitraryLoads</key>
        <true/>
    </dict>
    
```

### 参考资料
- [网页适配iPhoneX,就这么简单](https://aotu.io/notes/2017/11/27/iphonex/index.html)
- [Designing Websites for iPhone X](https://webkit.org/blog/7929/designing-websites-for-iphone-x/)
- [viewport-fit](https://www.w3.org/TR/css-round-display-1/#viewport-fit-descriptor)
- [Adaptivity and Layout](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/adaptivity-and-layout/)
- [iPhone X适配方案 及 H5调试技巧 ](https://www.sohu.com/a/215996396_114819)
- [就H5调试技巧，浅谈：iPhone X 适配方案！](https://www.quxingdong.com/702.html)
- [H5 IphoneX 适配简述](https://www.jianshu.com/p/438c7c2e5664?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation)
- [关于H5页面在iPhoneX适配](https://www.cnblogs.com/lolDragon/p/7795174.html)