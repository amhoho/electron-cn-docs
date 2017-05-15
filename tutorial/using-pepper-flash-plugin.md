# 使用 Pepper Flash 插件
> 在Electron使用Pepper Flash插件，需要手动设置路径并在应用中启用它。

## 准备Flash插件的副本

在macOS和Linux上，可通过Chrome的 `chrome：// plugins`页面查看Pepper Flash插件的详细信息，接下来会用到其中的路径和版本信息，或者你也可以将其复制一份到其它位置。

##  在 Electron 中 添加支持

你可以直接添加在Electron中添加命令行: `--ppapi-flash-path` 和 `--ppapi-flash-version`,也可以在 `ready`之前调用 `app.commandLine.appendSwitch`方法. 

另外，需要设置 `BrowserWindow`的 `plugins`选项（下文例子最后一行提到）。

```javascript
const {app, BrowserWindow} = require('electron')
const path = require('path')

// 指定flash路径, 假设与main.js同一目录.
let pluginName
switch (process.platform) {
  case 'win32':
    pluginName = 'pepflashplayer.dll'
    break
  case 'darwin':
    pluginName = 'PepperFlashPlayer.plugin'
    break
  case 'linux':
    pluginName = 'libpepflashplayer.so'
    break
}
app.commandLine.appendSwitch('ppapi-flash-path', path.join(__dirname, pluginName))

// 可选：指定Flash版本,比如v17.0.0.169
app.commandLine.appendSwitch('ppapi-flash-version', '17.0.0.169')

app.on('ready', () => {
  let win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      plugins: true
    }
  })
  win.loadURL(`file://${__dirname}/index.html`)
  // 其它
})
```

另外，你也可以不必自己装载插件而先使用 `app.getPath('pepperFlashSystemPlugin')`获取系统自带的Pepper Flash插件路径，然后进行加载。

## 在 `<webview>`标签中启用Flash插件

将 `plugins` 属性添加到 `<webview>` 标签

```html
<webview src="http://www.adobe.com/software/flash/about/" plugins></webview>
```

## 故障排除

1. 当您不确定路径是否正确时，你可以在控制台中输出 `navigator.plugins`来检查是否加载了Pepper Flash插件。
2. Pepper Flash插件必须与Electron的架构相匹配，比如在Windows中常犯的错误是在64位版本的Electron使用32位版本的Flash插件.
3. 在Windows中， `--ppapi-flash-path`的路径参数必须使用 `\`作为分隔符，使用POSIX样式的路径是无效的。
4. 对于某些操作，如使用RTMP的流媒体，有必要向播放器的 `.swf`文件授予更多的权限。 实现这点的方式之一是使用 [nw-flash-trust](https://github.com/szwacz/nw-flash-trust).