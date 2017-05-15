# 本文介绍: Debugger类(调试工具)

> 用于Chrome远程调试协议的替代传输。

进程: [主进程](../glossary.md#主进程)       

Chrome调试工具在JavaScript运行时具有[特殊绑定][rdp]，允许与页面交互并对其进行检测。    
```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()

try {
  win.webContents.debugger.attach('1.1')
} catch (err) {
  console.log('Debugger attach failed : ', err)
}

win.webContents.debugger.on('detach', (event, reason) => {
  console.log('Debugger detached due to : ', reason)
})

win.webContents.debugger.on('message', (event, method, params) => {
  if (method === 'Network.requestWillBeSent') {
    if (params.request.url === 'https://www.github.com') {
      win.webContents.debugger.detach()
    }
  }
})

win.webContents.debugger.sendCommand('Network.enable')
```

### 实例方法

#### `debugger.attach([protocolVersion])`
> 用途:**将调试器附加到 `webContents`**

* `protocolVersion`  String(可选) - 请求的调试协议版本。

#### `debugger.isAttached()`
> 用途:**判断调试器是否已附加到`webContents`**

#### `debugger.detach()`
> 用途:**从`webContents`里分离调试器**

#### `debugger.sendCommand(method[, commandParams, callback])`
> 用途:**发送给定命令到调试目标**

* `method` String - 方法名, 名称通过远程调试协议定义.
* `commandParams` Object (可选) -带请求参数的JSON对象。
* `callback` Function (可选) - 响应方法
  * `error` Object - 错误后的指示命令
  * `result` Any -  由远程调试协议中的命令描述的“returns”属性定义的响应。

### 实例事件

#### 事件: 'detach'
> 触发:**在 `webContents`关闭 或 `webContents` 调用调试工具时**

* `event` Event
* `reason` String -分离调试器的原因。


#### 事件: 'message'
> 触发:**调试提交报告时**

* `event` Event
* `method` String - 方法名
* `params` Object - 由远程调试协议中的 `parameters` 属性定义的事件参数。

[rdp]: https://developer.chrome.com/devtools/docs/debugger-protocol
[`webContents.findInPage`]: web-contents.md#contentsfindinpagetext-options