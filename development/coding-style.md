# 编码规范

> 以下是 Electron 项目的编码规范,您可以运行 `npm run lint`来显示 `cpplint`和 `eslint`检测到的任何规范问题

## C++ 和 Python

对于C ++和Python，我们遵循Chromium的[编码风格](http://www.chromium.org/developers/coding-style). 您可以使用[clang-format](clang-format.md)自动格式化C ++代码.也可以使用 `script/cpplint.py` 来检验文件是否符合要求.

我们目前使用的 Python 版本是 Python 2.7.

C++ 代码中用到了许多 Chromium 中的接口和数据类型，因此建议你能熟悉它们.Chromium  中的[重要接口和数据结构](https://www.chromium.org/developers/coding-style/important-abstractions-and-data-structures)就是一篇不错的入门文档，里面提到了一些特殊类型、域内类型(退出作用域时自动释放内存)、日志机制，等等.

## JavaScript

* 书写 [标准的JavaScript代码风格](http://npm.im/standard).             


* 文件名使用 `-` 而不是 `_` 来连接单词，比如 `file-name.js` 而不是 `file_name.js`，这是沿用 [github/atom](https://github.com/github/atom) 模块的命名方式(`module-name`).这条规则仅适用于 `.js` 文件.
* 在适当的地方使用较新的ES6/ES2015语法
  * [`const`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const) - 规定的或其他常量
  * [`let`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let) - 用于定义变量
  * [Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)- 而不是`function(){}`
  * [Template literal](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) - 而不是字符串连接使用`+`

## 命名方式

Electron API使用与Node.js相同的大小写方案：

- 当模块是一个类似 `BrowserWindow`的类，用 `CamelCase`.
- 当模块是一组类似 `globalShortcut`的API，用 `mixCase`.
- 当API是一个复杂的对象属性,例如 `win.webContents`, 用 `mixedCase`.
- 非模块API则使用自然标题，如 `<webview> Tag` 或 `Process Object`.

当新建一个 API 时，我们倾向于使用 getters 和 setters 而不是 jQuery 单函数的命名方式，比如 `.getText()` 和 `.setText(text)` ,而不是 `.text([text])`.这里有关于该规则的[讨论记录](https://github.com/electron/electron/issues/46).