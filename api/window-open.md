# 本文介绍:`window.open`

> 用于打开url或载入url至 `BrowserWindow` ,并对该窗口进行少量功能的控制.

使用 `window.open` 创建一个新窗口时,会自动创建一个 `BrowserWindowProxy`  的实例将返回一个标识,可通过标识对这个新窗口进行少量功能的控制.

不过希望完全的控制这个窗口,请直接创建一个 `BrowserWindow` .新创建的 `BrowserWindow` 默认为继承父窗口的属性参数,想重写属性的话可以在 `features` 中设置它们.

### `window.open(url[, frameName][, features])`
>用途:**新窗口并返回一个`BrowserWindowProxy`类的实例([`BrowserWindowProxy`](browser-window-proxy.md))**

* `url` String
* `frameName` String (可选)
* `features` String (可选) - 字符串遵循标准浏览器的格式,但是每个 `feature`必须是一个 `BrowserWindow`选项的字段。

**Notes:**

* 父窗口中禁用Node时，open的新开窗口中也始终禁用Node。
* 父窗口中禁用JavaScript时，open的新开窗口中也始终禁用JavaScript。
* 父窗口中启动上下文隔离时，open的新开窗口中也始终启动上下文隔离。

 `features` 中指定的不由Chromium或Electron处理的非标准特性将传递给 `additionalFeatures`参数中的任何已注册的 `webContent` 的 `new-window`新窗口事件处理程序。

### `window.opener.postMessage(message, targetOrigin)`
>用途:**向指定位置或用 `*` 来代替没有明确位置来向父窗口发送消息**

* `message` String
* `targetOrigin` String