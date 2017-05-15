# 本文介绍:无框窗口的应用与操作

> 没有工具栏或任何边框仅纯内容的窗口.

## 创建无框窗口
在 [BrowserWindow](browser-window.md)的 `options`中设置 `frame` 为 `false` 即可创建无框窗口.

```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow({width: 800, height: 600, frame: false})
win.show()
```

### macOS上的替代方案
在macOS 10.9 Mavericks和更新版本中，有一种不同于设置 `frame`为 `false`的替代方法来生成无边框窗口。

无边框通常会隐藏标题栏以及失去对窗口的控制，如果你希望无边框的同时又保持对窗口的控制( `traffic lights`)。你可以通过设置下文的 `titleBarStyle`两个属性来实现:

#### `hidden`
仅左上角有正常窗口控制( `traffic lights`)的无边框.

```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow({titleBarStyle: 'hidden'})
win.show()
```

#### `hidden-inset`
在边缘嵌入窗口控制( `traffic lights`)

```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow({titleBarStyle: 'hidden-inset'})
win.show()
```


## 透明窗口
设置 `transparent`为 `true`可使无框窗口透明:

```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow({transparent: true, frame: false})
win.show()
```

### 限制

* 无法点击穿透区域(透明窗口实质是个矩形窗口,假设内容是圆形,那么圆形与矩形的交集处透明区域无法点击穿透)。详见[讨论帖](https://github.com/electron/electron/issues/1335)       
* 透明窗口是不可调整大小的。在某些平台上，设置 `resizable`为 `true`也许会造成透明窗口停止工作。
* `blur`滤光器只适用于网页，所以没法将模糊效果用于窗口之下
* 在Windows系统中，当DWM被禁用时透明窗口不会工作。
* Linux用户需要命令行 `--enable-transparent-visuals --disable-gpu`
  来禁用GPU以及允许ARGB去渲染透明窗口，这是由于一个Linux上的上游bug造成的    [详见此文](https://code.google.com/p/chromium/issues/detail?id=369209)       
* 在Mac上，透明窗口的阴影不会显示出来.

## 临时的穿透方案
使用[win.setIgnoreMouseEvents(ignore)][ignore-mouse-events] API创建可穿透窗口(即不响应所有的鼠标事件):
```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.setIgnoreMouseEvents(true)
```

## 可拖动区域
默认情况下，无框窗口是不可拖动的。

您需要在CSS中设置 `-webkit-app-region: drag`设定可拖动区域,或设置 `-webkit-app-region: no-drag` 禁止拖动的区域。

需要注意的是，目前只支持矩形区域。

```html
<body style=`-webkit-app-region: drag`>
</body>
```
另外需要注意的是，如果你设置了整个窗口可拖动，你必须标记按钮为不可拖动,否则用户无法点击它们：
```css
button {
  -webkit-app-region: no-drag;
}
```
如果你设置一个自定义的标题栏可拖动，你同样需要将标题栏中所有按钮设置为不可拖动.

## 文本选择
在一个无框窗口中，拖动动作可能与文本选择发生冲突。比如，当你拖动标题栏，可能会变成选中标题栏上的文本。

为了防止这种情况发生，你需要向下面这样在一个可拖动区域中禁用文本选择:

```css
.titlebar {
  -webkit-user-select: none;
  -webkit-app-region: drag;
}
```

## 上下文菜单
在一些平台上，可拖动区域会被认为是非客户端框架(non-client frame)，当你点击右键时，会弹出系统菜单。

为了保证上下文菜单在所有平台下正确的显示，你不应该在可拖动区域使用自定义上下文菜单。

[ignore-mouse-events]: browser-window.md#winsetignoremouseeventsignore