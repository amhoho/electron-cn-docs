# 通知功能 (Windows, Linux, macOS)

这三个系统都为应用提供了向用户发送通知的API。 Electron允许开发人员使用[HTML5 Notification API](https://notifications.spec.whatwg.org/)快速发送通知,并通过当前系统的自带通知API来显示。

**注意:** 由于这是一个HTML5 API，因此它仅在渲染器进程中可用

```javascript
let myNotification = new Notification('测试标题', {
  body: '测试通知'
})

myNotification.onclick = () => {
  console.log('通知已被点击')
}
```

虽然跨操作系统的代码和用户体验基本相似，但还是有一些差异。

## Windows

* 在Windows 10中, 可正常通知.
* 在Windows 8.1 或 Windows 8中, 需要先使用[Application User Model ID][app-user-model-id]将快捷方式创建到开始屏幕上.
* 在Windows 7中, 无法使用通知. 不过你可以使用[Tray API][tray-balloon]发送一个 `气泡通知`. 

另外,在Win8中,通知内容长度不超过250个字节,Windows团队建议小于200个字符.虽然Win10删除了这个限制,但是如果发送过如上千字的长文本将导致异常.

### Windows中的高级通知

最新版本的Windows支持使用图文影像元素等自定义模板的高级通知.如需使用此类通知,您可以使用[electron-windows-notifications](https://github.com/felixrieseberg/electron-windows-notifications)模块来发送 `ToastNotification` 和 `TileNotification` 对象进行实现.

仅仅通知则只需要 `electron-windows-notifications`就够了, 但是想要处理答复的话,您需要使用[`electron-windows-interactive-notifications`](https://github.com/felixrieseberg/electron-windows-interactive-notifications)模块进行注册COM组件并根据输入数据调用Electron应用来实现.

## macOS

MacOS上的通知是直接转发的，但您应该了解[Apple关于通知的人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/NotificationCenter.html)                       

另外,通知内容长度不超过256个字节

### macOS中的高级通知

最新版本的macOS支持发送带有快速回复的输入框的通知,如需要这一功能,请使用[node-mac-notifier](https://github.com/CharlieHess/node-mac-notifier)实现

## Windows和macOS检测系统是否支持收发通知(静默/演示模式)

您可以使用[electron-notification-state](https://github.com/felixrieseberg/electron-notification-state)模块检测系统是否允许收发通知,这样就可以避免Windows默认忽略通知的情况.

## Linux

通知使用 `libnotify`进行发送, 只要桌面环境支持 [桌面通知规范][notification-spec]即可正常显示, 包括 Cinnamon, Enlightenment, Unity, GNOME, KDE.

[tray-balloon]: ../api/tray.md#traydisplayballoonoptions-windows
[app-user-model-id]: https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx
[notification-spec]: https://developer.gnome.org/notification-spec/