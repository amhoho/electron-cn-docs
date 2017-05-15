# 专业术语
> 本文介绍了Electron开发时常用的专业属于.

### ASAR
> 含义:**ASAR即Atom Shell 程序包**

[asar][asar]类似常用的 `tar` 格式文件,Electron 无需解压即可读取其中的文件。

[asar的使用方法](https://github.com/electron/asar),通常用来将文档打包在一个.asar中,防止js,html,css等文件直接暴露在外.

简单例子:
```
安装:$ npm install asar -g
压缩:$ asar pack app app.asar
解压:$ asar extract app.asar testpath
```

### Brightray
> 含义:**一个专为Electron开发的可将 [libchromiumcontent] 应用到程序中的静态库**

### DMG
> 含义:**苹果系统的磁盘镜像打包格式**

DMG文件通常用于分发应用程序的 `installers`(安装包)。[electron-builder] 支持使用 `dmg` 来作为编译目标。 

### IPC
> 含义:**进程间通信**

Electron 使用 IPC 来在 [主进程] 和 [渲染进程] 之间传递 JSON 信息。

主进程与渲染进程的ipc通信例子:
```
//主进程中ipcMain监听(on)了事件asynchronous-message并传回给渲染层中的asynchronous-reply.
const ipcMain = require('electron').ipcMain;
ipcMain.on('asynchronous-message', function(event, arg) {
  console.log(arg);  // 输出 `ping`
  event.sender.send('asynchronous-reply', 'pong');
});

ipcMain.on('synchronous-message', function(event, arg) {
  console.log(arg);  // 输出 `ping`
  event.returnValue = 'pong';
});
// 渲染进程中发送(send)了ping给asynchronous-message并监听了asynchronous-reply.
const ipcRenderer = require('electron').ipcRenderer;
console.log(ipcRenderer.sendSync('synchronous-message', 'ping')); // 输出 `pong`

ipcRenderer.on('asynchronous-reply', function(event, arg) {
  console.log(arg); // 输出 `pong`
});
ipcRenderer.send('asynchronous-message', 'ping');
```

### libchromiumcontent
> 含义:**一个单独的开源库,包含了 Chromium 的模块及其所有依赖(比如 Blink, [V8] 等)**

### 主进程
> 含义:**主进程,通常是指 `main.js` 文件,是每个Electron程序的入口点**

它全程控制着整个 APP 的生命周期,也管理着比如菜单,菜单栏,Dock,托盘等元素,也负责创建 APP 的每个渲染进程,而且整个 Node API 都集成在里面。

主进程在 `package.json` 中的 `main` 属性当中定义,这也是为什么 `electron .` 能够知道应该使用哪个文件来启动。

参见: [进程](#进程), [渲染进程](#渲染进程)

### MAS
> 含义:**Mac App Store 的缩略词**

关于如何提交你的 app 至 MAS ,详见 [Mac App Store Submission Guide] 。

### native modules
> 含义:**原生模块是用C或C ++编写的模块，它们和普通的Node.js模块一样使用require()函数加载到Node.js或Electron中**

通常,原生模块为Node.js中运行的JavaScript和C / C ++库之间提供接口.

Natural虽然支持Electron的模块，但由于Electron与Node的V8版本可能不同，所以在构建原生模块时指定 Electron headers的位置。参见:  [Using Native Node Modules]

使用原生模块有以下三个方法:

#### 1.最简单的方式

通过 [`electron-rebuild`][electron-rebuild]包重新编译原生模块,它帮你自动完成了下载 headers, 编译原生模块等步骤:

```
npm install --save-dev electron-rebuild
```

每次运行`npm install`时运行这条命令:
```
./node_modules/.bin/electron-rebuild
```

在windows下如果上述命令遇到了问题,尝试这个:
```
.\node_modules\.bin\electron-rebuild.cmd
```

#### 2.通过 npm 安装的方式

大部分步骤和安装普通模块时一样,除了以下一些系统环境变量你需要自己操作:

```
export npm_config_disturl=https://atom.io/download/atom-shell
export npm_config_target=0.33.1
export npm_config_arch=x64
export npm_config_runtime=electron
HOME=~/.electron-gyp npm install module-name
```

#### 3.通过 node-gyp 安装
为 node-gyp 指定 Electron headers的下载路径和版本:

```
$ cd /path-to-module/
$ HOME=~/.electron-gyp node-gyp rebuild --target=0.29.1 --arch=x64 --dist-url=https://atom.io/download/atom-shell
```
  

## NSIS
> 含义:**Nullsoft Scriptable Install System缩写,它是个WIN系统的安装包制作工具**

[electron-builder] 支持使用 NSIS 作为编译目标。

### 进程
> 含义:**进程是正在执行的计算机程序的实例**

Electron 应用同时使用了 [主进程](主进程) 和数个 [渲染进程](渲染进程)来运行多个程序。

在 Node.js 和 Electron 里面,每个运行的进程都有个 `进程` 对象。此对象是一个全局变量，提供有关当前进程的信息和控制。

参见: [主进程](#主进程), [渲染进程](#渲染进程)

### 渲染进程
> 含义:**渲染进程是你的应用内的一个浏览器窗口**

与主进程不同，可以有多个这些进程，每个进程在一个单独的进程中运行.在 Node.js  API 支持下,Electron 可在页面中和操作系统进行一些低级别的交互,而普通的浏览器中由于受限于沙箱环境是办不到这种交互的。

参见: [进程](#进程), [主进程](#主进程)

### Squirrel
> 含义:**Electron应用程序能够在新版本发布时自动更新的一个开源框架**

详见 [autoUpdater] API 了解如何开始使用 Squirrel。

### userland
> 含义:** `userland`或`userspace`都是指在操作系统内核之外运行的程​​序**

像Node一样，Electron专注于为开发多平台桌面应用程序而提供更精简必要的API。这种设计理念让 Electron 能够保持灵活而不拘束于各种规定.

### V8
> 含义:**Google的一款用C ++编写的专用于Google Chrome的开源JavaScript引擎**

 V8可以独立运行，也可以嵌入到任何C ++应用程序中。

### webview

`webview`标签用于在您的Electron应用程序中嵌入 `guest` 内容（如外部网页）。与 `iframe`不同,每个 webview 都运行在独立的进程中。 

为了确保应用程序免受嵌入内容的危害, `webview`没有普通网页一样的权限，应用和嵌入内容之间的所有互动都将是异步的。

[electron-rebuild]: https://github.com/paulcbetts/electron-rebuild
[addons]: https://nodejs.org/api/addons.html
[asar]: https://github.com/electron/asar
[autoUpdater]: api/auto-updater.md
[electron-builder]: https://github.com/electron-userland/electron-builder
[libchromiumcontent]: #libchromiumcontent
[Mac App Store Submission Guide]: tutorials/mac-app-store-submission-guide.md
[主进程]: #主进程
[渲染进程]: #渲染进程
[Using Native Node Modules]: tutorial/using-native-node-modules.md
[userland]: #userland
[V8]: #v8