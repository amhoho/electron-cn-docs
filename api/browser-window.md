# 本文介绍:应用窗口的创建与控制
> 窗口的创建与控制

进程:[主进程](../glossary.md#主进程)                

```JavaScript
//在主进程中.
const {BrowserWindow} = require('electron')

//或者从渲染器进程中使用 `remote`.
// const {BrowserWindow} = require('electron').remote

let win = new BrowserWindow({width:800,height:600})
win.on('closed',() => {
  win = null
})

//加载远程URL
win.loadURL('https://github.com')

//或加载本地HTML文件
win.loadURL(`file://${__dirname}/app/index.html`)
```

## 无框窗口

使用[Frameless Window](frameless-window.md) 即可创建仅内容本身或任意形状的透明窗口.

## 显示前过渡办法

如果像浏览器一样边加载页面边显示界面,会令人觉得很丑,这里提供了一个办法:背景色一致与加载前事件.

### 使用 `ready-to-show` 事件

 加载页面时,先用 `ready-to-show`事件进程完成首次绘制,在该事件后显示窗口. 
 
```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow({show:false})
win.once('ready-to-show',() => {
  win.show()
})
```

这是事件通常发生在 `did-finish-load` 事件之后,但是页面有许多远程资源,它可能会在 `did-finish-load` 之前触发事件.

### 设置`backgroundColor`

如果程序较复杂,`ready-to-show`事件可能好久才完成,窗口背景色 `backgroundColor` 应当网页的背景色一致,避免突兀的感觉.

```JavaScript
const {BrowserWindow} = require('electron')

let win = new BrowserWindow({backgroundColor:'＃2e2c29'})
win.loadURL('https://github.com')
```

### 父子窗口

通过使用`parent`选项,可以创建子窗口:

```JavaScript
const {BrowserWindow} = require('electron')

let top = new BrowserWindow()
let child = new BrowserWindow({parent:top})
child.show()
top.show()
```

`child` 窗口将总是显示在 `top` 窗口的顶部.

### 模态窗口

模态窗口必须设置`parent`和`modal`选项,它可以与父窗口无关联:

```JavaScript
const {BrowserWindow} = require('electron')

let child = new BrowserWindow({parent:top,modal:true,show:false})
child.loadURL('https://github.com')
child.once('ready-to-show',()=> {
  child.show()
})
```

### 平台差异
* 在Windows上,不支持动态更改父窗口.
* 在MacOS上,模态窗口附属于父窗口.
* 在MacOS上,当父窗口移动时子窗口将保持与父窗口的相对位置,而在Windows和Linux子窗口则不会移动.
* 在Linux上,模态窗口的类型将更改为`对话框`,许多桌面环境不支持隐藏模态窗口.

## 类:BrowserWindow

>创建和控制浏览器窗口.

过程:[主进程](../glossary.md#main-process)      

### `new BrowserWindow([options])`
> 触发:**构建自定义的窗口**

* `options`Object(可选)
* `width` Integer(可选) - 窗口的宽度(以px为单位).默认值为`800`.
* `height` Integer(可选) - 窗口的高度(以px为单位).默认值为`600`.
* `x` Integer(可选,y存在时必须) - 屏幕左侧偏移.默认居中.
* `y`Integer(可选,x存在时必须) - 屏幕顶部偏移距离.默认居中.
* `useContentSize` Boolean(可选) - 是否采用内容宽高为当前窗口的宽高 .默认值为   `false`  .
* `center` Boolean(可选) - 在屏幕中心显示窗口.
* `minWidth` Integer(可选) - 窗口的最小宽度.默认值为0.
* `minHeight` Integer(可选) - 窗口的最小高度. 默认值为0.
* `maxWidth` Integer(可选) - 窗口的最大宽度.默认是没有限制.
* `maxHeight` Integer(可选) - 窗口的最大高度. 默认是没有限制.
* `resizable` Boolean(可选) - 窗口是否可调整大小. 默认值为 `true`.
* `movable` Boolean(可选) - 窗口是否可移动,默认值为 `true`.在Linux中无效
* `minimizable` Boolean(可选) - 窗口是否可最小化,默认值为 `true`.在Linux中无效
* `maximizable` Boolean(可选) - 窗口是否可以最大化,默认值为 `true`.在Linux中无效
* `closable` Boolean(可选) - 窗口是否可关闭,默认值为 `true`.在Linux中无效
* `focusable` Boolean(可选) - 窗口是否可以聚焦,默认值为 `true`.在Windows设置 `focusable:false`也意味着设置 `skipTaskbar:true`.在Linux设置 `focusable:false`使窗口停止与wm交互且窗口始终置顶.
* `alwaysOnTop` Boolean(可选) - 窗口是否置顶,默认值为  `false` .
* `fullscreen` Boolean(可选) - 窗口是否全屏.当设置为 `false`时,全屏按钮将在macOS上被隐藏或禁用.默认值为 `false`.
* `fullscreenable` Boolean(可选) - 窗口是否可以进入全屏模式.在macOS上,最大化/缩放按钮是否切换全屏模式或最大化窗口.默认值为 `true`.
* `skipTaskbar` Boolean(可选) - 是否在任务栏中显示窗口.默认值为 `false`.
* `kiosk` Boolean(可选) -  kiosk模式. 默认值为  `false` .
* `title` String(可选) - 默认窗口标题.默认为 `Electron`.
* `icon`([NativeImage](native-image.md)| String)(可选) - 窗口图标.在Windows上推荐使用 `ICO`图标来获得最佳的视觉效果.默认使用可执行文件的图标.
* `show` Boolean(可选) - 创建时是否显示窗口.  默认值为 `true`.
* `frame` Boolean(可选) - `false` 则创建一个[无框窗口](frameless-window.md) .     默认值为 `true`.
* `parent` BrowserWindow(可选) - 指定父窗口. 默认值为null.
* `modal` Boolean(可选) -是否模态窗口,仅子窗口时使用.  默认值为  `false` .
* `acceptFirstMouse` Boolean(可选) -   是否允许单击web view来激活窗口 . 默认值为 `false`.
* `disableAutoHideCursor` Boolean(可选) - 是否在键入时隐藏光标. 默认值为  `false` .
* `autoHideMenuBar` Boolean(可选) - 自动隐藏菜单栏,除非`Alt`键. 默认值为  `false` .
* `enableLargerThanScreen` Boolean(可选) - 是否允许改变窗口大小大于屏幕. 默认值为  `false` .
* `backgroundColor` String(可选) - 窗口的背景颜色如 `＃66CD00`或 `＃FFF`(支持透明度). 默认值为 `＃FFF`(白色).
* `hasShadow` Boolean(可选) -窗口是否阴影.仅macOS中有效.  默认值为 `true`.
* `darkTheme` Boolean(可选) - 强制使用深色dark主题的窗口,只适用于一些GTK + 3桌面环境. 默认值为  `false` .
* `transparent` Boolean(可选) - 窗口是否透明[transparent](frameless-window.md). 默认值为  `false` .
* `titleBarStyle` String(可选) - 窗口标题栏的样式.默认值为 `default`.
    * `default` 标准的灰色不透明的Mac标题栏.
    * `hidden` 隐藏标题栏,内容充满整个窗口,但它依然在左上角,仍然受标准窗口控制.
    * `hidden-inset` 主体隐藏,显示小的控制按钮在窗口边缘.
* `type` String(可选) - 窗口的类型,可选值与效果根据不同系统展示不同效果.
 可选值有:
    * 在Linux上,可选值 `desktop` ,`dock` ,`toolbar` ,`splash`,
    * 在macOS上,可选值 `desktop` ,`textured`.
      * `textured`类型添加金属渐变外观( `NSTexturedBackgroundWindowMask`).
      * `desktop`类型将窗口放置在桌面背景窗口级别( `kCGDesktopWindowLevel  -  1`).注意桌面窗口不会收到焦点,键盘或鼠标事件,但您可以使用 `globalShortcut` 接收输入.
    * 在Windows上,可选值 `toolbar`.
* `thickFrame` Boolean(可选) - 对无框架窗口使用`WS_THICKFRAME'风格. 设置为  `false` 将删除窗口阴影和窗口动画.默认值为 `true`.
* `vibrancy` String(可选) - 窗口是否使用vibrancy动态效果,仅macOS中有效.可选值有:`appearance-based` ,`light` ,`dark` ,`titlebar` ,`selection`, `menu` ,`popover` ,`sidebar` ,`medium-light`or `ultra-dark`.
* `zoomToPageWidth` Boolean(可选) - 单击工具栏上的绿色信号灯按钮或单击 窗口>缩放菜单项时的行为,仅macOS中有效.
    如果`true`,窗口将放大为网页本身宽度,`false` 会使它放大到屏幕宽度.该设置直接影响 `maximize()`的调用. 默认值为  `false` .
* `tabbingIdentifier` String (可选) - 选项卡组的名称,在macOS 10.12+上可使窗口在原生选项卡中打开.  具有相同的标识符选项卡窗口会被分在同一组.
* `webPreferences` Object(可选) - 网页功能的设置,下方是它的对象属性:
  * `devTools` Boolean(可选) - 是否启用DevTools.
    如果它设置为  `false` ,不能使用`BrowserWindow.webContents.openDevTools()`来打开DevTools. 默认值为 `true`.
  * `nodeIntegration` Boolean(可选) - 是否完整支持node.默认值为 `true`.
  * `nodeIntegrationInWorker`  Boolean(可选) - 是否在Web工作器中启用了Node集成。默认值为  `false`,更多细节详见[多线程](../tutorial/multithreading.md)       
  * `preload` String(可选) - 预载脚本,其它脚本运行之前预先加载指定脚本. 无论页面是否集成Node,此脚本都可以访问所有Node API. 脚本路径为绝对路径.      
  当 node integration 关闭,预加载的脚本将从全局范围重新引入node的全局引用标志. [这里是例子](process.md#event-loaded).        
  * `sandbox` Boolean(可选) - 是否启用Chromium操作系统级沙盒.
  * `session`[Session](session.md#class-session) - 设置界面session. 而不是直接忽略session对象 ,也可用 `partition`来代替,它接受一个 partition 字符串. 当同时使用 `session`和 `partition` ,`session`优先级更高.
  默认使用默认 session.
  * `partition`String - 通过session的partition字符串来设置界面session. 如果 `partition`以 `persist:`开头,这个界面将会为所有界面使用相同的 `partition`. 如果没有 `persist:`前缀,界面使用历史session. 通过分享同一个 `partition` ,所有界面使用相同的session. 默认使用默认 session.
  * `zoomFactor` Number(可选) - 页面的默认缩放系数,`3.0`表示 `300％`.默认值为 `1.0`.
  * `javascript` Boolean - 是否启用javascript支持. 默认为 `true`.
  * `webSecurity` Boolean(可选) -  当设置为 `false` ,它将禁用同源策略 (通常用来测试网站),并且如果有2个非用户设置的参数,就设置
  `allowDisplayingInsecureContent`和 `allowRunningInsecureContent`的值为 `true`. 默认为 `true`.
  * `allowDisplayingInsecureContent`Boolean -允许一个使用 https的界面来展示由 http URLs 传过来的资源. 默认 `false`.
  * `allowRunningInsecureContent`Boolean - Boolean -允许一个使用 https的界面来渲染由 http URLs 提交的html,css,javascript. 默认为 `false`.
  * `images` Boolean(可选) - 启用图像支持.默认值为 `true`.
  * `textAreasAreResizable` Boolean(可选) - 使TextArea元素可调整大小.默认值为 `true`.
  * `webgl` Boolean(可选) - 启用WebGL支持. 默认值为 `true`.
  * `webaudio` Boolean(可选) - 启用WebAudio支持.默认值为 `true`.
  * `plugins` Boolean(可选) - 是否应启用插件.默认值为  `false` .
  * `experimentalFeatures` Boolean(可选) - 启用Chromium的实验功能. 默认值为  `false` .
  * `experimentalCanvasFeatures` Boolean(可选) - 启用Chromium的实验画布功能.默认值为  `false` .
  * `scrollBounce` Boolean(可选) - 启用弹力动画(橡皮筋)效果,macOS中有效.默认值为  `false` .
  * `blinkFeatures` String(可选) -  以  `,` 分隔的特性列表,如 `CSSVariables,KeyboardEventKey`.在 [setFeatureEnabledFromString][blink-feature-string]文件中查看被支持的所有特性.
  * `disableBlinkFeatures` String(可选) -  以 `,`分隔的特性列表,如 `CSSVariables,KeyboardEventKey`.在 [RuntimeEnabledFeatures.in][blink-feature-string]文件中查看被支持的所有特性.
  * `defaultFontFamily` Object(可选) - 设置font-family的默认字体.
    * `standard` String(可选) - 默认为 `Times New Roman`.
    * `serif` String(可选) - 默认为 `Times New Roman`.
    * `sansSerif` String(可选) - 默认为 `Arial`.
    * `monospace` String(可选) - 默认为 `Courier New`.
    * `cursive` String(可选) - 默认为 `Script`.
    * `fantasy` String(可选) - 默认为 `Impact`.
  * `defaultFontSize` Integer(可选) - 默认为 `16`.
  * `defaultMonospaceFontSize` Integer(可选) - 默认为 `13`.
  * `minimumFontSize` Integer(可选) - 默认为 `0`.
  * `defaultEncoding` String(可选) - 默认为 `ISO-8859-1`.
  * `backgroundThrottling` Boolean(可选) - 页面变成背景时是否限制动画和计时器.默认为 `true`.
  * `offscreen` Boolean(可选) - 是否绘制和渲染可视区域外的窗口 [更多细节](../tutorial/offscreen-rendering.md),默认为  `false` .
  * `contextIsolation` Boolean (可选) - 是否在独立JavaScript环境中运行Electron API和指定的 `preload`脚本,默认为`false`.该 `preload`脚本仍然可完全访问 `document` 与 `window` 全局变量,但它将使用自身内置函数如( `Array`, `Object`, `JSON`等等.) 并且将被加载的页面与对全局环境所做的任何更改隔离开来. 该选项类同[Chrome Content Scripts][chrome-content-scripts],其目的是确保潜在的不受信任内容在加载时无法篡改 `preload`脚本或Electron API. _试验功能_
   * `nativeWindowOpen` Boolean (可选) - 是否使用原生默认的 `window.open()`. 默认为 `false`.
   * `webviewTag` Boolean (可选) - 是否启用 [`<webview>` tag](webview-tag.md)标签. 默认为上文中 `nodeIntegration`设置的值. **注意:** 您应当确保 `<webview>`中远程或不受信任的内容里不会创建恶意的 `preload` 脚本,因为该脚本将会集成Node.不过,您可以使用 [webContents](web-contents.md) 中的 `will-attach-webview` 事件对 `preload` 脚本进行剥离并验证或更改 `<webview>`的初始设置.

当使用 `minWidth`/ `maxWidth`/ `minHeight`/ `maxHeight`设置最小或最大窗口大小时,它只限制用户.它不会阻止您将不符合大小限制的大小传递给 `setBounds`/ `setSize`或 `BrowserWindow`的构造函数.

### 事件列表
 `new BrowserWindow`对象会触发以下事件:

#### 事件:'page-title-updated'
> 触发:**文档更改标题时**

* `event` Event
* `title` String

使用 `event.preventDefault()` 可以阻止标题更改.

#### 事件:'close'
> 触发:**窗口正在关闭时,且位于DOM的 `beforeunload` and `unload` 事件之前**

* `event` Event

使用 `event.preventDefault()` 可以阻止这个操作.
通常你想通过 `beforeunload` 处理器来决定是否关闭窗口,但是它也会在窗口重载的时候被触发.
 在 Electron 中,返回除 `undefined`之外的任何值都将取消关闭.例如:
```JavaScript
window.onbeforeunload =(e)=> {
  console.log('I do not want to be closed')

  //与通常的浏览器不同,会提示给用户一个消息框,返回
  //非空值将默认取消关闭.
  //建议使用对话框API让用户确认关闭应用程序.
  e.returnValue = false
}}
```

#### 事件:'closed'
> 触发:**窗口已经关闭时**

当你接收到这个事件的时候,你应当删除对已经关闭的窗口的引用对象和避免再次使用它.

#### 事件:'session-end' _Windows_
> 触发:**因为强制关机或机器重启或会话注销而导致窗口会话结束时**

#### 事件:'unresponsive'
> 触发:**网页变得未响应时**

#### 事件:`responsive`
> 触发:**未响应再次变得响应时**

#### 事件:'blur'
> 触发:**窗口失去焦点时**

#### 事件:'focus'
> 触发:**窗口获得焦点时**

#### 事件:'show'
> 触发:**显示窗口时**

#### 事件:'hide'
> 触发:**窗口隐藏时**

#### 事件:'ready-to-show'
> 触发:**网页即将呈现时触发**

#### 事件:`maximize`
> 触发:**窗口最大化时**

#### 事件:'unmaximize'
> 触发:**当窗口从最大化状态退出时**

#### 事件:'minimize'
> 触发:**窗口最小化时**

#### 事件:'restore'
> 触发:**当窗口从最小化状态恢时**

#### 事件:'resize'
> 触发:**调整窗口大小时**

#### 事件:'move'
> 触发:**窗口移动到新位置时**
 **注意:** 在macOS上,此事件只是`moved`的别名.

#### 事件:'moved'_macOS_
> 触发:**当窗口移动到新位置时触发一次**

#### 事件:'enter-full-screen'
> 触发:**窗口进入全屏状态时**

#### 事件:'leave-full-screen'
> 触发:**窗口离开全屏状态时**

#### 事件:'enter-html-full-screen'
> 触发:**窗口进入由HTML API触发的全屏状态时**

#### 事件:'leave-html-full-screen'
> 触发:**窗口离开由HTML API触发的全屏状态时**

#### 事件:'app-command'_Windows_
> 触发:**请求一个[应用程序命令](https://msdn.microsoft.com/en-us/library/windows/desktop/ms646275(v=vs.85).aspx)时**

* `event` Event
* `command` String


典型的是键盘媒体或浏览器命令,Windows上的 `Back` 按钮用作鼠标也会触发.
命令是小写的,下划线用连字符替换,
`APPCOMMAND_`前缀被删除.
例如`APPCOMMAND_BROWSER_BACKWARD`被作为`browser-backward`触发.
```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.on('app-command',(e,cmd)=> {
  //当用户点击鼠标返回按钮时返回窗口
  if(cmd ==='browser-backward'&& win.webContents.canGoBack()){
    win.webContents.goBack()
  }}
})
```

#### 事件:'scroll-touch-begin'_macOS_
> 触发:**滚轮事件阶段开始时**

#### 事件:'scroll-touch-end'_macOS_
> 触发:**滚轮事件阶段结束时**

#### 事件:'scroll-touch-edge'_macOS_
> 触发:**滚轮事件阶段到达元素边缘时**

#### 事件:'swipe'_macOS_
> 触发:**三指拖移时**

* `event` Event
* `direction` String,可选方向 `up`, `down`, `left`, `right`,.

#### 事件: 'sheet-begin' _macOS_
> 触发:**窗口打开sheet(工作表)时**

#### 事件: 'sheet-end' _macOS_
> 触发:**窗口关闭sheet(工作表)时**

## 静态方法

`BrowserWindow`类有以下静态方法:

#### `BrowserWindow.getAllWindows()`
> 用途:**返回所有打开的窗口的数组**

返回 `BrowserWindow []`

#### `BrowserWindow.getFocusedWindow()`
> 用途:**当前获得焦点的窗口,如果没有就返回 `null`**

返回 `BrowserWindow` 

### `BrowserWindow.fromWebContents(webContents)`
> 用途:**拥有给定 `webContents` [WebContents](web-contents.md) 的窗口**

返回 `BrowserWindow` 

#### `BrowserWindow.fromId(id)`
> 用途:**拥有给定  `id` 的窗口**

* `id`Integer

#### `BrowserWindow.addDevToolsExtension(path)`
> 用途:**添加位于 `path` 的扩展,并且返回扩展名**

* `path` String

扩展将被记住,所以你只需要调用这个API一次,这个API不是用于编程使用.

如果尝试添加已经加载的扩展,此方法将不会返回,而是会向控制台记录警告.

如果扩展程序的清单丢失或不完整,该方法也不会返回.

 **注意:** 这个API不能在 `app`模块的 `ready`事件之前调用.

#### `BrowserWindow.removeDevToolsExtension(name)`
> 用途:**删除名为 `name` 的扩展**

* `name` String

 **注意:** 这个API不能在 `app`模块的 `ready`事件之前调用.

#### `BrowserWindow.getDevToolsExtensions()`
> 用途:**获得一个由已安装的扩展名称与版本组成的对象( `Object`)**

返回 `Object` - 键是扩展名,每个值都是一个包含 `name`和 `version`属性的对象.

要检查是否安装了扩展,您可以运行以下命令:
```JavaScript
const {BrowserWindow} = require('electron')
let installed = BrowserWindow.getDevToolsExtensions().hasOwnProperty('devtron')
console.log(已安装)
```
 **注意:** 这个API不能在 `app`模块的 `ready`事件之前调用.

## 实例属性
使用`new BrowserWindow`创建的对象具有以下属性:

```JavaScript
const {BrowserWindow} = require('electron')
//在这个例子中,win是我们的实例
let win = new BrowserWindow({width:800,height:600})
win.loadURL('https://github.com')
```

#### `win.webContents`
> 属性:**窗口拥有的 `WebContents`对象**
所有与网页相关的事件和操作都将通过它完成.有关它的方法和事件,请参见[`webContents`文档](web-contents.md).  

#### `win.id`
> 属性:**窗口的唯一ID**

## 实例方法
使用 `new BrowserWindow` 创建的对象有以下实例方法:

#### `win.destroy()`
> 用途:**强制销毁窗口**

关闭时候,除了 `closed` 外, `unload` 和 `beforeunload` 及  `close` 都不会触发.

#### `win.close()`
> 用途:**与用户点击关闭按钮的效果一样的尝试关闭窗口,但网页可能会取消关闭**

具体详情,请查看[关闭事件](#event-close)       

#### `win.focus()`
> 用途:**聚焦于窗口**

#### `win.blur()`
> 用途:**取消窗口的聚焦**

#### `win.isFocused()`
> 用途:**判断窗口是否聚焦**

#### `win.isDestroyed()`
> 用途:**判断窗口是否被销毁**

#### `win.show()`
> 用途:**显示并聚焦于窗口**

#### `win.showInactive()`
> 用途:**显示但不聚焦于窗口**

#### `win.hide()`
> 用途:**隐藏窗口**

#### `win.isVisible()`
> 用途:**判断窗口是否可见**

#### `win.isModal()`
> 用途:**判断是否为模态窗口**

#### `win.maximize()`
> 用途:**最大化窗口**

#### `win.unmaximize()`
> 用途:**取消窗口最大化**

#### `win.isMaximized()`
> 用途:**判断窗口是否最大化**

#### `win.minimize()`
> 用途:**窗口最小化**

在某些平台上,最小化的窗口将显示在Dock中.

#### `win.restore()`
> 用途:**窗口从最小化状态恢复到之前的状态**

#### `win.isMinimized()`
> 用途:**判断窗口是否最小化**

#### `win.setFullScreen(flag)`
> 用途:**设置窗口是否可全屏**

* `flag` Boolean

### `win.isFullScreen()`
> 用途:**窗口当前是否已全屏**

### `win.setAspectRatio(aspectRatio[,extraSize])` _macOS_
> 用途:**设置窗口保持的宽高比**

* `aspectRatio` 为内容视图保持的宽高比.
* `extraSize` Object (可选) - 维持高宽比值时不包含的额外大小.
  * `width` Integer
  * `height` Integer
  
 `extraSize`允许开发人员具有不包括在宽高比计算中的额外空间(px为单位).
 
想象一个正常可控的视频播放器窗口. 假如左边缘有15px,右边缘有25px,在播放器下面有50px.

为了在播放器内保持一个 16:9 的高宽比例,我们可以调用这个api传入参数16/9 和[ 40,50 ].

第二个参数不管网页中的额外的宽度和高度在什么位置,只要它们存在就行.只需要把网页中的所有额外的高度和宽度加起来就行.

 此API已经充分考虑窗口大小和其内容大小之间的差异.
 
####  `win.closeFilePreview()` _macOS_
> 用途:**关闭当前打开的 [快速查看][quick-look] 面板**  

### `win.setBounds(options[,animate])`
> 用途:**重置窗口的宽高值,并且移动到指定的 `x` ,`y` 位置**  

* `options` Object
  * `x` Integer
  * `y` Integer
  * `width` Integer
  * `height` Integer
* `animate` Boolean (可选) _macOS_

### `win.getBounds()`
> 用途:**返回一个包含了窗口的宽,高,x坐标,y坐标的[`Rectangle`](structures/rectangle.md)对象**  

### `win.setSize(width,height[,animate])`
> 用途:**重置窗口的宽高值**  

* `width` Integer
* `height` Integer
* `animate` Boolean (可选) _macOS_

### `win.getSize()`
> 用途:**获得由窗口宽高组成的数组**  

#### `win.setContentSize(width,height [,animate])`
> 用途:**重置窗口内容区(例如网页界面)的宽高**  

* `width` Integer
* `height` Integer
* `animate` Boolean(可选)_macOS_

#### `win.getContentSize()`
> 用途:**获得窗口内容区(例如网页界面)的宽高组成的数组**  

#### `win.setMinimumSize(width,height)`
> 用途:**设置窗口最小宽度和最小高度**  

* `width`Integer
* `height`Integer

#### `win.getMinimumSize()`
> 用途:**获得窗口最小宽度和最小高度组成的数组**  

#### `win.setMaximumSize(width,height)`
> 用途:**设置窗口最大宽度和最大高度**  

* `width`Integer
* `height`Integer

#### `win.getMaximumSize()`
> 用途:**获得窗口最大宽度和最大高度的数组**  

#### `win.setResizable(resizable)`
> 用途:**设置窗口是否允许用户手动调整大小**  

* `resizable` Boolean

#### `win.isResizable()`
> 用途:**判断窗口是否允许用户手动调整大小**  

#### `win.setMovable(movable)`_macOS_ _Windows_
> 用途:**设置窗口是否允许用户拖拽. Linux中无效**  

* `movable` Boolean

#### `win.isMovable()`_macOS_ _Windows_
> 用途:**判断窗口是否允许用户拖拽. Linux中无效且总是返回 `true`**  

#### `win.setMinimizable(minimizable)`_macOS_ _Windows_
> 用途:**设置窗口是否可以最小化. Linux中无效**  

* `minimizable` Boolean

#### `win.isMinimizable()`_macOS_ _Windows_
> 用途:**判断窗口是否可以最小化. Linux中无效且总是返回 `true`**  

#### `win.setMaximizable(maximizable)`_macOS_ _Windows_
> 用途:**设置窗口是否可以最大化. Linux中无效**  

* `maximizable` Boolean

#### `win.isMaximizable()`_macOS_ _Windows_
> 用途:**判断窗口是否可以最大化. Linux中无效且总是返回 `true`**  

#### `win.setFullScreenable(fullscreenable)`
> 用途:**设置窗口是否可以最小化. Linux中无效**  

* `fullscreenable` Boolean

#### `win.isFullScreenable()`
> 用途:**判断最大化/缩放窗口按钮是否可以切换全屏模式或最大化窗口**  

#### `win.setClosable(closable)`_macOS_ _Windows_
> 用途:**设置窗口是否可以人为关闭. Linux中无效**  

* `closable` Boolean

#### `win.isClosable()`_macOS_ _Windows_
> 用途:**判断窗口是否可以人为关闭. Linux中无效且总是返回 `true`**  

#### `win.setAlwaysOnTop(flag [,level])`
> 用途:**设置窗口是否始终置顶(位于其它窗口之上)**  

* `flag`Boolean
* `level` String(可选)_macOS_  - 值包括 `normal`,`floating` ,`torn-off-menu`,`modal-panel`,`main-menu`,`status`,`pop-up-menu`,`screen-saver`.默认是 `floating`.在[macOS docs][window-levels]了解更多信息.

#### `win.isAlwaysOnTop()`
> 用途:**判断窗口是否始终置顶(位于其它窗口之上)**  

#### `win.center()`
> 用途:**移动窗口到屏幕中心(居中)**  

#### `win.setPosition(x,y [,animate])`
> 用途:**将窗口移动到 `x`和 `y`**  

* `x`Integer
* `y`Integer
* `animate` Boolean(可选)_macOS_

#### `win.getPosition()`
> 用途:**获得窗口的位置坐标**  

#### `win.setTitle(title)`
> 用途:**更改窗口标题**

* `title` String

#### `win.getTitle()`
> 用途:**获得窗口标题**

**注意:** 网页标题可以不同于本机窗口标题.

#### `win.setSheetOffset(offsetY [,offsetX])`_macOS_
> 用途:**设置macOS上工作表的附加点**

* `offsetY` Float
* `offsetX`  Float(可选)

默认情况下,工作表附加在窗口框架的正下方,但您可能希望在呈现HTML的工具栏下方显示它们.例如

```JavaScript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()

let toolbarRect = document.getElementById('toolbar').getBoundingClientRect()
win.setSheetOffset(toolbarRect.height)
```

#### `win.flashFrame(flag)`
> 用途:**开始或停止闪烁窗口以吸引用户的注意**

* `flag`Boolean


#### `win.setSkipTaskbar(skip)`
> 用途:**使窗口不显示在任务栏中**

* `skip` Boolean

#### `win.setKiosk(flag)`
> 用途:**进入或离开 kiosk 模式**

* `flag`Boolean

#### `win.isKiosk()`
> 用途:**判断窗口是否处于kiosk模式**

#### `win.getNativeWindowHandle()`
> 用途:**以 `Buffer` 形式返回窗口的平台特定句柄**

windows上句柄类型为 `HWND` ,macOS `NSView *` ,Linux `Window`.

####  `win.hookWindowMessage(message,callback)` _Windows_
> 用途:**在 WndProc 接收到消息时调用 `callback`挂起windows 消息**

* `message` Integer
* `callback` Function

#### `win.isWindowMessageHooked(message)` _Windows_
> 用途:**判断是否成功挂起消息.成功 `true`,否则 `flase`**

* `message` Integer

#### `win.unhookWindowMessage(message)` _Windows_
> 用途:**取消挂起窗口消息**

* `message` Integer

#### `win.unhookAllWindowMessages()`_Windows_
> 用途:**解除所有窗口消息**

### `win.setRepresentedFilename(filename)` _macOS_
> 用途:**设置窗口当前文件路径,并且将这个文件的图标放在窗口标题栏上**

* `filename` String

#### `win.getRepresentedFilename()`_macOS_
> 用途:**获取窗口当前文件路径**

#### `win.setDocumentEdited(edited)`_macOS_
> 用途:**明确指出窗口文档是否可以编辑,如果可以编辑则将标题栏的图标变成灰色.**

* `edited`Boolean

#### `win.isDocumentEdited()`_macOS_
> 用途:**判断当前窗口文档是否可编辑**

#### `win.focusOnWebView()`
> 用途:**聚焦于WebView**

#### `win.blurWebView()`
> 用途:**从WebView移除焦点**

#### `win.capturePage([rect,] callback)`
> 用途:**网页截屏**

* `rect` Object (可选) - 截图的 `rect`区域
  * `x` Integer
  * `y` Integer
  * `width` Integer
  * `height` Integer
* `callback` Function
  * `image` [NativeImage](native-image.md)

与 `webContents.capturePage([rect,] callback)`相同.

#### `win.loadURL(url [,options])`
> 用途:**载入指定url内容到页面中**

* `url` String
* `options` Object(可选)
  * `httpReferrer` String(可选) - HTTP来源网址.
  * `userAgent` String(可选) - 发起请求的userAgent.
  * `extraHeaders`String(可选) - 用 `\ n`分割的额外标题
  * `postData`([UploadRawData](structures/upload-raw-data.md)| [UploadFile](structures/upload-file.md)| [UploadFileSystem](structures/upload-file-system.md)| [UploadBlob](structures/upload-blob.md))[] - (可选)

与 `webContents.loadURL(url [,options])`相同.

 `url`可以是一个远程地址(例如 `http://`),也可以是使用 `file://`协议的本地HTML文件的路径.

为了确保文件网址格式正确,建议使用Node的[`url.format`](https://nodejs.org/api/url.html#url_url_format_urlobject)方法:
```JavaScript
let url = require('url').format({
  protocol:'file',
  slashes:true,
  pathname:require('path').join(__ dirname,'index.html')
})
win.loadURL(url)
```

您可以通过执行以下操作,使用带有网址编码数据的 `POST`请求​​加载网址:
```JavaScript
win.loadURL('http://localhost:8000/post',{
  postData:[{
    type:'rawData',
    bytes:Buffer.from('hello=world')
  }],
  extraHeaders:'Content-Type:application/x-www-form-urlencoded'
})
```

#### `win.reload()`
> 用途:**重载窗口内容**

与 `webContents.reload` 相同.

#### `win.setMenu(menu)`_Linux_ _Windows_
> 用途:**设置窗口菜单栏**

* `menu`菜单

设置它为 `null` 则表示不设置菜单栏.

#### `win.setProgressBar(progress [,options])`
> 用途:**在进度栏中设置进度值**

* `progress` Double,有效范围为[0,1.0]
* `options` Object(可选)
  * `mode` String _Windows_ - 进度条的模式.可以是 `none`,`normal`,`indeterminate`,`error`或 `paused`,默认 `normal`.

当进度小于0时则不显示进度;

当进度大于0时显示结果不确定.

在Linux中只支持Unity桌面环境,在 `package.json` 中添加文件名字并指定 `*.desktop` 文件,默认为 `app.getName().desktop`.

#### `win.setOverlayIcon(overlay,description)`_Windows_
> 用途:**设置任务栏右边显示图标及消息,常用语应用程序状态或发布通知**

* `overlay` [NativeImage](native-image.md) -  在底部任务栏右边显示图标.16 x 16像素,参数为`null`则清除已有覆盖
* `description` String - 图标的相关气泡描述

#### `win.setHasShadow(hasShadow)`_macOS_
> 用途:**设置窗口是否应该有阴影.在Windows和Linux系统无效**

* `hasShadow` Boolean

#### `win.hasShadow()`_macOS_
> 用途:**判断窗口是否有阴影. Windows和Linux中无效且总是返回 `true`**  

#### `win.setThumbarButtons(buttons)`_Windows_
> 用途:**将指定的一组按钮添加到菜单栏的缩图工具栏上**

* `buttons` [ThumbarButton []](structures/thumbar-button.md) 是否成功添加了缩略图工具栏按钮

由于空间有限,缩图工具栏中的按钮数量不超过7个且一旦设置则无法移动或删除,但你可以调用API传递一个空数组来清除按钮.

`buttons`是一个`Button`对象的数组:

* `Button` Object
  * `icon` [NativeImage](native-image.md) - 在缩图工具栏上显示的图标.
  * `click` Function
  * `tooltip` String (可选) - 按钮提示文本.
  * `flags` Array (可选) - 按钮的状态与行为. 默认它是 `['enabled']`.

 `flags` 是一个数组,它包含下面这些 `String`:

* `enabled` - 该按钮是活动的,可供用户使用.
* `disabled` - 该按钮被禁用如灰色.它具有不响应用户操作的视觉状态.
* `dismissonclick` - 点击按钮,关闭缩图窗口
* `nobackground` - 仅仅使用图像而不绘制边框.
* `hidden` -该按钮不向用户显示
* `noninteractive` - 该按钮可启用但不是交互式;也不绘制按下的状态.此值适用于在通知中使用按钮的情况.


#### `win.setThumbnailClip(region)`_Windows_
> 用途:**选定窗口区域为任务栏缩图**

* `region` [Rectangle](structures/rectangle.md) - 选定窗口区域

当 `region` 为空即 `{x:0,y:0,width:0,height:0}`时,则重置缩图为整个窗口

#### `win.setThumbnailToolTip(toolTip)`_Windows_
> 用途:**设置鼠标悬停在任务栏缩略图时的提示文本**

* `toolTip` String

#### `win.setAppDetails(options)`_Windows_
> 用途:**设置窗口的任务栏按钮的属性**

* `options`对象
  * `appId` String(可选) - Window的[App User Model ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391569(v=vs.85).aspx).该项必须设置,否则其他选项将没有效果.
  * `appIconPath` String(可选) - 窗口的[重新启动图标](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391573(v=vs.85).aspx)
  * `appIconIndex` Integer(可选) - `appIconPath`中的图标索引.未设置 `appIconPath`时忽略.默认值为0.   
  * `relaunchCommand` String(可选) - Window的[重新启动命令](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391571(v=vs.85).aspx).            
  * `relaunchDisplayName` String(可选) - 窗口的[重新启动显示名称](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391572(v=vs.85).aspx).            

**注意:** `relaunchCommand`和 `relaunchDisplayName`必须始终设置在一起.如果这些属性中的一个没有设置,那么两者都不会被使用.    

#### `win.showDefinitionForSelection()`_macOS_
> 用途:**在窗口上搜索所选字词时弹出字典**

与 `webContents.showDefinitionForSelection()`相同.

#### `win.setIcon(icon)`_Windows_ _Linux_
> 用途:**设置窗口图标**

* `icon` [NativeImage](native-image.md)

### `win.setAutoHideMenuBar(hide)`
> 用途:**设置窗口的菜单栏是否自动隐藏**

* `hide` Boolean

设置后只有当用户按下 `Alt` 键时则显示菜单栏.如果菜单栏已经可见,调用 `setAutoHideMenuBar(true)` 时则不会立刻隐藏.

### `win.isMenuBarAutoHide()`
> 用途:**判断窗口的菜单栏是否自动隐藏**

### `win.setMenuBarVisibility(visible)` _Windows_ _Linux_
> 用途:**设置窗口的菜单栏是否可见**

* `visible` Boolean

如果设为可见,菜单栏自动隐藏时,用户仍然可以按下 `Alt` 键来显示.

### `win.isMenuBarVisible()`
> 用途:**判断窗口的菜单栏是否可见**

### `win.setVisibleOnAllWorkspaces(visible)`
> 用途:**设置窗口是否在所有工作空间上可见**

* `visible` Boolean

**注意:** 这个api 在windows无效.

#### `win.isVisibleOnAllWorkspaces()`
> 用途:**判断窗口是否在所有工作空间上可见**

**注意:** 此API在Windows上始终返回false.

#### `win.setIgnoreMouseEvents(ignore)`
> 用途:**强制忽略窗口内的所有鼠标事件**

* `ignore` Boolean

在此窗口中发生的所有鼠标事件将被传递到此窗口下面的窗口,但如果此窗口具有焦点,它仍然会接收键盘事件

#### `win.setContentProtection(enable)`_macOS_ _Windows_
> 用途:**防止窗口内容被其他应用捕获**

* `enable` Boolean

在macOS中,它设置 `NSWindow`的 `sharingType`为 `NSWindowSharingNone`.

在Windows中,它设置 `WDA_MONITOR`调用 `SetWindowDisplayAffinity`.

#### `win.setFocusable(focusable)`_Windows_
> 用途:**设置窗口是否可聚焦**

* `focusable` Boolean

#### `win.setParentWindow(parent)`_Linux_ _macOS_
> 用途:**设置当前窗口的父窗口或直接转为顶级窗口**

* `parent` BrowserWindow,当前窗口的父窗口,为 `null`时表示将当前窗口转为顶级窗口.

#### `win.getParentWindow()`
> 用途:**获取窗口的父窗口**

返回`BrowserWindow` - 父窗口.

#### `win.getChildWindows()`
> 用途:**获取窗口的所有子窗口**

返回`BrowserWindow []` - 所有子窗口.

#### `win.setAutoHideCursor(autoHide)`_macOS_
> 用途:**设置输入时是否隐藏光标**

* `autoHide` Boolean

#### `win.setVibrancy(type)`_macOS_
> 用途:**为窗口设置动态效果**

* `type` String - 可选 `appearance-based`,`light`,`dark`,`titlebar`, `selection`,`menu`,`popover`,`sidebar`,`medium-light` ,`ultra-dark`,`null`或 `undefined`.

`null`或 `undefined` 表示删除窗口上的动态效果.

更多细节请前往[macos documentation][vibrancy-docs]
  
#### `win.setTouchBar(touchBar)` _macOS_ 实验功能
> 用途:**设置窗口的touchBar布局**

* `touchBar`  - TouchBar, `null`或 `undefined` 则清除触摸条.

此方法只有在macOS 10.12.1+且设备支持触摸条TouchBar时可用.

**注意：** TouchBar API目前为实验性质，可能会更改或删除。

#### `win.setBrowserView(browserView)` _实验功能_

* `browserView` [BrowserView](browser-view.md)

**注意：** BrowserView API目前为实验性质，可能会更改或删除。

[blink-feature-string]:https://cs.chromium.org/chromium/src/third_party/WebKit/Source/platform/RuntimeEnabledFeatures.in
[quick-look]:https://en.wikipedia.org/wiki/Quick_Look
[vibrancy-docs]:https://developer.apple.com/reference/appkit/nsvisualeffectview?language=objc
[window-levels]:https://developer.apple.com/reference/appkit/nswindow/1664726-window_levels
[chrome-content-scripts]:https://developer.chrome.com/extensions/content_scripts#execution-environment