# 类:Tray(系统托盘如Win右下角)

>将图标和上下文菜单添加到系统托盘。

进程: [主进程](../glossary.md#主进程)           

 `Tray`是一个[EventEmitter][event-emitter].
 
```JavaScript
const {app, Menu, Tray} = require('electron')
let tray = null
app.on('ready', () => {
  tray = new Tray('/path/to/my/icon')
  const contextMenu = Menu.buildFromTemplate([
    {label: 'Item1', type: 'radio'},
    {label: 'Item2', type: 'radio'},
    {label: 'Item3', type: 'radio', checked: true},
    {label: 'Item4', type: 'radio'}
  ])
  tray.setToolTip('这是我的应用程序.')
  tray.setContextMenu(contextMenu)
})
```

 __平台限制:__
* 在Windows上,建议使用ICO图标以获得最佳的视觉效果。
* 如果应用程序指示器没有一个上下文菜单时,它将不会显示。
* 在Linux中,如果支持应用程序指示器则使用它,否则使用 `GtkStatusIcon`。
* 在Linux的发行版中,仅支持应用程序指示器,您需要安装 `libappindicator1` ,以便使用托盘图标(tray icon)。
* 在Linux中,如果使用了应用指示器, `click`事件则被忽略.
* 在Linux中,为了让个别的 `MenuItem` 起效,你必须再次调用 `setContextMenu` .例如:
```JavaScript
const {app, Menu, Tray} = require('electron')

let appIcon = null
app.on('ready', () => {
  appIcon = new Tray('/path/to/my/icon')
  const contextMenu = Menu.buildFromTemplate([
    {label: 'Item1', type: 'radio'},
    {label: 'Item2', type: 'radio'}
  ])

//更改上下文菜单
  contextMenu.items[1].checked = false
//因为我们修改了上下文菜单,所以再次为Linux调用
  appIcon.setContextMenu(contextMenu)
})
```
如果你想在所有平台上保持完全相同的行为,你不应该依赖于click事件,而是附加上下文菜单到托盘图标。

### `new Tray(image)`
> 用途:**创建与 `image`关联的新托盘图标**

* `image` ([NativeImage](native-image.md) | String)

### 实例事件

 `Tray` 模块内含下列事件:

#### 事件: 'click'
> 触发:**单击托盘图标时**

* `event` Event
  * `altKey` Boolean
  * `shiftKey` Boolean
  * `ctrlKey` Boolean
  * `metaKey` Boolean
* `bounds` [Rectangle](structures/rectangle.md) - 托盘图标的边界

#### 事件: 'right-click' _macOS_ _Windows_
> 触发:**右击托盘图标时**

* `event` Event
  * `altKey` Boolean
  * `shiftKey` Boolean
  * `ctrlKey` Boolean
  * `metaKey` Boolean
* `bounds` [Rectangle](structures/rectangle.md) - 托盘图标的边界

#### 事件: 'double-click' _macOS_ _Windows_
> 触发:**双击托盘图标时**

* `event` Event
  * `altKey` Boolean
  * `shiftKey` Boolean
  * `ctrlKey` Boolean
  * `metaKey` Boolean
* `bounds` [Rectangle](structures/rectangle.md) - 托盘图标的边界



#### 事件: 'balloon-show' _Windows_
> 触发:**显示托盘气泡时**

#### 事件: 'balloon-click' _Windows_
> 触发:**点击托盘气泡时**

#### 事件: 'balloon-closed' _Windows_
> 触发:**因超时而关闭托盘气泡时**

#### 事件: 'drop' _macOS_
> 触发:**将任何内容拖动到托盘图标时**

#### 事件: 'drop-files' _macOS_
> 触发:**将文件拖动到托盘图标时**

* `event` Event
* `files` String[] - 被拖动文件的路径.

#### 事件: 'drop-text' _macOS_
> 触发:**将文本内容拖动到托盘图标时**

* `event` Event
* `text` String - 被拖动的文本

#### 事件: 'drag-enter' _macOS_
> 触发:**当拖动操作进入托盘图标时**

#### 事件: 'drag-leave' _macOS_
> 触发:**当拖动操作离开托盘图标时**

#### 事件: 'drag-end' _macOS_
> 触发:**托盘或其它地方结束拖动时**

## 实例方法
 `Tray` 模块有以下方法:

#### `tray.destroy()`
> 用途:**销毁托盘图标**

#### `tray.setImage(image)`
> 用途:**设置与此托盘图标相关联的 `image`**

* `image` ([NativeImage](native-image.md) | String)

#### `tray.setPressedImage(image)` _macOS_
> 用途:**在macOS中点按托盘图标时,设置与此托盘图标相关联的 `image`**

* `image` [NativeImage](native-image.md)

#### `tray.setToolTip(toolTip)`
> 用途:**设置此托盘图标的悬停提示内容**

* `toolTip` String

#### `tray.setTitle(title)` _macOS_
> 用途:**状态栏里托盘图标旁边需要显示的标题**

* `title` String

#### `tray.setHighlightMode(mode)` _macOS_
> 用途:**当托盘图标被点击的时候,是否高亮它的背景色为蓝色**

* `mode` String - 高亮方式,可选以下:
  * `selection` - 单击托盘图标或其上下文菜单处于打开状态时,进行高亮。这是默认值。
  * `always` - 始终高亮
  * `never` - 禁止高亮


**注意:** 当窗口可见性进行更改时,你可以使用 `highlightMode`模块让一个 [`BrowserWindow`](browser-window.md) 切换为 `'never'` 或 `'always'` 模式.
```JavaScript
const {BrowserWindow, Tray} = require('electron')
const win = new BrowserWindow({width: 800, height: 600})
const tray = new Tray('/path/to/my/icon')
tray.on('click', () => {
  win.isVisible() ? win.hide() : win.show()
})
win.on('show', () => {
  tray.setHighlightMode('always')
})
win.on('hide', () => {
  tray.setHighlightMode('never')
})
```

#### `tray.displayBalloon(options)` _Windows_
> 用途:**显示托盘气球**

* `options` Object
  * `icon` ([NativeImage](native-image.md) | String) - (可选)
  * `title` String - (可选)
  * `content` String - (可选)

#### `tray.popUpContextMenu([menu, position])` _macOS_ _Windows_
> 用途:**弹出托盘图标的上下文菜单**

* `menu` Menu (可选)
* `position` Object (可选) - 弹出位置。
  * `x` Integer
  * `y` Integer

划过 `menu`时则显示 `menu`,而不是托盘图标的上下文菜单。`position`仅在Windows上可用,默认为(0,0)。

#### `tray.setContextMenu(menu)`
> 用途:**设置此图标的上下文菜单。**
* `menu` Menu

#### `tray.getBounds()` _macOS_ _Windows_
> 用途:**获取此图标的 `bounds`对象([`Rectangle`](structures/rectangle.md))**


#### `tray.isDestroyed()`
> 用途:**判断托盘图标是否已被销毁( `Boolean`)**

[event-emitter]: https://nodejs.org/api/events.html#events_class_eventemitter