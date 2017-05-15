# 本文介绍:对话框

> 打开或保存文件,弹出警告等对话框

进程: [主进程](../glossary.md#主进程)    

选择多个文件和目录的对话框:
```JavaScript
const {dialog} = require('electron')
console.log(dialog.showOpenDialog({properties: ['openFile', 'openDirectory', 'multiSelections']}))
```

对话框默认是在主线程中打开,一下例子展示了如何接收渲染器进程的弹出框对象:

```JavaScript
const {dialog} = require('electron').remote
console.log(dialog)
```

## 方法

### `dialog.showOpenDialog([browserWindow, ]options[, callback])`
> 用途:**创建并弹出新的对话框**

* `browserWindow` BrowserWindow (可选)
* `options` Object
  * `title` String (可选)
  * `defaultPath` String (可选)
  * `buttonLabel` String (可选) - 自定义标的确认按钮,留空即系统默认。
  * `filters` [FileFilter[]](structures/file-filter.md) (可选) 限定文件类型的数组
  * `properties` String[] (可选) - 对话框的功能,以下可选:
    * `openFile`  - 允许选择文件。
    * `openDirectory`  - 允许选择目录。
    * `multiSelections`  - 允许选择多个路径。
    * `showHiddenFiles`  - 在对话框中显示隐藏文件。
    * `createDirectory`  - 允许从对话框创建新目录。_macOS_ 
    * `promptToCreate`  - 如果在对话框中输入的文件路径不存在,则提示进行创建。_Windows_ 
    * `noResolveAliases`  - 禁用自动别名(符号链接)路径解析。所选别名会返回别名路径而非目标路径。_macOS_ 
* `message` String(可选) 要在输入框上显示的消息。_macOS_ 
* `callback` Function (可选)
  * `filePaths` String[] - 用户选择的文件路径数组

成功就返回一个供用户选择的文件路径数组 `String[]`,失败则返回 `undefined`. 
`browserWindow`参数允许对话框将自身附加到父窗口,使其成为模态。
 可使用 `filters` 限定文件类型,比如,限制用户选择特定类型比如图片格式时。
```JavaScript
{
  filters: [
    {name: 'Images', extensions: ['jpg', 'png', 'gif']},
    {name: 'Movies', extensions: ['mkv', 'avi', 'mp4']},
    {name: 'Custom File Type', extensions: ['as']},
    {name: 'All Files', extensions: ['*']}
  ]
}
```
 `extensions` 数组不可包括通配符或含有点号的扩展名 (比如 `'png'`是正确的,而 `'.png'` 或 `'*.png'` 都是错的). 你可以直接用`'*'` 通配符显示所有文件.
 
如果 `callback` 已通过传递,  API 结果将通过 `callback(filenames)`异步传递.

 **注意:**在Windows和Linux上,打开的对话框不能同时是文件选择器和目录选择器,因此如果在这些平台上将 `properties`设置为 `['openFile','openDirectory']` 选择器。

### `dialog.showSaveDialog([browserWindow, ]options[, callback])`
> 用途:**显示保存对话框并返回用户选择文件的路径**

* `browserWindow` BrowserWindow (可选)
* `options` Object
  * `title` String (可选)
  * `defaultPath` String (可选)
  * `buttonLabel` String (可选) - 自定义标的确认按钮,留空即系统默认。
  * `filters` [FileFilter[]](structures/file-filter.md) (可选) 限定文件类型的数组
* `callback` Function (可选)
  * `filename` String

返回 `String`即用户选择文件的路径,如果提供回调,则返回 `undefined`。

 `browserWindow`参数允许对话框将自身附加到父窗口,使其成为模态。
 
 可使用 `filters` 限定文件类型,比如,限制用户选择特定类型比如图片格式,详见上文 `dialog.showOpenDialog`一个例子。
 
如果 `callback` 已通过传递,  API 结果将通过 `callback(filenames)`异步传递.

### `dialog.showMessageBox([browserWindow, ]options[, callback])`
> 用途:**显示消息框并返回用户选择结果(按钮ID)**

* `browserWindow` BrowserWindow (可选)
* `options` Object
  * `type` String (可选) - 可选 `none`, `info`, `error`, `question` 或
  `warning`. 在Windows上, `question`显示的图标与 `info`一样,除非您使用了 `icon`选项设置图标.在macOS中, `"warning"`显示的图标与 `error`一样.
  * `buttons` String[] (可选) - 按钮的文本数组。在Windows上,空数组将导致一个按钮标记为`确定`。
  * `defaultId` Integer (可选) - 当消息框打开时,默认选中按钮的ID,ID在上方按钮数组中。
  * `title` String (可选) - 消息框标题,某些平台可能不会显示。
  * `message` String - 消息框内容.
  * `detail` String (可选) - 消息的额外信息.
  * `checkboxLabel` String (可选) - 如果提供，消息框将包含带有给定标签的复选框。只有使用 `callback`时，才能检查复选框的状态。
   * `checkboxChecked` Boolean (可选) - 复选框的初始选中状态 ,默认为 `false`
  * `icon` [NativeImage](native-image.md) (可选)
  * `cancelId` Integer (可选) - - 通过 `ESC`键用于取消对话框按钮的索引。默认分配给具有 `cancel`或 `no`的第一个按钮。如果没有这样的按钮或未设置此选项，则将使用 `0`作为返回值或回调响应。在Windows上将忽略此选项。
  * `noLink` Boolean (可选) - 在Windows上,应用将尝试找出哪个 `buttons` 是常用按钮(如 `cancel`或 `yes`),并在对话框中显示其他作为命令链接。
    这可以使对话框以现代Windows应用程序的风格显示。
    如果你不喜欢这个行为,你可以设置 `noLink`为 `true`。
 * `normalizeAccessKeys` Boolean (可选) - 规范化跨平台的键盘访问键。默认值为 `false`。
    用 `&` 连接和转换键盘访问键,以便它们在每个平台上正常工作.
    `&` 字符在macOS上会被删除,而在Linux上被转换为  `_` ,在Windows上则保持不变。
    例如, `Vie＆w`的按钮标签将在Linux上转换为 `Vie_w`,在macOS上转换为 `View`,而Windows和Linux上表示 `Alt-W`。
* `callback` Function (可选)
  * `response` Number - 被点击按钮的索引
  * `checkboxChecked` Boolean - 如果设置了checkboxLabel，则选中复选框的状态。否则为false。
  
返回 `Integer`,即被点击按钮的索引,如果提供回调,它返回 `undefined`。

作用:显示消息框时,将阻止进程直到消息框关闭。最终返回点击按钮的索引。 `browserWindow`参数允许对话框将自身附加到父窗口,使其成为模态。

如果 `callback` 已通过传递,  API 结果将通过 `callback(response)`异步传递.

### `dialog.showErrorBox(title, content)`
> 用途:**显示错误提示框**

* `title` String - 显示在错误框中的标题
* `content` String - 显示在错误框中的文本内容

作用:显示一个显示错误消息的模态对话框。

这个API可以在 `app`模块触发 `ready`事件之前被安全地调用,它通常用在启动时报告错误。

在Linux上, `ready` 事件之前调用这个API,消息将被发送到stderr,并且不会出现GUI对话框。

### `dialog.showCertificateTrustDialog([browserWindow, ]options, callback)` _macOS_ _Windows_
> 用途:**弹出一个用于展示消息与证书信息并向用户提供信任/导入证书的选项的模态对话框**

* `browserWindow` BrowserWindow (可选) 在macOS中,如果存在该 `browserWindow`参数,对话框会被附加到父窗口而成为模态.
* `options` Object
  * `certificate` [Certificate](structures/certificate.md) - 信任/导入的证书
  * `message` String - 要向用户显示的消息
* `callback` Function

在macOS中,将弹出一个用于展示消息与证书信息并向用户提供信任/导入证书的选项的模态对话框。 

在Windows中,受限于Win32 API，可选项变得更为有限:

  - `message`参数无效，因为操作系统提供了自身的确认对话框。
  -  `browserWindow`参数被忽略，因此无法成为模态对话框。

## Sheets

在macOS上,如果您在 `browserWindow`参数中提供 `BrowserWindow`引用,或者如果没有提供窗口的话,对话框将显示为附加到窗口的工作表。

您可以调用 `BrowserWindow.getCurrentWindow()。setSheetOffset(offset)`来更改与附加工作表的窗口框架的偏移量。