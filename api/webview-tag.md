# `<webview>` 标签
>在隔离的框架和过程中显示外部Web内容。

进程: [渲染进程](../glossary.md#渲染进程)              

 `webview` 标签可将 `guest` 内容（例如外部网页）嵌入到应用中. 它不像 `iframe` , `webview` 和你的应用运行的是不同的进程. 

它不具有与您的网页相同的权限，为确保应用不受嵌入内容的影响,应用和嵌入内容之间的交互全部都是异步的.

## 使用例子

 `webview`嵌入范例:

```html
<webview id="foo" src="https://www.github.com/" style="display:inline-flex; width:640px; height:480px"></webview>
```

如果你要控制 `guest` 内容,你可以使用下文的 `webview`事件监听或方法进行响应.

下面是个loading条示例,它在页面开始加载时进行了监听,显示了 `loading`提示,加载完成后则停止继续监听:
```html
<script>
  onload = () => {
    const webview = document.getElementById('foo')
    const indicator = document.querySelector('.indicator')

    const loadstart = () => {
      indicator.innerText = 'loading...'
    }

    const loadstop = () => {
      indicator.innerText = ''
    }

    webview.addEventListener('did-start-loading', loadstart)
    webview.addEventListener('did-stop-loading', loadstop)
  }
</script>
```

## CSS样式的两个注意点

 第一点: 不要轻易覆盖 `webview`的`display:flex`
 
 `webview`默认使用 `display:flex`来确保内部元素填充的宽高。 除非你为内联布局指定了 `display:inline-flex`,否则别覆盖默认的 `display:flex` CSS属性。
 
第二点:隐藏webview的正确方式

不要用 `hidden`属性或使用 `display:none`来隐藏  `webview`,则会导致内部 `browserplugin`对象出现异常.同时,当webview被取消隐藏时，web页面实际上是进行重新加载而不是如 `display:block`样式呈现的可见.

建议的方式是将 `webview`的宽高设置为零，并通过`flex`将元素缩小到0px尺寸,示例:

```html
<style>
  webview {
    display:inline-flex;
    width:640px;
    height:480px;
  }
  webview.hide {
    flex: 0 1;
    width: 0px;
    height: 0px;
  }
</style>
```

## 标签属性

`webview`标签具有以下属性：

### `src`
>属性:**加载到 `webview` 的 `src` 还支持data协议,比如 `data:text/plain,Hello, world!`**

```html
<webview src="https://www.github.com/"></webview>
```

### `autosize`
>属性:**`on`表示 `webview` 在 `minwidth`, `minheight`, `maxwidth`, 和 `maxheight`这些值范围内自适应尺寸**

```html
<webview src="https://www.github.com/" autosize="on" minwidth="576" minheight="432"></webview>
```
 
### `nodeintegration`
>属性:**`on`表示 `webview`中的guest page 将整合 Node，并且拥有可以使用如 `require` 和 `process`等系统底层资源**

```html
<webview src="http://www.google.com/" nodeintegration></webview>
```

### `plugins`
>属性:**`on`表示 `webview`中的访客页面将能够使用浏览器插件。默认情况下禁用插件**

```html
<webview src="https://www.github.com/" plugins></webview>
```

### `preload`
>属性:**其它脚本运行之前预先加载指定脚本**

```html
<webview src="https://www.github.com/" preload="./test.js"></webview>
```

脚本的URL协议必须是 `file:` 或 `asar:`,因为它是由 `require` 加载到访客页面 无论页面是否集成Node，此脚本都可以访问所有Node API，但Node插入的全局对象会在此脚本完成执行后被删除。

### `httpreferrer`
>属性:**设置访客页面的的引荐来源页**

```html
<webview src="https://www.github.com/" httpreferrer="http://cheng.guru"></webview>
```

### `useragent`
>属性:**在导航到页面之前设置访客页面的用户代理**

```html
<webview src="https://www.github.com/" useragent="Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko"></webview>
```

页面被加载后,可使用 `setUserAgent` 方法改变 `useragent`。

### `disablewebsecurity`
>属性:**`on`表示允许 `webview`中的访客页面将能够禁用Web安全。默认情况下启用Web安全**

```html
<webview src="https://www.github.com/" disablewebsecurity></webview>
```

### `partition`
>属性:**设置页面使用的session**

```html
<webview src="https://github.com" partition="persist:github"></webview>
<webview src="http://electron.atom.io" partition="electron"></webview>
```

如果 `partition`以 `persist:`前缀,该页面将使用一个持久会话,该会话可用于应用程序中具有相同 `partition`的所有页面。

如果 `partition`没有 `persist:`前缀,该页面将使用内存中会话。

如果 `partition`为空,那么将返回应用程序的默认会话。

因为活动渲染器进程的会话不能更改,所以此值只能在第一个导航之前进行修改。此后尝试修改将会失败并抛出DOM异常。

### `allowpopups`
>属性:** `on`表示允许访客页面打开新窗口。默认情况下禁用弹出窗口**

```html
<webview src="https://www.github.com/" allowpopups></webview>
```

### `webpreferences`
>属性:**在webview上 进行设置的 `web首选项` 的字符串列表，用 `,`分隔**

```html
<webview src="https://github.com" webpreferences="allowRunningInsecureContent, javascript=no"></webview>
```

支持的首选项字符串的完整列表,详见[BrowserWindow](browser-window.md#new-browserwindowoptions).

字符串格式和 `window.open`中的features字符串一样,每个名称都默认为 `true',允许使用 `=` 设置为另一个值。

其中,特殊值 `yes'和 `1`被解释为 `true`，而 `no`和 `0`被解释为 `false`。

### `blinkfeatures`
>属性:**要启用的 `特性` 的字符串列表，用 `,`分隔**

```html
<webview src="https://www.github.com/" blinkfeatures="PreciseMemoryInfo, CSSVariables"></webview>
```

 `特性` 完整列表,请详见[RuntimeEnabledFeatures.in][blink-feature-string].

### `disableblinkfeatures`
>属性:**要禁用的 `特性` 的字符串列表，用 `,`分隔**

```html
<webview src="https://www.github.com/" disableblinkfeatures="PreciseMemoryInfo, CSSVariables"></webview>
```

 `特性` 完整列表,请详见[RuntimeEnabledFeatures.in][blink-feature-string].

### `guestinstance`
>属性:**将webview链接到实例标识符为指定值(如下文的 `3`)的webContents**

```html
<webview src="https://www.github.com/" guestinstance="3"></webview>
```

webview首次加载的时候，将创建一个guestinstance等于其实例标识符的webContents。

现有的webview会触发 `destroy`事件，然后在加载新的URL时创建一个新的webContents。

### `disableguestresize`
>属性:**调整webview元素尺寸时禁止其内容也跟随调整大小**

```html
<webview src="https://www.github.com/" disableguestresize></webview>
```

通常和[`webContents.setSize`](web-contents.md#contentssetsizeoptions)结合使用,进行调整webview尺寸.

```JavaScript
const {webContents} = require('electron')
//假设`win`是 指向一个包含`disableguestresize`属性的`webview`的`BrowserWindow`实例。
win.on('resize', () => {
  const [width, height] = win.getContentSize()
  for (let wc of webContents.getAllWebContents()) {
//检查`wc'是否属于win 中的webview。
    if (wc.hostWebContents &&
        wc.hostWebContents.id === win.webContents.id) {
      wc.setSize({
        normal: {
          width: width,
          height: height
        }
      })
    }
  }
})
```

## 方法
 `webview` 标签有以下方法：
 **注意：** webview元素加载后才可以使用下列使用方法
 
```JavaScript
const webview = document.getElementById('foo')
webview.addEventListener('dom-ready', () => {
  webview.openDevTools()
})
```

### `<webview>.loadURL(url[, options])`
 > 用途:**加载 `url`到这个webview中,  `url` 必须指定协议如 `http://` 或 `file://`**

* `url` URL
* `options` Object (可选)
  * `httpReferrer` String (可选) - HTTP引荐来源网址
  * `userAgent` String (可选) - 发起请求的用户代理
  * `extraHeaders` String (可选) - 用`\ n`分隔的额外头
  * `postData` ([UploadRawData](structures/upload-raw-data.md) | [UploadFile](structures/upload-file.md) | [UploadFileSystem](structures/upload-file-system.md) | [UploadBlob](structures/upload-blob.md))[] - (可选)
    * `baseURLForDataURL` String (可选) -  用于由数据URL加载的文件的基本URL(带尾随路径分隔符),只有当指定的`url`是数据url并且需要加载其他文件时,才需要这样做。

### `<webview>.getURL()`
 > 用途:**获得当前页面网址( `String`)**

### `<webview>.getTitle()`
 > 用途:**获得当前页面标题( `String`)**

### `<webview>.isLoading()`
 > 用途:**判断获得当前页面是否仍在加载资源( `Boolean`)**

### `<webview>.isWaitingForResponse()`
 > 用途:**判断获得当前页面是否等待页面主资源的第一次响应( `Boolean`)**

### `<webview>.stop()`
 > 用途:**停止任何待处理的导航**

### `<webview>.reload()`
 > 用途:**重新加载当前网页**

### `<webview>.reloadIgnoringCache()`
 > 用途:**忽略缓存的重新加载当前网页**

### `<webview>.canGoBack()`
 > 用途:**判断页面是否可以可后退至上个网页( `Boolean`)**

### `<webview>.canGoForward()`
 > 用途:**判断页面是否可以前进到下一个网页( `Boolean`)**

### `<webview>.canGoToOffset(offset)`
 > 用途:**判断网页是否可以转到 `offset`这个页面( `Boolean`)**
 
* `offset`String

### `<webview>.clearHistory()`
 > 用途:**清除历史记录**

### `<webview>.goBack()`
 > 用途:**使页面后退往上一页**

### `<webview>.goForward()`
 > 用途:**使页面前进往下一页**

### `<webview>.goToIndex(index)`
 > 用途:**将页面导航到指定索引 `index` 的网页**
 
* `index`String

### `<webview>.goToOffset(offset)`
 > 用途:**从当前页到导航指定 `offset`**
 
* `offset` Integer

### `<webview>.isCrashed()`
 > 用途:**判断渲染器进程是否崩溃( `Boolean`)**


### `<webview>.setUserAgent(userAgent)`
 > 用途:**重定义网页的userAgent( `Boolean`)**
 
* `userAgent` String

### `<webview>.getUserAgent()`
 > 用途:**获取网页的userAgent( `String`)**

### `<webview>.insertCSS(css)`
 > 用途:**将CSS插入到当前网页中**
 
* `css` String

### `<webview>.executeJavaScript(code, userGesture, callback)`
 > 用途:**判断是否可解析(eval)执行代码`code`并返回执行 `promise`解析结果( `Promise`)**
 
* `code` String
* `userGesture` Boolean (可选)  - 是否支持用户手势 默认为 `false`.
* `callback` Function (可选) - 脚本执行后调用
  * `result` Any

将 `userGesture` 设置为 `true`,可对去除 某些HTML API 只能通过 手势 进行调用 的限制,比如 `requestFullScreen`。

例子,返回一个代码解析结果:
```JavaScript
contents.executeJavaScript('fetch(`https://jsonplaceholder.typicode.com/users/1`).then(resp => resp.json())', true)
  .then((result) => {
    console.log(result) // 来自fetch调用的JSON对象
  })
```

### `<webview>.openDevTools()`
 > 用途:**打开页面的开发者工具栏**

### `<webview>.closeDevTools()`
 > 用途:**关闭页面的开发者工具栏**

### `<webview>.isDevToolsOpened()`
 > 用途:**判断页面的开发者工具栏是否已打开( `Boolean`)**

### `<webview>.isDevToolsFocused()`
 > 用途:**判断页面的开发者工具栏是否已聚焦( `Boolean`)**

### `<webview>.inspectElement(x, y)`
 > 用途:**在页面的 ( `x`,  `y`) 开始检查元素**
 
* `x` Integer
* `y` Integer

### `<webview>.inspectServiceWorker()`
 > 用途:**打开页面中用于服务工作者上下文的开发者工具栏**

### `<webview>.setAudioMuted(muted)`
 > 用途:**使当前网页上的音频静音**
 
* `muted` Boolean

### `<webview>.isAudioMuted()`
 > 用途:**判断网页是否已静音( `Boolean`)**

### `<webview>.undo()`
 > 用途:**在网页中执行撤销( `undo`)**

### `<webview>.redo()`
 > 用途:**在网页中执行重做( `redo`)**

### `<webview>.cut()`
 > 用途:**在网页中执行剪切( `cut`)**

### `<webview>.copy()`
 > 用途:**在网页中执行复制( `copy`)**

### `<webview>.paste()`
 > 用途:**在网页中执行粘贴( `paste`)**

### `<webview>.pasteAndMatchStyle()`
 > 用途:**在网页中执行粘贴并清除样式( `pasteAndMatchStyle`)**
 
### `<webview>.delete()`
 > 用途:**在网页中执行删除( `delete`)**

### `<webview>.selectAll()`
 > 用途:**在网页中执行全选( `selectAll`)**

### `<webview>.unselect()`
 > 用途:**在网页中执行取消选择( `unselect`)**

### `<webview>.replace(text)`
 > 用途:**在网页中执行替换( `replace`)文本( `text`)**
 
* `text` String

### `<webview>.replaceMisspelling(text)`
 > 用途:**在网页中执行校正( `replaceMisspelling`)错别字( `text`)**
 
* `text` String

### `<webview>.insertText(text)`
 > 用途:**插入`text`到焦点元素**
 
* `text` String

### `<webview>.findInPage(text[, options])`
 > 用途:**在页面中发起一个请求进行查找 `text` 并返回一个 `Integer` 当做请求id**
 
* `text` String -  查找的内容, 不能为空.
* `options` Object (可选)
  * `forward` Boolean - (可选) 是否向前或向后查找, 默认为 `true`.
  * `findNext` Boolean - (可选) 是否连续查找, 默认为 `false`.
  * `matchCase` Boolean - (可选) 查找是否区分大小写,,  默认为 `false`.
  * `wordStart` Boolean - (可选) 是否仅以首字母查找.. 默认为 `false`.
  * `medialCapitalAsWordStart` Boolean - (可选) 如果按大写字母开头匹配,那么后面的小写字母或无字母或多词合成等都视为匹配,默认为 `false`.

 您可以通过[`found-in-page`](web-contents.md#event-found-in-page) 事件获取相应的请求结果.

### `<webview>.stopFindInPage(action)`
 > 用途:**使用给定的 `action` 来为 `webContents` 停止任何 `findInPage` 请求**
 
* `action` String - 指派[`webContents.findInPage`]请求结束时要执行的操作。
  * `clearSelection` - 清除选择。
  * `keepSelection` - 转为没有任何action的普通选择
  * `activateSelection` - 聚焦并单击该选择

### `<webview>.print([options])`
 > 用途:**打印`webview`'页面**

更多细节,详见 `webContents.print([options])`.

### `<webview>.printToPDF(options, callback)`
 > 用途:**打印预览中将 `webview`网页打印为PDF并调用 `callback`**

更多细节,详见 `webContents.printToPDF(options, callback)`.

### `<webview>.capturePage([rect, ]callback)`
 > 用途:**截图 `rect`区域并调用 `callback`**
 
更多细节,详见 `webContents.capturePage([rect, ]callback)`.

### `<webview>.send(channel[, arg1][, arg2][, ...])`
> 用途:**通过 `channel` 向渲染进程异步发送消息或任意参数**

* `channel` String
* `...args` any[]

参数将在JSON内部序列化，因此不会包含函数或原型链。渲染进程通过用 `ipcRenderer` 模块侦听 `channel`来处理它。
更多细节,详见[webContents.send](web-contents.md#webcontentssendchannel-args)

### `<webview>.sendInputEvent(event)`
 > 用途:**向页面发送输入 `event`**
 
* `event` Object
更多细节,详见[webContents.sendInputEvent](web-contents.md#webcontentssendinputeventevent)

### `<webview>.setZoomFactor(factor)`
 > 用途:**设置页面的缩放系数**
 
* `factor` Number - 缩放系数

注意:缩放系数是百分制的,如3.0=300％。

### `<webview>.setZoomLevel(level)`
 > 用途:**将缩放级别更改为指定级别**

* `level` Number - 缩放级别

原始大小为0,每个增量表示放大或缩小20％,默认限制为原始大小的300％至50％。

### `<webview>.showDefinitionForSelection()` _macOS_
 > 用途:**在页面上搜索所选字词时弹出字典**

### `<webview>.getWebContents()`
 > 用途:**获取 `webview`.页面内容( [`WebContents`](web-contents.md))**

## DOM事件列表
 `webview`含有以下DOM事件：
 
### 事件: 'load-commit'
 > 触发:**加载完成时**
 
* `url` String
* `isMainFrame` Boolean

包含当前文档导航和副框架的文档加载，但是不包含异步资源加载.

### 事件: 'did-finish-load'
> 触发:**导航完成时(即选项卡中的微调框(spinner)已停止旋转,并且已分派 `onload`事件)**   

### 事件: 'did-fail-load'
> 触发:**加载失败或者是被取消(比如调用了 `window.stop()`)时**   

返回:
* `errorCode` Integer
* `errorDescription` String
* `validatedURL` String
* `isMainFrame` Boolean

[错误代码列表](https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h).           
 
### 事件: 'did-frame-finish-load'
> 触发:**文档完成导航时**   

返回:
* `isMainFrame` Boolean

### 事件: 'did-start-loading'
> 触发:**选项卡中的微调框(spinner)开始旋转(loading)时**   

### 事件: 'did-stop-loading'
> 触发:**选项卡中的微调框(spinner)结束了旋转(loading)时**   

### 事件: 'did-get-response-details'
> 触发:**所请求资源的相关详细信息可用时**   

返回:
* `status` Boolean 下载资源的套接字连接
* `newURL` String
* `originalURL` String
* `httpResponseCode` Integer
* `requestMethod` String
* `referrer` String
* `headers` Object
* `resourceType` String

### 事件: 'did-get-redirect-request'
> 触发:**发起的请求资源被重定向时**   

返回:
* `oldURL` String
* `newURL` String
* `isMainFrame` Boolean

### 事件: 'dom-ready'
> 触发:**文档 加载完成时**   

### 事件: 'page-title-updated'
> 触发:**网页标题被更新时**   
返回:

* `title` String
* `explicitSet` Boolean

当异步加载文档标题时, `explicitSet`为 `false`.

### 事件: 'page-favicon-updated'

> 触发:**网页接收到(图标的url)favicon url时**   

返回:
* `favicons` String[] - URL数组

### 事件: 'enter-html-full-screen'
> 触发:** 通过HTML API进入全屏时触发时**   


### 事件: 'leave-html-full-screen'
> 触发:**通过HTML API退出全屏时触发时**   

### 事件: 'console-message'
> 触发:**客户机窗口记录控制台消息时**   

返回:
* `level` Integer
* `message` String
* `line` Integer
* `sourceId` String

以下是将监听控制台并输出日志消息的示例:

```javascript
const webview = document.getElementById('foo')
webview.addEventListener('console-message', (e) => {
  console.log('Guest page logged a message:', e.message)
})
```

### 事件: 'found-in-page'
 > 触发:**[`webContents.findInPage`]进行页内查找并且找到可用值时**
 
返回:
* `result` Object
  * `requestId` Integer
  * `activeMatchOrdinal` Integer - 活动匹配位置
  * `matches` Integer -匹配数
  * `selectionArea` Object -第一个匹配区域的坐标。
  * `finalUpdate` Boolean
  
```JavaScript
const webview = document.getElementById('foo')
webview.addEventListener('found-in-page', (e) => {
  webview.stopFindInPage('keepSelection')
})

const requestId = webview.findInPage('test')
console.log(requestId)
```

### 事件: 'new-window'
> 触发:**页面请求为`url`打开一个新窗口时**   
 
返回:
* `url` String
* `frameName` String
* `disposition` String - 可为 `default`, `foreground-tab`, `background-tab`, `new-window`, `save-to-disk` 和 `other`.
* `options` Object - 创建新的 `BrowserWindow`时使用的参数.

当页面通过 `window.open`或外部链接(如 `<a target='_blank'>`)来请求 `url`打开一个新窗口(默认创建一个新的 `BrowserWindow`)时触发。

使用系统默认浏览器打开链接的示例:

```JavaScript
const {shell} = require('electron')
const webview = document.getElementById('foo')

webview.addEventListener('new-window', (e) => {
  const protocol = require('url').parse(e.url).protocol
  if (protocol === 'http:' || protocol === 'https:') {
    shell.openExternal(e.url)
  }
})
```

### 事件: 'will-navigate'
> 触发:**用户或页面想要开始导航(比如更改 `window.location`对象或用户单击页面中的链接)时**   
 
返回:
* `url` String

如果是 以`<webview>.loadURL`和 `<webview>.back`之类的API以编程方式启动导航,以及在页内跳转如点击了锚链接或更新 `window.location.hash`时,该事件不会触发,这时您可以使用 `did-navigate-in-page`事件达到目的。

需要阻止导航,请调用 `event.preventDefault()`。

### 事件: 'did-navigate'
> 触发:**导航完成时**   

返回:
* `url` String

 导航完成时触发.

在页内跳转如点击了锚链接或更新 `window.location.hash`,时,该事件不会触发,这时您可以使用 `did-navigate-in-page`事件达到目的。

### 事件: 'did-navigate-in-page'
> 触发:**页内跳转时**   

返回:
* `isMainFrame` Boolean
* `url` String

如点击了锚链接 或 DOM的`hashchange`事件被触发时.虽然页面的url已改变但它不会跳出界面

### 事件: 'close'
> 触发:**访客页面尝试关闭自身时**   

关闭时客户端将 `webview`导航到 `about:blank`的例子:
```JavaScript
const webview = document.getElementById('foo')
webview.addEventListener('close', () => {
  webview.src = 'about:blank'
})
```

### 事件: 'ipc-message'
返回:
> 触发:**访客页面发送异步消息至它所在的嵌入页时**   

* `channel` String
* `args` Array


您可以通过 `sendToHost`方法和 `ipc-message`事件在访客页面与它所在的嵌入页之间轻松地进行通信:
```JavaScript
// guest page的所在嵌入页中
const webview = document.getElementById('foo')
webview.addEventListener('ipc-message', (event) => {
  console.log(event.channel)
  // Prints "pong"
})
webview.send('ping')
```
```JavaScript
// guest page 中
const {ipcRenderer} = require('electron')
ipcRenderer.on('ping', () => {
  ipcRenderer.sendToHost('pong')
})
```

### 事件: 'crashed'
> 触发:**渲染器进程崩溃时**   

### 事件: 'gpu-crashed'
> 触发:**gpu进程崩溃时**   

### 事件: 'plugin-crashed'
> 触发:**插件进程崩溃时**   

返回:
* `name` String
* `version` String

### 事件: 'destroyed'
> 触发:** `webContents`被销毁时**   


### 事件: 'media-started-playing'
 > 触发:**媒体开始播放时**

### 事件: 'media-paused'
 > 触发:**媒体暂停或播放完毕时**

### 事件: 'did-change-theme-color'
 > 触发:**页面的主题颜色被更改时**
 
返回:
* `themeColor` String

通常是因为使用了元标记如theme-color引起:
```html
<meta name='theme-color' content='#ff0000'>
```

### 事件: 'update-target-url'
 > 触发:**鼠标移动到链接或键盘将焦点移动到链接时**

返回:
* `url` String

#### 事件: 'devtools-opened'
> 触发:**开发调试工具打开时**   

#### 事件: 'devtools-closed'
> 触发:**开发调试工具关闭时**   

#### 事件: 'devtools-focused'
> 触发:**开发调试工具被聚焦或打开时**   

[blink-feature-string]: https://cs.chromium.org/chromium/src/third_party/WebKit/Source/platform/RuntimeEnabledFeatures.in