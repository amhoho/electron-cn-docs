# 构建说明(Windows)

> 在Windows中,构建Electron

## 前提要求

* Windows 7/Server 2008 R2或者更高的版本 
* Visual Studio 2015 Update 3 - [免费下载VS 2015社区版](https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx)               
* [Python 2.7](http://www.python.org/download/releases/2.7/) (已实践证明的一点的是该版本的Python安装后重启电脑才可使用!)
* [Node.js](http://nodejs.org/download/)
* [Git](http://git-scm.com)
* [Windows调试工具](https://msdn.microsoft.com/en-us/library/windows/hardware/ff551063.aspx)         

假如需要创建一个完整发行版,则使用 `symstore.exe`在`.pdb`文件中新建符号存储.
  
如果你现在还没有安装 Windows , [modern.ie](https://www.modern.ie/en-us/virtualization-tools#downloads) 有一个 timebombed 版本的 Windows ，你可以用它来构建 Electron.

Building Electron完全使用命令行脚本完成，您可以使用任何编辑器开发Electron,但不能使用Visual Studio完成(将来可能支持)。

**注意：**即使Visual Studio不用于构建，但它仍然是**必须的**，因为我们需要它提供的构建工具链。

## 获取代码

```powershell
$ git clone https://github.com/electron/electron.git
```

## 引导

引导脚本将下载所有必需的构建依赖项并创建构建项目文件,由于使用了 `ninja`进行构建,因此不存在 `Visual Studio`项目.
```powershell
$ cd electron
$ python script\bootstrap.py -v
```

## 构建

创建 `Release`和 `Debug` target:
```powershell
$ python script\build.py
```

还可以仅构建 `Debug` target :
```powershell
$ python script\build.py -c D
```

在构建完成后,你可以在 `out\D` (debug target) 或者 `out\R` (release target)下找到 `Electron.exe` .

## 32位系统支持

要构建32位目标，需要在运行引导脚本时传递 `--target_arch=ia32`参数:
```powershell
$ python script\bootstrap.py -v --target_arch=ia32
```

## Visual Studio项目

要生成Visual Studio项目,可以传递 `--msvs`参数:
```powershell
$ python script\bootstrap.py --msvs
```

## 清理

清理构建文件:
```powershell
$ npm run clean
```

只清理`out`和`dist`目录:

```bash
$ npm run clean-build
```

**注意：**以上两个clean命令都需要在构建之前再次运行 `bootstrap`。

## 相关测试

参见[构建系统概述:测试](build-system-overview.md#tests)                  

## 故障排除

### 未找到命令xxxx

遇到如 `Command xxxx not found`之类错误, 你可以尝试使用 `VS2015命令提示`控制台来执行构建脚本

### 致命内部编译器错误:C1001

确保您已安装最新的Visual Studio

### Assertion failed: ((handle))->activecnt >= 0

如果在 Cygwin 下构建的，你可能会看到 `bootstrap.py` 失败并且附带下面错误 :
```
Assertion failed: ((handle))->activecnt >= 0, file src\win\pipe.c, line 1430

Traceback (most recent call last):
  File "script/bootstrap.py", line 87, in <module>
    sys.exit(main())
  File "script/bootstrap.py", line 22, in main
    update_node_modules('.')
  File "script/bootstrap.py", line 56, in update_node_modules
    execute([NPM, 'install'])
  File "/home/zcbenz/codes/raven/script/lib/util.py", line 118, in execute
    raise e
subprocess.CalledProcessError: Command '['npm.cmd', 'install']' returned non-zero exit status 3
```

这是由同时使用 Cygwin Python 和 Win32 Node 造成的 bug.解决办法就是使用 Win32 Python 执行 引导脚本 (假设你已经在目录 `C:\Python27` 下安装了 Python):

```powershell
$ /cygdrive/c/Python27/python.exe script/bootstrap.py
```

### LNK1181: cannot open input file 'kernel32.lib'

重新安装 32位的 Node.js.

### Error: ENOENT, stat 'C:\Users\USERNAME\AppData\Roaming\npm'

创建一个一样的目录 ,[应该可以解决问题](http://stackoverflow.com/a/25095327/102704).                  

```powershell
$ mkdir ~\AppData\Roaming\npm
```

### node-gyp is not recognized as an internal or external command

如果你使用 Git Bash 来构建，或许会遇到这个错误，可以使用 PowerShell 或 VS2015 Command Prompt 来代替 .
