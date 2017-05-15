# 本文介绍:shell(使用系统默认应用程序管理文件或URL)

> `shell`模块提供与 桌面集成 相关的功能,如点击url时调用默认浏览器

进程: [主进程](../glossary.md#主进程), [渲染进程](../glossary.md#渲染进程)      

使用默认浏览器打开链接:
```JavaScript
const {shell} = require('electron')
shell.openExternal('https://github.com')
```

## 方法

### `shell.showItemInFolder(fullPath)`
> 用途:**判断是否在文件管理器中显示并选中指定文件( `Boolean`)**

* `fullPath` String

### `shell.openItem(fullPath)`
> 用途:**判断是否用默认程序打开指定文件( `Boolean`)**

* `fullPath` String

### `shell.openExternal(url[, options, call,back])`
> 用途:**判断链接是否关联的默认程序打开,如`mailto:`以邮件程序打开( `Boolean`)**

* `url` String
* `options` Object (可选) _macOS_
  * `activate` Boolean - `true`将打开默认应用程序。默认值为 `true`。
* `callback` Function (可选) -指定后将执行异步打开. _macOS_
  * `error` Error

### `shell.moveItemToTrash(fullPath)`
> 用途:**判断是否将指定项目扔到回收站( `Boolean`)**

* `fullPath` String

### `shell.beep()`
> 用途:**播放蜂鸣声**

### `shell.writeShortcutLink(shortcutPath[, operation], options)` _Windows_
> 用途:**判断是否成功创建或更新,覆盖快捷方式( `Boolean`)**

* `shortcutPath` String
* `operation` String (可选) - 操作方式,默认为 `create`,可选:
  * `create` - 创建新的快捷方式,已存在则覆盖。
  * `update` -仅在现有快捷方式上更新指定的属性
  * `replace` - 覆盖现有快捷方式,如果快捷方式不存在则失败。
* `options` [ShortcutDetails](structures/shortcut-details.md)

### `shell.readShortcutLink(shortcutPath)` _Windows_
> 用途:**读取链接至指定路径 `shortcutPath`的快捷方式( ` [`ShortcutDetails`](structures/shortcut-details.md)`)**

* `shortcutPath` String

发生任何错误时将抛出异常。