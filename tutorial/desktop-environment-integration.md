# 桌面环境集成

不同的操作系统在各自的桌面应用上提供了不同的特性。例如，在 windows 上应用曾经打开的文件会出现在任务栏的跳转列表，在 Mac 上，应用可以把自定义菜单放在鱼眼菜单上。

本指南介绍如何使用Electron API将应用程序集成到这些桌面环境中。

## 通知功能

详见[通知文档](notifications.md)          

## 最近文档 (Windows & macOS)

在Windows 和 macOS 中,打开跳转列表或鱼眼菜单即可轻松访问最近文档.

__跳转列表:__

![JumpList Recent Files](https://cloud.githubusercontent.com/assets/2289/23446924/11a27b98-fdfc-11e6-8485-cc3b1e86b80a.png)

__鱼眼菜单:__

<img src="https://cloud.githubusercontent.com/assets/639601/5069610/2aa80758-6e97-11e4-8cfb-c1a414a10774.png" height="353" width="428" >

使用[app.addRecentDocument][addrecentdocument] API将文件添加到最近文档：  
```javascript
const {app} = require('electron')
app.addRecentDocument('/Users/USERNAME/Desktop/work.type')
```

使用[app.clearRecentDocuments][clearrecentdocuments] API清空最近文档：  
```javascript
const {app} = require('electron')
app.clearRecentDocuments()
```

### Windows 上的注意项

了能够在Windows上使用此功能，您的应用程序必须注册为文档的文件类型的处理程序，否则即使添加文件，该文件也不会出现在跳转列表中。您可以在[应用程序注册][app-registration]指南找到注册事宜。当用户在跳转列表中点击文件时，应用程序的新实例将以添加为命令行参数的文件路径启动。

### macOS 上的注意项
  
当从最近文件列表中请求文件时， `app`模块的 `open-file`事件会被触发.

## 自定义的鱼眼菜单(macOS)

macOS允许开发者定制菜单, 通常会包含一些常用特性的快捷方式:

__Dock菜单的终端Terminal.app:__

<img src="https://cloud.githubusercontent.com/assets/639601/5069962/6032658a-6e9c-11e4-9953-aa84006bdfff.png" height="354" width="341" >

只能在macOS中才可以使用 `app.dock.setMenu` API 来设置菜单:
```javascript
const {app, Menu} = require('electron')

const dockMenu = Menu.buildFromTemplate([
  {label: 'New Window', click () { console.log('New Window') }},
  {label: 'New Window with Settings',
    submenu: [
      {label: 'Basic'},
      {label: 'Pro'}
    ]
  },
  {label: 'New Command...'}
])
app.dock.setMenu(dockMenu)
```

## 用户任务(Windows)

在Windows上，您可以在跳转列表的`'任务'`中指定自定义操作(比如鼠标悬停在开始菜单的迅雷时即出现相关 `任务`菜单)，如MSDN所引用:

应用程序根据程序的功能和用户期望与他们做的关键事项来定义任务。任务应该是上下文的，因为应用程序不需要运行它们就能工作。它们还应该是普通用户在应用程序中执行的统计上最常见的操作，例如撰写电子邮件消息或在邮件程序中打开日历，在文字处理程序中创建新文档，以特定模式启动应用程序，或启动其子命令之一。应用程序不应该将菜单与标准用户不需要的高级功能或一次性注册等操作混淆。不要为促销项目使用任务，如升级或特别优惠。强烈建议任务列表是静态的。无论应用程序的状态或状态如何，它都应保持不变。虽然可以动态地更改列表，但您应该考虑到这可能会混淆不希望目标列表的那部分更改的用户。

__Internet Explorer的任务:__

![IE](http://i.msdn.microsoft.com/dynimg/IC420539.png)

不同于 macOS 的鱼眼菜单，Windows 上的用户任务表现得更像一个快捷方式，当用户单击任务时，将使用指定的参数执行程序。

你可以使用 [app.setUserTasks][setusertaskstasks] API 来设置用户任务：


```javascript
const {app} = require('electron')
app.setUserTasks([
  {
    program: process.execPath,
    arguments: '--new-window',
    iconPath: process.execPath,
    iconIndex: 0,
    title: '新窗口',
    description: '创建新窗口'
  }
])
```

调用 `app.setUserTasks` 并传入空数组就可以清除你的任务列表：

```javascript
const {app} = require('electron')
app.setUserTasks([])
```

当你的应用关闭时，用户任务会仍然会出现，在你的应用被卸载前，任务指定的图标和程序的路径必须是存在的。

## 缩略图工具栏

在Windows上，您可以在应用程序窗口的任务栏布局中添加指定按钮的缩略图工具栏。它为用户提供了一种访问特定窗口命令的方式，而无需恢复或激活窗口。如MSDN所引用:

该工具栏只是熟悉的标准工具栏常用控件。它最多支持7个按钮。每个按钮的ID，图像，工具提示和状态都在一个结构中定义，然后传递给任务栏。应用程序可以根据当前状态的要求显示，启用，禁用或隐藏缩略图工具栏中的按钮。例如，Windows Media Player可能会提供标准媒体传输控制，如播放，暂停，静音和停止。

__Windows Media Player的缩略图工具栏:__

![player](https://i-msdn.sec.s-msft.com/dynimg/IC420540.png)


你可以使用 [BrowserWindow.setThumbarButtons][setthumbarbuttons] 来设置缩略图工具栏:

```javascript
const {BrowserWindow} = require('electron')
const path = require('path')

let win = new BrowserWindow({
  width: 800,
  height: 600
})

win.setThumbarButtons([
  {
    tooltip: 'button1',
    icon: path.join(__dirname, 'button1.png'),
    click () { console.log('button1 clicked') }
  },
  {
    tooltip: 'button2',
    icon: path.join(__dirname, 'button2.png'),
    flags: ['enabled', 'dismissonclick'],
    click () { console.log('button2 clicked.') }
  }
])
```

调用 `app.setUserTasks` 并传入空数组即可清空缩略图工具栏：

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.setThumbarButtons([])
```

## Unity launcher 快捷方式(Linux)

在Unity中，您可以通过修改 `.desktop`文件向其启动器添加自定义条目，参见[向启动器添加快捷方式][unity-launcher]                          

__Audacious的Launcher快捷方式:__

![audacious](https://help.ubuntu.com/community/UnityLaunchersAndDesktopFiles?action=AttachFile&do=get&target=shortcuts.png)

## 任务栏的进度条(Windows, macOS, Unity)

在Windows上, 任务栏按钮上即可显示进度条,这样可以使窗口直接显示进度信息,而不需要用户切换至窗口本身.

在Windows上, Dock图标上即可显示进度条.

Unity DE 也具有同样的特性，在启动器中显示进度条。

__任务栏按钮进度条:__

![Taskbar Progress Bar](https://cloud.githubusercontent.com/assets/639601/5081682/16691fda-6f0e-11e4-9676-49b6418f1264.png)

你可以使用 [BrowserWindow.setProgressBar][setprogressbar]来设置进度条:

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.setProgressBar(0.5)
```

## 任务栏中的图标叠加(Windows)

在 Windows，任务栏按钮可以使用小型叠加层显示应用程序状态，如MSDN所引用:

图标叠加作为状态的上下文通知，并且旨在否定需要单独的通知区域状态图标来将该信息传达到用户。例如，目前在通知区域显示的Microsoft Outlook中的新邮件状态现在可以通过任务栏按钮上的叠加显示。同样，您必须在开发周期内决定哪种方法最适合您的应用。叠加图标旨在提供重要的长期状态或通知，例如网络状态，信使状态或新邮件。用户不应该被呈现不断变化的叠加或动画。

__在任务栏按钮上的叠加:__

![Overlay on taskbar button](https://i-msdn.sec.s-msft.com/dynimg/IC420441.png)

你可以使用 [BrowserWindow.setOverlayIcon][setoverlayicon]来设置叠加图标:

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.setOverlayIcon('path/to/overlay.png', 'Description for overlay')
```

## 突出显示 (Windows)

在Windows任务栏中可以突出显示应用的任务栏按钮,类似于Dock中的弹跳图标,以获得用户关注，如MSDN所引用:

> 窗口的闪烁,通常因为窗口需要引起用户关注.但该关注并不是鼠标聚焦.

你可以使用 [BrowserWindow.flashFrame][flashframe]来设置突出显示:

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.once('focus', () => win.flashFrame(false))
win.flashFrame(true)
```

不要忘记在调用 `flashFrame` 方法后，设置 `false` 来关闭突出显示。在上面的例子中，当窗口进入焦点时调用它，但是你可能需要使用超时或其他一些事件来禁用它。

## 展示文件窗口 (macOS)

在 macOS，窗口可以设置它要展示的文件，文件图标可以出现在标题栏中，当用户 Command-Click 或者 Control-Click 标题栏，文件路径弹窗将会出现。

您还可以设置窗口的编辑状态，以便文件图标可以指示该窗口中的文档是否已修改。

__弹出菜单的展示文件:__

<img src="https://cloud.githubusercontent.com/assets/639601/5082061/670a949a-6f14-11e4-987a-9aaa04b23c1d.png" height="232" width="663" >
你可以使用 [BrowserWindow.setRepresentedFilename][setrepresentedfilename] 和 [BrowserWindow.setDocumentEdited][setdocumentedited]来设置展示文件:

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.setRepresentedFilename('/etc/passwd')
win.setDocumentEdited(true)
```

## 将文件拖出窗口


对于某些在文件上操作的应用程序，将文件从 `Electron`中拖动到其他应用程序中   是很重要的功能。

要在应用程序中实现此功能，您需要在 `ondragstart`事件上调用 `webContents.startDrag(item)` API。

在网页端：

```html
<a href="#" id="drag">item</a>
<script type="text/javascript" charset="utf-8">
  document.getElementById('drag').ondragstart = (event) => {
    event.preventDefault()
    ipcRenderer.send('ondragstart', '/path/to/item')
  }
</script>
```

在主进程：

```javascript
const {ipcMain} = require('electron')
ipcMain.on('ondragstart', (event, filePath) => {
  event.sender.startDrag({
    file: filePath,
    icon: '/path/to/icon.png'
  })
})
```

[addrecentdocument]: ../api/app.md#appaddrecentdocumentpath-os-x-windows
[clearrecentdocuments]: ../api/app.md#appclearrecentdocuments-os-x-windows
[setusertaskstasks]: ../api/app.md#appsetusertaskstasks-windows
[setprogressbar]: ../api/browser-window.md#winsetprogressbarprogress
[setoverlayicon]: ../api/browser-window.md#winsetoverlayiconoverlay-description-windows-7
[setrepresentedfilename]: ../api/browser-window.md#winsetrepresentedfilenamefilename-os-x
[setdocumentedited]: ../api/browser-window.md#winsetdocumenteditededited-os-x
[app-registration]: http://msdn.microsoft.com/en-us/library/windows/desktop/ee872121(v=vs.85).aspx
[unity-launcher]: https://help.ubuntu.com/community/UnityLaunchersAndDesktopFiles#Adding_shortcuts_to_a_launcher
[setthumbarbuttons]: ../api/browser-window.md#winsetthumbarbuttonsbuttons-windows-7
[tray-balloon]: ../api/tray.md#traydisplayballoonoptions-windows
[app-user-model-id]: https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx
[notification-spec]: https://developer.gnome.org/notification-spec/
[flashframe]: ../api/browser-window.md#winflashframeflag