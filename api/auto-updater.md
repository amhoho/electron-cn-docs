# 本文介绍:自动更新应用
> `autoUpdater`模块为[Squirrel](https://github.com/Squirrel)框架提供了一个接口。

进程: [主进程](../glossary.md#主进程)          

您可以使用这些项目之一进行快速启动多平台发布服务器以分发应用程序:
- [nuts][nuts]:*为您的应用程序使用智能版本服务器,使用GitHub作为后端。使用Squirrel(Mac和Windows)自动更新*
- [electron-release-server][electron-release-server]:*功能齐全,自主托管的electron应用程序的发布服务器,兼容自动更新器*
- [squirrel-updates-server][squirrel-updates-server]:*简单的node.js服务器为Squirrel.Mac和Squirrel.Windows使用GitHub版本*
- [squirrel-release-server][squirrel-release-server]:*简单的Squirrel.Windows的PHP应用程序,它从文件夹读取更新。支持增量更新。*

## 平台相关的提示

虽然 `autoUpdater` 模块提供了一套各平台通用的接口,但是在每个平台间依然会有一些微小的差异。

### macOS

在macOS上,`autoUpdater`模块建立在[Squirrel.Mac][squirrel-mac]上,这意味着你不需要任何特殊的设置来使它工作。

对于服务器端要求,您可以阅读 [Server Support][server-support]. 

注意[App传输安全](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)(ATS)适用于所有请求作为更新过程的一部分。

如需禁用ATS的应用程序可以在其应用程序的plist中添加 `NSAllowsArbitraryLoads`键。

**注意:**您的应用程序必须在macOS上进行自动更新。这是[Squirrel.Mac][squirrel-mac]的要求。

### Windows

在 Windows 上,你必须使用安装程序将你的应用装到用户的计算机上,所以比较推荐的方法是用 [electron-winstaller][installer-lib], [electron-builder][electron-builder-lib] 或 [grunt-electron-installer][installer] 模块来生成Windows安装程序。

当使用[electron-winstaller][installer-lib]或[electron-winstaller][installer-lib], [electron-builder][electron-builder-lib]时,确保你不要尝试更新你的应用程序[第一次运行](https://github.com/electron/windows-installer＃handling-squirrel-events)(另见[这个问题的更多信息](https://github.com/electron/electron/issues/7155))。还建议使用[electron-squirrel-startup](https://github.com/mongodb-js/electron-squirrel-startup)获取应用程序的桌面快捷方式。

使用Squirrel生成的安装程序将以 `com.squirrel.PACKAGE_ID.YOUR_EXE_WITHOUT_DOT_EXE`的格式创建一个带有[Application User Model ID][app-user-model-id]的快捷图标,例子是 `com.squirrel.slack.Slack'和 `com.squirrel.code.Code`。

您应确保程序具有 `app.setAppUserModelId` API且ID相同,否则Windows将无法在应用程式的工作列中正确匹配您的应用程序。

服务器端设置也不同于macOS。您可以阅读[Squirrel.Windows][squirrel-windows]的文档以获得更多详细信息。

### Linux

在Linux上没有自动更新器的内置支持,因此建议使用分发包的包管理器来更新您的应用程序。

## 事件列表

`autoUpdater` 对象会触发以下的事件:

### 事件:'error'
> 触发:**更新发生错误时**

* `error` Error

### 事件:'checking-for-update'
> 触发:**开始检查更新时**

### 事件:'update-available'
> 触发:**发现一个可用更新时**

### 事件:'update-not-available'
> 触发:**没有可用更新时**

### 事件:'update-downloaded'
> 触发:**更新下载完成时**

* `event` Event
* `releaseNotes` String - 新版本更新公告
* `releaseName` String - 新的版本号
* `releaseDate` Date - 新版本发布的日期
* `updateURL` String - 更新地址

在 Windows 上只有 `releaseName` 是有效的。

## 方法列表

`autoUpdater` 对象有以下的方法:

### `autoUpdater.setFeedURL(url)`
> 用途:**设置 `url`并初始化自动更新器**

* `url` String
* `requestHeaders` Object _macOS_ (可选) - HTTP请求标头.

### `autoUpdater.getFeedURL()`
> 用途:**获得当前更新的Feed链接**

 返回 `String` 
 
### `autoUpdater.checkForUpdates()`
> 用途:**询问服务器是否有更新**

在调用这个方法之前,必须要先调用 `setFeedURL`。

### `autoUpdater.quitAndInstall()`
> 用途:**重新启动应用程序,并在更新已下载后安装**

该方法只有在 `update-downloaded`被释放后才被调用。

**注意项:** `autoUpdater.quitAndInstall()`和普通退出有所区别,它将先关闭所有应用程序窗口,然后在 `app`上发出 `before-quit`事件。

[squirrel-mac]: https://github.com/Squirrel/Squirrel.Mac
[server-support]: https://github.com/Squirrel/Squirrel.Mac#server-support
[squirrel-windows]: https://github.com/Squirrel/Squirrel.Windows
[installer]: https://github.com/electron/grunt-electron-installer
[installer-lib]: https://github.com/electron/windows-installer
[electron-builder-lib]: https://github.com/electron-userland/electron-builder
[app-user-model-id]: https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx
[electron-release-server]: https://github.com/ArekSredzki/electron-release-server
[squirrel-updates-server]: https://github.com/Aluxian/squirrel-updates-server
[nuts]: https://github.com/GitbookIO/nuts
[squirrel-release-server]: https://github.com/Arcath/squirrel-release-server