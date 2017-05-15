# 在调试器中设置符号服务器
> 调试符号允许您有更好的调试会话sessions

他们有关于可执行文件和动态库中包含的函数的信息，并为您提供获取干净的调用堆栈的信息。

符号服务器允许调试器自动加载正确的符号，二进制文件和源文件，而不强制用户下载大型调试文件。服务器的功能类似[Microsoft的符号服务器](http://support.microsoft.com/kb/311503)            

请注意，因为已发布的Electron版本已大量优化，调试并不总是容易。调试器将无法向您显示所有变量的内容，并且执行路径可能看起来很奇怪，因为内联，尾调用和其他编译器已经过优化。

唯一的解决方法是构建未优化的本地构建。

Electron的官方符号服务器网址为https://electron-symbols.githubapp.com。您无法直接访问此URL，而是必须将其添加到调试工具的符号路径。

在下面的示例中，使用本地缓存目录来避免从服务器重复提取PDB。用机器上合适的缓存目录替换 `c:\code\symbols`

## 在Windbg中使用符号服务器

> Windbg符号路径使用以星号字符分隔的字符串值配置

要仅使用Electron符号服务器，请将以下条目添加到符号路径(**注意:**如果您希望使用其他位置下载符号,您可以用计算机上的任何可写目录替换 `c:\code\symbols`):

```
SRV*c:\code\symbols\*https://electron-symbols.githubapp.com
```

在环境中使用Windbg菜单或通过键入 `.sympath`命令将此字符串设置为 `_NT_SYMBOL_PATH`。

如果你也想从微软的符号服务器获取符号，你应该先进行列出:
```
SRV*c:\code\symbols\*http://msdl.microsoft.com/download/symbols;SRV*c:\code\symbols\*https://electron-symbols.githubapp.com
```

## 在Visual Studio中使用符号服务器

<img src='https://mdn.mozillademos.org/files/733/symbol-server-vc8express-menu.jpg'>
<img src='https://mdn.mozillademos.org/files/2497/2005_options.gif'>

## 故障排除:符号将无法加载

在Windbg中键入以下命令以输出为什么符号未加载:
```
> !sym noisy
> .reload /f electron.exe
```
