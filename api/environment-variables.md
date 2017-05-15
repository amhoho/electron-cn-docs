# 本文介绍:环境变量
> 设置变量,可以不必更改代码的控制应用程序配置和行为。

某些Electron行为由环境变量控制,因为它们比命令行和代码更早地初始化。

POSIX shell示例:
```bash
$ export ELECTRON_ENABLE_LOGGING=true
$ electron
```

Windows控制台示例:
```powershell
> set ELECTRON_ENABLE_LOGGING=true
> electron
```

## 生产变量

下面的环境变量主要用于应用运行时。

### `GOOGLE_API_KEY`
Electron包含用于向Google地理编码网络服务发出请求的硬编码API密钥。因为此API密钥包含在每个版本的Electron中，所以通常会超过其使用配额。

要解决此问题，您可以在环境中提供自己的Google API密钥。将以下代码放置在主进程文件中，然后再打开任何可以进行地理编码请求的浏览器窗口:

```JavaScript
process.env.GOOGLE_API_KEY = 'YOUR_KEY_HERE'
```

有关如何获取谷歌API密钥的说明,请访问:[此页](https://www.chromium.org/developers/how-tos/api-keys)。   

默认情况下,新生成的Google API密钥可能不允许进行地理编码请求。要启用地理编码请求，请访问:[此页](https://console.developers.google.com/apis/api/geolocation/overview)。   

### `ELECTRON_NO_ASAR`
禁用ASAR支持。这个变量只有在设置 `ELECTRON_RUN_AS_NODE`派生和催生的子进程中支持。

### `ELECTRON_RUN_AS_NODE`
将该进程作为正常的Node.js进程启动。

### `ELECTRON_NO_ATTACH_CONSOLE` _Windows_
不要附加到当前控制台会话。

### `ELECTRON_FORCE_WINDOW_MENU_BAR` _Linux_
不要使用Linux上的全局菜单栏。

## 开发变量

下面的环境变量主要用于开发和调试的目的。

### `ELECTRON_ENABLE_LOGGING`
将Chrome的内部日志记录打印到控制台。

### `ELECTRON_LOG_ASAR_READS`
当Electron从ASAR文件读取时，将读偏移和文件路径记录到系统 `tmpdir`。生成的文件可以提供给ASAR模块以优化文件排序。

### `ELECTRON_ENABLE_STACK_DUMPING`
Electron崩溃时,将堆栈跟踪打印到控制台。

如果 `crashReporter`启动,这个环境变量将不起作用。

### `ELECTRON_DEFAULT_ERROR_MODE` _Windows_
Electron崩溃时,显示Windows的崩溃对话框。

如果 `crashReporter`启动,这个环境变量将不起作用。