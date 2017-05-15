# 本文介绍:crashReporter类(应用奔溃报告)

>将崩溃报告提交到远程服务器。

进程: [主进程](../glossary.md#主进程), [渲染进程](../glossary.md#渲染进程) 

以下是自动向远程服务器提交崩溃报告的示例：
```JavaScript
const {crashReporter} = require('electron')

crashReporter.start({
  productName: 'YourName',
  companyName: 'YourCompany',
  submitURL: 'https://your-domain.com/url-to-submit',
  uploadToServer: true
})
```

要设置服务器以接受和处理崩溃报告，您可以使用以下项目：

* [socorro](https://github.com/mozilla/socorro)
* [mini-breakpad-server](https://github.com/electron/mini-breakpad-server)

崩溃报告保存在相对于应用程序的临时目录文件夹中。

对于 `YourName`的 `productName`,崩溃报告将存储在临时目录里的 `YourName Crashes`的文件夹中.

你也可以在启动崩溃报告器之前通过 `app.setPath('temp', '/my/custom/temp')`定义相应目录.

## 方法

### `crashReporter.start(options)`
> 用途:**开始收集奔溃报告**

* `options` Object
  * `companyName` String (可选)
  * `submitURL` String - 以POST接收数据的URL
  * `productName` String (可选) -  默认为 `app.getName()`.
  * `uploadToServer` Boolean (可选) - 是否上传奔溃报告至系统服务,默认为 `true`.
  * `ignoreSystemCrashHandler` Boolean (可选) - 默认为 `false`.
  * `extra` Object (可选) - 一个你可以定义的对象,附带在崩溃报告上一起发送 . 只有字符串属性可以被正确发送,不支持嵌套对象.
  
此方法在使用任何其他 `crashReporter` API之前调用.

您可从中收集崩溃报告的每个进程(主/渲染器)。

当从不同进程调用时,可以向 `crashReporter.start`传递不同的选项。

 **注意**由于通过`child_process`模块创建的子进程无法访问Electron模块。因此，请使用`process.crashReporter.start`来收集它们的崩溃报告。传递的选项与上面一致,多了一个 `crashesDirectory`(时存储崩溃报告的目录)。你也可以通过调用`process.crash()`来测试这个过程。
 
**注意：**要在Windows中收集子进程的崩溃报告，您还需要添加此额外的代码。这将启动监控和发送崩溃报告的过程。请用适当的值替换 `submitURL`， `productName`和 `crashesDirectory`。

**注意：**如果你需要在第一次调用 `start`后发送额外的/更新的 `extra`参数，你可以:

在macOS上调用 `setExtraParameter`.

在Linux或WIN上用新的/更新的 `extra`参数再次调用 `start`.

```js
 const args = [
   `--reporter-url=${submitURL}`,
   `--application-name=${productName}`,
   `--crashes-directory=${crashesDirectory}`
 ]
 const env = {
   ELECTRON_INTERNAL_CRASH_SERVICE: 1
 }
 spawn(process.execPath, args, {
   env: env,
   detached: true
 })
```

 **注意:**在macOS上,Electron是使用新的 `crashpad`客户端进行崩溃收集和报告,可以从主,渲染器进程和通过 `child_process`模块创建的任何子进程中收集崩溃。

如果要启用崩溃报告,则需要使用 `crashReporter.start`从主进程初始化 `crashpad`。

一旦这样初始化,crashpad处理程序收集来自所有进程的崩溃。

你必须从渲染器进程调用 `crashReporter.start`,否则渲染器进程的奔溃报告将不含 `companyName`, `productName`或任何 `extra`信息。

### `crashReporter.getLastCrashReport()`
> 用途:**返回上次崩溃报告的日期和 ID[`CrashReport`](structures/crash-report.md)**

如果没有发送崩溃报告或崩溃报告器尚未启动，则返回 `nul`。

### `crashReporter.getUploadedReports()`
> 用途:**返回所有上传的崩溃报告。每个报告包含日期和上传的ID**

### `crashReporter.getUploadToServer()` _Linux_ _macOS_
> 用途:**判断是否已将报告提交到服务器**

返回 `Boolean` - 通过 `start`方法或 `setUploadToServer`设置

 **注意:** 该API仅限主进程内调用

### `crashReporter.setUploadToServer(uploadToServer)` _Linux_ _macOS_
> 用途:**判断是否已将报告提交到服务器**

* `uploadToServer` Boolean _Linux_ _macOS_ - 是否应将报告提交到服务器

通常,是否提交是由用户对系统进行偏好设置而决定的.且不能在 `start`之前调用该方法,否则无效.

 **注意:** 该API仅限主进程内调用

### `crashReporter.setExtraParameter(key, value)` _macOS_
> 用途:**设置要与崩溃报告一起发送的额外参数**

* `key` String  - 参数键。
* `value` String  - 参数值。指定 `null`或 `undefined`将从额外的参数中删除键。


当调用 `start`时,除了通过 `extra`选项设置的值之外,此处指定值也将被发送。

此API仅在macOS上可用, `start` 首次调用后,如果您希望在 在Linux和Windows上添加或更新额外参数,

您可以更新 `extra`选项并再次调用 `start`。

## 崩溃报告负载

崩溃报告将发送下面 `multipart/form-data` `POST` 型的数据给 `submitURL` :

* `ver` String - Electron 版本.
* `platform` String - 例如 'win32'.
* `process_type` String - 例如 'renderer'.
* `guid` String - 例如 '5e1286fc-da97-479e-918b-6bfb0c3d1c72'
* `_version` String - `package.json` 版本.
* `_productName` String - `crashReporter` `options`对象中的产品名字.
* `prod` String - 基础产品名字. 这种情况为 Electron.
* `_companyName` String - `crashReporter` `options`对象中的公司名字.
* `upload_file_minidump` File - `minidump` 格式的崩溃报告.
* `crashReporter` 中的 `extra` 对象的所有等级和一个属性.
  `options` object