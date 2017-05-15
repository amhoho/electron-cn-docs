# 本文介绍:BrowserWindowProxy类(子窗口的控制与应用)

>操纵子窗口

进程:[渲染进程](../glossary.md#渲染进程)              

使用 `window.open` 创建一个新窗口时,会自动创建一个 `BrowserWindowProxy`  的实例将返回一个标识,可通过标识对这个新窗口进行少量功能的控制.

## 实例方法

`BrowserWindowProxy`对象具有以下实例方法:

### `win.blur()`
> 用途:**从子窗口中去焦**

### `win.close()`
> 用途:**强制关闭子窗口,而不调用其卸载事件(unload event)**

### `win.eval(code)`
> 用途:**eval子窗口中的代码**

* `code`String

### `win.focus()`
> 用途:**聚焦子窗口(即窗口置顶)**

### `win.print()`
> 用途:**调用子窗口上的打印对话框**

### `win.postMessage(message,targetOrigin)`
> 用途:**调通过指定位置 或 用 `*` 来代替不明位置 向父窗口发送信息**
* `message` String
* `targetOrigin` String

除了这些方法,子窗口还可以无特性和使用单一方法来实现  `window.opener` 对象.

## 实例属性
`BrowserWindowProxy`对象具有以下实例属性:

### `win.closed`
> 用途:**关闭子窗口后恢复为 `true`**  