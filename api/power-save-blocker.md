# 本文介绍:阻止系统进入低功耗(睡眠)模式

> 当需要应用在前后台无间断工作时,应避免系统进入睡眠.

进程: [主进程](../glossary.md#主进程)               
```JavaScript
const {powerSaveBlocker} = require('electron')
const id = powerSaveBlocker.start('prevent-display-sleep')
console.log(powerSaveBlocker.isStarted(id))
powerSaveBlocker.stop(id)
```

## 方法

`powerSaveBlocker` 模块有如下方法:

### `powerSaveBlocker.start(type)`
> 用途:**开始阻止系统进入睡眠模式.返回识别节能模块的整数( `Integer`)**

* `type` String  - 节电模块类型。
  * `prevent-app-suspension`  -  阻止应用挂起.保持系统活跃,但允许屏幕关闭。如:下载文件或播放音频。
  * `prevent-display-sleep`  - 防止显示器进入睡眠状态。保持系统和屏幕活动。如:播放视频。

 **注意:**  `prevent-display-sleep`总是优先生效于 `prevent-app-suspension`
例如,一个API调用A请求 `prevent-app-suspension`,另一个调用B请求 `prevent-display-sleep`。只有当B停止了 `prevent-display-sleep`,A的 `prevent-app-suspension`才会生效.

### `powerSaveBlocker.stop(id)`
> 用途:**停止指定的省电模式.返回节电阻止程序ID( `Integer`)**

* `id` Integer - 通过 `powerSaveBlocker.start` 返回的保持活跃的 blocker id.

### `powerSaveBlocker.isStarted(id)`
> 用途:**判断 `powerSaveBlocker`是否已经开始( `Boolean`)**

* `id` Integer - 通过 `powerSaveBlocker.start` 返回的保持活跃的 blocker id.