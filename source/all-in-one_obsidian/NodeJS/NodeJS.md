# 执行[[Shell脚本]]
## 原生支持的执行
每一种有权限的语言环境都可以调用系统相关的命令，NodeJS中也不例外，可以通过 `child_process`中的方法来执行[[Shell脚本]]，比如:
- `child_process.exec`
- `child_process.execFile`
- `child_process.fork`
- `child_process.spawn`
- `child_process.execFileSync`
- `child_process.execSync`
- `child_process.spawnSync`

`exec`方法和`exec`系统调用命令不同，它不会替换现有进程使用一个Shell来执行命令，`execFile`方法默认不新开一个Shell，执行的file是直接作为新进程来执行（比`exec`更高效）

`fork` 是`spawn`的一个特殊情况，它默认会新开NodeJS 进程来执行，同时跟`spawn`一样，返回子进程对象。跟`fork`系统调用不一样，`fork`方法不会复制当前进程。

## 第三方 npm包 `shelljs`
包下面实现了Shell命令对应的方法， 可以直接用NodeJS函数调用的方式进行命令执行。可直接查看源码`shelljs/shelljs`来查看实现。

直接通过NodeJS调用函数的方式来操作就行了，看官方的Demo:
```javascript
var shell = require('shelljs');

if (!shell.which('git')) {
  shell.echo('Sorry, this script requires git');
  shell.exit(1);
}

// Copy files to release dir
shell.rm('-rf', 'out/Release');
shell.cp('-R', 'stuff/', 'out/Release');

// Replace macros in each .js file
shell.cd('lib');
shell.ls('*.js').forEach(function (file) {
  shell.sed('-i', 'BUILD_VERSION', 'v0.1.2', file);
  shell.sed('-i', /^.*REMOVE_THIS_LINE.*$/, '', file);
  shell.sed('-i', /.*REPLACE_LINE_WITH_MACRO.*\n/, shell.cat('macro.js'), file);
});
shell.cd('..');

// Run external tool synchronously
if (shell.exec('git commit -am "Auto-commit"').code !== 0) {
  shell.echo('Error: Git commit failed');
  shell.exit(1);
}
```


## 融合[[Shell脚本]]  `zx`
比起`shelljs`来，`zx`不但包装NodeJS的执行能力，还在语言层面做了更多的内容，可以认为是JavaScript语言的超集，扩展了[[Shell脚本]]的支持，同时是兼容运行在NodeJS的平台上。源码在`google/zx`上。

为了使用 ES Module的各种特性，文件后缀需要`.mjs`，这个是NodeJS对ES Module的支持的方式

### 语法规则
- 第一行类似于 Shell 的shebang `#! /usr/bin/env zx`
- 执行方式`chmod +x ./script.mjs && ./script.mjs` 或者 `zx ./script.mjs`
- $`command` 用NodeJS 的`child_process.spawn` 执行给定的命令字符串，并返回ProcessPromise\<PocessOutput\> 
- `$` 引用的命令从`process.stdin` 中读取输入，也可以写入到子进程中
- `pipe()` 用来处理重定向到`stdout`，类似于Shell中的`|`
- `${}` 中的参数可以不用加引号，需要的话会自动加
- 非零的返回值会抛出异常，需要捕获处理
- 解析环境变量，数组值，
- 全局函数可以直接用
	- `cd`
	- `fetch`
	- `question` 交互录入
	- `sleep`
	- `nothrow`
- 全局包可以直接用
	- `chalk`
	- `fs`
	- `globby`
	- `os`
	- `path`
	- `minimist`
- 全局配置
	- `$.shell`
	- `$.prefix`
	- `$.quote`
	- `$.verbose`



### 有意思的特性 `zx test.md`
- 支持用`Markdown`写代码，可以执行`.md`文件
	- 只有代码块中的代码才会被执行
	- 可以使用动态`import()`
	- 也可以执行`bash` 或`sh`标记的代码

- 支持用TS写代码，然后用`ts-node`执行
- `zx` 可以执行远端脚本 `zx http://xxx`
- 支持从`stdin`输入脚本，为了避免不必要的解析，这里需要引用LIMITSTRING(`'EOF'`)
```shell
zx <<'EOF'
await $`pwd`
EOF
```
- polifills
	- ESM 中没有的 `__filename` 和 `__dirname`补齐
	- ESM 中没有的 `require`补齐


# 操作[[nginx]]配置文件
使用 npm 包`nginx-conf` 可以直接通过`tmont/nginx-conf`查看源代码