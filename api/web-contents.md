#  本文介绍: webContents网页内容的渲染与控制方法

> 渲染并控制网页

进程: [主进程](../glossary.md#主进程)           

 `webContents`是个专门负责渲染和控制页面的[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter),它也是[`BrowserWindow`](browser-window.md) 对象的属性,
 
访问`webContents`对象的示例:
```JavaScript
const {BrowserWindow} = require('electron')

let win = new BrowserWindow({width: 800, height: 1500})
win.loadURL('http://github.com')

let contents = win.webContents
console.log(contents)
```

##  方法
`webContents` 对象有下列方法:

```JavaScript
const {webContents} = require('electron')
console.log(webContents)
```

###  `webContents.getAllWebContents()`
> 用途:**获取所有 `WebContents`实例组成的数组( `WebContents[]`)**

实例包含了全部窗口,网页视图,打开的开发者工具栏和开发者工具栏背景页面的所有web内容。

###  `webContents.getFocusedWebContents()`
> 用途:**获取应用程序里已聚焦的Web内容( `WebContents`或 `null`)**   

###  `webContents.fromId(id)`
> 用途:**获取具有给定ID的WebContents实例( `WebContents`)**   
* `id` Integer


###  实例事件
 `WebContents`具有以下实例事件:

####  事件: 'did-finish-load'
> 触发:**导航完成时(即选项卡中的微调框(spinner)已停止旋转,并且已分派 `onload`事件)**   

####  事件: 'did-fail-load'
> 触发:**加载失败或者是被取消(比如调用了 `window.stop()`)时**   

返回:
* `event` Event
* `errorCode` Integer
* `errorDescription` String
* `validatedURL` String
* `isMainFrame` Boolean

[错误代码列表](https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h).         
 
####  事件: 'did-frame-finish-load'
> 触发:**frame窗口 完成导航时**   

返回:
* `event` Event
* `isMainFrame` Boolean

####  事件: 'did-start-loading'
> 触发:**选项卡中的微调框(spinner)开始旋转(loading)时**   

####  事件: 'did-stop-loading'
> 触发:**选项卡中的微调框(spinner)结束了旋转(loading)时**   

####  事件: 'did-get-response-details'
> 触发:**所请求资源的相关详细信息可用时**   

返回:
* `event` Event
* `status` Boolean 下载资源的套接字连接
* `newURL` String
* `originalURL` String
* `httpResponseCode` Integer
* `requestMethod` String
* `referrer` String
* `headers` Object
* `resourceType` String

####  事件: 'did-get-redirect-request'
> 触发:**发起的请求资源被重定向时**   

返回:
* `event` Event
* `oldURL` String
* `newURL` String
* `isMainFrame` Boolean
* `httpResponseCode` Integer
* `requestMethod` String
* `referrer` String
* `headers` Object

####  事件: 'dom-ready'
> 触发:**指定 frame 中的文档 加载完成时**   

返回:
* `event` Event

####  事件: 'page-favicon-updated'
> 触发:**网页接收到(图标的url)favicon url时**   

返回:
* `event` Event
* `favicons` String[] - 网址数组

####  事件: 'new-window'
> 触发:**页面请求为`url`打开一个新窗口时**   

返回:
* `event` Event
* `url` String
* `frameName` String
* `disposition` String - 可为 `default`, `foreground-tab`, `background-tab`, `new-window`, `save-to-disk` 和 `other`.
* `options` Object - 创建新 `BrowserWindow`时使用的参数.
* `additionalFeatures` String[] - 给予`window.open()`的非标准句柄(不是由Chromium或Electron处理的句柄)。

当页面通过 `window.open`或外部链接(如 `<a target='_blank'>`)来请求 `url`打开一个新窗口(默认创建一个新的 `BrowserWindow`)时触发。

如果调用 `event.preventDefault()`,阻止创建新 `BrowserWindow`,你必须设置 `event.newGuest`来引用新的 `BrowserWindow`实例，否则可能会导致意外的行为.

```JavaScript
myBrowserWindow.webContents.on('new-window', (event, url) => {
  event.preventDefault()
  const win = new BrowserWindow({show: false})
  win.once('ready-to-show', () => win.show())
  win.loadURL(url)
  event.newGuest = win
})
```

####  事件: 'will-navigate'
> 触发:**用户或页面想要开始导航(比如更改`window.location`对象或用户单击页面中的链接)时**   

返回:
* `event` Event
* `url` String

如果是 以`webContents.loadURL`和 `webContents.back`之类的API以编程方式启动导航,以及在页内跳转如点击了锚链接或更新 `window.location.hash`时,该事件不会触发,这时您可以使用 `did-navigate-in-page`事件达到目的。

需要阻止导航,请调用 `event.preventDefault()`。

####  事件: 'did-navigate'
> 触发:**导航完成时**   

返回:
* `event` Event
* `url` String

在页内跳转如点击了锚链接或更新 `window.location.hash`,时,该事件不会触发,这时您可以使用 `did-navigate-in-page`事件达到目的。

####  事件: 'did-navigate-in-page'
> 触发:**页内跳转时**   

返回:
* `event` Event
* `url` String
* `isMainFrame` Boolean

如点击了锚链接 或 DOM的`hashchange`事件被触发时.虽然页面的url已改变但它不会跳出界面

#### Event: 'will-prevent-unload'
> 触发:**当 `beforeunload`尝试取消卸载页面时**   

返回:
* `event` Event

调用 `event.preventDefault()` 可忽略 `beforeunload`并允许卸载页面.

```javascript
const {BrowserWindow, dialog} = require('electron')
const win = new BrowserWindow({width: 800, height: 600})
win.webContents.on('will-prevent-unload', (event) => {
  const choice = dialog.showMessageBox(win, {
    type: 'question',
    buttons: ['离开', '保留'],
    title: '你要离开本站吗?',
    message: '您所做过的更改可能无法保存哦!',
    defaultId: 0,
    cancelId: 1
  })
  const leave = (choice === 0)
  if (leave) {
    event.preventDefault()
  }
})
```

####  事件: 'crashed'
> 触发:**渲染进程崩溃或被中断时**   

返回:
* `event` Event
* `killed` Boolean

####  事件: 'plugin-crashed'
> 触发:**插件进程崩溃时**   

返回:
* `event` Event
* `name` String
* `version` String

####  事件: 'destroyed'
> 触发:** `webContents`被销毁时**   

####  事件: 'before-input-event'
> 触发:**进行分派 `keydown`和 `keyup`事件之前**   

返回:
* `event` Event
* `input` Object -输入属性
  * `type` String - 如 `keyUp` 或 `keyDown`
  * `key` String - 相当于 [KeyboardEvent.key][keyboardevent]
  * `isAutoRepeat` Boolean - 相当于 [KeyboardEvent.repeat][keyboardevent]
  * `shift` Boolean - 相当于 [KeyboardEvent.shiftKey][keyboardevent]
  * `control` Boolean - 相当于 [KeyboardEvent.controlKey][keyboardevent]
  * `alt` Boolean - 相当于 [KeyboardEvent.altKey][keyboardevent]
  * `meta` Boolean - 相当于 [KeyboardEvent.metaKey][keyboardevent]

调用 `event.preventDefault`则禁止页面调用 `keydown`或 `keyup`事件。

####  事件: 'devtools-opened'
> 触发:**开发调试工具打开时**   

####  事件: 'devtools-closed'
> 触发:**开发调试工具关闭时**   

####  事件: 'devtools-focused'
> 触发:**开发调试工具被聚焦或打开时**   

####  事件: 'certificate-error'
 > 触发:**验证 `url`的证书( `certificate`) 失败或错误时**
 
返回:
* `event` Event
* `url` String
* `error` String - 错误代码
* `certificate` [Certificate](structures/certificate.md)
* `callback` Function
  * `isTrusted` Boolean -证书是否受信任
  
该事件使用方法类似 [ `app`模块的 `certificate-error`事件](app.md#event-certificate-error)        

####  事件: 'select-client-certificate'
 > 触发:**请求客户端证书时**

返回:
* `event` Event
* `url` URL
* `certificateList` [Certificate[]](structures/certificate.md)
* `callback` Function
  * `certificate` [Certificate](structures/certificate.md) - 必须是来自给定列表的证书

该事件使用方法类似 [ `app`模块的 `select-client-certificate`事件](app.md#event-select-client-certificate)         

####  事件: 'login'
 > 触发:** `webContents`进行基本验证时**
 
返回:
* `event` Event
* `request` Object
  * `method` String
  * `url` URL
  * `referrer` URL
* `authInfo` Object
  * `isProxy` Boolean
  * `scheme` String
  * `host` String
  * `port` Integer
  * `realm` String
* `callback` Function
  * `username` String
  * `password` String
 
该事件使用方法类似 [`app`模块的 `login` 事件](app.md#event-login).       

####  事件: 'found-in-page'
 > 触发:**[`webContents.findInPage`]进行页内查找并且找到可用值时**

返回:
* `event` Event
* `result` Object
  * `requestId` Integer
  * `activeMatchOrdinal` Integer - 活动匹配位置
  * `matches` Integer -匹配数
  * `selectionArea` Object -第一个匹配区域的坐标。
  * `finalUpdate` Boolean
  
####  事件: 'media-started-playing'
 > 触发:**媒体开始播放时**
 
####  事件: 'media-paused'
 > 触发:**媒体暂停或播放完毕时**
 
####  事件: 'did-change-theme-color'
 > 触发:**页面的主题颜色被更改时**

通常是因为使用了元标记如theme-color引起:
```html
<meta name='theme-color' content='#ff0000'>
```

####  事件: 'update-target-url'
 > 触发:**鼠标移动到链接或键盘将焦点移动到链接时**

返回:
* `event` Event
* `url` String

####  事件: 'cursor-changed'
 > 触发:**更改光标类型时**

返回:
* `event` Event
* `type` String 可选值有 `default`, `crosshair`, `pointer`, `text`, `wait`, `help`, `e-resize`, `n-resize`, `ne-resize`, `nw-resize`, `s-resize`, `se-resize`, `sw-resize`, `w-resize`, `ns-resize`, `ew-resize`, `nesw-resize`,  `nwse-resize`, `col-resize`, `row-resize`, `m-panning`, `e-panning`, `n-panning`, `ne-panning`, `nw-panning`, `s-panning`, `se-panning`, `sw-panning`, `w-panning`, `move`, `vertical-text`, `cell`, `context-menu`, `alias`, `progress`, `nodrop`, `copy`, `none`, `not-allowed`, `zoom-in`, `zoom-out`, `grab`, `grabbing`, `custom`,如果参数为 `custom`, `image`参数会在 `NativeImage`中保存自定义光标图像.而如果为 `scale`时, `size`和 `hotspot`则保存关于自定义光标的相关信息。
* `image` NativeImage (可选)
* `scale` Float (可选) - 光标缩放系数
* `size` Object (可选) -  `image`的尺寸
  * `width` Integer
  * `height` Integer
* `hotspot` Object (可选) - 光标的hotspot坐标
  * `x` Integer - x 坐标
  * `y` Integer - y 坐标

####  事件: 'context-menu'
 > 触发:**有一个新的上下文菜单需要处理时**
 
返回:
* `event` Event
* `params` Object
  * `x` Integer - x 坐标
  * `y` Integer - y 坐标
  * `linkURL` String - 如果链接的内容是图像，则包含上下文菜单被调用节点的链接.
  * `pageURL` String -  调用上下文菜单的顶级页面链接
  * `frameURL` String -  调用上下文菜单的子框架的URL
  * `srcURL` String -  调用上下文菜单的元素的源URL,如图片,音频或视频等链接
  * `mediaType` String - 上下文菜单被调用的节点类型,如 `none`, `image`, `audio`, `video`, `canvas`, `file` 或 `plugin`.
  * `hasImageContents` Boolean - 是否在具有非空内容的图像上调用上下文菜单。
  * `isEditable` Boolean - 上下文是否可编辑。
  * `selectionText` String - 调用上下文菜单的选择文本。
  * `titleText` String -调用上下文菜单的标题或替代文本。
  * `misspelledWord` String - 光标下的拼写错误的单词(如果有)。
  * `frameCharset` String -调用上下文菜单的框架的字符编码。
  * `inputFieldType` String -该字段的类型(如果在输入字段上调用了上下文菜单)。可选值为 `none`, `plainText`, `password`, `other`.
  * `menuSourceType` String - 调用上下文菜单的输入源。可选值为 `none`, `mouse`, `keyboard`, `touch`, `touchMenu`.
  * `mediaFlags` Object - 调用上下文菜单的媒体元素标志
    * `inError` Boolean - 媒体元素是否已崩溃
    * `isPaused` Boolean - 媒体元素是否已暂停。
    * `isMuted` Boolean - 媒体元素是否静音。
    * `hasAudio` Boolean - 媒体元素是否具有音频。
    * `isLooping` Boolean - 媒体元素是否循环。
    * `isControlsVisible` Boolean  - 媒体元素的控件是否可见。
    * `canToggleControls` Boolean  - 媒体元素的控件是否可以切换。
    * `canRotate` Boolean  - 是否可以旋转媒体元素。
  * `editFlags` Object  -渲染器是否相信它能够执行相应的操作。
    * `canUndo` Boolean  - 渲染器是否相信它可以撤消。
    * `canRedo` Boolean  - 渲染器是否相信它可以重做。
    * `canCut` Boolean  - 渲染器是否相信它可以裁剪。
    * `canCopy` Boolean  - 渲染器是否相信它可以复制
    * `canPaste` Boolean  - 渲染器是否相信它可以粘贴。
    * `canDelete` Boolean  - 渲染器是否相信它可以删除。
    * `canSelectAll` Boolean  - 渲染器是否相信它可以全选。

####  事件: 'select-bluetooth-device'
 > 触发:**调用 `navigator.bluetooth.requestDevice` 后进行蓝牙设备的选择时**
 
返回:
* `event` Event
* `devices` [BluetoothDevice[]](structures/bluetooth-device.md)
* `callback` Function
  * `deviceId` String

必须先启用 `webBluetooth`,才能调用 `navigator.bluetooth` .

如果没有调用 `event.preventDefault`,则默认选择第一个可用的设备。

 `callback`应该用 `deviceId`来调用被选择,如果传递空的字符串到 `callback`则意味着取消请求。
 
```JavaScript
const {app, webContents} = require('electron')
app.commandLine.appendSwitch('enable-web-bluetooth')

app.on('ready', () => {
  webContents.on('select-bluetooth-device', (event, deviceList, callback) => {
    event.preventDefault()
    let result = deviceList.find((device) => {
      return device.deviceName === 'test'
    })
    if (!result) {
      callback('')
    } else {
      callback(result.deviceId)
    }
  })
})
```

####  事件: 'paint'
 > 触发:**生成新frame时**
 
返回:
* `event` Event
* `dirtyRect` [Rectangle](structures/rectangle.md)
* `image` [NativeImage](native-image.md) - 整个 frame 的图像数据

只有脏区(dirty area)可传递至缓冲区。

```JavaScript
const {BrowserWindow} = require('electron')

let win = new BrowserWindow({webPreferences: {offscreen: true}})
win.webContents.on('paint', (event, dirty, image) => {
  // updateBitmap(dirty, image.getBitmap())
})
win.loadURL('http://github.com')
```

####  事件: 'devtools-reload-page'
 > 触发:**开发者调试工具栏中重新加载webContents时**

##  实例方法

####  `contents.loadURL(url[, options])`
 > 用途:**加载 `url`到窗口中**
 
* `url` String
* `options` Object (可选)
  * `httpReferrer` String (可选) - 来源网址
  * `userAgent` String (可选) - 发起请求的userAgent.
  * `extraHeaders` String (可选) - 用`\ n`分隔的额外头
  * `postData` ([UploadRawData](structures/upload-raw-data.md) | [UploadFile](structures/upload-file.md) | [UploadFileSystem](structures/upload-file-system.md) | [UploadBlob](structures/upload-blob.md))[] - (可选)


 `url` 必须包含协议前缀,比如 `http://` 或 `file://`. 如果想要忽略 http 缓存的加载,请使用 `pragma` 头实现.

 ```JavaScript
const {webContents} = require('electron')
const options = {extraHeaders: 'pragma: no-cache\n'}
webContents.loadURL('https://github.com', options)
```

####  `contents.downloadURL(url)`
 > 用途:**点击`url`进行直接下载而无需在标签页中打开**

* `url` String

此时 `session` 的 `will-download` 事件会被触发.

####  `contents.getURL()`
 > 用途:**获取当前页面的链接( `String`)**

```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('http://github.com')

let currentURL = win.webContents.getURL()
console.log(currentURL)
```

####  `contents.getTitle()`
 > 用途:**当前网页的标题( `String`)**

####  `contents.isDestroyed()`
 > 用途:**判断网页是否被销毁( `Boolean`)**
 
####  `contents.isFocused()`
 > 用途:**判断网页是否已聚焦( `Boolean`)**
 
####  `contents.isLoading()`
 > 用途:**判断网页是否仍在加载资源( `Boolean`)**

####  `contents.isLoadingMainFrame()`
 > 用途:**判断主框架(而不仅仅是其中的iframe或框架)是否仍在加载( `Boolean`)**

####  `contents.isWaitingForResponse()`
 > 用途:**网页是否等待来自页面的主资源的第一次响应(而不仅仅是其中的iframe或框架)是否仍在加载( `Boolean`)**

#### `content.stop()`
 > 用途:**停止任何待处理的导航**

#### `contents.reload()`
 > 用途:**重新加载当前网页**

#### `contents.reloadIgnoringCache()`
 > 用途:**忽略缓存的重新加载当前网页**

#### `content.canGoBack()`
 > 用途:**判断浏览器是否可以可后退至上个网页( `Boolean`)**

#### `contents.canGoForward()`
 > 用途:**判断浏览器是否可以前进到下一个网页( `Boolean`)**

#### `contents.canGoToOffset(offset)`
 > 用途:**判断网页是否可以转到 `offset`这个页面( `Boolean`)**
 
* `offset`String

#### `contents.clearHistory()`
 > 用途:**清除历史记录**

#### `contents.goBack()`
 > 用途:**使浏览器后退往上一页**

#### `contents.goForward()`
 > 用途:**使浏览器前进往下一页**

#### `contents.goToIndex(index)`
 > 用途:**将浏览器导航到指定索引 `index` 的网页**
 
* `index`String

#### `contents.goToOffset(offset)`
 > 用途:**从当前页到导航指定 `offset`**

* `offset`String

#### `contents.isCrashed()`
 > 用途:**判断渲染器进程是否崩溃( `Boolean`)**

#### `contents.setUserAgent(userAgent)`
 > 用途:**重定义网页的userAgent( `Boolean`)**
 
* `userAgent` String

#### `contents.getUserAgent()`
 > 用途:**获取网页的userAgent( `String`)**

#### `contents.insertCSS(css)`
 > 用途:**将CSS插入到当前网页中**
 
* `css` String

####  `contents.executeJavaScript(code[, userGesture, callback])`
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

####  `contents.setAudioMuted(muted)`
 > 用途:**使当前网页上的音频静音**
 
* `muted` Boolean

####  `contents.isAudioMuted()`
 > 用途:**判断网页是否已静音( `Boolean`)**

####  `contents.setZoomFactor(factor)`
 > 用途:**设置页面的缩放系数**
 
* `factor` Number - 缩放系数

注意:缩放系数是百分制的,如3.0=300％。

####  `contents.getZoomFactor(callback)`
 > 用途:**获得当前缩放系数并调用 `callback`**
 
* `callback` Function
  * `zoomFactor` Number

####  `contents.setZoomLevel(level)`
 > 用途:**将缩放级别更改为指定级别**

* `level` Number - 缩放级别

原始大小为0,每个增量表示放大或缩小20％,默认限制为原始大小的300％至50％。

####  `contents.getZoomLevel(callback)`
 > 用途:**获得当前缩放级别并调用 `callback`**
 
* `callback` Function
  * `zoomLevel` Number

####  `contents.setVisualZoomLevelLimits(minimumLevel, maximumLevel)`
 > 用途:**设置缩放级别的最大值和最小值**

* `minimumLevel` Number
* `maximumLevel` Number

####  `contents.setLayoutZoomLevelLimits(minimumLevel, maximumLevel)`
 > 用途:**设置基于布局(即非视觉)的缩放级别的最大值和最小值**

* `minimumLevel` Number
* `maximumLevel` Number

####  `contents.undo()`
 > 用途:**在网页中执行撤销( `undo`)**

####  `contents.redo()`
 > 用途:**在网页中执行重做( `redo`)**

####  `contents.cut()`
 > 用途:**在网页中执行剪切( `cut`)**

####  `contents.copy()`
 > 用途:**在网页中执行复制( `copy`)**

####  `contents.copyImageAt(x, y)`
 > 用途:**将指定位置的图像复制到剪贴板**
* `x` Integer
* `y` Integer


####  `contents.paste()`
 > 用途:**在网页中执行粘贴( `paste`)**

####  `contents.pasteAndMatchStyle()`
 > 用途:**在网页中执行粘贴并清除样式( `pasteAndMatchStyle`)**

####  `contents.delete()`
 > 用途:**在网页中执行删除( `delete`)**

####  `contents.selectAll()`
 > 用途:**在网页中执行全选( `selectAll`)**

####  `contents.unselect()`
 > 用途:**在网页中执行取消选择( `unselect`)**

####  `contents.replace(text)`
 > 用途:**在网页中执行替换( `replace`)文本( `text`)**
 
* `text` String

####  `contents.replaceMisspelling(text)`
 > 用途:**在网页中执行校正( `replaceMisspelling`)错别字( `text`)**
 
* `text` String

####  `contents.insertText(text)`
 > 用途:**插入`text`到焦点元素**
 
* `text` String

####  `contents.findInPage(text[, options])`
 > 用途:**在页面中发起一个请求进行查找 `text` 并返回一个 `Integer` 当做请求id**
 
* `text` String -  查找的内容, 不能为空.
* `options` Object (可选)
  * `forward` Boolean - (可选) 是否向前或向后查找, 默认为 `true`.
  * `findNext` Boolean - (可选) 是否连续查找, 默认为 `false`.
  * `matchCase` Boolean - (可选) 查找是否区分大小写,,  默认为 `false`.
  * `wordStart` Boolean - (可选) 是否仅以首字母查找.. 默认为 `false`.
  * `medialCapitalAsWordStart` Boolean - (可选) 如果按大写字母开头匹配,那么后面的小写字母或无字母或多词合成等都视为匹配,默认为 `false`.

 您可以通过[`found-in-page`](web-contents.md#event-found-in-page) 事件获取相应的请求结果.

####  `contents.stopFindInPage(action)`
 > 用途:**使用给定的 `action` 来为 `webContents` 停止任何 `findInPage` 请求**
 
* `action` String - 指派[`webContents.findInPage`]请求结束时要执行的操作。
  * `clearSelection` - 清除选择。
  * `keepSelection` - 转为没有任何action的普通选择
  * `activateSelection` - 聚焦并单击该选择

```JavaScript
const {webContents} = require('electron')
webContents.on('found-in-page', (event, result) => {
  if (result.finalUpdate) webContents.stopFindInPage('clearSelection')
})

const requestId = webContents.findInPage('api')
console.log(requestId)
```

####  `contents.capturePage([rect, ]callback)`
 > 用途:**截图 `rect`区域并调用 `callback`**
 
* `rect` [Rectangle](structures/rectangle.md) (可选) -截图的 `rect`区域, 如果为空,意味着截图整个页面.
* `callback` Function
  * `image` [NativeImage](native-image.md) -  [NativeImage](native-image.md) 实例,用于存储截图.

####  `contents.hasServiceWorker(callback)`
 > 用途:**检查是否有已注册的ServiceWorker并调用 `callback`**
 
* `callback` Function
  * `hasWorker` Boolean
 
####  `contents.unregisterServiceWorker(callback)`
 > 用途:**注销所有ServiceWorker并调用 `callback`**
 
* `callback` Function
  * `success` Boolean
  
#### `contents.getPrinters()`
 > 用途:**获取系统打印机列表([PrinterInfo[]](structures/printer-info.md))**

例子:
```javascript
name: 'Zebra_LP2844',
description: 'Zebra LP2844',
status: 3,
isDefault: false,
options: {
  copies: '1',
  'device-uri': 'usb://Zebra/LP2844?location=14200000',
  finishings: '3',
  'job-cancel-after': '10800',
  'job-hold-until': 'no-hold',
  'job-priority': '50',
  'job-sheets': 'none,none',
  'marker-change-time': '0',
  'number-up': '1',
  'printer-commands': 'none',
  'printer-info': 'Zebra LP2844',
  'printer-is-accepting-jobs': 'true',
  'printer-is-shared': 'true',
  'printer-location': '',
  'printer-make-and-model': 'Zebra EPL2 Label Printer',
  'printer-state': '3',
  'printer-state-change-time': '1484872644',
  'printer-state-reasons': 'offline-report',
  'printer-type': '36932',
  'printer-uri-supported': 'ipp://localhost/printers/Zebra_LP2844',
  system_driverinfo: 'Z'
}
}]
```

####  `contents.print([options])`
 > 用途:**打印窗口页面**
 
* `options` Object (可选)
  * `silent` Boolean - `true`表示静默打印即将选择系统的默认打印机和默认设置进行打印.一般默认为 `false`.
  * `printBackground` Boolean - 同时打印网页的背景颜色和图像,默认为 `false`.

在网页中调用 `window.print()` 即等同于 `webContents.print({silent: false, printBackground: false})`.

您可以使用 `page-break-before: always;` CSS样式强制打印到新页面中.

####  `contents.printToPDF(options, callback)`
 > 用途:**打印预览中将网页打印为PDF并调用 `callback`**
 
* `options` Object
  * `marginsType` Integer - (可选) 边距类型, `0`为默认边距, `1`为无边距, and `2`为中等边距.
  * `pageSize` String - (可选) 大小类型, 可选 `A3`, `A4`, `A5`, `Legal`, `Letter`, `Tabloid` 或包含有 `height`和 `width`属性的对象
  * `printBackground` Boolean - (可选) 是否打印CSS背景
  * `printSelectionOnly` Boolean - (可选) 是否只打印选择的内容
  * `landscape` Boolean - (可选) `true`为横向, `false`为纵向,如果css样式中强制定义了 `@page` , `landscape`会被忽略.
* `callback` Function
  * `error` Error  
  * `data` Buffer - 一个包含生成的PDF数据的 `Buffer`.

默认情况下,`options`为空时将被视为:

```JavaScript
{
  marginsType: 0,
  printBackground: false,
  printSelectionOnly: false,
  landscape: false
}
```

您可以使用 `page-break-before: always;` CSS样式强制打印到新页面中.

```JavaScript
const {BrowserWindow} = require('electron')
const fs = require('fs')

let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('http://github.com')

win.webContents.on('did-finish-load', () => {
//使用默认打印选项
  win.webContents.printToPDF({}, (error, data) => {
    if (error) throw error
    fs.writeFile('/tmp/print.pdf', data, (error) => {
      if (error) throw error
      console.log('Write PDF successfully.')
    })
  })
})
```

####  `contents.addWorkSpace(path)`
 > 用途:**把指定路径添加到开发者工具栏的 工作区.但必须在 开发者工具栏 创建之后才能使用它**

* `path` String

```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.webContents.on('devtools-opened', () => {
  win.webContents.addWorkSpace(__dirname)
})
```

####  `contents.removeWorkSpace(path)`
 > 用途:**从开发者工具栏的工作区中移除指定的路径**
 
* `path` String

####  `contents.openDevTools([options])`
 > 用途:**打开开发者工具栏**

* `options` Object (可选)
  * `mode` String - 停靠方向类型, 可选 `right`(右侧), `bottom`(下方), `undocked`(窗口与工具栏分离), `detach`(新窗口打开工具栏). 默认为上次(最后一次)的方向

####  `contents.closeDevTools()`
 > 用途:**关闭开发者工具栏**

####  `contents.isDevToolsOpened()`
 > 用途:**判断开发者工具栏是否已打开( `Boolean`)**

####  `contents.isDevToolsFocused()`
 > 用途:**判断开发者工具栏是否已聚焦( `Boolean`)**

####  `contents.toggleDevTools()`
 > 用途:**切换开发者工具栏**

####  `contents.inspectElement(x, y)`
 > 用途:**在 ( `x`,  `y`) 开始检查元素**
* `x` Integer
* `y` Integer


####  `contents.inspectServiceWorker()`
 > 用途:**打开用于服务工作者上下文的开发者工具栏**

####  `contents.send(channel[, arg1][, arg2][, ...])`
> 用途:**通过 `channel` 向渲染进程异步发送消息或任意参数**

* `channel` String
* `...args` any[]

参数将在JSON内部序列化，因此不会包含函数或原型链。渲染进程通过用 `ipcRenderer` 模块侦听 `channel`来处理它。

如从主进程向渲染进程发送消息 :
```JavaScript
// 主进程.
const {app, BrowserWindow} = require('electron')
let win = null

app.on('ready', () => {
  win = new BrowserWindow({width: 800, height: 600})
  win.loadURL(`file://${__dirname}/index.html`)
  win.webContents.on('did-finish-load', () => {
    win.webContents.send('ping', 'whoooooooh!')
  })
})
```

```html
<!-- index.html -->
<html>
<body>
  <script>
    require('electron').ipcRenderer.on('ping', (event, message) => {
      console.log(message)  // Prints 'whoooooooh!'
    })
  </script>
</body>
</html>
```

####  `contents.enableDeviceEmulation(parameters)`
 > 用途:**开启设备模拟**
 
* `parameters` Object
  * `screenPosition` String - 指定要模拟的屏幕类型(默认: `desktop`)
    * `desktop` - 桌面屏幕类型
    * `mobile` - 移动屏幕类型
  * `screenSize` Object - 屏幕大小(screenPosition == mobile)
    * `width` Integer - 模拟屏幕宽度
    * `height` Integer - 模拟屏幕高度
  * `viewPosition` Object - 在屏幕上定位视图(screenPosition == mobile) (默认: `{x: 0, y: 0}`)
    * `x` Integer -从左上角的x偏移
    * `y` Integer - 从左上角的y偏移
  * `deviceScaleFactor` Integer - 设备缩放系数 (默认: `0`即设备原始缩放系数)
  * `viewSize` Object - 模拟视图大小 (空表示不覆盖)
    * `width` Integer - 模拟视图宽度
    * `height` Integer - 模拟视图高度
  * `fitToView` Boolean - 是否需要缩小模拟视图以适应可用空间(默认:`false`)
  * `offset` Object - 可用空间内的模拟视图偏移 (不适合视图模式)(默认: `{x: 0, y: 0}`)
  * `x` Float - 从左上角的x偏移
  * `y` Float - 从左上角的y偏移
* `scale` Float - 可用空间内的模拟视图的比例 (不适合视图模式) (默认: `1`)

####  `contents.disableDeviceEmulation()`
 > 用途:**禁止 `webContents.enableDeviceEmulation` 启用设备模拟**

####  `contents.sendInputEvent(event)`
 > 用途:**向页面发送输入 `event`**
 
* `event` Object
  * `type` String (**必填**) - 事件类型,可选 `mouseDown`, `mouseUp`, `mouseEnter`, `mouseLeave`, `contextMenu`, `mouseWheel`, `mouseMove`, `keyDown`, `keyUp`, `char`.
  * `modifiers` String[] -事件的修饰符数组, 可选 `shift`, `control`, `alt`, `meta`, `isKeypad`, `isAutoRepeat`, `leftButtonDown`, `middleButtonDown`, `rightButtonDown`, `capsLock`, `numLock`, `left`, `right`.

对键盘事件而言, `event` 对象有如下属性 :
* `keyCode` String (**必需**) - 将作为键盘事件发送的字符. 参考[有效键代码列表](accelerator.md)                         

对鼠标事件而言, `event` 对象有如下属性 :
* `x` Integer (**必填**)
* `y` Integer (**必填**)
* `button` String - 被按下的按钮,可选 `left`, `middle`, `right`
* `globalX` Integer
* `globalY` Integer
* `movementX` Integer
* `movementY` Integer
* `clickCount` Integer

对鼠标滚轮事件 `mouseWheel`来说, `event` 对象还有如下属性 :

* `deltaX` Integer
* `deltaY` Integer
* `wheelTicksX` Integer
* `wheelTicksY` Integer
* `accelerationRatioX` Integer
* `accelerationRatioY` Integer
* `hasPreciseScrollingDeltas` Boolean
* `canScroll` Boolean

####  `contents.beginFrameSubscription([onlyDirty ,]callback)`
 > 用途:**开始订阅演示事件和捕获的帧，当有一个演示事件时调用 `callback`**
 
* `onlyDirty` Boolean (可选) - 默认为 `false`
* `callback` Function
  * `frameBuffer` Buffer 一个包含原始像素数据的 `Buffer`
  * `dirtyRect` [Rectangle](structures/rectangle.md)

 `frameBuffer` 的数据在大多数机器上，像素数据有效地以32位BGRA格式存储,但是实际情况是取决于处理器的字节顺序的(大多数的处理器是存放小端序的,如果是在大端序的处理器上, 数据是32位ARGB格式） 。


####  `contents.endFrameSubscription()`
 > 用途:**结束订阅帧展示事件**

####  `contents.startDrag(item)`
 > 用途:**开始对 `item`拖放操作**
 
* `item` Object 
  * `file` String 或 `files` Array  被拖动文件的绝对路径 
  * `icon` [NativeImage](native-image.md) 被拖动时显示在光标下的图像,MacOS上必须是非空的图标。

####  `contents.savePage(fullPath, saveType, callback)`
 > 用途:**尝试保存页面并返回是否成功的布尔( `Boolean`)**
 
* `fullPath` String - 完整的文件路径
* `saveType` String - 指定保存类型.
  * `HTMLOnly` - 只保存页面的HTML
  * `HTMLComplete` - 保存完整的html页面
  * `MHTML` - 将完整的HTML页面保存为MHTML
* `callback` Function - `function(error) {}`.
  * `error` Error

```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()

win.loadURL('https://github.com')

win.webContents.on('did-finish-load', () => {
  win.webContents.savePage('/tmp/test.html', 'HTMLComplete', (error) => {
    if (!error) console.log('Save page successfully')
  })
})
```

####  `contents.showDefinitionForSelection()` _macOS_
 > 用途:**在页面上搜索所选字词时弹出字典**
 
####  `contents.setSize(options)`
 > 用途:**设置页面 `<webview>`访客内容的页面尺寸**

* `options` Object
  * `normal` Object (可选) - 页面的正常大小.   结合 [`disableguestresize`](webview-tag.md#disableguestresize)属性可手动重新调整 `<webview>`内容尺寸
    * `width` Integer
    * `height` Integer

####  `contents.isOffscreen()`
 > 用途:**是否启用了离屏渲染( `Boolean`)**

####  `contents.startPainting()`
 > 用途:**如果启用离屏渲染但未绘制则开始绘制**

####  `contents.stopPainting()`
 > 用途:**如果启用和正在绘制离屏渲染则停止绘制**

####  `contents.isPainting()`
 > 用途:** 判断如果离屏渲染被启用,它当前是否正在绘制( `Boolean`)**

####  `contents.setFrameRate(fps)`
 > 用途:** 如果离屏渲染被启用,则将帧率设置为1-60之间的值**
 
* `fps` Integer

####  `contents.getFrameRate()`
 > 用途:** 如果离屏渲染被启用,则返回当前帧速率( `Integer`)**

####  `contents.invalidate()`
 > 用途:**完整重绘此网页内容所在窗口**
 
如果*屏幕渲染*启用，则使框架无效并通过 `'paint'`事件生成一个新的。

####  `contents.getWebRTCIPHandlingPolicy()`
 > 用途:**获取WebRTC IP处理策略( `String`)**

####  `contents.setWebRTCIPHandlingPolicy(policy)`
 > 用途:**完整重绘此网页内容所在窗口**
* `policy` String - 指定WebRTC IP处理策略
  * `default` - 暴露用户的公共和本地IP。使用此策略时，WebRTC将并有权枚举所有接口并绑定它们以发现公共接口.
  * `default_public_interface_only` -暴露用户的公共IP但不公开用户的本地IP。使用此策略时，WebRTC仅使用http默认路由并不会公开任何本地地址。
  * `default_public_and_private_interfaces` - 暴露用户的公共和本地IP。使用此策略时，WebRTC仅使用http由OS在多宿主端点上选择的默认路由。此项将会也暴露相关联的默认私人地址。
  * `disable_non_proxied_udp` - 不公开公共或本地IP。使用此策略时，WebRTC仅使用TCP联系对等体或服务器，除非代理服务器支持UDP。

设置WebRTC IP处理策略允许您控制通过WebRTC公开哪些IP。有关详细信息，请参阅[BrowserLeaks](https://browserleaks.com/webrtc)

###  实例属性

####  `contents.id`
 > 属性:** `WebContents`的唯一ID**
 
####  `contents.session`
 > 属性:** `WebContents`使用的一个Session对象([session](session.md))**

####  `contents.hostWebContents`
 > 属性:**可能拥有 `WebContents`的[`WebContents`](web-contents.md) 实例**


####  `contents.devToolsWebContents`
 > 属性:** `WebContents`的开发调试工具栏**
 
**注意:** 用户不应存储此对象,因为当DevTools已关闭时,它可能变为 `null`.

####  `contents.debugger`
`WebContents`的[Debugger](debugger.md)实例。

[keyboardevent]: https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent