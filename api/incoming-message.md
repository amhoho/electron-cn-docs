## 本文介绍: 处理对HTTP / HTTPS请求的响应

> 通常用于HTTP/HTTPS请求失败/成功等处理. 

进程: [主进程](../glossary.md#主进程)    

`IncomingMessage`是由 [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)响应[可读流](https://nodejs.org/api/stream.html#stream_readable_streams)接口  

### 实例事件

#### 事件: 'data'
> 用途:**响应或回调传送到应用的数据**

* `chunk` Buffer - 响应正文的数据块.

#### 事件: 'end'
> 触发:**响应正文已结束时**

#### 事件: 'aborted'
> 触发:**正在进行的HTTP事务期间请求已取消时**

#### 事件: 'error'
> 触发:**流响应数据事件遇到错误时**

返回 `error` Error - 错误字符串,标识失败原因

例如，服务器已关闭但响应仍在流式传输，则将在响应对象上发出一个 `error` 事件，随后在请求对象上将会出现一个 `close` 事件。

### 实例属性
#### `response.statusCode`
> 属性:**HTTP响应状态的数字代码**

#### `response.statusMessage`
> 属性:**HTTP响应状态的消息文本**

#### `response.headers`
> 属性:**HTTP响应头对象**

格式如下:
* 所有标题名称均为小写。
* 每个响应名称必须是对象中的键.
* 每个响应名称和它的值是可匹配的

#### `response.httpVersion`
> 属性:**HTTP协议版本号,典型值是 `1.0`或 `1.1`**

#### `response.httpVersionMajor`
> 属性:**HTTP协议的主版本号**

#### `response.httpVersionMinor`
> 属性:**HTTP协议的次版本号**