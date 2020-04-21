---
layout: post
title: "Node PDF打印各种割字问题解决"
categories: [Web开发,前端开发,自动化]
tags: [自动化,Node,PDF,字体]
---



# macOS测试方法
- 运行`test/test-html-print/test-html-print.js` 调整代码中的`format`参数为`A4`或者`A5`测试不同的样例
- `A5` html文件为 `test-*.html`, `A4` html文件为 `test.html`
- Chronium打印，打开`node_modules/puppeteer/.local-chromium/mac-xxxxxx/chrome-mac/Chromium.app` 并打开对应的 html文件，通过设置横版竖版，以及代码中的打印边距，勾选打印背景，去掉页眉页脚,打印出结果
- Chrome打印，打开最新版的Chrome(最好是79以上版本更接近Chromium的内容)，打开对应html，通过设置横版竖版，以及代码中的打印边距，勾选打印背景，去掉页眉页脚,打印出结果

# 测试结果说明
## puppeteer 2.0
### 对于`test.html` A4 打印说明
- 这个文件是原先在某个环境出现过角标割字的案例，在这个环境下没有复现,都正常
- `puppeteer2.0`生成的`test.html-A4.pdf`与chrome打印的`test.html-A4-chrome-print.pdf` 二进制完全一样
- `puppeteer2.0`生成的`test.html-A4.pdf`与对于版本的chromium打印的`test.html-A4-chromium-print.pdf`有少量的字节顺序不一样，但pdf显示结果看起来无差别

### 对于`test-simple.html` A5 打印说明
- 这个文件一直有行割字的问题
- `puppeteer2.0`生成的`test-simple.html-A5.pdf`有割字现象，与浏览器打印的有换行和少量字节不一致的差异
- chrome打印的`test-simple.html-A5-chrome-print.pdf` 与 `puppeteer2.0`对于版本的chromium打印的 `test-simple.html-A5-chromium-print.pdf` 二进制完全一致，且pdf显示正常

## puppeteer 2.1.1 
- `test.html`,`test-simple.html` 测试结果跟 `puppeteer 2.0` 一致
### 对于`test-complex`测试结果
- `puppeteer`生成结果：第5题标签割字，第16题选项割字，第17题答案标签割字，第18题解析标签割字，第19题答案图片和文字割字，第20、21题答案标签割字，第27题答案文字割字，第29题解析标签割字，第31题答案文字割字，第32题解析标签割字
- chrome打印结果：第7题标签和题目不在一页，第17题答案标签割字，第18题解析标签割字，第19题图形和文字割字，第21题答案标签割字，第27题标签和题目不在一页文字割字，第29题图形割字，第30题标签割字，第31题图形割字，第34题答案标签割字

# 结论
- chrome打印本身也不行，那从工具的角度来解决问题就行不通了
- 从另一个方面来看，就是我们写的页面有问题或者说可以从页面本身来解决

# 解决办法
前期有过一些其他的探索，比如 [定位puppeteer](https://github.com/rawbin-/puppeteer-test-pdf), 也尝试过换[一些其他的方案](https://github.com/rawbin-/node-print-to-pdf-test)，都在向工具方面探索，也提过几个issue未解决，最后直接被上面的chrome 打印结果击垮，最终回过头来向代码方面探索，最终解决如下：

> 目前问题定位结论是`display:flex`引起的

```$xslt
<style type="text/css">
/*@media all !*just for debug*! {*/
@media print {
    /*解决 答案选项 割字*/
    .opts{
        display: block !important;
    }
    .column-4 .opt, .column-2 .opt, .column-1 .opt{
        display: inline-block !important;
    }
    .column-4 .opt{
        width: 24.5% !important;
    }

    .column-2 .opt{
        width: 49.5% !important;
    }

    /*解决 题号 割字*/
    .one-page ul .outer{
        display: block !important;
        position: relative !important;
    }

    .timu_index{
        position: absolute !important;
        margin-top: 0px !important;
    }

    .outer .timu_index + div{
        margin-left: 50px !important;
    }

    /*解决 答案和解析标签 割字 */
    .wrapper .tag{
        display: block !important;
        clear: both;
    }

    /*解决 答案解析文字图片 割字*/
    .tag-content{
        page-break-inside:auto;
        display: block !important;
    }

    .tag-content img{
        display: block !important;
    }

    /*修复水印没撑开的问题*/
    .question-stem{
        display: block !important;
    }
}
</style>
```
