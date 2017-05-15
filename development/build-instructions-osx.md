# 构建说明(macOS)

> 在macOS中,构建Electron

## 前提要求

* macOS >= 10.11.6
* [Xcode](https://developer.apple.com/technologies/tools/) >= 8.2.1
* [node.js](http://nodejs.org) (外部)

如果你使用的是由Homebrew下载的Python,你还需要安装以下Python模块:

* [pyobjc](https://pythonhosted.org/pyobjc/install.html)

## 获取代码

```bash
$ git clone https://github.com/electron/electron
```

## 引导

引导脚本将下载所有必需的构建依赖项并创建构建项目文件,由于使用了 `ninja`进行构建,因此不存在 `Xcode`项目.
```bash
$ cd electron
$ ./script/bootstrap.py -v
```

## 构建

创建 `Release`和 `Debug` target:
```bash
$ ./script/build.py
```

还可以仅构建 `Debug` target :
```bash
$ ./script/build.py -c D
```

在构建完成后,你可以在 `out/D`下找到 `Electron.app` .

## 32位系统支持

Electron仅支持在64位的macOS中构建.并不也从未计划支持32位系统.

## 清理

清理构建文件:
```bash
$ npm run clean
```

只清理`out`和`dist`目录:

```bash
$ npm run clean-build
```

**注意：**以上两个clean命令都需要在构建之前再次运行 `bootstrap`。

## 相关测试

参见[构建系统概述:测试](build-system-overview.md#tests)            