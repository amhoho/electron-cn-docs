# 模块:webFrame(自定义渲染页面)

> `web-frame` 模块可自定义渲染当前网页

进程: [渲染进程](../glossary.md#渲染进程)              

例如放大当前页至 200%.

```JavaScript
const {webFrame} = require('electron')
webFrame.setZoomFactor(2)
```

## 方法

### `webFrame.setZoomFactor(factor)`
 > 用途:**设置页面的缩放系数**
 
* `factor` Number - 缩放系数

注意:缩放系数是百分制的,如3.0=300％。

### `webFrame.getZoomFactor()`
 > 用途:**获得当前缩放系数( `Number`)**

### `webFrame.setZoomLevel(level)`
 > 用途:**将缩放级别更改为指定级别**

* `level` Number - 缩放级别

原始大小为0,每个增量表示放大或缩小20％,默认限制为原始大小的300％至50％。

### `webFrame.getZoomLevel()`
 > 用途:**获得当前缩放级别( `Number`)**

### `webFrame.setVisualZoomLevelLimits(minimumLevel, maximumLevel)`
 > 用途:**设置缩放级别的最大值和最小值**

* `minimumLevel` Number
* `maximumLevel` Number

### `webFrame.setLayoutZoomLevelLimits(minimumLevel, maximumLevel)`
 > 用途:**设置基于布局(即非视觉)的缩放级别的最大值和最小值**

* `minimumLevel` Number
* `maximumLevel` Number

### `webFrame.setSpellCheckProvider(language, autoCorrectWord, provider)`
 > 用途:**为输入框或文本域设置拼写检查的提供程序**
 
* `language` String
* `autoCorrectWord` Boolean
* `provider` Object
  * `spellCheck` Function - 返回 `Boolean`
    * `text` String

 `provider`必须是具有 `spellCheck`方法的对象,该方法返回扫过的单词是否拼写正确。
 
把[node-spellchecker][spellchecker]作为提供程序的示例:

```JavaScript
const {webFrame} = require('electron')
webFrame.setSpellCheckProvider('en-US', true, {
  spellCheck (text) {
    return !(require('spellchecker').isMisspelled(text))
  }
})
```

### `webFrame.registerURLSchemeAsSecure(scheme)`
 > 用途:**将 `scheme` 注册为安全协议**
 
* `scheme` String

例如, `https`和 `data`是安全的协议,它不能轻易的被黑客破坏。

### `webFrame.registerURLSchemeAsBypassingCSP(scheme)`
 > 用途:**忽略当前页面的内容安全策略,资源全部从此 `scheme` 加载**
 
* `scheme` String

### `webFrame.registerURLSchemeAsPrivileged(scheme[, options])`
 > 用途:**忽略内容安全策略,将 `scheme` 注册为安全协议且允许注册ServiceWorker和支持fetch API**
 
* `scheme` String
* `options` Object (可选)
  * `secure` Boolean - (可选) 默认为 `true`
  * `bypassCSP` Boolean - (可选) 默认为 `true`
  * `allowServiceWorkers` Boolean - (可选) 默认为 `true`
  * `supportFetchAPI` Boolean - (可选) 默认为 `true`
  * `corsEnabled` Boolean - (可选) 默认为 `true`

以上选项值如指定 `false`则表示从注册中忽视该选项。

在不绕过内容安全策略的情况下注册特权方案的示例:
```JavaScript
const {webFrame} = require('electron')
webFrame.registerURLSchemeAsPrivileged('foo', { bypassCSP: false })
```

### `webFrame.insertText(text)`
 > 用途:**插入 `text` 到焦点元素**
 
* `text` String

### `webFrame.executeJavaScript(code[, userGesture, callback])`
 > 用途:**在页面中评估(eval) `code`( `Promise`)**
 
* `code` String
* `userGesture` Boolean (可选) - 默认为 `false`,设置为 `true`时可使某些HTML API(如 `requestFullScreen`)手势去除由用户手势进行调用的限制 。
* `callback` Function (可选) - 在脚本执行后调用
  * `result` Any


返回 `Promise`  - 如果被正确执行则返回可用的承诺，否则将被拒绝。

在浏览器窗口中，一些HTML API（如`requestFullScreen`)只能由用户的手势调用。 需要将 `userGesture`设置为 `true`进行消除这个限制。

### `webFrame.getResourceUsage()`
 > 用途:**获取描述Blink内存缓存信息的对象( `Object`)**

* `images` [MemoryUsageDetails](structures/memory-usage-details.md)
* `cssStyleSheets` [MemoryUsageDetails](structures/memory-usage-details.md)
* `xslStyleSheets` [MemoryUsageDetails](structures/memory-usage-details.md)
* `fonts` [MemoryUsageDetails](structures/memory-usage-details.md)
* `other` [MemoryUsageDetails](structures/memory-usage-details.md)

```JavaScript
const {webFrame} = require('electron')
console.log(webFrame.getResourceUsage())
```

这将生成:
```JavaScript
{
  images: {
    count: 22,
    size: 2549,
    liveSize: 2542,
  },
  cssStyleSheets: { /* 类同上行的 `images` */ },
  xslStyleSheets: { /* 类同上行的 `images` */ },
  fonts: { /* 类同上行的 `images` */ },
  other: { /* 类同上行的 `images` */ }
}
```

### `webFrame.clearCache()`
 > 用途:**释放无用内存(如如来自上一个导航的图像)**

如果盲调用此方法,可能会使Electron运行比较卡,因为它将不得不重新填充这些空的缓存!

建议在某些比较消耗内存的应用事件时才进行调用,比如加载了个很冗长复杂且需长时间停留使用的页面.

[spellchecker]: https://github.com/atom/node-spellchecker