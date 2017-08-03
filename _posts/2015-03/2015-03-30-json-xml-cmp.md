---
layout: post
title: "JSON XML 简要比较"
categories: [开发技术]
tags: [JSON, XML]
---

### 1 一个实例:
表示行政区数据

+ XML格式表示

        <?xml version="1.0" encoding="UTF-8"?>
        <country>
            <name>中国</name>
            <provinces>
                <province>
                    <name>北京</name>
                </province>
            </provinces>
            <provinces>
                <province>
                    <name>河北</name>
                    <cities>
                        <city>
                            <name>石家庄</name>
                        </city>
                        <city>
                            <name>保定</name>
                        </city>
                    </cities>
                </province>
            </provinces>
        </country>        

+ JSON格式表示

        {
            "name":"中国"，
            "provinces":[
                {
                    "name":"北京"
                },
                {
                    "name":"河北",
                    "provinces":[
                        {
                            "name":"石家庄"
                        }，
                        {
                            "name":"保定"
                        }
                    ]
                }
            ]

        }

### 2 最大的区别：
JSON:

+ JSON更轻量，解析效率更高，更省带宽和存储空间；
+ 大文件没法分片解析，只能整体解析；
+ 难以做规范性校验；
+ 可读性要差些，在片面的数据下，不一定知道这个对象表示什么；
 
XML

+ XML能进行分片解析，再大的文件也能搞定解析的问题
+ XML可以通过Schema或者DTD等来做数据规范性校验；
+ 可读性要好些；




### 3 参考文档：
0. [W3School JSON教程][1]
0. [JSON 官网][2]
1. [W3School XML教程][3]
0. [JSON与XML的区别比较][9]




[1]: http://www.w3school.com.cn/json/index.asp "JSON 教程"
[2]: http://json.org/ "JSON 官网"
[3]: http://www.w3school.com.cn/xml/index.asp "XML 教程"
[9]: http://www.cnblogs.com/SanMaoSpace/p/3139186.html "JSON与XML的区别比较"