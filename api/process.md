# 本文介绍:进程

> 进展的相关属性和扩展管理

进程: [主进程](../glossary.md#主进程), [渲染进程](../glossary.md#渲染进程)      

Electron 中的 `process` 对象是[Node.js的`process` 对象](https://nodejs.org/api/process.html) 的扩展。

## 事件

### 事件: 'loaded'
> 触发:**Electron加载其内部预置脚本并开始载入网页或主脚本时**

当Node关闭时,预置脚本可以用loaded将 原本已删除的Node全局符号 重新添加回 全局范围:
```JavaScript
// 预置脚本
const _setImmediate = setImmediate
const _clearImmediate = clearImmediate
process.once('loaded', () => {
  global.setImmediate = _setImmediate
  global.clearImmediate = _clearImmediate
})
```

## 属性

### `process.noAsar`
> 用途:**一个用于控制应用程序内的ASAR支持的 `Boolean` ,设置为 `true` 可禁用Node的内置模块中的 `asar`**

### `process.type`
> 用途:**返回当前当前进程类型( `String`)**

返回值为 `browser` (即主进程) 或 `renderer`(即渲染进程)。

### `process.versions.electron`
> 用途:**返回Electron的版本号( `String`)***

### `process.versions.chrome`
> 用途:**返回Chrome的版本号( `String`)***

### `process.resourcesPath`
> 用途:**返回资源目录的路径( `String`)***

### `process.mas`
> 用途:**判断是否Mac App Store app( `Boolean`)***

作为Mac App Store应用（app）运行，该属性为 `true`, 否则为 `undefined`。

### `process.windowsStore`
> 用途:**判断是否Windows Store app( `Boolean`)**

作为Windows应用商店应用（appx）运行，则此属性为 `true`, 否则为 `undefined`。

### `process.defaultApp`
> 用途:**判断是否作为参数传递到默认应用程序启动( `Boolean`)**

当 app 在启动时,被作为参数传递给默认应用程序,在主进程中该属性为 `true`, 其他情况均为 `undefined`。

## 方法

### `process.crash()`
> 用途:**使当前进程的主线程崩溃**

### `process.hang()`
> 用途:**使当前进程的主线程挂起**

### `process.setFdLimit(maxDescriptors)` _macOS_ _Linux_
> 用途:**将文件描述符软限制设置为 `maxDescriptors`或OS硬限制,以当前进程的较低者为准**

* `maxDescriptors` Integer

### `process.getProcessMemoryInfo()`
> 用途:**获取有关当前进程内存使用统计信息的对象( `Object`)**

* `workingSetSize` Integer  - 当前固定到实际物理RAM的内存量。
* `peakWorkingSetSize` Integer  - 已经固定到实际物理RAM的最大内存量。
* `privateBytes` Integer  - 其他进程不共享的内存量,例如JS堆或HTML内容。
* `sharedBytes` Integer  - 进程之间共享的内存量,通常是Electron代码本身消耗的内存

请注意,所有统计信息都以千字节(KB)为单位。

### `process.getSystemMemoryInfo()`

返回 `Object`:
* `total` Integer  - 系统可用的物理内存总量。
* `free` Integer  - 应用程序或磁盘缓存未使用的内存总量。
* `swapTotal` Integer  - 系统可用的交换内存总量。 _Windows_ _Linux_
* `swapFree` Integer  - 系统可用的交换内存的自由量。 _Windows_ _Linux_

请注意,所有统计信息都以千字节(KB)为单位。


### `process.getCPUUsage()`
> 用途:**获取处理器使用率( `CPUUsage`)**

返回:
* `CPUUsage` [CPUUsage](structures/cpu-usage.md)

### `process.getIOCounters()` _Windows_ _Linux_
> 用途:**获取进程IO占用值( `CPUUsage`)**

返回:
* `IOCounters` [IOCounters](structures/io-counters.md)
