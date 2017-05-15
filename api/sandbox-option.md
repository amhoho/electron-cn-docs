# `sandbox` 沙盒选项

>创建一个渲染器可运行于 Chromium OS 沙盒中的浏览器窗口. 启用此选项后，渲染器必须通过IPC与主进程进行通信才能访问Node API。同时,为了使Chromium OS沙盒可运行,electron 必须使用 `--enable-sandbox`命令行参数运行。

Chromium主要的安全功能之一是所有Blink渲染或JavaScript都运行在沙盒中,该沙盒使用了特定于操作系统的功能以确保渲染器进程不会对系统造成危害.

也就是说,启用沙盒时,渲染器只能通过IPC将任务委托给主进程来对系统进行更改。详情参考[chromium沙盒文档](https://www.chromium.org/developers/design-documents/sandbox)      

因为Electron的主要特性是可在渲染器进程中运行node.js(以便使用web技术轻松开发应用),所以Electron默认禁用了沙盒,

之所以这样,是因为大多数的node.js API都需要使用系统访问权限,比如 `require()`缺少了系统访问权限则无法使用, 但在沙盒环境中这样做是被禁止的.

 通常,引起这样的情况并不是由于桌面应用程序本身的问题,而是代码之间的可靠和依赖性使Electron在安全性上不像Chromium可显示不受信任的Web内容. 
 
 对于需要更安全性的应用程序, `sandbox`会强制electron使用基础的chromium渲染器以便兼容沙盒.

沙盒渲染器即没有运行node.js环境也不会将node.js的JavaScript API发布到客户端代码中更不会修改任何默认的JavaScript API,但有个例外是预加载脚本可访问Electron渲染器API子集.

综上所述,诸如`window.open`之类的API会是和chromium一样的运作方式(即它们不返回 `BrowserWindowProxy`)

## 例子

在`webPreferences`中设置`sandbox：true`即可创建一个沙盒窗口:

```js
let win
app.on('ready', () => {
  win = new BrowserWindow({
    webPreferences: {
      sandbox: true
    }
  })
  w.loadURL('http://google.com')
})
```

上面代码中,用于创建 `BrowserWindow` 的node.js是禁用的,只能通过IPC进行通信.使用该选项可阻止electron在渲染器中创建 `node.js runtime`.

另外,在这个新窗口中, `window.open`将遵循默认的原生行为即electron创建一个 `BrowserWindow` 并通过 `window.open`返回一个 `proxy`.

还有,此选项本身不会启动操作系统自带的OS沙盒.需要将 `--enable-sandbox`命令行参数传递给electron( 即 `app.commandLine.appendSwitch('--enable-sandbox')` )才可启用该功能,该参数将对所有的 `BrowserWindow` 实例强制设置 `sandbox: true`.

```js
let win
app.on('ready', () => {
  // 启用了`--enable-sandbox`就无需设置`sandbox: true` .
  win = new BrowserWindow()
  w.loadURL('http://google.com')
})
```

请注意,对chromium沙盒的设置进行了更改之后,electron/node启动运行时除了调用 `app.commandLine.appendSwitch('--enable-sandbox')`,还需要执行下列命令行:

```
electron --enable-sandbox app.js
```

一旦启用了 `--enable-sandbox`参数,则无法使OS沙盒仅对某些渲染器生效,也就是无法创建普通的electron窗口.

如果需要在应用中混合使用沙盒和非沙盒渲染器,那么你只需忽略 `--enable-sandbox`这个参数,但是没有它,用 `sandbox：true`创建的窗口仍然被禁用node.js ,还是只能通过IPC进行通信,IPC本身已经是安全POV的增益。

## 预加载脚本

应用程序使用预加载脚本自定义沙盒渲染器:

```js
let win
app.on('ready', () => {
  win = new BrowserWindow({
    webPreferences: {
      sandbox: true,
      preload: 'preload.js'
    }
  })
  w.loadURL('http://google.com')
})
```

preload.js:

```js
// 每次创建javascript上下文时都会加载这个文件,它仅运行在可访问Electron渲染器子集的局部范围中,你务必
//小心谨慎的使任何内容都不影响至全局范围中.
const fs = require('fs')
const {ipcRenderer} = require('electron')

// 使用`fs`模块读取配置文件
const buf = fs.readFileSync('allowed-popup-urls.json')
const allowedUrls = JSON.parse(buf.toString('utf8'))

const defaultWindowOpen = window.open

function customWindowOpen (url, ...args) {
  if (allowedUrls.indexOf(url) === -1) {
    ipcRenderer.sendSync('blocked-popup-notification', location.origin, url)
    return null
  }
  return defaultWindowOpen(url, ...args)
}

window.open = customWindowOpen
```

预载脚本中要注意的重要事项：

- 即使沙盒渲染器未运行node.js,它也可访问 `Buffer`, `process`, `setImmediate` 和 `require`等类似node的环境.
- 预加载脚本可由 `remote`和 `ipcRenderer`模块进行间接访问主进程中的所有API。 这就是 `fs`(上面使用的)和其他模块的实现方法:它们是主进程中的远程对等体的代理。
- 预加载脚本必须包含在一个单独的脚本中,但是你可以使用类似browserify之类的工具来合并含有多个模块的复杂预加载脚本.实际上,electron已使用了browserify为preload脚本提供类似node的环境.

使用以下内容创建一个browserify包,并将其当成预加载脚本的例子:

    browserify preload/index.js \
      -x electron \
      -x fs \
      --insert-global-vars=__filename,__dirname -o preload.js
      
详解:

 - `-x` 应该和预加载范围中已暴露的任何必须模块一起使用.并告知browserify使用封装的 `require`方法. 
 - `--insert-global-vars` 将确保 `process`, `Buffer` 和 `setImmediate`取自封闭的范围内(通常这个范围是指browserify注入代码).

目前在 `preload` 范围中提供的 `require`方法有以下模块：

- `child_process`
- `electron` (crashReporter, remote and ipcRenderer)
- `fs`
- `os`
- `timers`
- `url`

你也可以按需添加更多内容来实现在沙盒中暴露更多的电子API，而且主进程中的任何模块都可以通过`electron.remote.require`使用。

## 现状

 `sandbox`设置目前还是个实验性功能,所以需要谨慎使用,因为我们仍然不知道将electron渲染器API暴露给预加载脚本有什么样的安全隐患.
 
当渲染那些不受信任的内容前,请考虑以下事项:
- 预加载脚本可能会将某些特性API泄露给不受信任的代码.
- 恶意代码可利用V8引擎中的某些bug访问渲染器预加载API并通过 `remote`模块完全访问系统.

在electron中显示不受信任的内容和将预加载API暴露在沙盒中的行为目前还是很不稳定和不安全的,我们目前正在积极改善中.

有个简单的大幅提高安全性的方法是默认阻止来自沙盒渲染器的IPC消息,或在主进程标明可允许的内容.