# 本文介绍:ClientRequest类
>客户端的HTTP/HTTPS请求。

进程:[主进程](../glossary.md#主进程)     

 `ClientRequest`是由[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)来实现[Writable Stream](https://nodejs.org/api/stream.html#stream_writable_streams)

### `new ClientRequest(options)`
> 作用:**发起新的HTTP/HTTPS请求**

* `options`(Object | String) -  `options`是String时即请求URL。 `options` 是Object时则按以下属性请求:
  * `method` String(可选) - HTTP请求方法。默认为 `GET`。
  * `url` String(可选) - 请求URL。协议为http或https。
  * `session` Object(可选) - [`Session`](session.md)与请求相关联。 `session` 优先于 `partition`。
  * `partition` String(可选) - [`partition`]的名称[`partition`](session.md)与请求相关联。默认为空字符串。如果 `session`是显式的指定, `partition`被忽略。
  * `protocol` String(可选) - `scheme:`形式的协议方案。可选 `http:`或 `https:`。默认为 `http:`。
  * `host` String(可选) - 作为连接提供的服务器主机,主机名和端口号`hostname:port`
  * `hostname` String(可选) - 服务器主机名。
  * `port` Integer(可选) - 服务器的侦听端口号。
  * `path` String(可选) - 请求URL的路径部分。
  * `redirect` String（可选） - 请求的重定向模式。可选值 `follow`(跟随) , `error`(错误)或 `manual`(手动),默认为 `follow`。 `error`表示任何重定向将被中止。 `manual` 表示延迟重定向直到调用了[`request.followRedirect`](#requestfollowRedirect)。有关重定向请求的更多细节请查看[`redirect`](#event-redirect)事件。


`options`属性,如 `protocol`, `host`, `hostname`, `port`和 `path`严格遵循Node.js模型,如[URL](https://nodejs.org/api/url.html)模块。

例如,我们可以创建与`github.com`相同的请求如下:

```JavaScript
const request = net.request({
  method:'GET',
  protocol:'https:',
  hostname:'github.com',
  port:443,
  path:'/'
})
```

### 实例事件

#### 事件:'response'
> 触发:**HTTP响应消息对象时**

* `response` (IncomingMessage)

#### 事件:'login'
> 触发:**认证代理请求用户凭据时**

* `authInfo`对象
  * `isProxy` Boolean
  * `scheme` String
  * `host` String
  * `port` Integer
  * `realm` String
* `callback` Function 发起凭据并用`callback`回调
  * `username` String
  * `password` String

```JavaScript
request.on('login',(authInfo,callback)=> {
  callback('username','password')
})
```
提供空凭证将取消请求并在响应对象上报告认证错误：
```JavaScript
request.on('response',(response)=> {
  console.log(`STATUS:${response.statusCode}`);
  response.on('error',(error)=> {
    console.log(`ERROR:${JSON.stringify(error)}`)
  })
})
request.on('login',(authInfo,callback)=> {
  callback()
})
```

#### 事件:'finish'
> 触发:**请求完成时**

#### 事件:'abort'
> 触发:**请求中止时**

如果 `request`已经关闭, `abort`事件不会被触发。

#### 事件:'error'
> 触发:**请求错误时**

* `error` Error - 包含失败原因的一个对象。

`net`模块无法发出网络请求时触发。通常在 `request`对象发出一个 `error`事件,一个 `close`事件随后
跟随并且不提供响应对象。

####  事件:'close'
> 触发:**请求关闭时**

作为HTTP请求 - 响应事务中的最后一个事件。 `close`事件表示不再发出 `request`或者`response`对象。

####  事件: 'redirect'
> 触发:**发起模式为 `manual`(手动)的重定向时**
* `statusCode` Integer
* `method` String
* `redirectUrl` String
* `responseHeaders` Object

使用[`request.followRedirect`](#requestfollowRedirect)可继续重定向.

### 实例属性

#### `request.chunkedEncoding`
指定请求是否将使用HTTP分块传输编码的布尔值。默认为 `false`

该属性是可读写的,但它可以只在第一次写操作之前设置,在第一次写入后设置 `chunkedEncoding`属性后会抛出一个错误。

如果请求较大且复杂时,使用分块可有效提高效率.因为数据将以小块形式流传输，而不是在Electron进程内存中内部缓冲。


### 实例方法

#### `request.setHeader(name,value)`
> 用途:**设置HTTP头**

* `name` String - 额外的HTTP头名称。
* `value` String - 额外的HTTP头值。

必须注意的是,它只能在第一次写入前调用。在第一次写入后调用此方法将抛出一个错误。

#### `request.getHeader(name)`
> 用途:**获取HTTP头**

* `name` String - 指定的额外标题名称。
返回String - 以前设置的额外头名称的值。

#### `request.removeHeader(name)`
> 用途:**删除先前设置的HTTP头**

* `name` String - 指定的额外标题名称。

必须注意的是,它只能在第一次写入前调用。在第一次写入后调用此方法将抛出一个错误。

#### `request.write(chunk [,encoding] [,callback])`
> 用途:**写入新的请求并等待响应**

* `chunk`(String | Buffer) - 请求主体数据的一个块。如果是字符串,它将使用指定的编码转换为Buffer。
* `encoding` String(可选) - 用于将字符串块转换为Buffer对象。默认为 `utf-8`。
* `callback` Function(可选) - 在写操作结束后调用。

 `callback`本质上是为了保持与Node.js API的相似性而引入的虚拟函数。

在chunk的内容传递到Chromium网络层后，在下一个tick中被异步调用。与Node.js实现相反，不能保证 `chunk`内容在调用 `callback`之前已经被刷新了块内容.

向请求正文中添加一个数据块。

第一次写操作可能导致在线路上发出请求头。

第一次写操作后，不允许添加或删除自定义标题。

#### `request.end([chunk] [,encoding] [,callback])`
> 用途:**发送请求数据的最后一个块。将不允许后续写入或结束操作**

* `chunk`(String | Buffer)(可选)
* `encoding` String(可选)
* `callback`函数(可选)

 `finish`事件是在结束操作之后发出的。

#### `request.abort()`
> 用途:**取消正在进行的HTTP事务**

如果请求已经发出了 `close`事件，中止操作将无效。否则正在进行的事件将触发 `abort`和 `close`事件。

另外，如果有正在进行的响应对象，它将发出 `aborted`事件。

#### `request.followRedirect()`
> 用途:**重定向模式为 `manual`(手动)时,继续延迟重定向请求**