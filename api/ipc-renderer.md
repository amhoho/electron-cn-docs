# 本文介绍: ipcRenderer(从渲染进程发送消息至主进程)
> 从渲染进程到主进程的异步通信

进程: [渲染进程](../glossary.md#渲染进程)            

 `ipcRenderer`模块是[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)类的一个实例。
 
 它提供了几个方法，所以你可以从渲染进程(网页)发送同步和异步消息到主进程。您还可以从主流程接收回复。
  
## 事件方法

### `ipcRenderer.on(channel, listener)`
> 用途:**监听 `channel`,并调用 `listener(event, args...)` 处理新消息**

* `channel` String
* `listener` Function

### `ipcRenderer.once(channel, listener)`
> 用途:**一次性监听 `channel`,当调用 `listener(event, args...)` 处理新消息后删除监听**

* `channel` String
* `listener` Function - 一次性的 `listener` 方法.

### `ipcRenderer.removeListener(channel, listener)`
> 用途:**从指定 `channel` 的监听数组中删除特定的 `listener`**

* `channel` String
* `listener` Function

### `ipcRenderer.removeAllListeners([channel])`
> 用途:**删除所有监听,或指定 `channel` 的所有监听**

* `channel` String (可选)

### `ipcRenderer.send(channel[, arg1][, arg2][, ...])`
> 用途:**通过 `channel` 向主进程异步发送消息或任意参数**

* `channel` String
* `...args` any[]

参数将在JSON内部序列化，因此不会包含函数或原型链。主进程通过用ipcMain模块侦听 `channel`来处理它。

### `ipcRenderer.sendSync(channel[, arg1][, arg2][, ...])`
> 用途:**通过 `channel` 向主进程同步发送消息或任意参数**

* `channel` String
* `...args` any[]

参数将在JSON内部序列化，因此不会包含函数或原型链。主进程通过用ipcMain模块侦听 `channel`来处理它，并通过设置 `event.returnValue` 来回复。

**注意:**务必明确的一点是发送同步消息将阻止整个渲染进程。

### `ipcRenderer.sendToHost(channel[, arg1][, arg2][, ...])`
> 用途:**通过 `channel` 向主机页面(host page)的 `<webview>` 元素发送消息或任意参数**

* `channel` String
* `...args` any[]

类似 `ipcRenderer.send` ,但是事件是发往主机页面(host page)的 `<webview>` 元素,而不是主进程.