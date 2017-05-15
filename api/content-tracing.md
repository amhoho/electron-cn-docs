# 本文介绍:contentTracing类
>从Chromium的内容模块收集跟踪数据,以发现性能瓶颈和缓慢的操作

进程: [主进程](../glossary.md#主进程)    

这个模块不具备web接口,需要我们在chrome浏览器中添加 `chrome://tracing/` 来加载生成结果文件.

注意该模块应当在 `ready`事件之后使用.

```JavaScript
const {contentTracing} = require('electron')
const options = {
  categoryFilter: '*',
  traceOptions: 'record-until-full,enable-sampling'
}
contentTracing.startRecording(options, () => {
  console.log('Tracing started')

  setTimeout(() => {
    contentTracing.stopRecording('', (path) => {
      console.log('Tracing data recorded to ' + path)
    })
  }, 5000)
})
```
## 方法

### `contentTracing.getCategories(callback)`
> 用途:**获取一组类别组,而且类别组可以随着到达新的代码路径而改变**

* `callback` Function
  * `categories` String[]

一旦所有的子进程都接受到了 `getCategories`方法请求,  则调用 `callback`返回类别组.

### `contentTracing.startRecording(options, callback)`
> 用途:**在所有进程上开始记录**

* `options` Object
  * `categoryFilter` String
  * `traceOptions` String
* `callback` Function

一旦子进程接收到 `EnableRecording`请求,将立即以异步开始记录。

一旦所有子进程都确认了 `startRecording`请求, `callback`将被调用。

 `categoryFilter`是一个过滤器,用于控制应跟踪哪些类别组。
 
 过滤器可以具有可选的 `-`前缀,以排除包含匹配类别的类别组。不支持在同一列表中包括和排除类别模式。

比如:
* `test_MyTest*`,
* `test_MyTest*,test_OtherStuff`,
* `"-excluded_category1,-excluded_category2`

`traceOptions` 控制着哪种跟踪应该被启动,这是一个用逗号分隔的列表.可用参数如下:

* `record-until-full`
* `record-continuously`
* `trace-to-console`
* `enable-sampling`
* `enable-systrace`

前3个参数是来跟踪记录模块,并且是互斥关系.

如果在 `traceOptions` 中超过一个跟踪记录模式,那最后一个的优先级最高.

如果没有指明跟踪记录模式,那么它默认为 `record-until-full`.

在 `traceOptions` 中的参数被解析应用之前,跟踪参数会被初始化为默认参数 ( `record_mode` 设置为 `record-until-full`, `enable_sampling` 和 `enable_systrace` 设置为 `false`).

### `contentTracing.stopRecording(resultFilePath, callback)`
> 用途:**停止对所有子进程的记录**

* `resultFilePath` String
* `callback` Function
  * `resultFilePath` String

子进程通常缓存跟踪数据,并且仅仅将数据截取和发送给主进程.

这有利于在通过 IPC 发送跟踪数据之前减小跟踪时的运行开销,这样做很有价值.

因此,发送跟踪数据,我们应当异步通知所有子进程来截取任何待跟踪的数据.

一旦所有子进程接收到了 `stopRecording` 请求,将调用 `callback` ,并且返回一个包含跟踪数据的文件.

如果 `resultFilePath` 不为空,那么将把跟踪数据写入其中,否则写入一个临时文件.实际文件路径如果不为空,则将调用 `callback` .

### `contentTracing.startMonitoring(options, callback)`
> 用途:**开始监视所有进程**

* `options` Object
  * `categoryFilter` String
  * `traceOptions` String
* `callback` Function

一旦子进程接收到 `startMonitoring`请求,将立即以异步开始监听。

一旦所有子进程都确认了 `startRecording`请求, `callback`将被调用。

### `contentTracing.stopMonitoring(callback)`
> 用途:**停止对所有子进程的监听**

* `callback` Function

一旦所有子进程接收到了 `stopMonitoring` 请求, `callback`将被调用。

### `contentTracing.captureMonitoringSnapshot(resultFilePath, callback)`
> 用途:**获取当前监听的跟踪数据**

* `resultFilePath` String
* `callback` Function
  * `resultFilePath` String

子进程通常缓存跟踪数据,并且仅仅将数据截取和发送给主进程.

因为如果直接通过 IPC 来发送跟踪数据的代价昂贵,我们应当避免不必要的跟踪运行开销.

因此,为了停止跟踪,我们可以通过异步通知所有子进程来刷新任何挂起的跟踪数据。

一旦所有子进程接收到了 `captureMonitoringSnapshot` 请求,将调用 `callback` ,并且返回一个包含跟踪数据的文件.

### `contentTracing.getTraceBufferUsage(callback)`
> 用途:**获取当前监听的跟踪数据**

* `callback` Function
  * `value` Number
  * `percentage` Number
  
获取跟踪缓冲区进程的最大使用量与完整状态的百分比。当TraceBufferUsage值被确定时,就调用 `callback` .