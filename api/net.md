# 本文介绍:Chromium原生网络库

> 使用Chromium原生网络库发起HTTP/HTTPS请求

进程: [主进程](../glossary.md#主进程)               

 `net` 模块是用于发出 HTTP(S) 请求问题的客户端 API。
 
 它类似于Node.js的[HTTP](https://nodejs.org/api/http.html) 和 [HTTPS](https://nodejs.org/api/https.html)模块,但是它基于Chromium 的原生API 而非Node.js  ,相对而言更适合处理 web 端的请求。

关于为什么使用 `net` 模块 而非Node.js,这里有个简要的原因列表:

* 自动管理系统代理设置,支持 wpad 协议和 pac 代理配置文件。
* 自动使用隧道通过 HTTPS 请求。
* 支持 basic, digest, NTLM, Kerberos 或 negotiate 等认证方案的身份验证代理。
* 支持流量监控代理: 用于访问控制和监控的Fiddler类代理。

`net` 模块的 API 在设计上特别的模仿了 Node.js 的 API 从而达到比较接近的体验。也就是说,它非常相似 Node.js 的 [HTTP](https://nodejs.org/api/http.html)/[HTTPS](https://nodejs.org/api/https.html) 模块。

如何使用 `net` API的小例子：

```JavaScript
const {app} = require('electron')
app.on('ready', () => {
  const {net} = require('electron')
  const request = net.request('https://github.com')
  request.on('response', (response) => {
    console.log(`状态: ${response.statusCode}`)
    console.log(`文件头: ${JSON.stringify(response.headers)}`)
    response.on('data', (chunk) => {
      console.log(`正文: ${chunk}`)
    })
    response.on('end', () => {
      console.log('没有更多的数据响应.')
    })
  })
  request.end()
})
```

`net` API 必须在 `ready` 事件后使用。否则会抛出一个错误。

## 方法

### `net.request(options)`
> 用途:**根据 `options`对象中指定的协议方案发出安全和不安全的HTTP请求( [`ClientRequest`](./client-request.md))*

* `options` (Object | String) - `ClientRequest` 的构造参数,使用提供的 `options` 创建一个[`ClientRequest`](./ client-request.md)实例。