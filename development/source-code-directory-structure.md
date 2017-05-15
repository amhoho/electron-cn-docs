# 源代码目录结构

> Electron 的源代码主要依据 Chromium 的拆分约定被拆成了许多部分。

为了更好地理解源代码，您可能需要了解一下
[Chromium 的多进程架构](http://dev.chromium.org/developers/design-documents/multi-process-architecture)           

## 源代码结构

```
Electron
├── atom/ - C ++源代码.
|   ├── app/ - 系统入口代码
|   ├── browser/ - 前端包含了主窗口、UI 和其他所有与主进程有关的东西，它会告诉渲染进程如何管理页面
|   |   ├── ui/ - 不同平台上 UI 部分的实现
|   |   |   ├── cocoa/ - Cocoa特定的源代码
|   |   |   ├── win/ - Windows GUI特定的源代码
|   |   |   └── x/ - X11 特定的源代码
|   |   ├── api/ - 主进程 API 的实现
|   |   ├── net/ - 网络相关的代码
|   |   ├── mac/ - Mac特定的Objective-C源代码
|   |   └── resources/ -图标，平台相关的文件等
|   ├── renderer/ - 在渲染器进程中运行的代码
|   |   └── api/ - 渲染器进程API的实现
|   └── common/ - 同时被主进程和渲染进程用到的代码，包括了一些用来将 node 的事件循环
|      |        整合到 Chromium 的事件循环中时用到的工具函数和代码
|       └── api/ - 实现常见的API，以及Electron的内置模块的基础
├── chromium_src/ - 从Chromium复制的源代码
├── default_app/ -  当Electron未启动时显示的默认应用
├── docs/ - 文档
├── lib/ - JavaScript源代码
|   ├── browser/ -  主进程中的Javascript初始化代码
|   |   └── api/ - Javascript API实现.
|   ├── common/ - 主进程和渲染进程公用的JavaScript
|   |   └── api/ - Javascript API实现.
|   └── renderer/ - 渲染进程中的Javascript初始化代码
|       └── api/ - Javascript API实现.
├── spec/ - 自动测试
├── electron.gyp -  Electron 的构建规则
└── common.gypi -为诸如 `node` 和 `breakpad` 等其他组件准备的编译设置和构建规则
```

## 其他目录的结构

* **script** - 用于诸如构建、打包、测试等开发用途的脚本
* **tools** - 在 gyp 文件中用到的工具脚本，但与 `script` 目录不同， 该目录中的脚本不应该被用户直接调用
* **vendor** - 第三方依赖项的源代码，为了防止人们将它与 Chromium 源码中的同名目录相混淆，  在这里我们不使用 `third_party` 作为目录名
* **node_modules** - 在构建中用到的第三方 node 模块
* **out** - `ninja` 的临时输出目录
* **dist** - 由脚本 `script/create-dist.py` 创建的临时发布目录
* **external_binaries** - 下载的不支持通过 `gyp` 构建的预编译第三方框架

## 保持Git子模块为最新
在[/vendor][vendor]目录中有一些Electron存储库的依赖项。运行 `git status`时你可能看到这样的提示:

```sh
$ git status
	modified:   vendor/libchromiumcontent  (new commits)
	modified:   vendor/node (new commits)
```

要更新这些被提供的依赖关系，请运行以下命令:

```sh
git submodule update --init --recursive
```

如果你发现自己经常运行这个命令，你可以在你的 `~/.gitconfig`文件中为它创建一个别名:

```
[alias]
	su = submodule update --init --recursive
```

[vendor]: https://github.com/electron/electron/tree/master/vendor