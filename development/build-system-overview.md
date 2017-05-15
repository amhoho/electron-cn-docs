# 构建系统概述

Electron使用[gyp](https://gyp.gsrc.io/)进行项目生成，使用[ninja](https://ninja-build.org/)进行构建。项目配置可以在 `.gyp`和 `.gypi`文件中找到。

## Gyp文件

下面的 `gyp`文件包含了构建Electron的主要规则:

* `electron.gyp`定义了Electron本身是如何构建的。
* `common.gypi`调整Node的构建配置，使其与Chromium一起构建。
* `brightray/brightray.gyp`定义`brightray`是如何构建的，并包含与Chromium关联的默认配置。
* `brightray/brightray.gypi`包括关于构建的一般构建配置。

## 组件构建

由于Chromium是一个相当大的项目，最终的连接阶段可能需要几分钟，这使得开发变得困难。

为了解决这个问题，Chromium引入了 `组件构建`，它将每个组件构建为一个单独的共享库，使得链接非常快，但牺牲了文件大小和性能。

在Electron中，我们采用了一个非常相似的方法:

对于 `Debug`构建，二进制将链接到共享库版本的Chromium组件，以实现快速链接时间;

对于 `Release`构建，二进制将链接到静态库版本，所以我们可以有最好的二进制大小和性能。

##最小引导

运行引导脚本时，会下载所有Chromium的预构建二进制文件( `libchromiumcontent`)。默认情况下，将下载静态库和共享库，最终大小应在800MB和2GB之间，具体取决于平台。

默认情况下， `libchromiumcontent`是从Amazon Web Services下载的。

如果设置了 `LIBCHROMIUMCONTENT_MIRROR`环境变量，引导脚本将从中下载。

 [`libchromiumcontent-qiniu-mirror`](https://github.com/hokein/libchromiumcontent-qiniu-mirror)是 `libchromiumcontent`的镜像。
 
 如果您在访问AWS时遇到问题，可以通过 `export LIBCHROMIUMCONTENT_MIRROR = http:// 7xk3d2.dl1.z0.glb.clouddn.com /`

如果你只想快速构建Electron来进行测试或开发，可以通过传递 `--dev`参数来下载共享库版本:

```bash
$ ./script/bootstrap.py --dev
$ ./script/build.py -c D
```

## 双相(Two-Phase)项目生成

在 `Release`和 `Debug`中,Electron 链接了不同配置的库。 `gyp`不支持为不同的配置配置不同的链接设置。

要解决这个问题，Electron使用 `gyp`的变量 `libchromiumcontent_component`来控制要使用的链接设置，并在运行 `gyp`时只生成一个目标。

## Target名称

与大多数项目使用 `Release`和 `Debug`作为 target名称不同，Electron使用 `R`和 `D`。

这是因为 `gyp`随机崩溃，如果只有一个 `Release`或 `Debug`构建配置被定义，Electron只需要一次生成一个目标，如上所述。

这只会影响开发人员，如果你重新构建了 Electron则不受影响。

## 相关测试

使用以下方法测试您的更改符合项目编码规范:

```bash
$ npm run lint
```

测试功能使用:
```bash
$ npm test
```

每当您对Electron源代码进行更改时，您都需要在测试之前重新运行构建:
```bash
$ npm run build && npm test
```

您可以通过使用Mocha的[独家测试](https://mochajs.org/#exclusive-tests)功能隔离您当前正在使用的特定测试或块，使测试套件运行更快。

只需将 `.only`附加到任何 `describe`或 `it`函数时调用:
```js
describe.only('some feature'，function(){
  // ...只有在这个块中的测试才会运行
})
```

或者，您可以使用mocha的 `grep`选项仅运行与给定的正则表达式模式匹配的测试:
```sh
$ npm test - --grep child_process
```

包含本地模块(例如`runas`)的测试不能使用调试版本执行(有关详细信息，请参阅[＃2558](https://github.com/electron/electron/issues/2558))，使用发布版本。

要使用发布版本运行测试:
```bash
$ npm test - -R
```