# 本文介绍:session(会话,缓存和代理等控制)

> 管理浏览器 session ,Cookie,缓存,代理设置等。

进程: [主进程](../glossary.md#主进程)               

 `session`模块可以用来创建新的 `Session`对象,你也可以通过使用 [`webContents`](web-contents.md) 的 `session`属性或模块访问现有页面的 `session`.

```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('http://github.com')
const ses = win.webContents.session
console.log(ses.getUserAgent())
```

可以在 `session` 模块中创建一个 `Session` 对象 :

```JavaScript
const {session} = require('electron')
const ses = session.fromPartition('persist:name')
console.log(ses.getUserAgent())
```

## 方法

### `session.fromPartition(partition[, options])`
> 用途:**获取来自 `partition`字符串的 `Session`实例( `Session`)**

* `partition` String
* `options` Object
  * `cache` Boolean - 是否启用缓存

如果存在一个具有相同 `partition`的 `Session`时,它将被返回;否则将使用 `options`创建一个新的 `Session`实例。

如果 `partition`以 `persist:`前缀,该页面将使用一个持久会话,该会话可用于应用程序中具有相同 `partition`的所有页面。

如果 `partition`没有 `persist:`前缀,该页面将使用内存中会话。

如果 `partition`为空,那么将返回应用程序的默认会话。

要用 `options`创建一个 `Session`, 您必须确保带有 `partition`的 `Session`从来没有被使用过. 因为没有办法改变现有 `Session` 对象中的 `options`。

## 属性

### `session.defaultSession`
> 属性:**返回应用程序的默认 `Session`对象**

### 事件

#### 事件: 'will-download'
> 触发:**Electron 将要从 `webContents` 下载 `item` 时**

* `event` Event
* `item` [DownloadItem](download-item.md)
* `webContents` [WebContents](web-contents.md)

调用 `event.preventDefault()` 可以取消下载, `item`将不能从进程的下一刻开始。
```JavaScript
const {session} = require('electron')
session.defaultSession.on('will-download', (event, item, webContents) => {
  event.preventDefault()
  require('request')(item.getURL(), (data) => {
    require('fs').writeFileSync('/somewhere', data)
  })
})
```

### 实例方法

#### `ses.getCacheSize(callback)`
> 用途:**获取当前缓存大小并调用 `callback`**

* `callback` Function
  * `size` Integer - 缓存大小(字节)

#### `ses.clearCache(callback)`
> 用途:**清除会话的HTTP缓存并调用 `callback`**

* `callback` Function

#### `ses.clearStorageData([options, callback])`
> 用途:**清除Web存储的数据**

* `options` Object (可选)
  * `origin` String - 应该遵循 `window.location.origin`的格式 `scheme://host:port`。
  * `storages` String[] - 要清除的存储类型, 可以包含 : `appcache`, `cookies`, `filesystem`, `indexdb`, `localstorage`,`shadercache`, `websql`, `serviceworkers`
  * `quotas` String[] - 要清除的配额类型, 可以包含: `temporary`, `persistent`, `syncable`.
* `callback` Function (可选) - 操作完成时调用.

#### `ses.flushStorageData()`
> 用途:**将没有写入的DOMStorage数据写入磁盘**

#### `ses.setProxy(config, callback)`
> 用途:**设置代理并调用 `callback`**

* `config` Object
  * `pacScript` String - 与 PAC 文件相关的 URL.
  * `proxyRules` String -  要使用的代理规则.
  * `proxyBypassRules` String - 应绕过代理设置的网址规则。
* `callback` Function - 操作完成时调用.

当 `pacScript` 和 `proxyRules` 一起提供时,将忽略 `proxyRules`,并应用 `pacScript` 配置 .

`proxyRules` 需要遵循下面的规则:
```
proxyRules = schemeProxies[`;`<schemeProxies>]
schemeProxies = [<urlScheme>`=`]<proxyURIList>
urlScheme = `http` | `https` | `ftp` | `socks`
proxyURIList = <proxyURL>[`,`<proxyURIList>]
proxyURL = [<proxyScheme>`://`]<proxyHost>[`:`<proxyPort>]
```

例子:
* `http=foopy:80;ftp=foopy2` - 为 `http://`使用HTTP代理 `foopy:80` , 为 `ftp://`使用HTTP代理 `foopy2:80` .
* `foopy:80` - 为所有 URL 使用HTTP代理 `foopy:80` .
* `foopy:80,bar,direct://` - 为所有 URL 使用HTTP代理 `foopy:80` , 如果 `foopy:80` 不可用,则切换使用  `bar`, 再往后就不使用代理了.
* `socks4://foopy` - 为所有 URL 使用SOCKS4代理 `foopy:1080`.
* `http=foopy,socks5://bar.com` - 为所有 URL 使用HTTP代理 `foopy`, 如果 `foopy`不可用,则切换到SOCKS5代理 `bar.com`.
* `http=foopy,direct://` - 为所有http url 使用 HTTP 代理,如果 `foopy`不可用,则不使用代理.
* `http=foopy;socks=foopy2` -  为所有http url 使用 `foopy` 代理,为所有其他 url 使用 `socks4://foopy2` 代理.

 `proxyBypassRules`是一个以逗号分隔的规则列表,如下所述:
 
* `[ URL_SCHEME "://" ] HOSTNAME_PATTERN [ ":" <port> ]` 
匹配与所有与HOSTNAME_PATTERN模式相匹配的主机名。如:"foobar.com", "*foobar.com", "*.foobar.com", "*foobar.com:99", "https://x.*.y.com:99"

 * `"." HOSTNAME_SUFFIX_PATTERN [ ":" PORT ]` 
 匹配特定域后缀。如:".google.com", ".com", "http://.google.com"

* `[ SCHEME "://" ] IP_LITERAL [ ":" PORT ]`
匹配作为IP地址文字的网址。如:"127.0.1", "[0:0::1]", "[::1]", "http://[::1]:99"

*  `IP_LITERAL `/` PREFIX_LENGHT_IN_BITS`
匹配到指定范围之间的IP字面值的任何URL。 IP范围使用CIDR表示法指定。如:"192.168.1.1/16", "fefe:13::abc/33".

*  `<local>`
匹配本地地址。即匹配"127.0.0.1", "::1", "localhost"之一。

#### `ses.resolveProxy(url, callback)`
> 用途:**解析 `url` 的代理信息并调用 `callback`**

* `url` URL
* `callback` Function
  * `proxy` Object

#### `ses.setDownloadPath(path)`
> 用途:**设置下载目录**

* `path` String - 下载地址

下载目录默认是应用所在目录里的 `Downloads`文件夹.

#### `ses.enableNetworkEmulation(options)`
> 用途:**通过 `session`的给定配置模拟网络**

* `options` Object
  * `offline` Boolean - 是否模拟网络中断。默认为 `false`
  * `latency` Double (可选) -每毫秒的 RTT。默认为0,禁用延迟调节。
  * `downloadThroughput` Double (可选) -下载速率(以Bps为单位)。默认值为0,禁用下载限制。
  * `uploadThroughput` Double (可选) - 上传速率(以Bps为单位)。默认值为0,禁用上传限制。

```JavaScript
//模拟具有50kbps吞吐量和500 ms延迟的GPRS连接
window.webContents.session.enableNetworkEmulation({
  latency: 500,
  downloadThroughput: 6400,
  uploadThroughput: 6400
})

//模拟网络中断
window.webContents.session.enableNetworkEmulation({offline: true})
```

#### `ses.disableNetworkEmulation()`
> 用途:**重置为默认网络配置并停止所有已经使用 `session` 的活跃模拟网络**

#### `ses.setCertificateVerifyProc(proc)`
> 用途:**每当请求服务器证书验证时为`session`验证证书并调用 `callback`接收结果**

* `proc` Function
  * `request` Object
    * `hostname` String
    * `certificate` [Certificate](structures/certificate.md)
    * `error` String - 验证结果.
  * `callback` Function
    * `verificationResult` Integer - [证书错误码](https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h).以下为常见错误代码:
      * `0` - 表示成功并禁用证书Transperancy验证
      * `-2` - 表示失败
      * `-3` - 使用chrome的验证结果。
，

 `callback(0)` 来接收证书 , `callback(-2)` 来拒绝验证证书., `setCertificateVerifyProc(null)` ,则恢复为默认证书验证过程。

```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()

win.webContents.session.setCertificateVerifyProc((request, callback) => {
  const {hostname} = request
  if (hostname === 'github.com') {
    callback(0)
  } else {
    callback(-2)
  }
})
```

#### `ses.setPermissionRequestHandler(handler)`
> 用途:**设置可以用于响应 `session`权限请求的处理程序并通过 `callback(true)` 允许或 `callback(false)` 拒绝该权限**

* `handler` Function
  * `webContents` [WebContents](web-contents.md) -  [WebContents](web-contents.md) 请求权限.
  * `permission` String -枚举了 `'media'`, `'geolocation'`, `'notifications'`, `'midiSysex'`, `'pointerLock'`, `'fullscreen'`, `'openExternal'`.
  * `callback` Function
    * `permissionGranted` Boolean -允许或拒绝该权限
    

```JavaScript
const {session} = require('electron')
session.fromPartition('some-partition').setPermissionRequestHandler((webContents, permission, callback) => {
  if (webContents.getURL() === 'some-host' && permission === 'notifications') {
    return callback(false) // 被拒绝.
  }

  callback(true)
})
```

#### `ses.clearHostResolverCache([callback])`
> 用途:**清除主机解析器缓存**

* `callback` Function (可选) - 操作完成时调用.

#### `ses.allowNTLMCredentialsForDomains(domains)`
> 用途:**动态设置是否始终发送HTTP NTLM或协商身份验证的凭据**

* `domains` String - 启用集成身份验证的服务器的逗号分隔列表。

```JavaScript
const {session} = require('electron')
// 以 `example.com`,`foobar.com`,`baz`等结尾用于集成身份验证的网址
session.defaultSession.allowNTLMCredentialsForDomains('*example.com, *foobar.com, *baz')

//用于集成身份验证的所有网址
session.defaultSession.allowNTLMCredentialsForDomains('*')
```

#### `ses.setUserAgent(userAgent[, acceptLanguages])`
> 用途:**覆盖此会话的 `userAgent`和 `acceptLanguages`**

* `userAgent` String
* `acceptLanguages` String (可选) 以逗号分隔的语言列表,例如 `en-US,fr,de,ko,zh-CN,ja`。

该方法不会影响现有 `WebContents`,每个 `WebContents`可以使用 `webContents.setUserAgent`来覆盖会话范围的 `userAgent`。

#### `ses.getUserAgent()`
> 用途:**获取此会话的 `userAgent`( `String`)**

#### `ses.getBlobData(identifier, callback)`
> 用途:**获取与 `identifier`相关联的 `blob`数据( `Blob`)**

* `identifier` String - Valid UUID.
* `callback` Function
  * `result` Buffer - Blob data.

#### `ses.createInterruptedDownload(options)`
> 用途:**允许从以前的 `Session` 恢复 `已取消`或 `已中断`的下载**

* `options` Object
  * `path` String - 下载的绝对路径
  * `urlChain` String[] - 完整的下载链接
  * `mimeType` String (可选)
  * `offset` Integer - 下载的开始范围
  * `length` Integer - 下载大小
  * `lastModified` String - Last-Modified标头值
  * `eTag` String - ETag头值。
  * `startTime` Double (可选) - 秒级的下载时间


API将生成可以用[will-download](＃event-will-download)进行访问的[DownloadItem](download-item.md)事件。该事件没有任何可关联的 `WebContents`且它的初始状态将被 `中断`。

只有当在[DownloadItem](download-item.md)上调用 `resume` API时,下载将开始。

#### `ses.clearAuthCache(options[, callback])`
> 用途:**清除会话的HTTP身份验证缓存**

* `options` ([RemovePassword](structures/remove-password.md) | [RemoveClientCertificate](structures/remove-client-certificate.md))
* `callback` Function (可选) - 操作完成时调用

### 实例属性

#### `ses.cookies`
> 属性:**此会话的Cookie对象**

#### `ses.webRequest`
> 属性:**此会话的WebRequest对象**


#### `ses.protocol`
> 属性:**此会话的Protocol对象([protocol](protocol.md)模块的实例)**

```JavaScript
const {app, session} = require('electron')
const path = require('path')

app.on('ready', function () {
  const protocol = session.fromPartition('some-partition').protocol
  protocol.registerFileProtocol('atom', function (request, callback) {
    var url = request.url.substr(7)
    callback({path: path.normalize(`${__dirname}/${url}`)})
  }, function (error) {
    if (error) console.error('无法注册协议')
  })
})
```