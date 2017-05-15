# REPL
读取(Read)-运算(Eval)-输出(Print)-循环(Loop) (REPL) 是很简单的交互式计算机编程环境，它采用单个用户输入，运算并返回结果给用户。 `repl`模块提供了可按照以下方式访问的REPL：

* 如果你的 `electron` 或 `electron-prebuilt` 已经安装为本地项目依赖项:
```sh
  ./node_modules/.bin/electron --interactive
```
  
* 如果你的 `electron` 或 `electron-prebuilt` 已经安装为全局方式:

```sh
  electron --interactive
```

这里只会为主进程创建一个REPL。 您可通过调试器控制台获取渲染器进程的REPL。

**注意:** `electron --interactive` 在 Windows 上不可用. 更多细节详见[Node.js REPL docs](https://nodejs.org/dist/latest/docs/api/repl.html).