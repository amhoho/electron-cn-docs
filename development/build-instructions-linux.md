# 构建说明(Linux)

> 在Linux中,构建Electron

## 前提要求

* 磁盘可用空间>=25GB
* 内存>=8GB
* Python>=2.7.x以上版本,比如 CentOS 6.x 是基于Python 2.6.x.你可以使用 `python -V`命令自查版本.
* Node.js. 你可以在[Node.js](http://nodejs.org)或者[NodeSource](https://nodesource.com/blog/nodejs-v012-iojs-and-the-nodesource-linux-repositories)下载Node.js.
* Clang>=3.4以上版本
* GTK+开发头文件和libnotify.

> Ubuntu:**安装以下库**

```bash
$ sudo apt-get install build-essential clang libdbus-1-dev libgtk2.0-dev \
                       libnotify-dev libgnome-keyring-dev libgconf2-dev \
                       libasound2-dev libcap-dev libcups2-dev libxtst-dev \
                       libxss1 libnss3-dev gcc-multilib g++-multilib curl \
                       gperf bison
```

> RHEL/CentOS:**安装以下库**

```bash
$ sudo yum install clang dbus-devel gtk2-devel libnotify-devel \
                   libgnome-keyring-devel xorg-x11-server-utils libcap-devel \
                   cups-devel libXtst-devel alsa-lib-devel libXrandr-devel \
                   GConf2-devel nss-devel
```

> Fedora:**安装以下库**

```bash
$ sudo dnf install clang dbus-devel gtk2-devel libnotify-devel \
                   libgnome-keyring-devel xorg-x11-server-utils libcap-devel \
                   cups-devel libXtst-devel alsa-lib-devel libXrandr-devel \
                   GConf2-devel nss-devel
```

其他发行版可利用pacman之类的软件包管理器安装,也或者可以从源代码编译.

## 获取代码
```bash
$ git clone https://github.com/electron/electron.git
```

## 引导

引导脚本将下载所有必需的构建依赖项并创建构建项目文件,某些文件可能需要花费很长的时间.

注意,必须有Python 2.7.x才能成功,由于使用了 `ninja`进行构建,因此不存在 `Makefile`项目.

```bash
$ cd electron
$ ./script/bootstrap.py -v
```

### 交叉编译

如果你想要构建一个 `arm` target,你还应该安装以下依赖:
```bash
$ sudo apt-get install libc6-dev-armhf-cross linux-libc-dev-armhf-cross \
                       g++-arm-linux-gnueabihf
```

要交叉编译 `arm`或 `ia32` target,你应该将 `--target_arch`参数传递给 `bootstrap.py`脚本:
```bash
$ ./script/bootstrap.py -v --target_arch=arm
```

## 构建

创建 `Release`和 `Debug` target:
```bash
$ ./script/build.py
```

这个脚本将在 `out/R`目录下生成一个大小超过1.3GB的Electron可执行文件.

之所以这么大,是因为Release target包含了调试符号.运行 `create-dist.py`脚本可减小文件大小:

```bash
$ ./script/create-dist.py
```

这将在 `dist`目录里创建一个大量小文件的工作分配.运行create-dist.py脚本后,或许你想删除仍然在 `out/R`中的那个超过1.3GB的文件.

还可以仅构建 `Debug` target :

```bash
$ ./script/build.py -c D
```

在构建完成后,你可以在 `out/D`下找到 `electron` debug二进制文件.

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

## 故障排除

### 加载共享库时出错:libtinfo.so.5

预构建的 `clang`会尝试链接到 `libtinfo.so.5`,并根据主机体系结构进行链接到适当的 `libncurses`:
```bash
$ sudo ln -s /usr/lib/libncurses.so.5 /usr/lib/libtinfo.so.5
```

## 相关测试

参见[构建系统概述:测试](build-system-overview.md#tests)                  

## 延伸问题

默认的构建配置是针对主流的Linux桌面发行版,至于特定发行版或设备进行构建,你可以了解以下信息:

### 本地构建 `libchromiumcontent`

为了避免使用 `libchromiumcontent`的预编译二进制文件,你可以将 `--build_libchromiumcontent`切换到 `bootstrap.py`脚本:
```bash
$ ./script/bootstrap.py -v --build_libchromiumcontent
```

默认情况下不会以 `shared_library` 方式进行构建, 所以你如果使用以下模式的话, 只能构建 Electron的 `Release` 版本:
```bash
$ ./script/build.py -c R
```

### 使用系统`clang`

默认情况下 Electron 使用 Chromium 项目提供的预编译的 `clang` 进行编译. 

如果基于某些原因,你想要使用已经安装到系统的 `clang` 进行编译, 可以添加 `--clang_dir=<path>` 参数给 `bootstrap.py` 以指定 `clang` 安装路径. 

上面参数告诉编译脚本, 在目录 `<path>/bin/` 下有 `clang` 程序.

假设你的 `clang` 安装路径为 `/user/local/bin/clang`:
```bash
$ ./script/bootstrap.py -v --build_libchromiumcontent --clang_dir /usr/local
$ ./script/build.py -c R
```

### 使用 `clang` 之外的其它编译器

要使用 `g++`这样的编译器来构建Electron,先使用 `--disable_clang` 参数禁用 `clang`,然后将 `CC`和 `CXX`环境变量设置为你想要的.

例如使用 GCC工具链 构建:
```bash
$ env CC=gcc CXX=g++ ./script/bootstrap.py -v --build_libchromiumcontent --disable_clang
$ ./script/build.py -c R
```

### 环境变量

除了 `CC` 及 `CXX`, 您还可以设置以下环境变量来自定义构建配置:

* `CPPFLAGS`
* `CPPFLAGS_host`
* `CFLAGS`
* `CFLAGS_host`
* `CXXFLAGS`
* `CXXFLAGS_host`
* `AR`
* `AR_host`
* `CC`
* `CC_host`
* `CXX`
* `CXX_host`
* `LDFLAGS`

以上环境变量需要在执行 `bootstrap.py` 前设置, 在执行 `build.py` 的时候再设置将无效.