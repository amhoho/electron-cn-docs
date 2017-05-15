# 本文介绍:screen(检索屏幕信息)

> `screen` 模块用于检索有关屏幕大小,显示,光标位置等的信息
进程: [主进程](../glossary.md#主进程) [渲染进程](../glossary.md#渲染进程)       
           
 `screen` 是一个 [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter).
 
 **注意:** `app`模块必须用在 `ready`事件后.
 
 **注意:** 在渲染进程或开发者工具栏中, `window.screen` 是一个预设值的 DOM属性, 所以 `var screen = require('electron').screen` 这样写的话是无效的.
 
创建一个全屏窗口的例子 :

```JavaScript
const electron = require('electron')
const {app, BrowserWindow} = electron

let win

app.on('ready', () => {
  const {width, height} = electron.screen.getPrimaryDisplay().workAreaSize
  win = new BrowserWindow({width, height})
  win.loadURL('https://github.com')
})
```

在外部显示器中创建窗口的另一个示例：
```JavaScript
const electron = require('electron')
const {app, BrowserWindow} = require('electron')

let win

app.on('ready', () => {
  let displays = electron.screen.getAllDisplays()
  let externalDisplay = displays.find((display) => {
    return display.bounds.x !== 0 || display.bounds.y !== 0
  })

  if (externalDisplay) {
    win = new BrowserWindow({
      x: externalDisplay.bounds.x + 50,
      y: externalDisplay.bounds.y + 50
    })
    win.loadURL('https://github.com')
  }
})
```
## `Display` 对象

`Display`对象表示连接到系统的物理显示器。虚拟 `Display` 可以存在于无头系统上, `Display` 也可以是对应于远程的虚拟显示器。
* `display` object
  * `id` Integer -  与显示相关联的唯一标识符。
  * `rotation` Integer - 可选 `0`, `1`, `2`, `3`, 每个代表顺时针方向的屏幕旋转角度, 可选 `0`, `90`, `180`, `270`。
  * `scaleFactor` Number -输出设备的像素比例因子。
  * `touchSupport` String - 是否支持触摸,可选 `available`, `unavailable`, `unknown`.
  * `bounds` Object [Rectangle](rectangle.md)
  * `size` Object
   * `height` Number
   * `width` Number
  *  `workArea` [Rectangle](rectangle.md)
  * `workAreaSize` Object
   * `height` Number
   * `width` Number
  
## 事件

### 事件: 'display-added'
> 触发:**添加 `newDisplay`显示器时**

* `event` Event
* `newDisplay` [Display](structures/display.md)

### 事件: 'display-removed'
> 触发:**移除 `oldDisplay`显示器时**

* `event` Event
* `oldDisplay` [Display](structures/display.md)

### 事件: 'display-metrics-changed'
> 触发:**更改 `display` 中的一个或多个度量时**

* `event` Event
* `display` [Display](structures/display.md)
* `changedMetrics` String[]  描述变化的字符串数组。可选 `bounds`, `workArea`, `scaleFactor`和 `rotation`。

## 方法

### `screen.getCursorScreenPoint()`

> 用途:**获取当前鼠标指针坐标( `Object`)**

* `x` Integer
* `y` Integer

### `screen.getPrimaryDisplay()`
> 用途:**获取当前主显示屏([`Display`](structures/display.md))**

### `screen.getAllDisplays()`
> 用途:**获取所有可用显示屏组成的数组([`Display`](structures/display.md))**

### `screen.getDisplayNearestPoint(point)`
> 用途:**获取离指定点最近的显示屏([`Display`](structures/display.md))**

* `point` Object
  * `x` Integer
  * `y` Integer
  
### `screen.getDisplayMatching(rect)`
> 用途:**获取与提供的边界最接近的显示屏([`Display`](structures/display.md))**

* `rect` [Rectangle](structures/rectangle.md)