# 使用 node-inspector 进行主进程调试

尽管[`node-inspector`][node-inspector] 已经提供了一个熟悉的调试器 GUI 即可在 Chrome 中来调试 Electron 的主进程, 但因为 `node-inspector` 依赖于一些必须重新编译的原生 Node 模块. 

所以你需要重编译所调试的Electron版本.你可以重建 `node-inspector` 依赖项, 或直接使用 [`electron-inspector`][electron-inspector] 重建.在使用 `electron-inspector`时需要注意的是,有时它可能无法修补依赖关系.


## 使用 `electron-inspector` 进行调试

### 1. 安装 [node-gyp required tools][node-gyp-required-tools]

### 2. 安装 [`electron-rebuild`][electron-rebuild]

```shell
npm install electron-rebuild --save-dev
```

### 3. 安装 [`electron-inspector`][electron-inspector]

```shell
npm install electron-inspector --save-dev
```

### 4. 启动 Electron

用 `--debug` 参数来运行 Electron:

```shell
electron --debug=5858 your/app
```

或者，在第一行暂停脚本:

```shell
electron --debug-brk=5858 your/app
```

### 5. 启动 electron-inspector

在 macOS / Linux:

```shell
node_modules/.bin/electron-inspector
```

在 Windows:

```shell
node_modules\\.bin\\electron-inspector
```

`electron-inspector` 在首次运行时，或者更改了 Electron 的版本时需要重新编译 `node-inspector` 的依赖关系，重新编译的过程可能需要互联网连接，并花费一些时间才能下载 Node headers 和lib。

### 6. 加载调试器UI

在Chrome浏览器中打开http://127.0.0.1:8080/debug?ws=127.0.0.1:8080&port=5858。如果以`--debug-brk`开头，您可能需要点击“暂停”才能强制更新UI。


## U使用`node-inspector`进行调试

### 1. 安装 [node-gyp required tools][node-gyp-required-tools]

### 2. 安装 [`node-inspector`][node-inspector]

```bash
$ npm install node-inspector
```

### 3. 安装 [`node-pre-gyp`][node-pre-gyp]

```bash
$ npm install node-pre-gyp
```

### 4. 重新编译Electron的 `node-inspector` `v8`模块

**注意：** 将 target 参数修改为你的 Electron 的版本号

```bash
$ node_modules/.bin/node-pre-gyp --target=1.2.5 --runtime=electron --fallback-to-build --directory node_modules/v8-debug/ --dist-url=https://atom.io/download/atom-shell reinstall
$ node_modules/.bin/node-pre-gyp --target=1.2.5 --runtime=electron --fallback-to-build --directory node_modules/v8-profiler/ --dist-url=https://atom.io/download/atom-shell reinstall
```

详见 [如何下载原生模块][how-to-install-native-modules]。               

### 5. 启用Electron的调试模式

您可以使用以下参数启动Electron:

```bash
$ electron --debug=5858 your/app
```

或者，在第一行暂停脚本:

```bash
$ electron --debug-brk=5858 your/app
```

### 6. 使用 Electron 开启 [`node-inspector`][node-inspector] 服务

```bash
$ ELECTRON_RUN_AS_NODE=true path/to/electron.exe node_modules/node-inspector/bin/inspector.js
```

### 7. 加载调试器UI

在Chrome浏览器中打开http://127.0.0.1:8080/debug?ws=127.0.0.1:8080&port=5858。如果从 `--debug-brk`开始查看输入行，则可能需要单击暂停。

[electron-inspector]: https://github.com/enlight/electron-inspector
[electron-rebuild]: https://github.com/electron/electron-rebuild
[node-inspector]: https://github.com/node-inspector/node-inspector
[node-pre-gyp]: https://github.com/mapbox/node-pre-gyp
[node-gyp-required-tools]: https://github.com/nodejs/node-gyp#installation
[how-to-install-native-modules]: using-native-node-modules.md#how-to-install-native-modules
