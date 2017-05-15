# 支持的平台

目前 Electron 支持以下平台：

### macOS

* macOS系统版本>=10.9
* 仅提供64位版本的Electron.

### Windows

* Windows系统版本>=7
* 提供 `ia32` (x86) 和 `amd64` (x64) 版本的Electron.

需要注意的是 `ARM` 版本的 Windows 目前尚不支持。

### Linux

* Ubuntu系统版本>=12.04,Fedora系统版本>=21,Debian系统版本>=8
* 提供 `ia32` (x86) 和 `amd64` (x64) 版本的Electron.

预编译版本是否能够正常运行，取决于其中是否包含了编译平台的链接库。

Linux中的Electron  `ia32` (`i686`) 和 `x64` (`amd64`) 预编译版本均是在Ubuntu 12.04 下编译的， `arm` 版的二进制文件是在 ARM v7（硬浮点 ABI 与
Debian Wheezy 版本的 NEON）下完成的。

所以只有 Ubuntu 12.04 是可以保证能正常运行的，并且以上平台已被证实可正常运行 Electron 的预编译版本.