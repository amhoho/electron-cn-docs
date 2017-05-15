# 应用分发
首先,你需要下载Electron的 [prebuilt binaries](https://github.com/electron/electron/releases)进行分发. 

接着,把应用所在目录命名为 `'app'`,并像下面例子一样放到Electron的 `resources`目录(macOS 中是指 `Electron.app/Contents/Resources/`,Linux 和 Windows 中是指 `resources/`)里,下面的例子是把Electron的预构建二进制文件的位置假定为 `electron/`:

macOS中示例:
```text
electron/Electron.app/Contents/Resources/app/
├── package.json
├── main.js
└── index.html
```

Windows 和 Linux中示例:
```text
electron/resources/app
├── package.json
├── main.js
└── index.html
```

最后,运行 `Electron.app` (Linux的 `electron`, Windows的 `electron.exe`), Electron就会作为您的应用进行启动.  `electron` 目录将被部署并可以分发给用户.

## 将应用程序打包成单一文件

除了通过拷贝所有的资源文件来分发你的应用程序之外, 您还可以打包成[asar](https://github.com/electron/asar)归档以避免将源代码直接暴露给用户.

要把 `app`文件夹打包成 `asar`，你要把 `app`重命名为 `app.asar`，并像下面例子一样放到Electron的 `resources`目录，Electron就会试图读取存档并从中启动。

macOS中示例:
```text
electron/Electron.app/Contents/Resources/
└── app.asar
```

Windows 和 Linux中示例:
```text
electron/resources/
└── app.asar
```

更多细节详见[应用程序包](application-packaging.md).        

## 更换名称与下载二进制文件

在使用 Electron 打包你的应用程序之后，你可能需要在分发给用户之前修改打包的名字。

### Windows

你可以重命名 `electron.exe`并使用类似[rcedit](https://github.com/atom/rcedit)之类的软件编辑它的图标和属性信息.

### macOS

在macOS中也可以重命名 `Electron.app`,但同时也要重命名以下两个文件中的 `CFBundleDisplayName`， `CFBundleIdentifier`和 `CFBundleName`字段:

* `Electron.app/Contents/Info.plist`
* `Electron.app/Contents/Frameworks/Electron Helper.app/Contents/Info.plist`

你也可以重命名 `应用帮助` 以避免在应用程序监视器中显示 `Electron Helper`，但是请确保你已经修改了 `应用帮助`的程序名称。

改过名称的应用构造示例：
```
MyApp.app/Contents
├── Info.plist
├── MacOS/
│   └── MyApp
└── Frameworks/
    ├── MyApp Helper EH.app
    |   ├── Info.plist
    |   └── MacOS/
    |       └── MyApp Helper EH
    ├── MyApp Helper NP.app
    |   ├── Info.plist
    |   └── MacOS/
    |       └── MyApp Helper NP
    └── MyApp Helper.app
        ├── Info.plist
        └── MacOS/
            └── MyApp Helper
```

### Linux

您可以将 `electron`重命名为任何名称。

## 打包工具

除了手动打包外, 你也可以使用第三方打包工具:

* [electron-builder](https://github.com/electron-userland/electron-builder)
* [electron-packager](https://github.com/electron-userland/electron-packager)

## 通过重编译源代码来更换名称

你也可以直接修改和重编译 `atom.gyp` 文件,达到彻底更换Electron名称

### grunt-build-atom-shell

手动对Electron的代码修改或重编译一般都很复杂晦涩,所以可以创建一个自动处理的Grunt任务:[grunt-build-atom-shell](https://github.com/paulcbetts/grunt-build-atom-shell).

该任务会从源代码中自动处理 `.gyp` 文件并重编译应用程序的本地 Node模块以匹配这个新名称.

### 创建一个自定义 Electron 分支

如果你只是为了构建应用而已,即便是`生产级`的应用也无需创建分支,你只需使用类似 `electron-packager` 或 `electron-builder` 之类的工具即可重构Electron,而不需要去创建分支.

只有你希望将自定义C++ 代码补丁到Electron并且无法提交至upstream或被已被官方版本拒绝时,你才需要创建分支.

#### 通过 surf-build 创建一个自定义版本

1. 通过npm安装[Surf](https://github.com/surf-build/surf): 
`npm install -g surf-build@latest`

2. 创建一个新的 S3 bucket 并按照以下结构创建文件夹：

```
    - atom-shell/
      - symbols/
      - dist/
```

3. 设置以下环境变量：

  * `ELECTRON_GITHUB_TOKEN` - 可以在GitHub上创建发行版的令牌
  * `ELECTRON_S3_ACCESS_KEY`, `ELECTRON_S3_BUCKET`, `ELECTRON_S3_SECRET_KEY` - 上传 Node.js 的 headers 以及符号的位置
  * `ELECTRON_RELEASE` - `true`即上传部分将被运行但不设置离开, `surf-build`仅进行CI-type的检查, 适合每次运行都拉取请求.
  * `CI` -  必须 `true` ,否则无效.
  * `GITHUB_TOKEN` - 设置与 `ELECTRON_GITHUB_TOKEN`相同
  * `SURF_TEMP` -  在Windows上设置为 `C:\Temp` ,防止路径过长的问题.
  * `TARGET_ARCH` - 设置 `ia32`或 `x64`  

4. 在 `script/upload.py`中,务必设置 `ELECTRON_REPO` 到你的fork(`MYORG/electron`), 尤其是你本身是Electron的贡献者.

5. `surf-build -r https://github.com/MYORG/electron -s YOUR_COMMIT -n 'surf-PLATFORM-ARCH'`

6. 可能需要等待很长时间才能完成构建.
