# 本文介绍: ipcMain(主进程中回复或接收渲染进程发送的消息)
> 在主进程中处理由渲染进程发起的异步通信.

进程: [主进程](../glossary.md#主进程)    

 `ipcMain` 模块是类[EventEmitter](https://nodejs.org/api/events.html)类的一个实例.
 
浅显的打个比方,渲染进程给主进程挂个号,这里就开始忙活起来.当然,你也可以从主进程中向渲染进程发送消息.

## 发送消息
如果从主进程向渲染进程发送消息,请查看 [web-contents-send](web-contents.md#contentssendchannel-arg1-arg2-)

* 发送消息,事件名为 `channel`.
* 回应同步消息, 请设置 `event.returnValue`.
* 回应异步消息, 请使用 `event.sender.send(...)`.

在主进程和渲染进程之间发送和处理消息的例子:
```JavaScript
// 主进程中
const {ipcMain} = require('electron')
ipcMain.on('asynchronous-message', (event, arg) => {
  console.log(arg)  // 输出 `ping`
  event.sender.send('asynchronous-reply', 'pong')
})

ipcMain.on('synchronous-message', (event, arg) => {
  console.log(arg)  // 输出 `ping`
  event.returnValue = 'pong'
})
```

```JavaScript
// 渲染进程中(即网页).
const {ipcRenderer} = require('electron')
console.log(ipcRenderer.sendSync('synchronous-message', 'ping')) // 输出 `pong`

ipcRenderer.on('asynchronous-reply', (event, arg) => {
  console.log(arg) // 输出 `pong`
})
ipcRenderer.send('asynchronous-message', 'ping')
```

## 监听事件

### `ipcMain.on(channel, listener)`
> 用途:**监听 `channel`,并调用 `listener(event, args...)` 处理新消息**

* `channel` String
* `listener` Function

### `ipcMain.once(channel, listener)`
> 用途:**一次性监听 `channel`,当调用 `listener(event, args...)` 处理新消息后删除监听**

* `channel` String
* `listener` Function - 一次性的 `listener`.

### `ipcMain.removeListener(channel, listener)`
> 用途:**从指定 `channel` 的监听数组中删除特定的 `listener`**

* `channel` String
* `listener` Function

### `ipcMain.removeAllListeners([channel])`
> 用途:**删除所有监听,或指定 `channel` 的所有监听**

* `channel` String (可选)

## 事件对象
传递给 `callback` 的 `event` 对象有如下方法:

### `event.returnValue`
将此设置成同步消息中返回的值.

### `event.sender`
返回发送消息的 `webContents` ,你可以调用 `event.sender.send`  来回复异步消息,详见[webContents.send][web-contents-send].

