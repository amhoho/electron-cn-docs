# 快速入门

Electron允许您使用JavaScript轻松创建桌面应用程序,并且可以调用丰富的操作系统级原生API.你可以把它当成Node.js变体,只不过它更专注于桌面应用而非Web服务器.

但这并不意味着Electron将JavaScript绑定到图形用户界面(GUI)库,恰恰相反,Electron使用网页作为其GUI,因此你可以将其当成由JavaScript控制的迷你精简版Chromium浏览器.

### 主进程

在 Electron中, 把  `'package.json'`中设定的 `main` 脚本的所在进程称为 __主进程__. 这个进程中运行的脚本也可通过 创建网页 这种方式来展现其GUI.

### 渲染进程

因为Electron是通过Chromium来显示页面,所以Chromium自带的多进程架构也一同被利用. 这样每个页面都运行着一个独立的进程,它们被统称为 __渲染进程__.

通常来说,浏览器中的网页会被限制在沙盒环境中运行并且不允许访问系统原生资源.但是由于Eelectron用户可在页面中调用Node.js API,所以可以和底层操作系统直接交互.

### 主进程与渲染进程的区别

主进程通过创建 `BrowserWindow`实例来创建网页.每个 `BrowserWindow`实例都在自己的渲染进程中运行页面. 一旦 `BrowserWindow`实例被销毁,相应的渲染进程也会被终止.

主进程管理着所有的页面及其对应的渲染进程.每个渲染进程是相互独立的,它只关心在其中运行的页面.

Electron 在页面中禁用了原生GUI相关的API,这是因为在页面中管理原生 GUI是非常危险并容易泄露资源.

不过,如果需要在页面中执行GUI操作,则页面所在的渲染进程必须与主进程通信并请求由主进程执行相关的 GUI 操作.

在Electron中,主进程和渲染进程有多种通信方式,如[`ipcRenderer`](../api/ipc-renderer.md) 和 [`ipcMain`](../api/ipc-main.md)模块可进行消息处理, [remote](../api/remote.md) 模块可进行 `RPC` 方式通信. 详情可参考FAQ章节中的[如何在页面之间共享数据?][share-data].                            

## 打造您的第一个 Electron 应用

Electron应用的结构通常如下:
```text
your-app/
├── package.json
├── main.js
└── index.html
```

`package.json`文件格式和Node模块完全一致,其中`main`字段所标明的脚本即启动脚本,示例如下:
```json
{
  "name"    : "your-app",
  "version" : "0.1.0",
  "main"    : "main.js"
}
```

__注意__: 如果 `package.json`中未标明`main`字段, Electron 将默认加载 `index.js`.

`main.js` 应该用于创建窗口与处理系统事件,例子如下:

```javascript
const {app, BrowserWindow} = require('electron')
const path = require('path')
const url = require('url')

// 保持win对象的全局引用,避免JavaScript对象被垃圾回收时,窗口被自动关闭.
let win

function createWindow () {
  //创建浏览器窗口
  win = new BrowserWindow({width: 800, height: 600})

  // 加载应用的 index.html
  win.loadURL(url.format({
    pathname: path.join(__dirname, 'index.html'),
    protocol: 'file:',
    slashes: true
  }))

  // 打开开发者工具
  win.webContents.openDevTools()

  // 关闭window时触发下列事件.
  win.on('closed', () => {
    // 取消引用 window 对象，通常如果应用支持多窗口，则会将
    // 窗口存储在数组中,现在应该进行删除了.
    win = null
  })
}


// 当Electron完成初始化并准备创建浏览器窗口时调用此方法
// 部分 API 只能使用于 ready 事件触发后。
app.on('ready', createWindow)

// 所有窗口关闭时退出应用.
app.on('window-all-closed', () => {
  // macOS中除非用户按下 `Cmd + Q` 显式退出,否则应用与菜单栏始终处于活动状态.
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

app.on('activate', () => {
  // macOS中点击Dock图标时没有已打开的其余应用窗口时,则通常在应用中重建一个窗口
  if (win === null) {
    createWindow()
  }
})

// 你可以在这个脚本中续写或者使用require引入独立的js文件.
```

最后,你想展示的`index.html` 页面:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
  </head>
  <body>
    <h1>Hello World!</h1>
    We are using node <script>document.write(process.versions.node)</script>,
    Chrome <script>document.write(process.versions.chrome)</script>,
    and Electron <script>document.write(process.versions.electron)</script>.
  </body>
</html>
```

## 运行应用

当你已创建 `main.js`, `index.html`, 和 `package.json` 文件, 你可以按以下方式尝试在本地运行或测试.

### `electron`

[`electron`](https://github.com/electron-userland/electron-prebuilt) 是一个包含Electron预编译版本的 `npm` 模块,如果你已使用`npm`全局安装了它,那么你只需在应用所在目录执行以下命令:

```bash
electron .
```

如果你是局部安装,那运行:

#### macOS / Linux

```bash
$ ./node_modules/.bin/electron .
```

#### Windows

```bash
$ .\node_modules\.bin\electron .
```

### 手动下载 Electron 二进制文件

如果您手动下载了Electron，您也可以使用随附的二进制文件直接运行应用.

#### macOS

```bash
$ ./Electron.app/Contents/MacOS/Electron your-app/
```

#### Linux

```bash
$ ./electron/electron your-app/
```

#### Windows

```bash
$ .\electron\electron.exe your-app\
```

`Electron.app` 是Electron发行包的一部分, 你可以 [在这里直接下载](https://github.com/electron/electron/releases).              

### 以发行版运行

在完成你的应用之后,请参照 [应用分发](./application-distribution.md) 章节分发已封装的应用.

### 参考示例

复制和运行这个库:[`electron/electron-quick-start`](https://github.com/electron/electron-quick-start).

 **注意**: 运行前确保系统已安装[Git](https://git-scm.com) 和 [Node.js](https://nodejs.org/en/download/) (包括[npm](https://npmjs.org)) .

```bash
# 克隆库
$ git clone https://github.com/electron/electron-quick-start
# 进入库
$ cd electron-quick-start
# 安装依赖项
$ npm install
# 运行应用
$ npm start
```

更多的例子,请参考[list of boilerplates](https://electron.atom.io/community/#boilerplates).

[share-data]: ../faq.md#how-to-share-data-between-web-pages
