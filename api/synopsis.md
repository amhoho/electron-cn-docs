# 本文介绍:开发概要
> 如何使用Node.js和Electron API.

所有的[Node.js的内置模块](https://nodejs.org/api/)都可以完美运用于Electron和第三方Node模块中(包括[native modules](../tutorial/using-node-node-modules.md))。

Electron还为开发本地桌面应用程序提供了一些额外的内置模块。有些仅在主进程中或渲染器进程(网页)中可用,有的则通用于两个进程。

基本规则:GUI模块或者系统底层的模块只能在主进程中使用。

要使用这些模块,你必须熟稔[主进程vs渲染进程](../tutorial/quick-start.md#main-process)。           

像普通Node.js一样的主进程脚本:
```JavaScript
const {app, BrowserWindow} = require('electron')
let win = null

app.on('ready', () => {
  win = new BrowserWindow({width: 800, height: 600})
  win.loadURL('https://github.com')
})
```

渲染进程和传统的web界面一样,除了它具有使用node模块的能力:

```html
<!DOCTYPE html>
<html>
<body>
<script>
  const {app} = require('electron').remote
  console.log(app.getVersion())
</script>
</body>
</html>
```

如果想运行应用,请参考 [运行应用程序](../tutorial/quick-start.md#run-your-app).         

## 解构任务
从0.37版本起,你可以使用[解构赋值][destructuring-assignment]更简便的使用内置模块。

```JavaScript
const {app, BrowserWindow} = require('electron')
let win
app.on('ready', () => {
  win = new BrowserWindow()
  win.loadURL('https://github.com')
})
```

只需把模块引入( `require`)并解构至 `electron`中即可使用:
```JavaScript
const electron = require('electron')
const {app, BrowserWindow} = electron

let win

app.on('ready', () => {
  win = new BrowserWindow()
  win.loadURL('https://github.com')
})
```
上面等同下面的代码:
```JavaScript
const electron = require('electron')
const app = electron.app
const BrowserWindow = electron.BrowserWindow
let win

app.on('ready', () => {
  win = new BrowserWindow()
  win.loadURL('https://github.com')
})
```

[gui]: https://en.wikipedia.org/wiki/Graphical_user_interface
[destructuring-assignment]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment