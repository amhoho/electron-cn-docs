# 本文介绍:快捷键字符串

>使用[`globalShortcut`](global-shortcut.md)中的[`register`](global-shortcut.md#globalshortcutregisteraccelerator-callback)绑定快捷键.

# 快捷键字符串

多个则采用 `+` 连接,例如:
* `CommandOrControl+A`
* `CommandOrControl+Shift+Z`

例子:

```JavaScript
const {app, globalShortcut} = require('electron')

app.on('ready', () => {
  // 注册 'CommandOrControl+Y' 快捷方式监听器
  globalShortcut.register('CommandOrControl+Y', () => {
    // 按下 Y + either Command/Control后的处理
  })
})
```

## 可用的功能键

* `Command`(或缩写为 `Cmd`)
* `Control`(或缩写为 `Ctrl`)
* `CommandOrControl`(或缩写为 `CmdOrCtrl`) - Linux 和 Windows 下的 `Control` 键,macOS 的 `Command` 键。
* `Alt`
* `Option`
* `AltGr`
* `Shift`
* `Super` - Linux 和 Windows 上的 `Windows` 键,macOS 的 `Command` 键。

## 可用的普通键

* `0` 到 `9`
* `A` 到 `Z`
* `F1` 到 `F12`
* 类似 `~`,  `!`,  `@`,  `#`,  `$` 的标点符号。
* `Plus`
* `Space`
* `Backspace`
* `Delete`
* `Insert`
* `Return`(等同 `Enter` )
* `Up`,  `Down`,  `Left` 和 `Right`
* `Home` 和 `End`
* `PageUp` 和 `PageDown`
* `Escape`(可缩写为 `Esc`)
* `VolumeUp` ,  `VolumeDown`  和 `VolumeMute`
* `MediaNextTrack` ,  `MediaPreviousTrack` ,  `MediaStop`  和   `MediaPlayPause`
* `PrintScreen`
