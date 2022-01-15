# 基本语法
## 查看帮助
Bash中查看命令基本的几种方式
- `man command`
- `command --help`
- `command -h`
如果用`zsh`，结果可能不一样，要查看内建命令的帮助文档可能找不到，因为`zsh`没有提供`help`命令，比如找`type`内建命令的帮助，拿不到想要的，这个时候需要先进入`bash`，然后再用`help type`就好了
- `bash -c "help xxx"` 查看 xxx的帮助，比如`set`，`type`等

## 数据结构
### 列表/数组
- 直接用字符串列表表示，跟`$IFS`相关，`a b c`,`a,b,c`等都可以，也可以是`"a b","a c" "ad"` 
- 可以是命令产生的`[list]`结构，比如`echo`上面的例子，或者其他命令得到的列表结构
- 可以使用`array[xx]`来进行初始化，使用`${array[xx]}`进行取值
- 也可以把变量当做数组来进行操作，字符串当数组是只有一个元素的数组
- `${array[0]}` 第一个元素
- `${array:0}` 第一个元素第一个字符开始
- `${array:1}` 第一个元素的第二个字符开始
- `${#array[0]}` 第一个元素的长度
- `${#array}` 第一个元素的长度
- `${#array[*]}` 元素个数
- `${#array[@]}` 元素个数
- 大部分标准字符串操作可以用在数组中

## 变量
### 变量声明
#### 使用`declare` 或 `typeset`声明变量
`declare/typeset option varname`
- `-r` 只读
- `-i` 整型
- `-a` 数组
-  `-f` 函数
-  `-x` export  
-  `-x varname=value`

#### readonly 与 `declare -r`相同

### 变量引用
使用`${param}`（更通用） 或者`$param`来引用变量
变量间接引用可以用`eval varA=\$$varB`的形式，来把`varB`的值当做变量名，另一种方式是`${!varB}`

### 变量计算
- `let` 执行变量的算术操作
- `expr` 
- `eval`
- `set` 修改内部脚本变量值，触发选项标志位来帮助决定脚本的行为，或者以一个命令的结果来设置脚本的位置参数，脚本将从命令的输出中重新分析出位置参数
- `unset` 删除一个Shell变量
- `export` 使得export的变量在所运行的脚本或者Shell的所有子进程中都可用，但没办法在父进程当中用，经常用在启动文件中

## 函数
函数的返回值可以 `return` 来指定，也可以由函数最后一条命令的退出状态来决定 0为成功，其他为失败
```bash
# 函数定义
function function_name{
command
}

或者 

function_name() {
command
}

#函数调用
function_name $arg1 $arg2

# 间接引用
message=Hello
Hello=Goodbye
function_name "${!messge}" # Goodbye

# 解除引用
newvar=\$"$1"
eval echo -n "[\$$1]"
```

要让函数返回字符串或数组，可以用共享的变量的方式来实现。

使用 `local` 关键字声明的变量为局部变量，只能够在该变量被声明的代码块中可见，这个代码块就是局部的范围。

## 命令行参数相关
- `$0` 程序名
- `$i`  第`i`个位置参数，`i`超过2位数需要用`${i}` 来引用
- `$#` 参数个数
-  `$*` 所有位置参数都被看做一个单词，需要被"$*"
-  `$@` 每个参数当做一个单独的单词，需要被"$@"
-  `$?` 最近一个执行命令的退出状态，0表示执行成功
-  `$_` 上一个命令的最后一个参数
-  `$$` 当前进程的PID
-  `$!` 最后一个作业的PID


## 参数替换
- `${param}` 比$param 更通用，尤其是在把变量和字符串连用的时候
- `${param-default}` 如果变量没有被声明，则用默认值
- `${param:-default}` 如果变量没有被设置，则用默认值
- `${param=default}` 如果变量没有被声明，则把它的值设置为`default`
- `${param:=default}` 如果变量没有被设置，则把它的值设置为`default`
- `${param+value}` 如果变量被声明了，就使用value，否则使用`null`字符串  
-  `${param:+value}`  如果变量被设置了，则使用value，否则使用`null`字符串
-   `${param?errmsg}` 如果变量被声明了，就使用设置的值，否则使用`errmsg` 
-  `${param:?errmsg}`  如果变量被设置了，则使用设置的值，否则使用`errmsg`
-   `${#*}` 或 `${#@}` 表示位置参数的个数
-   `${array#*}` 或 `${array#@}` 表示数组中元素个数
-   更多参考 [[Shell脚本#字符串处理]]

## 命令或进程替换
### 命令替换
#### `command`
典型方式为 `command`，可以把命令的执行结果放入变量中或者传递到另一个命令中作为参数，命令替换会启动一个子Shell[[Shell脚本#命令执行]]
- 启动一个子Shell
- 引起单词分割
- 没有引起单词分割也会去掉多余的新行
- 如果`echo`输出了一个未引用变量，而且这个变量以命令替换的结果作为值，那么这个变量的换行符将会被删除，这可能引起一些异常情况。

```
COMMAND `echo a b` # 两个参数 a 和 b
COMMAND "`echo a b`" # 1个参数，a b
COMMAND `echo` # 无参数
COMMAND "`echo`" # 一个空参数
```

#### `${command}`
这种形式的命令替换可以取代后置引用的方式，它允许嵌套，更加灵活。在处理双反斜线的时候与后置引用不同，后置引用输出会双反斜线，这个会输出一个

### 进程替换
类似于命令替换，命令替换是将一个命令的结果赋值给一个变量，进程替换则把一个进程的输出给另一个进程，或者说一个命令的结果发给另一©个命令

#### `>(command)` `<(command)`
启动进程替换，它使用`/dev/fd/<n>` 文件将圆括号中的进程处理结果发送给另一个进程。可以简单用`echo >(true)` 测试一下。
可以利用进程替换来比较两个命令的输出，比如`comm/diff <(ls -l) <(ls -al)`

## 特殊符号
- `#`  井号 注释和其他用法
- `;`  分号 命令分隔符
- `;;`  双分号 `case`终止符
- `.`  点号 等价于 `source` 命令
- `"` 双引号部分引用，`"STRING"`会阻止解释STRING中大部分特殊字符，除了$,反引号和转义符
- `'` 单引号全引用，`'STRING'` 将会阻止解释STRING中全部的特殊字符
- ` `  反引号命令替换，`COMMAND`可以将命令的输出赋值到一个变量中，
- `,` 逗号操作符，返回最后一个 
- `:` 冒号，空命令 等价于 `NOP`，与重定向一起用可以清空文件而不改变权限没有就创建，也可以在一个二元操作中作为占位符
- `$` 变量引用
- `[]` 数组元素，字符范围，条件测试等等
- `<<` Here Document中的重定向
- `<<<` Here String中的重定向
- `|` 管道操作符
- `-` 选项前缀，重定向的破折号，先前工作目录
- `~` home目录，~user 为user的家目录，和~/一样

## 分支结构
###  if-then
### if-then-else
### 嵌套if 改 elif => if-then-elif-then
### if-then-elif-then-else else属于最后一个if或elif
### `case`分支
```
case variable in  
pattern1 | pattern2) commands1;; 
pattern3) commands2;;  
*) default commands;;  
esac
```


## 条件判断
### 判断方式
####  `test`命令
test命令中只能使用简单的算术操作，
#### `[]` 内建命令
这个命令与`test`等价
####  `()`
命令组，小括号中的命令组会作为一个子Shell来运行，(command1; command2; command3;)
#### 算术运算扩展`(())`
双括号支持更多的高级数学表达式，比如`++`,`--`,`!`,`~`,`**`,`<<`,`>>`,`&`,`|`,`&&`,`||`
#### 字符串运算扩展`[[]]` 扩展`test`命令 
`[[`是一个关键字，不是一个命令,它针对更高级的字符串比较，比test命令增加了模式匹配

### 字符串比较
- `str1 = str2`
- `str1 != str2`
- `str1 > str2`
- `-n str1` 长度是否大于0
- `-z str1` 长度是否为0 


### 文件比较
- `-d file` 是否 存在且为目录
- `-e file` 文件是否存在
- `-f file` 是否 存在并且是个文件
- `-r file` 是否 存在且可读
- `-s file` 是否 存在且不为空
- `-w file` 是否 存在且可写
- `-x file` 是否 存在且可执行
- `-O file` 是否 存在且被当前用户拥有
- `-G file` 是否 存在且默认组为当前用户组
- `file1 nt file2` file1 是否比 file2 新
- `file1 ot file2` file1 是否比 file2 旧

## 循环结构
### for 循环
```
for arg in [list]；# 不加分号要换行，加了可以在同一行
do
commands
done
```
省略`in list`的时候，循环会操作`$@` 从命令行带入的参数

### select 分支
```
select arg in [list]；# 不加分号要换行，加了可以在同一行
do
commands
done
```
省略`in list`的时候，循环会操作`$@` 从命令行带入的参数

### while 循环
```
while [condition]; # 不加分号要换行，加了可以在同一行
do
commands
done
```

### until 循环
跟`while`循环刚好相反，这里是条件为真的时候不执行了
```
util [condition-is-true]; # 不加分号要换行，加了可以在同一行
do
commands
done
```

### 循环嵌套

### 循环控制
- `break [pos]` 不带参数只能退出最内层循环，带参数可以跨层级
- `continue [pos]` 不带参数只能退出最内层循环，带参数可以跨层级

## Here Document
一种特殊的处理内容的方式，使用I/O重定向的方式来将一个命令序列传递到一个交互程序或者命令当中，两个边界符中间的就是命令序列
```
COMMAND <<LIMITSTRING
...
...
LIMITSTRING

```

输出多行文本，可以有效地避免文本中出现的双引号类的字符，用`<<-` 来标记Here Document 可以抑制文本中的tab，增加输出的可读性

Here Document支持参数和命令替换，也可以给消息体传递不同的参数，但期间的内容会原样输出。如果将LIMITSTRING引用（加上引号）或者转义，会禁用参数替换。

同样的的也可以将Here Document的输出保存到变量中`$(command <<HERE ... HERE)`

Here Document可以配合`:` 做一个假命令，实际上是创建了一个匿名的Here Document，这种方式也可以用来作为块命令的注释，块注释的快捷方式，避免每行加`#`，当然同理，也可以用来作为代码的块注释。

### Here String
```
COMMAND <<<"$String"
```

## I/O 重定向
### 重定向符
- `>` 输出重定向，覆写
- `>>` 输出重定向，追加
- `<` 输入重定向，覆写
- `<<` 输入重定向， 追加
- `/dev/null`  `/dev/zero`常用的不处理的重定向位置
### 关闭描述符
- `n<&-` 关闭输入文件描述符
- `0<&-` `<&-` 关闭stdin
- `n>&-` 关闭输出文件描述符
- `1>&-` `>&-` 关闭stdout

### 使用`exec`

### 代码块重定向 
配合`exec`，或者直接重定向 `while` `untile` `for` `if-then`等等

## 正则表达式
### 基础语法
- `*` 匹配0到多次
- `?` 匹配0次或1次，至多1次
- `+` 匹配至少一次
- `{}`匹配次数定义 "[0-9]\{5\}" 匹配五个数字
- `.` 匹配一个除换行符之外的字符
- `^` 匹配行首
- `$` 匹配行尾
- `[]` 匹配其中的某一个字符
- `\` 转义字符
- `()` 一组正则表达式 
- `|` 或操作
#### 特殊POSIX字符类
- `[:class:]` 通用格式
- `[:alnum:]` 字母和数字
- `[:alpha:]` 字母
- `[:blank:]` 一个空格或一个制表符
- `[:cntrl:]` 匹配控制字符
- `[:digit:]` 匹配数字 0-9
- `[:graph:]` 可打印字符，ASCII码范围在33-126之间
- `[:lower:]` 小写字母 a-z
- `[:print:]`可打印字符 包括空格32
- `[:space:]` 匹配空白符（空格和水平制表符）
- `[:upper:]` 匹配大写字母 A-Z
- `[:xdigit:]` 匹配16进制数字 0-9A-Fa-f

## 常见的坑
- 将保留字或特殊字符声明为变量名
- 使用连字符或其他保留字符来做变量名
- 让变量名和函数名相同，是脚本可读性变得很差
- 不合时宜的空白字符，跟其他语言不一样
- 大括号包含的代码块中，最后一条命令没有分号结尾
- 假定未初始化的变量值为0（其实为null）
- 混淆测试符号`=` 和 `-eq` `=`用来比较字符变量，`-eq`用来比较整数
- 误用了字符串比较当做整数比较`>` `<` 应该用`-gt` `-lt`
- 脚本设置的权限可能用户不具备相应的执行权限
- Here Document结尾没有顶格写
- 变量作用域，父进程不能访问子进程的变量，父Shell不能访问子Shell的变量
- 脚本中使用suid 非常危险，非常危险，危险

## 编程风格
- 习惯性注释
- 避免魔法数字，写死的字符常量，一改全改
- 变量和函数起有意义的名字
- 退出码采用系统有意义的命名方式
- 脚本调用使用标准化的参数标识
- 将复杂脚本拆分为简单的模块，使用合适的函数来实现模块的功能
- 可以简单表达就不用复杂表达

# 脚本调试
- 使用`echo` 打印
- 使用过滤器 `tee`来检查临界点上的进程或数据流
- 使用命令选项
	- 设置 `-n` 选项 只检查语法错误，等价于`set -n` 或`set -o noexec`
	- 设置 `-v` 选项 在脚本之前打印出每一个命令，等价于`set -v`或`set -o verbose`
	- 设置 `-x` 选项 打印出每个命令执行的结果，但只使用缩写形式，等价于 `set -x` 或 `set -o verbose`
	- 设置 `-u` 选项，会在试图使用未声明变量的地方给出提示信息，等价于 `set -u` 或`set -o xtrace`
	- 更多选项可通过`bash -c "help set"`查看
- 使用 `assert`函数放在脚本临界点上测试变量或条件
- 使用`$LINENO` 和`caller`
- 捕获 `exit` `trap` 命令必须放到脚本的第一个命令的位置上

# 字符串处理
## 字符串截取替换
也可以用`expr` 命令来获取相关的内容
### 包裹变量 `${param}`
- 避免参数后面的字符导致的冲突
- 访问`>=10` 的位置参数

### 获取长度 `${#string}`
- 输出字符串长度

### 子串截取 `${string:position}`
- 从 `position`的位置开始截取


### 截取指定长度子串 `${string:position:length}`
- 从`position`的位置开始截取长度为`length`的

 
###  截取最短匹配`${string#substring}`
- 从前往后，截取最短匹配`substring`的


### 截取最长匹配`${string##substrng}`
-  从前往后，截取最长匹配`substring`的


### 从结尾截取最短匹配`${string%substring}`
- 从后往前，截取最短匹配`substring`的


### 从结尾截取最长匹配`${string%%substrng}`
-  从后往前，截取最长匹配`substring`的


###  替换第一个匹配的子串`${string/substring/replacement}`
- 替换第一个匹配`substring`的为`replacement`


###  替换所有匹配的子串`${string//substring/replacement}`
- 替换所有匹配`substring`的为`replacement`


###  匹配开头则替换`${string/#substring/replacement}`
- 如果`substring`能匹配首部，就替换成`replacement`


### 匹配结尾则替换`${string/%substring/replacement}`
- 如果`substring`能匹配尾部，就替换成`replacement`


## 案例解析

```bash
#!/usr/bin/env bash

hello="this is a hello from code ode"

## 变量的长度
echo ${#hello}

## 从截取从10开始的子串
echo ${hello:10}

## 从10开始截取长度为5的子串
echo ${hello:10:5}

## 从前往后截掉最短的*is的匹配
echo ${hello#*is}

## 从前往后截掉最长的*is的匹配
echo ${hello##*is}

## 从后往前截掉最短的ode*的匹配
echo ${hello%ode*}

## 从后往前截掉最长的ode*的匹配
echo ${hello%%ode*}

## 把第一个is换成are
echo ${hello/is/are}

## 把所有is换成are
echo ${hello//is/are}

## 从前往后匹配this的话就替换成THIS
echo ${hello/#this/THIS}

## 从后往前匹配ode的话就替换成ODE
echo ${hello/%ode/ODE}

```



```bash
#!/usr/bin/env bash

## 处理逗号分隔的字符串
## 把内容复制一遍并输出

## 把第一个参数中的所有逗号替换成空格，便于后面的循环
arg_info=${1//,/ }

result=""
## 
for arg in $arg_info
do
result="$result,$arg$arg"
done

## 去掉第一个逗号并输出
echo ${result#?}
```

# 切换用户权限

在处理 [[前端项目一键部署]] 配置的过程中，[[SSH#免密登录]] 的用户可能是没有相应权限的，但在部署 [[nginx]] 的用户可能是 `root`，部署过程中会导致没有权限，所以需要在执行脚本的过程中切换不同的权限来执行。

## Linux权限相关说明

## 切换用户权限的方式
### 切换权限相关命令
#### su （substitute user identity）
默认情况下只有`admin`和`wheel`用户组下的用户才可以切换到UID 0(也就是`root`)
- `-` / `-l`/`--login` 模拟登录流程，更改环境变量和家目录
	-  保留`HOME`,`SHELL`,`PATH`,`TERM` 和 `USER`，其他环境变量会丢弃
- `-m` 不改变环境变量

```
su -l test # 登录test 用户

su - test # 登录test 用户

su -      # 登录 root用户

su        # 登录 root用户

su user -c command # 以user用户执行 command，这个是传给Shell的参数

su user -c `complex command` # 以user用户执行 complex command，这个是传给Shell的参数

```
#### sudo 
让一个有权限的用户去以`root`或者其他用户执行命令
- `-u`（小写) 以 -u 参数指定的用户执行命令（而不是默认的`root`)，不加这个参数默认`root
- `--` 参数分隔，表示后面的参数不是当前命令的参数了 

```
sudo command args # 以root用户权限执行带参数的命令

sudo -u user command args # 以user用户执行带参数的命名

sudoedit -u www /htdocs/index.html # 以www的身份编辑文件

sudo sh -c "command args" # 以root用户执行命令

```

### 修改权限相关
#### chmod 修改文件相关权限
#### chown 修改文件所属关系
### 修改GID/UID/StickyBit

### 直接给对应用户添加`sudo`权限
修改`/etc/sudoers`参照`root`用户的状态，给其他的用户加同样的配置

### 直接新建类似的用户和用户组
比如新增一个`admin`的用户组，然后如上加上`sudo`的权限，然后加入对应用户到这个组里面，获得相应的权限


##  切换权限命令执行特殊处理
### Shell的执行特殊性
如果需要在Shell脚本中来切换用户权限来执行，必须把要执行的命令放入到对应的命令中当做一条命令来执行，比如要切换到`root`然后执行一些命令，这个时候按照命令行一条一条的执行的方式是无法完成的，比如如下脚本：

```bash
#!/bin/sh
if [[ $(type -t cp) == 'alias' ]]; then

unalias cp

fi

export srcdir="/home/sys_su/build_targets/$1"

echo $srcdir

sudo su <<HERE

#srcdir="/home/sys_su/build_targets/$1"

cp -rf $srcdir /usr/share/nginx/html

HERE

alias cp="cp -i"

```
如果把 `HERE`代码包裹段去掉，这个时候就是`sudo su`命令执行后，后面的命令没有效果或者权限失败，因为 `sudo su`是以子进程的方式来执行的，没有保留权限给后续的命令，不是一个执行上下文。所以这里需要通过export 来共享变量，同时给子进程多条命令包裹起来当做和子进程的内容一个整体来执行 。


### 使用`expect` 来做交互式自动化
上面的过程如果切换用户需要密码，那么需要用`expect`来自动输入密码保证流程的无交互化

### 一些特殊的命令需要交互 
比如系统设置了`alias cp='cp -i'` 这个时候就需要先用`unalias`把交互确认的内容去掉；
比如系统禁用了 `rm -rf` 这个时候可能需要用`mv`来移动到一个专门的地方;
不管是`cp` 还是 `mv` 在执行的过程中都需要加`-f`来避免执行Shell脚本的过程中进行交互式确认或者执行失败的情况。



# 命令执行
## 子Shell
命令组会启动子Shell `(command1; command2; command3;)`
大括号中的不会启动子Shell  `{command1; command2; command3}`
## 内建命令
内建命令指的是包含在Bash工具包中的命令，比外部命令执行要快，一方面因为外部命令执行通常需要fork出一个单独的进程来，另一方面是内建命令需要直接访问Shell的内核部分。
一个内建命令通常会和一个系统命令同名，但是Bash内部重新实现的，虽然行为一致，但不是同一个东西。
- `echo`
- `printf`
- `read`
- `cd`
- `pwd`
- `pushd` `popd` `dirs`
- `let`
- `eval`
- `set` `unset`
- `export`
- `declare` `typeset` 
- `readonly`
- `getopts`
- `source` `.`
- `exit`
- `exec`
- `shopt`
- `caller`
- `true` `false`
- `type`  `bash && help type`查看帮助文档
- `hash`
- `bind`
- `help`
- `disown`
- `fg` `bg`
- `wait`
- `suspend`
- `logout`
- `times`
- `kill`
- `killall`
- `command`
- `builtin`
- `enable`
- `autoload`

## 外部命令
### 基本命令
- `ls`
- `cat` `tac`
- `rev`
- `cp`
- `mv`
- `rm` 
- `rmdir` `mkdir`
- `chmod`
- `chattr`
- `ln`
- `man` `info`

### 复杂命令
- `find`
- `xargs`
- `expr`

### 时间/日期
- `date`
- `zdump`
- `time`
- `touch`
- `at`
- `batch`
- `cal`
- `sleep`
- `unsleep`
- `hwclock` `clock`

### 文本处理
- `sort`
- `tsort`
- `uniq`
- `expand` `unexpand`
- `cut`
- `paste`
- `join`
- `grep`
- `look`
- `head` `tail`
- `more` `less`
- `sed` 非交互式流编辑器，在批处理模式下允许使用多个ex命令
- `awk` 可编程的文件提取器和文件格式化工具，结构化的文本文件中，处理或提取特定列表现优异
- `wc`
- `tr`
- `fold`
- `fmt`
- `col` `colrm`
- `column`
- `nl`
- `pr`
- `gettext`
- `msgfmt`
- `iconv`
- `recode`
- `Tex` `gs`
- `enscript`
- `groff` `tbl` `eqn`
- `lex` `yacc` `bison`

### 通讯命令
- `host`
- `ipcalc`
- `nslookup`
-  `dig`
-  `traceroute`
-  `ping`
-  `whois`
-  `finger`
-  `chfn`
-  `vrfy`

#### 远端主机
-  `sx` `rx`
-  `sz` `rz`
-  `ftp`
-  `uucp` `uux` `cu`
-  `telnet`
-  `wget`
-  `lynx`
-  `rlogin`
-  `rsh`
-  `rsync`
-  `ssh`
-  `scp`

#### 本地网络
- `write`
- `netconfig`
- `mail`
- `mainto`
- `vacation`


### 终端控制
- `tput`
- `infocmp`
- `reset`
- `clear`
- `script`

### 数学计算
- `factor`
- `bc`
- `dc`
- `awk` 浮点计算

### 其他命令
- `jot` `seq`
- `getopt`
- `run-parts`
- `yes`  => `yes | rm -r dirname` 与 `rm -rf dirname`效果相同
- `banner`
- `printenv`
- `lp` `lpr`
- `tee` 从管道中抽取数据不影响命令执行流
- `mkfifo`
- `patchk`
- `dd`
- `od`
- `hexdump`
- `objdump`
- `mcookie`
- `units`
- `m4`
- `doexec`
- `dialog`
- `sox`

##  系统与管理命令
### 用户和组
- `users`
- `groups`
- `chown` `chgrp`
- `useradd` `userdel`
- `usermod`
- `groupmod`
- `id`
- `who`
- `w`
- `logname`
- `su`
- `sudo`
- `passwd`
- `ac`
- `last`
- `newgrp`

### 终端类
- `tty`
- `stty`
- `setterm`
- `tset`
- `setserial`
- `gettty` `agetty`
- `mesg`
- `wall`

### 信息和统计类
- `uname` `uname -a`
- `arch`
- `file`
- `lastcomm`
- `lastlog`
- `lsof` 列出打开的文件
- `strace` 跟踪系统调用和信号的诊断和调试工具
- `ltrace`
- `nmap` 网络映射和端口扫描
- `nc` netcat 可以作为诊断和调试工具，也可以作为基于脚本的HTTP客户端和服务器组件
- `free`
- `procinfo`
- `lsdev`
- `du` 磁盘文件的使用情况
- `df` 文件系统的使用情况
- `dmesg` 系统消息输出端stdout
- `stat` 显示文件的详细信息
- `vmstat` 虚拟内存统计信息
- `netstat` 网络统计情况和信息，访问 `/proc/net`下的信息
	- `netstat -r` 等价于 `route`
- `uptime`
- `hostname`
- `hostid`
- `sar` 系统活动报告
- `readelf` elf 格式的二进制文件信息
- `size`
### 系统日志
- `logger`
- `logrotate`

### 作业控制
- `ps`
- `pgrep` `pkill`
- `pstree`
- `top`
- `nice`
- `nohup`
- `pidof`
- `fuser` 获取一个正在访问某个或某些文件的进程ID `-k` 会kill这些进程
- `cron`

### 启动和进程控制
- `init`
- `telinit`
- `runlevel`
- `halt` `shutdown` `reboot`
- `service`

### 网络类
- `ifconfig`
- `iwconfig`
- `route`
- `chkconfig`
- `tcpdump`

### 文件系统
- `mount` `unmount`
- `sync`
- `losetup`
- `mkswap`
- `swapon` `swapoff`
- `mke2fs`
- `tune2fs`
- `dump2fs`
- `hdparm`
- `fdisk`
- `fsck` `e2fsck` `debugfs`
- `badblocks`
- `lsusb` `usbmodules`
- `lspci`
- `mkbootdisk`
- `chroot`
- `lockfile`
- `flock`
- `mknod`
- `MAKEDEV` 必须是`root` 在 `/dev`下执行
- `tmpwatch`

### 备份
- `dump` `restore`
- `fdformat`

### 系统资源
- `ulimit`
- `quota`
- `setquota`
- `umask`
- `rdev`

### 模块类
- `lsmod`
- `insmod`
- `rmmod`
- `modprobe`
- `depmod`
- `modinfo`

### 其他
- `env` `#!/usr/bin/env`
- `ldd` 显示可执行文件和它需要的共享库之间的依赖关系
- `watch`
- `strip`
- `rdist`