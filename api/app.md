# 本文介绍:应用整体控制

> `app` 模块是为了控制整个应用的生命周期设计的,它就是应用的基础核心
 
进程: [主进程](../tutorial/quick-start.md#主进程)           

最后一个窗口被关闭时退出应用的示例:

```JavaScript
const {app} = require('electron')
app.on('window-all-closed',() => {
  app.quit()
})
```

## 事件
 `app` 对象有以下事件:

### 事件:'will-finish-launching'
> 触发:**应用程序完成基本启动时**

Windows 和 Linux 中, `will-finish-launching` 事件等同 `ready` 事件.

macOS 中,事件等同 `NSApplication` 中的 `applicationWillFinishLaunching` 提示.

通常在这里为 `open-file` 和 `open-url` 设置监听器,用于启动崩溃报告和自动更新之类。

但大多数的情况下只需在 `ready` 事件完成所有业务。

### 事件:'ready'
> 触发:**Electron 完成初始化**

* `launchInfo` Object  _macOS_

macOs 中,如果是从通知中心中启动,那么 `launchInfo` 中的 `userInfo`包含着用来打开应用程序的 `NSUserNotification` 信息。

 `app.isReady()` 方法可检查此事件是否已触发。

### 事件:'window-all-closed'
> 触发:**所有的窗口都被关闭时**
 
如果您没有监听此事件,当所有窗口都已关闭时,默认退出应用程序。

但如果你监听此事件,将由你来控制应用程序是否退出。

如果用户按下了 `Cmd + Q`,或者开发者调用了 `app.quit()` ,Electron 将会先尝试关闭所有的窗口再触发 `will-quit` 事件,在这种情况下 `window-all-closed`不会被触发。

### 事件:'before-quit'
> 触发:**应用程序开始关闭窗口并退出之前**

* `event` Event

调用 `event.preventDefault()` 可阻止应用程序默认的终止。

 **注意:** 如果退出是由 `autoUpdater.quitAndInstall()`启动的,所有窗口全部 `close`事件之后才会触发 `before-quit` 。

### 事件:'will-quit'
> 触发:**应用程序已经被关闭窗口且应用即将退出时**
 
* `event` Event

调用 `event.preventDefault()` 可阻止应用程序默认的终止。

 `window-all-closed` 事件的描述中详诉了 `will-quit`与 `window-all-closed`的区别。

### 事件:'quit'
> 触发:**应用程序退出时**
 
* `event` Event
* `exitCode` Integer- 线程结束代码(_C语言中常见_)

### 事件:'open-file' _macOS_
> 触发:**用户想要在应用中打开一个文件**
 
* `event` Event
* `path` String - 文件路径

常触发于应用已打开并且系统想要再次使用应用打开文件时,或者在一个文件被拖入 dock 且应用还没有运行的时候被触发。

请确认在应用启动的时候(甚至在 `ready` 事件被触发前)就对 `open-file` 事件进行监听。

如果你想处理这个事件,你应该调用 `event.preventDefault()` 。

在 Windows系统中,你需要通过解析 `process.argv` 来获取文件路径。

### 事件:'open-url' _macOS_
> 触发:**用户想要在应用中打开一个url**
 
* `event` Event
* `url` String - URL地址

应用程序的 `Info.plist`文件必须在 `CFBundleURLTypes` 键中定义 `url` 方案,并将 `NSPrincipalClass` 设为 `AtomApplication`。

如果你想处理这个事件,你应该调用 `event.preventDefault()` 。

### 事件:'activate' _macOS_
> 触发:**应用被激活时**
  
* `event` Event
* `hasVisibleWindows` Boolean- 是否有可见窗口

 各种操作都可以触发此事件,例如首次启动应用,重启应用,或单击应用程序的停靠栏或任务栏图标。

### 事件: 'continue-activity' _macOS_
> 触发:**来自不同设备的活动通过 [Handoff][handoff] 想要恢复时**

* `event` Event
* `type` String - 标识当前状态的字符串。 映射到[`NSUserActivity.activityType`][activity-type]。
* `userInfo` Object - 包含由另一个设备上的活动所存储的应用程序特定的状态。

恢复的前提是具有支持相应的活动类型并且开发团队ID一致。

所支持活动类型已在应用的 `Info.plist`中的 `NSUserActivityTypes`明确定义。

可调用 `event.preventDefault()` 处理这个事件。

### 事件:'browser-window-blur'
> 触发:**[BrowserWindow](browser-window.md) 失去焦点时**

* `event` Event
* `window` BrowserWindow - 指定窗口

### 事件:'browser-window-focus'
> 触发:**[BrowserWindow](browser-window.md) 获得焦点时**

* `event` Event
* `window` BrowserWindow - 指定窗口

### 事件:'browser-window-created'
> 触发:**当[BrowserWindow](browser-window.md) 被创建时**

* `event` Event
* `window` BrowserWindow - 新窗口

### 事件: 'web-contents-created'
> 触发:**在新的 [webContents](web-contents.md) 创建后**

* `event` Event
* `webContents` WebContents - 指定 `webContents`

### 事件:'certificate-error'
> 触发:**对 `url` 验证 `certificate` 证书失败时**

* `event` Event
* `webContents` [WebContents](web-contents.md)
* `url` String - URL 地址
* `error` String - 错误码
* `certificate` Object [Certificate](structures/certificate.md) - `certificate` 证书
  * `data` Buffer - PEM 编码数据
  * `issuerName` String - 发行者的公有名称
* `callback` Function
  * `isTrusted` Boolean - 是否信任这个证书

如果需要信任这个证书,你需要阻止默认行为 `event.preventDefault()` 并且调用 `callback(true)`。

```JavaScript
const {app} = require('electron')
app.on('certificate-error',(event,webContents,url,error,certificate,callback) => {
  if (url === 'https://github.com') {
    // Verification logic.
    event.preventDefault()
    callback(true)
  } else {
    callback(false)
  }
})
```

### 事件:'select-client-certificate'
> 触发:**请求客户端证书时**

* `event` Event
* `webContents` [WebContents](web-contents.md)
* `url` String - URL 地址
* `certificateList` [Object] - 证书列表
  * `data` Buffer - PEM 编码数据
  * `issuerName` String - 发行者的公有名称
* `callback` Function
  * `certificate` [Certificate](structures/certificate.md) (可选)

`url` 指的是请求客户端证书的网页地址,使用 `callback` 时从传入的证书列表中筛选出证书。

可调用 `event.preventDefault()` 来防止应用自动使用第一个证书进行验证。如下所示:
```JavaScript
app.on('select-certificate',function (event,host,url,list,callback) {
  event.preventDefault()
  callback(list[0])
})
```

### 事件: 'login'
> 触发:**`webContents`想要做基本的验证时**

* `event` Event
* `webContents` [WebContents](web-contents.md)
* `request` Object
  * `method` String
  * `url` URL
  * `referrer` URL
* `authInfo` Object
  * `isProxy` Boolean
  * `scheme` String
  * `host` String
  * `port` Integer
  * `realm` String
* `callback` Function
  * `username` String
  * `password` String
 
默认情况下,Electron 会取消所有的验证行为,如果需要重写这个行为,你需要用 `event.preventDefault()` 来阻止默认行为,并用 `callback(username,password)` 来进行验证。
```JavaScript
const {app} = require('electron')
app.on('login',(event,webContents,request,authInfo,callback) => {
  event.preventDefault()
  callback('username','secret')
})
```

### 事件:'gpu-process-crashed'
> 触发:**`GPU进程崩溃或被杀死时**

* `event` Event
* `killed` Boolean

### 事件: 'accessibility-support-changed' _macOS_ _Windows_
> 触发:**`Chrome 的辅助功能状态改变时如屏幕阅读被启用或被禁用**

* `event` Event
* `accessibilitySupportEnabled` Boolean - 当启用Chrome的辅助功能时候为 `true`,其他情况为 `false`.

[点此查看](https://www.chromium.org/developers/design-documents/accessibility) 更多详情.


## 方法列表
 `app`  对象拥有以下的方法:
 
**请注意** 有的方法只能用于特定的操作系统,已标注。

### `app.quit()`
> 用途:**`关闭所有已打开的窗口并退出程序**

 关窗前,触发 `before-quit` 事件。
 
 关窗后,触发 `will-quit` 事件。
 
 再用 `quit`进行终止应用程序。
 
这个方法保证了所有的 `beforeunload` 和 `unload` 事件处理器被正确执行。假如窗口的 `beforeunload` 事件处理器返回 `false`,那么整个应用可能会取消退出。

### `app.exit(exitCode)`
> 用途:**`带着 `exitCode` 忽略 `before-quit` 和 `will-quit` 事件强行退出应用**

* `exitCode` String 默认值为0

 所有的窗口会被立刻关闭且不会询问用户。
 
### `app.relaunch([options])`
> 用途:**退出当前实例,重启应用**

* `options` Object (可选)
  * `args` String[] (可选)
  * `execPath` String (可选)

默认情况下,新实例将使用与当前实例相同的工作目录和命令行参数。

当指定 `args`时, `args`将作为命令行参数传递。

当指定 `execPath`时,`execPath`将被执行以重新启动,而不是当前的应用程序。

请注意,此方法在执行时并不退出应用程序,您必须在 `app.relaunch`后调用 `app.quit`或 `app.exit`使应用程序重新启动。

当 `app.relaunch`被多次调用时,多个实例将在当前实例退出后启动。

立即重新启动当前实例并向新实例添加新的命令行参数的示例:

```JavaScript
const {app} = require('electron')

app.relaunch({args: process.argv.slice(1).concat(['--relaunch'])})
app.exit(0)
```

### `app.isReady()`
> 用途:**判断是否已完成初始化( `Boolean`)**

`true`即已经完成初始化,否则为false。

### `app.focus()`
> 用途:**聚焦首选窗口**

* Linux或Windows中,使应用的第一个窗口获取焦点.
* macOS中,让该应用成为活动应用程序。

### `app.hide()` _macOS_
> 用途:**隐藏所有应用程序窗口,而不将其最小化**

### `app.show()` _macOS_
> 用途:**重新显示隐藏的窗口,且无需对焦**

### `app.getAppPath()`
> 用途:**返回当前应用所在的文件路径( `String`)**

### `app.getPath(name)`
> 用途:**根据字符串查找指定路径**

* `name` String

返回与 `name` 参数相关的特殊文件夹或文件路径。当失败时抛出 `Error` 。

你可以通过名称请求以下的路径:
* `home` 用户的 home 文件夹(主目录)
* `appData` 当前用户的应用数据文件夹,默认对应:
  * `%APPDATA%` Windows 中
  * `$XDG_CONFIG_HOME` or `~/.config` Linux 中
  * `~/Library/Application Support` macOS 中
* `userData` 储存你应用程序设置文件的文件夹,默认是 `appData` 文件夹附加应用的名称
* `temp` 临时文件夹
* `exe` 当前的可执行文件
* `module`  `libchromiumcontent` 库
* `desktop` 当前用户的桌面文件夹
* `documents` 用户文档目录的路径
* `downloads` 用户下载目录的路径.
* `music` 用户音乐目录的路径.
* `pictures` 用户图片目录的路径.
* `videos` 用户视频目录的路径.
* `pepperFlashSystemPlugin`  Pepper Flash插件所在目录

### `app.getFileIcon(path[,options],callback)`
> 用途:**获取路径文件的关联图标**

* `path` String
* `options` Object (可选)
  * `size` String
    * `small` - 16x16
    * `normal` - 32x32
    * `large` - _Linux_里的最大值是48x48 , _Windows_里的最大值是32x32,_macOS_中无效.
* `callback` Function
  * `error` Error
  * `icon` [NativeImage](native-image.md)

在Windows中,有2种图标:

与某些文件扩展名(例如 `.mp3`,`.png`,等)相关联的图标

文件本身的图标,如 `.exe`,`.dll`,`.ico`.

在Linux和macOS上,图标取决于与文件MIME类型相关联的应用程序。

### `app.setPath(name,path)`
> 用途:**重写 `name` 的路径为 `path`**

* `name` String
* `path` String

 `path` 可以是目录或者文件,这个和 `name` 的类型有关。
 
如果 `path` 目录不存在,则该方法将创建 `path`,创建错误则会抛出 `Error`。

 `name` 参数只能使用 `app.getPath` 中定义的 `name`。

默认情况下,网页的 cookie 和缓存都会储存在 `userData` 目录

如果你想要改变这个位置,你需要在 `app` 模块中的 `ready` 事件被触发之前重写 `userData` 的路径。

### `app.getVersion()`
> 用途:**返回应用程序的版本( `String`)**

如果 `package.json` 文件中没有写版本号,将会返回当前包或者可执行文件的版本。

### `app.getName()`
> 用途:**返回`package.json` 文件中的应用名称( `String`)**

由于 npm 的命名规则, `name` 字段是一个简短的小写名称。

你应该定义一个 `productName` 字段,用于表示应用程序完整名称。

Electron 会优先使用这个字段作为应用名。

### `app.setName(name)`
> 用途:**重写当前应用的名称**

* `name` String - 新应用名称

### `app.getLocale()`
> 用途:**返回应用程序的语言( `String`)**

可能的返回值记录在这里[locales.md](locales.md)。

 **两点注意:**
 
当分发您的打包应用程序时,您必须指定 `locales`文件夹。

在Windows上,必须在 `ready`事件发出后调用它。

### `app.addRecentDocument(path)`  _macOS_ _Windows_
> 用途:**将 `path`添加到最近的文档列表中**

* `path` String

这个列表由操作系统进行管理。在 Windows 中从任务栏访问列表,在 macOS 中通过 dock 菜单进行访问。

### `app.clearRecentDocuments()` _macOS_ _Windows_
> 用途:**清除最近访问的文档列表**

### `app.setAsDefaultProtocolClient(protocol[,path,args])` _macOS_ _Windows_
> 用途:**自定义协议格式并设置为默认处理程序,同时判断调用是否成功( `Boolean`)**

* `protocol` String - 不包含 `://`的协议名称,.例如处理链接为 `electron://` ,填上 `electron` .
* `path` String (可选) _Windows_ - 默认为 `process.execPath`
* `args` String[] (可选) _Windows_ -  默认为空数组

此方法将当前可执行文件设置为协议(也称为URI方案)的默认处理程序。它允许您将应用程序更深入地集成到操作系统中。

一旦注册,所有与 `your-protocol://`的链接将与当前可执行文件一起打开。整个链接(包括协议)将作为参数传递到应用程序。

在Windows系统中,,您可以提供可选参数 `path`,可执行文件的路径和 `args`(在启动时传递给可执行文件的参数数组).

 **注意:**在macOS上,您只能注册已添加到应用程序的info.plist中的协议,这些协议不能在运行时修改。但是,您可以在构建时使用简单的文本编辑器或脚本更改文件。
 
 有关详细信息,请参阅 [Apple's documentation][CFBundleURLTypes] 
 
该API在内部使用Windows注册表和lssetdefaulthandlerforurlscheme。

### `app.removeAsDefaultProtocolClient(protocol[,path,args])` _macOS_ _Windows_
> 用途:**移除协议与默认程序之间的关联,同时返回是否成功( `Boolean`)**

* `protocol` String - 不包含 `://`的协议名称
* `path` String (可选) _Windows_ - 默认为 `process.execPath`
* `args` String[] (可选) _Windows_ - 默认为空数组

此方法检查当前可执行文件是否为协议(也称为URI方案)的默认处理程序。如果是,它会删除应用程序作为默认处理程序。

### `app.isDefaultProtocolClient(protocol[,path,args])` _macOS_ _Windows_
> 用途:**判断当前应用是否为指定协议的默认程序,同时返回是否成功( `Boolean`)**

* `protocol` String - 不包含 `://`的协议名称
* `path` String (可选) _Windows_ - 默认值  `process.execPath`
* `args` String[] (可选) _Windows_ - 默认为空数组

此方法检查当前可执行文件是否是协议(也称为URI方案)的默认处理程序。如果是这样,它将返回true。否则,它将返回false。

**注意:**在macOS上,您可以使用此方法检查应用程序是否已注册为协议的默认协议处理程序。

同时可以通过查看 `~/Library/Preferences/com.apple.LaunchServices.plist` 来验证这一点。 

有关详细信息,请参阅 [苹果说明文档][LSCopyDefaultHandlerForURLScheme] 。         

该API在内部使用Windows注册表和lssetdefaulthandlerforurlscheme。

### `app.setUserTasks(tasks)` _Windows_
> 用途:**将 `tasks` 添加到 Windows 中 JumpList(跳转列表) 功能的 [Tasks][tasks] 分类中,同时返回是否成功( `Boolean`)**

* `tasks` [Task](structures/task.md) - 任务对象数组

**提示:** 如果希望更多的自定义跳转列表,请使用 `app.setJumpList(categories)` 。

### `app.getJumpListSettings()` _Windows_
> 用途:**获得跳转列表( `Object`)**

返回的 `Object`:
* `minItems` Integer - 将在跳转列表中显示的项目的最小数量(有关此值的更详细描述,请参阅 [MSDN 文档][JumpListBeginListMSDN]). 
* `removedItems` [JumpListItem[]](structures/jump-list-item.md) - 对应于跳转列表中用户从自定义类别中明确删除的项目的JumpListItem对象数组。

这些项目不能在 **下一个**调用`app.setJumpList()`时重新添加到跳转列表中,Windows不会显示包含已删除项的自定义类别。

### `app.setJumpList(categories)` _Windows_
> 用途:**设置或删除应用程序的自定义跳转列表**

* `categories` [JumpListCategory[]](structures/jump-list-category.md) 或者 `null` - `JumpListCategory` 对象的数组.

返回以下字符串之一:
* `ok` - 没有出错
* `error` -发生一个或多个错误,启用运行时日志记录以找出可能的原因
* `invalidSeparatorError` -试图向跳转列表中的自定义类别添加分隔符。分隔符只允许在标准的 `Tasks` 类别中
* `fileTypeRegistrationError` - 尝试为应用程序未注册处理的文件类型向跳转列表添加文件链接
* `customCategoryAccessDeniedError` - 由于用户隐私或策略组设置,自定义类别无法添加到跳转列表。

如果 `categories` 值为 `null` ,之前设定的自定义跳转列表(如果存在)将被替换为标准的应用跳转列表(由windows生成)

**注意:** 如果 `JumpListCategory`对象没有设置 `type`和 `name`属性,那么 `type`默认为 `tasks`。 

如果设置 `name`属性,省略`type`属性,则 `type`默认为 `custom`。

**注意:**用户可以从自定义类别中删除项目,Windows不允许将删除的项目添加回自定义类别,直到**下一次**成功调用 `app.setJumpList(categories)`。

把之前删除的项目重新添加到自定义类别,将导致跳转列表中直接省略整个自定义类。

已删除项目的列表可以使用 `app.getJumpListSettings()`获取。

下面是一个创建自定义跳转列表的例子:

```JavaScript
const {app} = require('electron')

app.setJumpList([
  {
    type: 'custom',
    name: 'Recent Projects',
    items: [
      { type: 'file',path: 'C:\\Projects\\project1.proj' },
      { type: 'file',path: 'C:\\Projects\\project2.proj' }
    ]
  },
  { // has a name so `type` is assumed to be "custom"
    name: 'Tools',
    items: [
      {
        type: 'task',
        title: 'Tool A',
        program: process.execPath,
        args: '--run-tool-a',
        icon: process.execPath,
        iconIndex: 0,
        description: 'Runs Tool A'
      },
      {
        type: 'task',
        title: 'Tool B',
        program: process.execPath,
        args: '--run-tool-b',
        icon: process.execPath,
        iconIndex: 0,
        description: 'Runs Tool B'
      }
    ]
  },
  { type: 'frequent' },
  { // has no name and no type so `type` is assumed to be "tasks"
    items: [
      {
        type: 'task',
        title: 'New Project',
        program: process.execPath,
        args: '--new-project',
        description: 'Create a new project.'
      },
      { type: 'separator' },
      {
        type: 'task',
        title: 'Recover Project',
        program: process.execPath,
        args: '--recover-project',
        description: 'Recover Project'
      }
    ]
  }
])
```

### `app.makeSingleInstance(callback)`
> 用途:**确保当前应用以单实例运行( `Boolean`)**

* `callback` Function
  * `argv` String [] - 第二个实例的命令行参数数组
  * `workingDirectory` String - 第二个实例的工作目录

如果多个实例同时运行,那么第一个被运行的实例中 `callback` 会以 `callback(argv,workingDirectory)` 的形式被调用。其余的实例会被终止。

通常来说,我们会用通过将应用在主屏幕上激活并且取消最小化,来提醒用户这个应用已经被打开了。

在 `app` 的 `ready` 事件后,`callback` 才有可能被调用。

如果当前实例为第一个实例,那么在这个方法将会返回 `false` 来保证它继续运行。否则将会返回 `true` 来让它立刻退出。

在 macOS 中,如果用户通过 Finder, `open-file` 或者 `open-url` 打开应用,系统会强制确保只有一个实例在运行。

但是当用户在命令行中启动应用程序时,系统的单实例机制将被绕过,您必须使用此方法来确保单实例。

在第二个实例启动时激活主实例窗口的示例:

```JavaScript
const {app} = require('electron')
let myWindow = null

const shouldQuit = app.makeSingleInstance((commandLine,workingDirectory) => {
  //如果正在打开的是第二实例时,最大化主实例
  if (myWindow) {
    if (myWindow.isMinimized()) myWindow.restore()
    myWindow.focus()
  }
})
//此处经实践,加上return,防止闪烁关闭.
//即当前已打开主实例,则不再打开第二实例.
if (shouldQuit) {
  app.quit();
  return;
}

//后续...
app.on('ready',() => {
})
```

### `app.releaseSingleInstance()`  
> 用途:**释放由 `makeSingleInstance`创建的所有锁**

去除限制后,应用程序的多个实例可再次并排运行。

### `app.setUserActivity(type,userInfo[,webpageURL])` _macOS_
> 用途:**创建一个 `NSUserActivity`并将其设置为当前活动(activity)**

* `type` String - 唯一标识活动. 映射到[`NSUserActivity.activityType`][activity-type].
* `userInfo` Object - 存储供其他设备使用的应用程序特定状态。
* `webpageURL` String - 如果在恢复设备上未安装合适的应用程序,则在浏览器中加载网页。协议必须是“http”或“https”。

该活动(activity)之后可以进行[Handoff][handoff]到另一个设备。

### `app.getCurrentActivityType()` _macOS_
> 用途:**获取当前运行的活动( `activity`)类型**

 `String` - 正在运行的 活动( `activity`) 的类型.

### `app.setAppUserModelId(id)` _Windows_
> 用途:**更改[应用程序用户模型ID][app-user-model-id]为id **         

* `id` String

### `app.importCertificate(options,callback)` _LINUX_
> 用途:**将pkcs12格式的证书导入平台证书库**

* `options` Object
  * `certificate` String - pkcs12 文件的路径.
  * `password` String - 证书的密码.
* `callback` Function
  * `result` Integer - 导入结果.

将pkcs12格式的证书导入平台证书库。 `callback`使用import操作的`result`调用.

`0` 表示成功,其他值表示失败,参照 [net_error_list](https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h).

### `app.disableHardwareAcceleration()`
> 用途:**为当前应用程序禁用硬件加速**

该方法只能在应用ready之前调用

### `app.setBadgeCount(count)` _Linux_ _macOS_
> 用途:**设置当前应用的计数器提醒,同时返回是否成功( `Boolean`)**

* `count` Integer- `0` 将会隐藏该提醒.

在macOS系统中,它展示在dock图标上.

在Linux系统中,它只适用于Unity启动器.

**注意:**  Unity启动器工作依赖于 `.desktop`文件,详阅 [桌面环境集成]][unity-requiremnt].         

### `app.getBadgeCount()` _Linux_ _macOS_
> 用途:**获取计数器提醒(badge)中显示的当前值( `Integer`)**

### `app.isUnityRunning()` _Linux_
> 用途:**前桌面环境是否为Unity启动器,同时返回是否成功( `Boolean`)**

### `app.getLoginItemSettings()` _macOS_ _Windows_
> 用途:**获取应用的登录项设置( `Object`)**

* `options` Object(可选)
  * `path` String(可选)_Windows_ - 要比较的可执行路径。默认为 `process.execPath`。
  * `args` String [](可选)_Windows_ - 要比较的命令行参数。默认为空数组。

如果你为 `app.setLoginItemSettings`提供 `path`和 `args`选项,那么你需要在这里为 `openAtLogin`设置正确的参数。

返回 `Object` :

* `openAtLogin` Boolean -   如果应用程序设置为在登录时打开,则为 `true`。
* `openAsHidden` Boolean - 如果应用程序在登录时设置为隐藏,则为 `true`。
此设置仅在macOS中受支持。

* `wasOpenedAtLogin` Boolean - 如果应用程序在登录时自动打开,则为 `true`。
此设置仅在macOS上受支持。

* `wasOpenedAsHidden` Boolean - 如果应用程序作为隐藏的登录项打开,则为 `true`。

这表示应用程序不应在启动时打开任何窗口。此设置仅在macOS上受支持。

* `restoreState` Boolean - 如果应用程序作为一个登录项打开且恢复状态上一个会话,则为 `true`。

这表示应用程序应该还原上次关闭应用程序时打开的窗口。此设置仅在macOS上受支持。

**注意:**此API不影响[MAS构建][mas-builds]。

### `app.setLoginItemSettings(settings)` _macOS_ _Windows_
> 用途:**设置应用的登录项设置**

* `settings` Object
 * `openAtLogin` Boolean(可选) - `true`在登录时打开应用程序,`false`将应用程序作为登录项删除。默认为`false`。
 * `openAsHidden` Boolean(可选) - `true`将应用程序打开为隐藏。默认为 `false`。
由于用户可以从系统首选项编辑此设置,因此在打开应用程序时应该选中`app.getLoginItemStatus().wasOpenedAsHidden` 以了解当前值。
此设置仅在macOS上受支持。
* `path` String(可选)_Windows_ - 在登录时启动的可执行文件。默认为 `process.execPath`。
* `args` String [](可选)_Windows_ - 要传递给可执行文件的命令行参数。默认为空数组。小心地用引号括起路径。

如果需要在使用[Squirrel][Squirrel-Windows]的Windows上使用Electron的`autoUpdater`,您应将启动路径设置为Update.exe,并传递指定应用程序名称的参数。

示例:
```JavaScript
const appFolder = path.dirname(process.execPath)
const updateExe = path.resolve(appFolder,'..','Update.exe')
const exeName = path.basename(process.execPath)
app.setLoginItemSettings({
  openAtLogin: true,
  path: updateExe,
  args: [
    '--processStart',`"${exeName}"`,
    '--process-start-args',`"--hidden"`
  ]
})
```

**注意:**此API对[MAS构建][mas-builds]没有影响。      

### `app.isAccessibilitySupportEnabled()` _macOS_ _Windows_
> 用途:**当前应用是否开启了Chrome的辅助功能( `Boolean`)**

如果开启了Chrome的辅助功能(如检测到使用屏幕阅读功能),则返回 `true`,其他情况返回 `false`. 详细信息请参阅[chromium开发文档](https://www.chromium.org/developers/design-documents/accessibility)      

### `app.setAboutPanelOptions(options)` _macOS_
> 用途:**设置关于面板的选项**

* `options`对象
  * `applicationName` String(可选) - 应用程序的名称。
  * `applicationVersion` String(可选) - 应用程序的版本。
  * `copyright` String(可选) - 版权信息。
  * `credits` String(可选) - 信用信息。
  * `version` String(可选) - 应用程序的版本号。

该设定将覆盖应用程序 `.plist` 文件中定义的值。详细信息,请参阅 [苹果开发][about-panel-options]        

### `app.commandLine.appendSwitch(switch[,value])`  
> 用途:**向Chromium的命令行附加一个开关(带有可选的 `value`)**

* `switch` String - 命令行开关
* `value` String(可选) - 给定开关的值

**注意** 这个方法不会影响 `process.argv`,我们通常用这个方法控制一些底层 Chromium 行为。

### `app.commandLine.appendArgument(value)`
> 用途:**向Chromium的命令行附加参数**

* `value` String - 附加到命令行的参数,引号和格式必须正确。

**注意** 这个方法不会影响 `process.argv`。

### `app.dock.bounce([type])` _macOS_

> 用途:**设置dock应用图标的弹跳类型**

* `type` String(可选) -可选 `critical`或 `informational`。默认为 `informational`。

当传入的是 `critical` 时,dock 中的应用将会开始弹跳,直到这个应用被激活或者这个请求被取消。

当传入的是 `informational` 时,dock 中的图标只会弹跳一秒钟。但是,这个请求仍然会激活,直到应用被激活或者请求被取消。

返回 `Integer`一个表示请求的ID。

### `app.dock.cancelBounce(id)` _macOS_
> 用途:**取消这个 `id` 对应的请求**

* `id` Integer

### `app.dock.downloadFinished(filePath)` _macOS_
> 用途:**如果 `filePath`位于Downloads文件夹中,则弹出下载队列**

* `filePath` String 。

### `app.dock.setBadge(text)` _macOS_
> 用途:**设置应用在 dock 中显示的字符串**

* `text` String

### `app.dock.getBadge()` _macOS_
> 用途:**返回应用在 dock 中显示的字符串**

### `app.dock.hide()` _macOS_
> 用途:**隐藏应用在 dock 中的图标**

### `app.dock.show()` _macOS_
> 用途:**显示应用在 dock 中的图标**

### `app.dock.isVisible()` _macOS_
> 用途:**判断应用在 dock 中的图标是否为可见( `Boolean`)**

 `app.dock.show()`调用是异步的,所以这个方法可能不会在调用之后立即返回true。

### `app.dock.setMenu(menu)` _macOS_
> 用途:**设置应用的 [dock 菜单][dock-menu]**

* `menu` [Menu](menu.md)

### `app.dock.setIcon(image)` _macOS_
> 用途:**设置应用在 dock 中显示的图标**

* `image` [NativeImage](native-image.md)

[dock-menu]:https://developer.apple.com/library/mac/documentation/Carbon/Conceptual/customizing_docktile/concepts/dockconcepts.html#//apple_ref/doc/uid/TP30000986-CH2-TPXREF103
[tasks]:http://msdn.microsoft.com/en-us/library/windows/desktop/dd378460(v=vs.85).aspx#tasks
[app-user-model-id]: https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx
[CFBundleURLTypes]: https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-102207-TPXREF115
[LSCopyDefaultHandlerForURLScheme]: https://developer.apple.com/library/mac/documentation/Carbon/Reference/LaunchServicesReference/#//apple_ref/c/func/LSCopyDefaultHandlerForURLScheme
[handoff]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html
[activity-type]: https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSUserActivity_Class/index.html#//apple_ref/occ/instp/NSUserActivity/activityType
[unity-requiremnt]: ../tutorial/desktop-environment-integration.md#unity-launcher-shortcuts-linux
[mas-builds]: ../tutorial/mac-app-store-submission-guide.md
[JumpListBeginListMSDN]: https://msdn.microsoft.com/en-us/library/windows/desktop/dd378398(v=vs.85).aspx
[about-panel-options]: https://developer.apple.com/reference/appkit/nsapplication/1428479-orderfrontstandardaboutpanelwith?language=objc
