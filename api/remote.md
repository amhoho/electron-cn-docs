# 本文介绍:从渲染器进程通信至主进程

> `remote`模块是一种渲染器进程(网页)和主进程之间通信(IPC)的简单方法。

进程: [渲染进程](../glossary.md#渲染进程)              

在Electron中,GUI相关模块(例如 `dialog`, `menu`等)只能用在主进程而非渲染器进程中使用。

为了从渲染器进程使用它们, `ipc`模块是向主进程发送进程间消息所必需的。

 `remote`模块可以调用主进程对象的方法,而类似于Java的[RMI][rmi]无需显式地发送进程间消息。

从渲染器进程创建浏览器窗口的示例:

```JavaScript
const {BrowserWindow} = require('electron').remote
let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('https://github.com')
```

 **注意:** 对于反向操作(从主进程访问渲染进程),您可以使用[webContents.executeJavascript](web-contents.md#contentsexecutejavascriptcode-usergesture-callback).

## 远程对象

 `remote`模块返回的每个对象(包括函数)都代表主进程中的一个对象(我们称之为远程对象或远程函数)。
 
当 调用远程对象的方法 ,执行远程函数 或使用远程构造器(函数)创建新对象 时,其实就是在发送同步的进程间消息。

在上面的例子中, `BrowserWindow` 和 `win` 都是远程对象,但 `new BrowserWindow` 并不是在渲染进程中创建 `BrowserWindow` 对象,而是主进程中创建了 `BrowserWindow` 对象,并返回了对应的远程对象( 即 `win` 对象)到渲染进程。

**注意:**可通过 `remote` 访问的只有首次引用远程对象时出现的[可枚举属性][enumerable-properties].       

**注意:**当通过 `remote`模块访问时,数组和缓冲区复制在IPC上。在渲染器进程中修改它们不会在主进程中修改它们，反之亦然。

## 远程对象的生命周期

只要远程对象在渲染进程中生存(换句话说,就是没有被垃圾收集),相应的对象在主进程中就不会被释放。

当远程对象被垃圾回收时,主进程中的对应对象才会被取消引用。

如果远程对象在渲染器进程中泄漏(例如存储在映射中但从不释放),则主过程中的相应对象也将被泄露,因此您千万不要泄漏远程对象。不过,像字符串和数字这样的主值类型是通过副本发送的。

## 将回调传递给主进程

您应该非常小心使用此功能,因为主进程中的代码可以接受来自渲染器的回调 - 例如 `remote`模块。

首先:为了避免死锁,传递给主进程的回调函数会进行异步调用。所以不能期望主进程获得传递的回调的返回值。

比如,你不能主进程中给 `Array.map`传递来自渲染器进程的函数:

```JavaScript
// 主进程 mapNumbers.js
exports.withRendererCallback = (mapper) => {
  return [1, 2, 3].map(mapper)
}

exports.withLocalCallback = () => {
  return [1, 2, 3].map(x => x + 1)
}
```


```JavaScript
// 渲染器进程
const mapNumbers = require('electron').remote.require('./mapNumbers')
const withRendererCb = mapNumbers.withRendererCallback(x => x + 1)
const withLocalCb = mapNumbers.withLocalCallback()

console.log(withRendererCb, withLocalCb)
// [undefined, undefined, undefined], [2, 3, 4]
```

如你所见,渲染器回调函数的同步返回值没有按预期产生,并且不匹配主进程中存在的相同回调的返回值。

其次,传递给主进程的函数会持续到主进程对他们进行垃圾回收。

例如,下面的代码第一眼看上去毫无问题。它为远程对象上的`close`事件绑定了一个回调函数:

 ```JavaScript
require('electron').remote.getCurrentWindow().on('close', () => {
  // window was closed...
})
```

但是记住回调被主进程引用，直到你明确地卸载它。如果不卸载,每次重新载入窗口都会重新安装,这样每次重新启动时会丢失一个回调。

更严重的是,由于以前安装的回调的上下文已经释放，所以当主进程的 `close` 事件触发的时候,会抛出异常。

为了避免这个问题,请确保清除对渲染器进行回调并传递给主进程的任何引用。可以清理事件处理程序,或者明确告诉主进程取消来自正在退出的渲染器进程中的回调。

## 访问主进程中的内置模块

在主进程中的内置模块已经被添加为 `remote`模块中的属性,所以可以直接像使用 `electron`模块一样直接使用它们。
```JavaScript
const app = require('electron').remote.app
console.log(app)
```

## 方法

### `remote.require(module)`
> 用途:**获取主进程中由 `require(module)` 返回的对象( `any`),并且模块如果是通过相对路径进行指定,则由相对于主进程的入口点进行解析**

例如:

```
project/
├── main
│   ├── foo.js
│   └── index.js
├── package.json
└── renderer
    └── index.js
```

```JavaScript
// 主进程: main/index.js
const {app} = require('electron')
app.on('ready', () => { /* ... */ })
```

```JavaScript
// 相对模块例子: main/foo.js
module.exports = 'bar'
```

```JavaScript
// 渲染进程: renderer/index.js
const foo = require('electron').remote.require('./foo') // bar
```


### `remote.getCurrentWindow()`
> 用途:**获取此网页的当前窗口([BrowserWindow](browser-window.md))**

### `remote.getCurrentWebContents()`
> 用途:**获取此网页的网页内容([WebContents](web-contents.md))**

### `remote.getGlobal(name)`
> 用途:**获取主进程中名为 `name` 的全局变量(如 `global[name]`)**

* `name` String

## 属性

### `remote.process`
> 属性:**主进程中的 `process` 对象**

该属性等同于 `remote.getGlobal('process')` 但是有缓存。

[rmi]: http://en.wikipedia.org/wiki/Java_remote_method_invocation
[enumerable-properties]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties