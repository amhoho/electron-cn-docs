# 本文介绍:全局快捷键
> 应用于键盘无焦点时的键盘快捷键.

进程: [主进程](../glossary.md#主进程)    

 `global-shortcut`模块可以便捷的设置(注册/注销)各种自定义操作的快捷键. 
 
 **注意:** 这种快捷键是全局性的,即键盘没有聚焦于应用时,本模块需应用于在 `ready`后.
 
```JavaScript
const {app, globalShortcut} = require('electron')

app.on('ready', () => {
//注册一个“CommandOrControl + X”快捷方式监听
  const ret = globalShortcut.register('CommandOrControl+X', () => {
    console.log('用户按下了 CommandOrControl+X')
  })

  if (!ret) {console.log('快捷键注册失败')}

  // 检查快捷方式是否已注册。
  console.log(globalShortcut.isRegistered('CommandOrControl+X'))
})

app.on('will-quit', () => {
//取消已注册的一个快捷键。
  globalShortcut.unregister('CommandOrControl+X')

//注销应用注册的所有快捷键
  globalShortcut.unregisterAll()
})
```

## 事件方法

### `globalShortcut.register(accelerator, callback)`
> 用途:**注册快捷键 `accelerator`**

* `accelerator` [Accelerator](accelerator.md)
* `callback` Function 按下快捷键后的操作

如果快捷键已经被其他应用程序注册了,这个 `callback`将静默失败。

### `globalShortcut.isRegistered(accelerator)`
> 用途:**判断快捷键 `accelerator`是否已经被注册**

* `accelerator` [Accelerator](accelerator.md)

### `globalShortcut.unregister(accelerator)`
> 用途:**注销快捷键 `accelerator`**

* `accelerator` [Accelerator](accelerator.md)

### `globalShortcut.unregisterAll()`
> 用途:**注销应用注册的所有快捷键**