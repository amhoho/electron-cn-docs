# 键盘快捷键

> 配置本地和全局键盘快捷键

## 本地快捷键

您可以使用[Menu]模块来配置仅在应用程序集中时才触发的键盘快捷键。为此，在创建[MenuItem]时务必指定[`accelerator]属性。

```js
const {Menu, MenuItem} = require('electron')
const menu = new Menu()

menu.append(new MenuItem({
  label: 'Print',
  accelerator: 'CmdOrCtrl+P',
  click: () => { console.log('打印时间') }
}))
```

可以根据用户的操作系统轻松配置不同的组合键。

```js
{
  accelerator: process.platform === 'darwin' ? 'Alt+Cmd+I' : 'Ctrl+Shift+I'
}
```

## 全局快捷键

即使应用程序没有键盘焦点，也可以使用[globalShortcut]模块检测键盘事件。

```js
const {app, globalShortcut} = require('electron')

app.on('ready', () => {
  globalShortcut.register('CommandOrControl+X', () => {
    console.log('CommandOrControl+X is pressed')
  })
})
```

## BrowserWindow中的快捷键

如果要处理[BrowserWindow]的键盘快捷键，可以在渲染器进程中的窗口对象上使用 `keyup` 和 `keydown` 事件侦听器。
```js
window.addEventListener('keyup', doSomething, true)
```
注意第三个参数 `true`，这意味着监听器总是在其他监听器之前接收按键，所以它们不能对它们调用 `stopPropagation()`。

如果您不想手动进行快捷方式解析，那么有进行自动检测的库，如[mousetrap].
```js
Mousetrap.bind('4', () => { console.log('4') })
Mousetrap.bind('?', () => { console.log('show shortcuts!') })
Mousetrap.bind('esc', () => { console.log('escape') }, 'keyup')

// 组合
Mousetrap.bind('command+shift+k', () => { console.log('command shift k') })

// 将多个组合映射到相同的回调
Mousetrap.bind(['command+k', 'ctrl+k'], () => {
  console.log('command k or control k')

  // 返回false以防止默认行为，并阻止事件冒泡
  return false
})

//  gmail风格序列
Mousetrap.bind('g i', () => { console.log('go to inbox') })
Mousetrap.bind('* a', () => { console.log('select all') })

// konami代码
Mousetrap.bind('up up down down left right left right b a enter', () => {
  console.log('konami code')
})
```

[Menu]: ../api/menu.md
[MenuItem]: ../api/menu-item.md
[globalShortcut]: ../api/global-shortcut.md
[`accelerator`]: ../api/accelerator.md
[BrowserWindow]: ../api/browser-window.md
[mousetrap]: https://github.com/ccampbell/mousetrap
