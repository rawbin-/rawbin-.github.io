---
layout: post
title: "JavaScript 文件上传 change 触发问题"
categories: [Web开发,前端开发,JavaScript]
tags: [input,file,change,JavaScript]
---



### 1 问题来源

使用input type=“file”做文件上传，选择文件后自动上传，绑定input的change事件。

在相邻两次选择同一个文件时，不会触发change事件，原因是两次的值没有发生变化。

### 2 解决办法

在选择文件的时候，清空原来的值，每次自动触发change事件。

在标准浏览器中可以直接通过value=''来直接重置input type="file"的值，但在IE中无法实现。

于是通过Form自带的reset方法来实现。代码如下：

    ​<!DOCTYPE html>
    <html>

    <head>
        <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
    </head>
    <form id="test-form">
    <input type="file" id="file-test">
    </form>
    <body>
        <script>
            var form = document.getElementById('test-form');
            var el =  document.getElementById("file-test");
            
            var changeFunc = function(){
                    console.log("change event");
            }
            
            var clickFunc = function(){
                console.log('click event');
                //el.value = '';
                form.reset();
            }

            var propertychangeFunc = function(){
                console.log('property change event')
            }
            
                
            if(window.addEventListener){
                el.addEventListener('change',changeFunc);
                el.addEventListener('click',clickFunc);
                el.addEventListener('propertychange',propertychangeFunc);
            }else{
                el.attachEvent('onchange',changeFunc)
                el.attachEvent('onclick',clickFunc)
                el.attachEvent('onpropertychange',propertychangeFunc)
            }
        </script>
    </body>

    </html>

