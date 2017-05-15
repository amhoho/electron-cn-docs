## 类：WebRequest

> 在其生命周期的各个阶段截取和修改请求的内容

进程: [主进程](../glossary.md#主进程)         

 `WebRequest`类的实例是通过使用 `Session`的 `webRequest`属性来访问。
 
每个 API 接收一可选的 `filter` 和 `listener`,当 API 事件发生的时候使用 `listener(details)` 调用 `listener`, `details` 是一个用来描述请求的对象.将 `null`作为 `listener`传递将取消订阅事件.

 `filter` 是一个拥有 `urls` 属性的对象,这是一个 url 模式数组,用来过滤掉不匹配指定 url 模式的请求.如果忽略 `filter` ,那么所有请求都将可以成功匹配.

所有事件的 `listener` 都有一个回调事件,当 `listener` 完成它的工作的时候,它将使用一个 `response` 对象来调用.

为请求添加`User-Agent`标头的示例:
```JavaScript
const {session} = require('electron')
// 将所有请求的代理都修改为下列 url.
const filter = {
  urls: ['https://*.github.com/*', '*://electron.github.io']
}
session.defaultSession.webRequest.onBeforeSendHeaders(filter, (details, callback) => {
  details.requestHeaders['User-Agent'] = 'MyAgent'
  callback({cancel: false, requestHeaders: details.requestHeaders})
})
```

### 实例方法

#### `webRequest.onBeforeRequest([filter, ]listener)`
> 用途:**即将发起请求时,调用 `listener`**

* `filter` Object
* `listener` Function
  * `urls` String[] - URL模式数组,用于过滤与该数组不匹配的请求.
  * `details` Object
    * `id` Integer
    * `url` String
    * `method` String
    * `resourceType` String
    * `timestamp` Double
    * `uploadData` [UploadData[]](structures/upload-data.md) - `UploadData`对象的数组
  * `callback` Function 使用一个 `response` 对象来调用 `callback`
    * `response` Object
      * `cancel` Boolean (可选)
      * `redirectURL` String (可选) -   阻止原始请求被发送或完成,把它重定向到给定的URL。

#### `webRequest.onBeforeSendHeaders([filter, ]listener)`
> 用途:**在发送 HTTP 请求的之前,一旦请求头可用,调用 `listener`**

* `urls` String[] - URL模式数组,用于过滤与该数组不匹配的请求.
* `filter` Object
* `listener` Function- 可能会发生在和服务器发起tcp连接之后,发送任何 http 数据之前.
* `details` Object
  * `id` Integer
  * `url` String
  * `method` String
  * `resourceType` String
  * `timestamp` Double
  * `requestHeaders` Object
* `callback` Function 使用一个 `response` 对象来调用 `callback`
  * `response` Object
    * `cancel` Boolean (可选)
    * `requestHeaders` Object (可选) - 当提供时，将使用 headers 发出请求

#### `webRequest.onSendHeaders([filter, ]listener)`
> 用途:**当请求被发送到服务器之前,调用 `listener`**

* `urls` String[] - URL模式数组,用于过滤与该数组不匹配的请求.
* `filter` Object
* `listener` Function
  * `details` Object
    * `id` Integer
    * `url` String
    * `method` String
    * `resourceType` String
    * `timestamp` Double
    * `requestHeaders` Object

以前的 `onBeforeSendHeaders` 响应的修改在这个监听器触发时是可见的。

#### `webRequest.onHeadersReceived([filter, ]listener)`
> 用途:**当接收到请求的HTTP响应头时,调用 `listener`**

* `urls` String[] - URL模式数组,用于过滤与该数组不匹配的请求.
* `filter` Object
* `listener` Function
* `details` Object
  * `id` String
  * `url` String
  * `method` String
  * `resourceType` String
  * `timestamp` Double
  * `statusLine` String
  * `statusCode` Integer
  * `responseHeaders` Object
* `callback` Function 使用一个 `response` 对象来调用 `callback`
  * `response` Object
    * `cancel` Boolean
    * `responseHeaders` Object (可选) - 如果提供, 服务器将假定使用这些头来响应.
    * `statusLine` String (可选) - 应该在覆盖 `responseHeaders`更改标头状态时提供,否则将使用原始响应标头状态。


#### `webRequest.onResponseStarted([filter, ]listener)`
> 用途:**当接收到响应主体的第一个字节时,调用 `listener`**

* `urls` String[] - URL模式数组,用于过滤与该数组不匹配的请求.
* `filter` Object
* `listener` Function
  * `details` Object
    * `id` Integer
    * `url` String
    * `method` String
    * `resourceType` String
    * `timestamp` Double
    * `responseHeaders` Object
    * `fromCache` Boolean - 响应是否从磁盘缓存中获取
    * `statusCode` Integer
    * `statusLine` String
    
对于HTTP请求来说,这意味着状态线和响应头可用了.

#### `webRequest.onBeforeRedirect([filter, ]listener)`
> 用途:**当服务器发起重定向即将发生时,调用 `listener`**

* `urls` String[] - URL模式数组,用于过滤与该数组不匹配的请求.
* `filter` Object
* `listener` Function
  * `details` Object
    * `id` String
    * `url` String
    * `method` String
    * `resourceType` String
    * `timestamp` Double
    * `redirectURL` String
    * `statusCode` Integer
    * `ip` String (可选) - 请求的真实服务器ip
      actually sent to.
    * `fromCache` Boolean
    * `responseHeaders` Object

#### `webRequest.onCompleted([filter, ]listener)`
> 用途:**当请求完成时,调用 `listener`**

* `urls` String[] - URL模式数组,用于过滤与该数组不匹配的请求.
* `filter` Object
* `listener` Function
  * `details` Object
    * `id` Integer
    * `url` String
    * `method` String
    * `resourceType` String
    * `timestamp` Double
    * `responseHeaders` Object
    * `fromCache` Boolean
    * `statusCode` Integer
    * `statusLine` String

#### `webRequest.onErrorOccurred([filter, ]listener)`
> 用途:**当发生错误时,调用 `listener`**

* `urls` String[] - URL模式数组,用于过滤与该数组不匹配的请求.
* `filter` Object
* `listener` Function
  * `details` Object
    * `id` Integer
    * `url` String
    * `method` String
    * `resourceType` String
    * `timestamp` Double
    * `fromCache` Boolean
    * `error` String - 错误描述.