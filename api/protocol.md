# 本文介绍:协议的注册和处理

> 注册自定义协议并拦截现有协议请求

进程: [主进程](../glossary.md#主进程)               

实现类似 `file://` 协议的小例子 :
```JavaScript
const {app, protocol} = require('electron')
const path = require('path')
app.on('ready', () => {
  protocol.registerFileProtocol('atom', (request, callback) => {
    const url = request.url.substr(7)
    callback({path: path.normalize(`${__dirname}/${url}`)})
  }, (error) => {
    if (error) console.error('无法注册协议')
  })
})
```
 **注意:** 这个模块方法应用于 `app` 模块的 `ready` 事件之后

## 方法

### `protocol.registerStandardSchemes(schemes[, options])`
> 用途:**注册名为 `schemes`的标准协议**

* `schemes` String[] - 要注册为标准协议的自定义协议名.
* `options` Object (可选)
  * `secure` Boolean (可选) - 是否使用安全协议,默认为 `false`.

标准协议必须遵循RFC 3986调用[通用URI语法](https://tools.ietf.org/html/rfc3986#section-3)。例如`http`和`https`是标准协议,而`file`不是。

注册标准协议后,在服务时可正确相关资源。否则行为类似`file`,但无法解析URL。例如,下文无法加载图像test.png,,因为非标准协议无法识别相对URL:

```html
<body>
  <img src='test.png'>
</body>
```

注册为标准协议后,允许通过[系统文件API][file-system-api]访问文件。否则渲染器会抛出安全错误。

默认情况下,非标准协议禁用所有Web存储 API(localStorage,sessionStorage,webSQL,indexedDB,cookies)。

所以一般来说,如果你想注册一个自定义协议来代替`http`协议,你必须将它注册成标准协议:
```JavaScript
const {app, protocol} = require('electron')
protocol.registerStandardSchemes(['atom'])
app.on('ready', () => {
  protocol.registerHttpProtocol('atom', '...')
})
```

**注意:** 以上方法必须在 `app` 模块的 `ready` 事件之前使用

### `protocol.registerServiceWorkerSchemes(schemes)`
> 用途:**注册名为 `schemes`的service workers自定义协议**

* `schemes` String[] - 要注册为service workers自定义协议的自定义协议名.

service worker是运行于浏览器后台的一种脚本,它可以无需Web页面或者用户交互就能提供额外的功能.通常用在离线开发上.

举个例子:比如断网了,你仍然可以使用本地缓存数据.

### `protocol.registerFileProtocol(scheme, handler[, completion])`
> 用途:**注册名为 `schemes`的将发送文件作为响应的自定义协议**

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `filePath` String (可选)
* `completion` Function (可选)
  * `error` Error

当使用 `scheme`创建一个 `request`时, `handler`将被 `handler(request,callback)`调用。

当 `scheme` 被成功注册或者完成(错误)时失败, `completion(error)`会使用 `completion(null)`来调用 `completion`。

要处理 `request`, `callback`应该用文件的路径或一个具有 `path`属性的对象来调用,例如: `callback(filePath)` 或 `callback({path:filePath})`。

如果 `callback`为空或一个数字或存在`error`属性的对象, `request` 都将失败并显示为你指定的错误号,详见[错误号列表][net-error]          

默认情况下, `scheme`类似于 `http:`,它的解析方式不同于如 `file:`之类的 `通用URI语法`( `generic URI syntax` )协议.所以你或许应该调用 `protocol.registerStandardSchemes` 来创建一个标准协议。

### `protocol.registerBufferProtocol(scheme, handler[, completion])`
> 用途:**注册名为 `schemes`的将发送 `Buffer`作为响应的自定义协议**

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `buffer` (Buffer | [MimeTypedBuffer](structures/mime-typed-buffer.md)) (可选)
* `completion` Function (可选)
  * `error` Error


用法类似 `registerFileProtocol`,除了 `callback`应该用具有 `data`， `mimeType`和 `charset`属性的 `Buffer`对象来调用.
```JavaScript
const {protocol} = require('electron')
protocol.registerBufferProtocol('atom', (request, callback) => {
  callback({mimeType: 'text/html', data: new Buffer('<h5>回调响应</h5>')})
}, (error) => {
  if (error) console.error('无法注册协议')
})
```

### `protocol.registerStringProtocol(scheme, handler[, completion])`
> 用途:**注册名为 `schemes`的将发送 `String`作为响应的自定义协议**

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `data` String (可选)
* `completion` Function (可选)
  * `error` Error

用法类似 `registerFileProtocol`,除了 `callback`应该用 `String` 或一个 `data`， `mimeType`和 `charset`属性的对象来调用.

### `protocol.registerHttpProtocol(scheme, handler[, completion])`
> 用途:**注册名为 `schemes`的将发送 `HTTP`请求作为响应的自定义协议**

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `redirectRequest` Object
      * `url` String
      * `method` String
      * `session` Object (可选)
      * `uploadData` Object (可选)
        * `contentType` String - 内容的MIME类型
        * `data` String -要发送的内容
* `completion` Function (可选)
  * `error` Error

注册一个 `scheme` 协议,用来发送 HTTP 请求作为响应.

用法类似 `registerFileProtocol`,除了 `callback`应该用具有 `url`, `method`, `referrer`, `uploadData` 和 `session` 属性的 `redirectRequest`对象来调用.

HTTP 请求默认使用当前 session .如果你想使用不同的session值,你应该设置 `session` 为 `null`.

对于POST 请求,必须提供 `uploadData` 对象.

### `protocol.unregisterProtocol(scheme[, completion])`
> 用途:**注销名为 `scheme`的自定义协议**

* `scheme` String
* `completion` Function (可选)
  * `error` Error

### `protocol.isProtocolHandled(scheme, callback)`
> 用途:**判断是否已有一个处理 `scheme`协议的程序( `Boolean`)**

* `scheme` String
* `callback` Function
  * `error` Error

### `protocol.interceptFileProtocol(scheme, handler[, completion])`
> 用途:**拦截 `scheme`协议并使用 `handler`(发送一个 `文件`作为响应)作为协议的新处理程序**

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `filePath` String
* `completion` Function (可选)
  * `error` Error
  
### `protocol.interceptStringProtocol(scheme, handler[, completion])`
> 用途:**拦截 `scheme`协议并使用 `handler`(发送一个 `String`作为响应)作为协议的新处理程序**

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `data` String (可选)
* `completion` Function (可选)
  * `error` Error

### `protocol.interceptBufferProtocol(scheme, handler[, completion])`
> 用途:**拦截 `scheme`协议并使用 `handler`(发送一个 `Buffer`作为响应)作为协议的新处理程序**

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `buffer` Buffer (可选)
* `completion` Function (可选)
  * `error` Error

### `protocol.interceptHttpProtocol(scheme, handler[, completion])`
> 用途:**拦截 `scheme`协议并使用 `handler`(发送一个 `HTTP`请求作为响应)作为协议的新处理程序**
* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `redirectRequest` Object
      * `url` String
      * `method` String
      * `session` Object (可选)
      * `uploadData` Object (可选)
        * `contentType` String - 内容的MIME类型
        * `data` String -要发送的内容
* `completion` Function (可选)
  * `error` Error

### `protocol.uninterceptProtocol(scheme[, completion])`
> 用途:**删除为`scheme`协议安装的拦截器并恢复其原始处理程序**

* `scheme` String
* `completion` Function (可选)
  * `error` Error

[net-error]: https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h
[file-system-api]: https://developer.mozilla.org/en-US/docs/Web/API/LocalFileSystem