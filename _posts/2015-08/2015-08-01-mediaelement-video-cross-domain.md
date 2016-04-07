---
layout: post
title: "MediaElement HTML5 Video播放视频适配过程"
categories: [开发技术,Web前端]
tags: [MediaElement,HTML5,Video,Flash,跨域]

---


### 说明

网页中的音视频媒体格式众多，同时在不同的浏览器中的表现经常不一致

mediaelementjs 这个牛逼的组件就是为了给出一个统一的解决方案，来使得不同的浏览器环境尽量做到同样的输出。


### 参考：
+ [官方网站](http://mediaelementjs.com/)
+ [github](https://github.com/johndyer/mediaelement)


### 资源说明

+ swf文件，用来模拟视频播放界面的
+ svg png gif文件 界面交互相关展示的元素
+ css 
+ js 

### 操作方法

#### 部署资源

将上面的几部分资源分别部署到可以访问的位置，根据视频兼容性和经验建议，将视频转为MP4格式将减少许多麻烦,同时MP4的编码格式必须是AVC(H.264),否则看不到图像。

完成后需要根据部署进行相应的资源属性的修改,所以那些.min.* 的文件不能直接用了，你行你自己改

+ 修改css中图片引用的位置 

    将里面的相对路径都改为咱们部署的绝对路径
    
+ 修改flash引用路径
    
    源代码中的pluginPath 默认将js和flash放在同一路径，咱们这里不行，需要单独分开来，加一个options.flashPath的配置项
    
+ css 
+ js
    
    这些都放在本工程下了，看着改吧    

#### 资源引用

0. 将如下代码放入页面 \( 最好是head区域，[参考](http://mediaelementjs.com/) \)
    
        <script src="/path/to/jquery.js"></script>
        <script src="/path/to/mediaelement-and-player.min.js"></script>
        <link rel="stylesheet" href="/path/to/mediaelementplayer.min.css" />
    
1. 添加相应的媒体元素, 可选设置相应的属性

        <video src="/path/to/test_video.mp4" poster="/path/to/poster.jpg" width="320" height="240" controls="controls" preload="none"></video>
        
        <audio src="myaudio.mp3"></audio>
        
2. 执行代码绑定额外的事件
        
        $('video,audio').mediaelementplayer({
            success:function(mediaElement, domObject,player){
                console.log(mediaElement)
                console.log(domObject)
                console.log(player)
            }
        });
        
#### 使用方法

0. 使用跨域的代码:

        $(function(){
            $('video,audio').mediaelementplayer({
                //for related swf file,because they are not on the same path and host
                flashPath: '//path/to/media_element_flash/',
                // name of flash file
                flashName: 'flashmediaelement-cdn.swf',
                // set to 'always' for CDN version
                flashScriptAccess: 'always',
        
                success:function(mediaElement, domObject,player){
                    //console.log(mediaElement)
                    //console.log(domObject)
                    //console.log(player)
                }
            });
        });

0. 代码解释：

        //for related swf file,because they are not on the same path and host
        flashPath: '//path/to/media_element_flash/',

    源码中的js和swf文件都放在同一个路径，所以pluginPath引用了js和swf文件，而咱们的swf放在source下，js放在当前项目下，所以需要分开，新增一个flashPath路径专门引用swf文件，同时修改了源代码
       
        // name of flash file
        flashName: 'flashmediaelement-cdn.swf',

    这个是跨域需要引用的swf文件，默认的flashmediaelement.swf不允许跨域访问，具体表现为在IE8下播放视频是会报JS错误，这里配置的是flash的allowDomain。
       
        // set to 'always' for CDN version
        flashScriptAccess: 'always', 
        
    这个是swf加载后的回调需要用到的，默认的值是sameDomain，只允许访问相同域下的脚本，导致无法调用js回调。具体表现为在IE8下播放器样式无法正常渲染。这里配置的是flash的allowScriptAccess属性。           

    问题解决过程参考[MediaElement Issue](https://github.com/johndyer/mediaelement/issues/1657)

0. 属性说明
    + allowScriptAccess
     
     这个属性一般出现在下面的代码里
        
            <object classid='clsid:D27CDB6E-AE6D-11cf-96B8-444553540000' codebase='http://download.adobe.com/pub/shockwave/cabs/flash/swflash.cab#version=9,0,0,0' height='100%' width='100%'>  
                    <param name='src' value='test.swf'/>  
                    <param name='AllowScriptAccess' value='never'/>  
                    <embed name='test.swf' pluginspage='http://www.adobe.com/go/getflashplayer' src='test.swf' height='100%' width='100%' AllowScriptAccess='never'/>  
            </object>   
      
      allowScriptAccess 指定当前的Flash对象对外部环境（html）的脚本访问权限。  
	  
       + never 不允许当前Flash访问外部环境的脚本    
       + sameDomain 只允许当前Flash访问外部环境中与当前Flash同域的脚本
       + always 允许当前Flash访问外部环境所有脚本
        
    + allowDomain
    
    这个属性一般出现在如下代码
            <?xml version="1.0"?>
            <!--?http://www.mydomain.com/crossdomain.xml?-->
            <cross-domain-policy>
                <allow-access-from domain="www.otherdomain.com" />
                <allow-access-from domain="*.adobe.com" />
                <allow-access-from domain="*" />
            </cross-domain-policy>
            
     或者出现在如下的代码中
     
            Security.allowDomain("www.domain.com", "*");    
            Security.allowInsecureDomain("www.domain.com", "*");   
      
     allowDomain 允许参数中指定的域下的脚本或SWF访问当前SWF（加载XML策略文件的或者设置allowDomain的Flash文件）的对象和变量,这种权限开放是单向的。  
     allowInsecureDomain 则允许HTTP协议下的资源访问HTTPS下的资源 

#### 参考文档
0. [Creating a cross-browser video player](https://developer.mozilla.org/en-US/Apps/Build/Audio_and_video_delivery/cross_browser_video_player)
0. [Video on the Web](http://diveintohtml5.info/video.html)
0. [MediaElement Github](https://github.com/johndyer/mediaelement)
0. [MediaElement官网](http://mediaelementjs.com/)
0. [MediaElement Issue](https://github.com/johndyer/mediaelement/issues/1657)
0. [ActionScript ExternalInterface](http://help.adobe.com/zh_CN/FlashPlatform/reference/actionscript/3/flash/external/ExternalInterface.html)
0. [ActionScript Security](http://help.adobe.com/zh_CN/FlashPlatform/reference/actionscript/3/flash/system/Security.html)
0. [Flash跨域的完全解决方案](http://www.zystudios.cn/blog/post/51.Shtml)
0. [彻底理解crossdomain.xml 跨域SWF调用](http://www.cnblogs.com/kenkofox/p/3631339.html)
0. [页面中Flash对象参数](http://www.nowamagic.net/librarys/veda/detail/1615)
0. [Flash 跨域总结](http://blog.csdn.net/vince6799/article/details/4586312)
0. [Flash 安全总结](http://drops.wooyun.org/tips/153)
