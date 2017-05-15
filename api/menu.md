## 本文介绍:菜单

> 创建应用程序菜单和上下文菜单

进程: [主进程](../glossary.md#主进程)    

### `new Menu()`
> 用途:**创建新菜单**

## 静态方法

#### `Menu.setApplicationMenu(menu)`
> 用途:**设置应用菜单(在macOS中为应用菜单,Windows 和 Linux中为窗口顶部菜单)**

* `menu` Menu

 **注意:** 这个API必须置于 `app` 模块的 `ready`之后.

#### `Menu.getApplicationMenu()`
> 用途:**获取应用菜单**

 如果应用没有菜单的话,返回 `null`.有则返回菜单 `Menu` ,请注意,该返回菜单 `Menu`实例不支持动态添加或删除菜单项.但 [实例属性](#实例属性) 还是仍然支持动态修改.   

#### `Menu.sendActionToFirstResponder(action)` _macOS_
> 用途:**模拟 Cocoa 菜单的默认行为,发送 `action` 给应用的第一个响应器**

* `action` String

通常你只需使用 [`MenuItem`](menu-item.md) 的属性[`role`](menu-item.md#roles)模拟macOS默认的菜单行为.

更多macOS原生action,请参阅[macOS Cocoa事件处理指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/EventArchitecture/EventArchitecture.html#//apple_ref/doc/uid/10000060i-CH3-SW7)            

#### `Menu.buildFromTemplate(template)`
> 用途:**从 `template`中创建菜单**

* `template` MenuItemConstructorOptions[]

返回 `Menu`,一般来说, `template` 只是用来构造 [MenuItem](menu-item.md) 的数组 `options` .你也可以向 `template` 元素添加其它东西,使它们该菜单项的属性.

##实例方法

####  `menu.popup([browserWindow, options])`
> 用途:**在指定位置( `x, y`)弹出菜单**

* `browserWindow` BrowserWindow (可选) -  默认为聚焦窗口.
*  `options` Object(可选) 
  * `x` Number (可选) - 当前光标的 `x` ,默认为 -1.
  * `y` Number (如果存在 `x`,则该项必须) -当前光标的 `y`.
  * `async` Boolean (可选) - 同异步设置, `true`为同步即立即返回调用, `false`则是选菜单被择或关闭等操作后返回, 默认为 `false`.
  * `positioningItem` Number (可选) _macOS_ - 在指定坐标鼠标位置下面的菜单项的索引. 默认为 `-1`.弹出此菜单作为`browserWindow`中的上下文菜单.
  
#### `menu.closePopup([browserWindow])`
> 用途:**关闭 `browserWindow`中打开的上下文菜单**

* `browserWindow` BrowserWindow (可选) -  默认为聚焦窗口.

#### `menu.append(menuItem)`
> 用途:**将 `menuItem`附加到菜单**

* `menuItem` MenuItem

#### `menu.insert(pos, menuItem)`
> 用途:**将 `menuItem`插入到菜单的 `pos`位置**

* `pos` Integer
* `menuItem` MenuItem

### 实例属性

#### `menu.items`
一个包含菜单项的 `MenuItem[]`数组。每个`Menu`由多个[`MenuItem`](menu-item.md)组成,每个 `MenuItem`可以有一个子菜单。

## 例子
`Menu`类仅在主进程中可用,但您也可以通过[`remote`](remote.md)模块在渲染过程中使用它。

### 主进程
在主进程中创建应用程序菜单的示例:

```JavaScript
const {app, Menu} = require('electron')

const template = [
  {
    label: 'Edit',
    submenu: [
      {
        role: 'undo'
      },
      {
        role: 'redo'
      },
      {
        type: 'separator'
      },
      {
        role: 'cut'
      },
      {
        role: 'copy'
      },
      {
        role: 'paste'
      },
      {
        role: 'pasteandmatchstyle'
      },
      {
        role: 'delete'
      },
      {
        role: 'selectall'
      }
    ]
  },
  {
    label: 'View',
    submenu: [
      {
        role: 'reload'
      },
      {
        role: 'forcereload'
      },
      {
        role: 'toggledevtools'
      },
      {
        type: 'separator'
      },
      {
        role: 'resetzoom'
      },
      {
        role: 'zoomin'
      },
      {
        role: 'zoomout'
      },
      {
        type: 'separator'
      },
      {
        role: 'togglefullscreen'
      }
    ]
  },
  {
    role: 'window',
    submenu: [
      {
        role: 'minimize'
      },
      {
        role: 'close'
      }
    ]
  },
  {
    role: 'help',
    submenu: [
      {
        label: 'Learn More',
        click () { require('electron').shell.openExternal('http://electron.atom.io') }
      }
    ]
  }
]

if (process.platform === 'darwin') {
  template.unshift({
    label: app.getName(),
    submenu: [
      {
        role: 'about'
      },
      {
        type: 'separator'
      },
      {
        role: 'services',
        submenu: []
      },
      {
        type: 'separator'
      },
      {
        role: 'hide'
      },
      {
        role: 'hideothers'
      },
      {
        role: 'unhide'
      },
      {
        type: 'separator'
      },
      {
        role: 'quit'
      }
    ]
  })
  // 编辑菜单
  template[1].submenu.push(
    {
      type: 'separator'
    },
    {
      label: 'Speech',
      submenu: [
        {
          role: 'startspeaking'
        },
        {
          role: 'stopspeaking'
        }
      ]
    }
  )
//窗口菜单
  template[3].submenu = [
    {
      label: 'Close',
      accelerator: 'CmdOrCtrl+W',
      role: 'close'
    },
    {
      label: 'Minimize',
      accelerator: 'CmdOrCtrl+M',
      role: 'minimize'
    },
    {
      label: 'Zoom',
      role: 'zoom'
    },
    {
      type: 'separator'
    },
    {
      label: 'Bring All to Front',
      role: 'front'
    }
  ]
}

const menu = Menu.buildFromTemplate(template)
Menu.setApplicationMenu(menu)
```




### 渲染进程
使用[`remote`](remote.md)模块在渲染进程中动态创建菜单的示例,当用户右击页面时显示:

```html
<!-- index.html -->
<script>
const {remote} = require('electron')
const {Menu, MenuItem} = remote

const menu = new Menu()
menu.append(new MenuItem({label: 'MenuItem1', click() { console.log('item 1 clicked') }}))
menu.append(new MenuItem({type: 'separator'}))
menu.append(new MenuItem({label: 'MenuItem2', type: 'checkbox', checked: true}))

window.addEventListener('contextmenu', (e) => {
  e.preventDefault()
  menu.popup(remote.getCurrentWindow())
}, false)
</script>
```

## macOS应用程序菜单注意事项
相对于windows 和 linux, macOS 上的应用菜单是完全不同的样式,这里是一些注意事项,来让你的菜单项更原生化.

### 标准菜单

在macOS上有很多系统定义的标准菜单，例如 `Services`和 `Windows`菜单。要使菜单成为标准菜单，您设置菜单的 `role` 属性，Electron 将会自动识别,可选值有：

* `window`
* `help`
* `services`

### 标准菜单项操作
macOS为某些菜单项提供了标准操作，例如 `关于xxx`， `隐藏xxx`和 `隐藏其他`。要将菜单项操作设置为标准操作，你需要设置菜单的 `role` 属性。

### 主菜单的名称

在 macOS ,无论你设置的什么标签,应用菜单的第一个菜单项的标签始终未你的应用名字.

想要改变它的话,你必须通过修改应用程序包的 `Info.plist` 文件来修改应用名字.详见[关于信息属性列表文件][AboutInformationPropertyListFiles]                     

## 设置特定浏览器窗口的菜单 (*Linux* *Windows*)
浏览器窗口的[`setMenu` 方法][setMenu] 可以设置某些浏览

## 菜单项位置

当通过 `Menu.buildFromTemplate` 创建菜单的时候,你可以使用 `position` and `id` 来放置菜单项.
 `MenuItem` 的属性  `position` 格式为 `[placement]=[id]`, 
 `placement` 取值为 `before`, `after`, 或 `endof` 三个参数, `id` 是已存在菜单项的唯一 ID:

* `before` - 在指定id菜单项之前插入. 如果指定菜单项不存在,则将其插在菜单末尾.
* `after` - 在指定id菜单项之后插入. 如果指定菜单项不存在,则将其插在菜单末尾.
* `endof` - 在包含指定id菜单项的分组末尾插入. 如果指定菜单项不存在, 则将用id创建新的分组,并在分组中插入菜单项.

当某个菜单项插入成功后,未插入的其它菜单项会跟随插入.所以,如需在同位置插入一组菜单项,只需将这个组第一个菜单项插入指定位置.

### 示例

模板1:
```JavaScript
[
  {label: '4', id: '4'},
  {label: '5', id: '5'},
  {label: '1', id: '1', position: 'before=4'},
  {label: '2', id: '2'},
  {label: '3', id: '3'}
]
```

菜单1:
```
- 1
- 2
- 3
- 4
- 5
```

模板2:
```JavaScript
[
  {label: 'a', position: 'endof=letters'},
  {label: '1', position: 'endof=numbers'},
  {label: 'b', position: 'endof=letters'},
  {label: '2', position: 'endof=numbers'},
  {label: 'c', position: 'endof=letters'},
  {label: '3', position: 'endof=numbers'}
]
```

菜单2:

```
- ---
- a
- b
- c
- ---
- 1
- 2
- 3
```

[AboutInformationPropertyListFiles]: https://developer.apple.com/library/ios/documentation/general/Reference/InfoPlistKeyReference/Articles/AboutInformationPropertyListFiles.html
[setMenu]: browser-window.md#winsetmenumenulinux-windows
