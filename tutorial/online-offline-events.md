# 在线/离线事件检测

在渲染器进程中,使用标准HTML5 API实现在线和离线事件检测,例子:

_main.js_

```javascript
const {app, BrowserWindow} = require('electron')

let onlineStatusWindow

app.on('ready', () => {
  onlineStatusWindow = new BrowserWindow({ width: 0, height: 0, show: false })
  onlineStatusWindow.loadURL(`file://${__dirname}/online-status.html`)
})
```

_online-status.html_

```html
<!DOCTYPE html>
<html>
<body>
<script>
  const alertOnlineStatus = () => {
    window.alert(navigator.onLine ? 'online' : 'offline')
  }

  window.addEventListener('online',  alertOnlineStatus)
  window.addEventListener('offline',  alertOnlineStatus)

  alertOnlineStatus()
</script>
</body>
</html>
```

你也许会希望在主程序中回应这些事件,但由于主进程中不存在`navigator`对象，所以无法直接检测在线还是离线。

不过,你可以使用Electron的进程间通信方法将事件进行转发到主进程进行处理，例子:

_main.js_

```javascript
const {app, BrowserWindow, ipcMain} = require('electron')
let onlineStatusWindow
//ready时启用监听
app.on('ready', () => {
//隐藏窗口载入渲染进程,以便获取通信状态
  onlineStatusWindow = new BrowserWindow({ width: 0, height: 0, show: false })
  onlineStatusWindow.loadURL(`file://${__dirname}/online-status.html`)
})
//主进程中从渲染进程通信的网络状态
ipcMain.on('online-status-changed', (event, status) => {
  console.log(status)
})
```

_online-status.html_

```html
<!DOCTYPE html>
<html>
<body>
<script>
  const {ipcRenderer} = require('electron')
  //定义内容
  const updateOnlineStatus = () => {
    ipcRenderer.send('online-status-changed', navigator.onLine ? 'online' : 'offline')
  }
//启动监听
  window.addEventListener('online',  updateOnlineStatus)
  window.addEventListener('offline',  updateOnlineStatus)
  updateOnlineStatus()
</script>
</body>
</html>
```

**注意:** 除了Electron未连接局域网(LAN)或路由器被视为离线(脱机)之外,其他条件都返回 `true`。

尽管如此,当 `navigator.onLine` 返回一个 `false`值时,你不能想当然的以为 `true`即已联网,因为联网状态可以轻易伪造,比如计算机使用虚拟化软件始终连接着虚拟以太网网络适配器.

所以,如果要确定 Electron的真实联网状态，您应该使用更多检查手段。