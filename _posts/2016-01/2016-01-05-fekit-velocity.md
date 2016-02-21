---

layout: post
title: "使用node-inpector 调试fekit 解决渲染velocity JS语法错误"
categories: [前端开发,Web开发]
tags: [node-inspector,fekit,velocity,javascript]

---

## fekit velocity 渲染结果不理想

fekit 渲染velocity 在低版本使用的是`velocityjs`，从2015年8月中下旬开始的版本开始也使用`velocity.java`

同时支持低版本的velocity的渲染，在启动的时候启动使用`--without-java`或`-w`参数即可。

但同样的问题，本地mock vm拍的数据的时候，如果是复合类型的数据，都会有问题

使用velocity.java 的结果

    dataList value [{name=zhang, email=zhang@test.com}, {name=wang, email=wang@test.com}]

    dataMap value {key=key, value=value}


    <script type="text/javascript">
        var dataList = [{name=zhang, email=zhang@test.com}, {name=wang, email=wang@test.com}];
        var dataMap = {key=key, value=value};
    </script>

使用velocityjs 的结果：

    dataList value [object Object],[object Object]

    dataMap value [object Object]


    <script type="text/javascript">
        var dataList = [object Object],[object Object];
        var dataMap = [object Object];
    </script>

对于VM来说，可能是数据渲染本身写的有问题，对于JS代码来的，就是语法错误。

我们想要的是这个样子的：

    dataList value [{"name":"zhang","email":"zhang@test.com"},{"name":"wang","email":"wang@test.com"}]

    dataMap value {"key":"key","value":"value"}


    <script type="text/javascript">
        var dataList = [{"name":"zhang","email":"zhang@test.com"},{"name":"wang","email":"wang@test.com"}];
        var dataMap = {"key":"key","value":"value"};
    </script>

看起来很好改的样子，尤其是对于js来说。

## 使用node-inspector调试fekit 进程

### 安装

+ 安装node-inspector: `npm install node-inspector -g`
  
  ### 启动
  
  #### 简单的启动方法：
  
+ 第一个命令行窗口执行 `node-inspector`
  
+ 第二个命令行窗口执行 `node-debug cmd params` (这里会自动打开浏览器并定位到相同地址)

#### 完整的启动

+ 启动node-inspector 监听进程: `node-inspector` 
  
  + 这里会输出调试地址 类似于 `http://127.0.0.1:8080/?port=5858`
    
  + 启动node-inspector 监听的fekit进程
    
    `node --debug-brk "C:\Users\liao.zhang\AppData\Roaming\nvm\v4.3.0\node_modules\fekit\bin\fekit" server`
    
    这样会在服务器还没启动时断点在fekit 脚本中
  
+ 浏览器打开调试地址

#### 调试

+ 切换到刚打开的调试地址
  + 闭上眼睛，默念30遍“一定能出来”，然后等啊等啊等。。。心诚则灵，第一次总不是那么容易~
  + 不用打开F12，整个页面就是一个F12
+ 执行现有代码让服务器启动起来
+ 浏览器刷新要调试的页面 `http://zhangliao.qunar.com/tests/fekit-vm/test.vm`
+ 打响应位置的断点，比如我们要调试的velocity.js中的某处
+ 下次刷新要调试页面就可到断点处

## 修改相应的源代码

### 修改velocityjs 

代码路径在 `$FEKIT_HOME\node-modules\velocityjs`

经过翻看源代码和上面的调试，我们可以定位到渲染的代码在`$FEKIT_HOME\node-modules\velocityjs\src\compile\compile.js`中：
对compile.js中_render方法作如下更改，将JSON序列化的结果进行渲染而不是默认的toString：

      utils.forEach(asts, function(ast){
		var tmpValue;
        switch(ast.type) {
          case 'references':
			//str += this.getReferences(ast, true);
			tmpValue = this.getReferences(ast, true);
			try{
				if(typeof tmpValue === 'object'){
					str += JSON.stringify(tmpValue);
				}else{
					str += tmpValue;
				}
			}catch(e){
				str += tmpValue;
			}	
            
          break;
          
至此，我们就可以使用`fekit server -w`的情况下，将数据以JSON序列化的方式渲染到页面。          

### 修改velocity.java 

代码路径在 `$FEKIT_HOME\node-modules\velocity.java`

经过调试我们可以看出，这里面的渲染是通过内部的一个httpserver来做的，启动了一个执行了一个jar包的进程，来处理渲染并返回流数据。

jar包路径是`$FEKIT_HOME\node-modules\velocity.java\bin\velocity-cli.jar`其实就是一个压缩包。

使用压缩软件解压出来，可以看到一些响应的编译过的资源文件（字节码）。

从META-INF\MANIFEST.MF中可以看到主类是VelocityCli,当然这里也可以直接从源码中轻易看出来，就两个文件，带main方法那个肯定是主类。

下面开始搞：
+ 搞一个Java工程，把`velocity.java`中的两个java文件拷出来，放入源码中
+ 先下载zip包解压出来的相关jar包，jackson系列，velocity，velocity-tool的下载下来
+ 把我们的测试vm也拷贝到src目录中来
+ 配置环境，让VelocityCli.java能够正确的运行起来
+ 写一点劫持数据的代码进行断点调试：

        import com.fasterxml.jackson.databind.ObjectMapper;
        import com.fasterxml.jackson.databind.node.ObjectNode;

        import java.io.IOException;

        /**
        * Created by liao.zhang on 2016/2/21.
        */
        public class test {
            public static void main(String[] args) throws Exception {
                String data = "{\"dataList\":[{\"name\":\"zhang\",\"email\":\"zhang@test.com\"},{\"name\":\"wang\",\"email\":\"wang@test.com\"}],\"dataMap\":{\"key\":\"key\",\"value\":\"value\"},\"velocity.java.filename\":\"src\\\\test.vm\"}";
                String json = new String(data.getBytes(), "UTF-8");
                ObjectMapper mapper = new ObjectMapper();
                ObjectNode node = (ObjectNode)mapper.readTree(json);

                String res = VelocityCli.render(node);
                System.out.println(res);
            }
        }

+ 可以发现是在 org.apache.velocity.runtime.parser.node.ASTReference的render方法中，直接调用了Object.toString所致，跟js的一样

            value = EventHandlerUtil.referenceInsert(this.rsvc, context, this.literal(), value);
            String toString = null;
            if(value != null) {
                toString = value.toString();
            }

            if(value != null && toString != null) {
                if(context.getAllowRendering()) {
                    writer.write(this.escPrefix);
                    writer.write(this.morePrefix);
                    writer.write(toString);
                }

+ 不改了
+ 要改的话，下载一份velocity源代码，把这一部分改成JSON渲染的字符串， 然后编译，把对应的字节码替换到jar包相应位置即可。