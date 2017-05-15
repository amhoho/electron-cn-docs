# 在 macOS 中调试

如果你在Electron中遇到崩溃或问题并且你认为它不是由你的JavaScript应用程序引起的,而是由Electron本身引起的,调试起来可能有点棘手,特别是对于不习惯native / C ++调试的开发人员.

然而,使用lldb和Electron源代码,在Electron的源代码中使用断点启用逐步调试是相当容易的.

## 要求

* **Electron 的调试版本**: 最简单的方法是使用[macOS的构建说明](build-instructions-osx.md)中列出的工具和先决条件.

因为Electron本身经过大量优化,这使调试变得更加困难:因为内联,尾部调用和其他编译器优化,调试器将无法显示所有变量的内容和执行路径可以看起来很奇怪.

* **Xcode**: 除了Xcode,还要安装Xcode命令行工具.
它们包括LLDB,在Mac OS X上的Xcode中的默认调试器.它支持在桌面和iOS设备和模拟器上调试C,Objective-C和C ++.

## 附加并调试 Electron

要启动调试会话,打开Terminal并启动`lldb`,传递一个调试版本的Electron作为参数.
```bash
$ lldb ./out/D/Electron.app
(lldb) target create "./out/D/Electron.app"
Current executable set to './out/D/Electron.app' (x86_64).
```

### 设置断点

LLDB是一个强大的工具,支持进行多种策略的代码检查. 在这做一个基本的介绍,让我们假设你从 JavaScript 调用一个不正常的命令 - 所以你想打断该命令的 C++ 对应的 Electron 源.

相关的代码文件可以在 `./atom/` 以及 Brightray 中找到, 找到 `./brightray/browser` 和 `./brightray/common`. 如果是内核,你也可以直接调试 Chromium,这显然在 `chromium_src` 中.

让我们假设你想调试 `app.setName()`, 在 `browser.cc` 中定义为 `Browser::SetName()`. 使用 `breakpoint` 命令进行断点,指定文件和断点位置:
```bash
(lldb) breakpoint set --file browser.cc --line 117
Breakpoint 1: where = Electron Framework`atom::Browser::SetName(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) + 20 at browser.cc:118, address = 0x000000000015fdb4
```

然后, 启动 Electron:

```bash
(lldb) run
```

应用程式会立即暂停,因为 Electron 会在启动时设定应用程序名称:

```bash
(lldb) run
Process 25244 launched: '/Users/fr/Code/electron/out/D/Electron.app/Contents/MacOS/Electron' (x86_64)
Process 25244 stopped
* thread #1: tid = 0x839a4c, 0x0000000100162db4 Electron Framework`atom::Browser::SetName(this=0x0000000108b14f20, name="Electron") + 20 at browser.cc:118, queue = 'com.apple.main-thread', stop reason = breakpoint 1.1
    frame #0: 0x0000000100162db4 Electron Framework`atom::Browser::SetName(this=0x0000000108b14f20, name="Electron") + 20 at browser.cc:118
   115 	}
   116
   117 	void Browser::SetName(const std::string& name) {
-> 118 	  name_override_ = name;
   119 	}
   120
   121 	int Browser::GetBadgeCount() {
(lldb)
```

显示当前帧的参数和局部变量, 运行 `frame variable` (或 `fr v`), 这将显示你的应用程序当前设置名称为 `Electron`.
```bash
(lldb) frame variable
(atom::Browser *) this = 0x0000000108b14f20
(const string &) name = "Electron": {
    [...]
}
```

在当前选择的线程中执行源级单步执行, 执行 `step` (或 `s`). 这将带你进入 `name_override_.empty()`. 继续前进,步过,运行 `next` (或 `n`).
```bash
(lldb) step
Process 25244 stopped
* thread #1: tid = 0x839a4c, 0x0000000100162dcc Electron Framework`atom::Browser::SetName(this=0x0000000108b14f20, name="Electron") + 44 at browser.cc:119, queue = 'com.apple.main-thread', stop reason = step in
    frame #0: 0x0000000100162dcc Electron Framework`atom::Browser::SetName(this=0x0000000108b14f20, name="Electron") + 44 at browser.cc:119
   116
   117 	void Browser::SetName(const std::string& name) {
   118 	  name_override_ = name;
-> 119 	}
   120
   121 	int Browser::GetBadgeCount() {
   122 	  return badge_count_;
```

要完成此时的调试,运行 `process continue`. 你也可以继续,直到这个线程中的某一行被命中( `线程直到100`). 此命令将在当前帧中运行线程,直到它到达此帧中的行100,或者如果它离开当前帧,则停止.

现在,如果你打开 Electron 的开发工具并调用 `setName`,你将再次命中断点.

### 进一步阅读
LLDB是一个有着丰富文档的强大的工具,请参考 Apple 的调试文档,了解更多信息 例如 [LLDB命令结构参考][lldb-command-structure] 或 [使用LLDB作为独立调试器][lldb-standalone].

你也可以查看LLDB的 [手册和教程][lldb-tutorial], 它将解释更复杂的调试场景.

[lldb-command-structure]: https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/lldb-basics.html#//apple_ref/doc/uid/TP40012917-CH2-SW2
[lldb-standalone]: https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/lldb-terminal-workflow-tutorial.html
[lldb-tutorial]: http://lldb.llvm.org/tutorial.html
