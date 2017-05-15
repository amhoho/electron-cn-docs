## 本文介绍: MenuItem类(菜单项的创建与修改)

> 在程序菜单或上下文菜单中插入新的菜单项

进程: [主进程](../glossary.md#main-process)    

您可以在 [`Menu`](menu.md)末尾查看示例.  

### `new MenuItem(options)`

* `options` Object
  * `click` Function (可选) - 单击菜单项被调用 `click(menuItem,browserWindow)` 
     * `menuItem` MenuItem
     * `browserWindow` BrowserWindow
     * `event` Event
  * `role` String (可选) - 定义菜单项操作,指定为 `click` 属性时该项将会被忽略. 详见[任务章节](#roles).
  * `type` String (可选) - 可选`normal`, `separator`, `submenu`, `checkbox` 或 `radio`.
  * `label` String - (可选)
  * `sublabel` String - (可选)
  * `accelerator` [Accelerator](accelerator.md) (可选)
  * `icon` ([NativeImage](native-image.md) | String) (可选)
  * `enabled` Boolean (可选) - `false`表示菜单项显示为不可点击的灰色
  * `visible` Boolean (可选) - `false`表示菜单项完全隐藏。
  * `checked` Boolean (可选) - 仅 `checkbox`或 `radio`类型菜单项才需要指定。
  * `submenu` (MenuItemConstructorOptions[] | Menu) (可选) - 应为 `submenu` 类型菜单项而指定,如果指定了 `submenu`， 则 `type：'submenu'`可以省略。如果它的值不是 `Menu`,将自动转为 `Menu.buildFromTemplate`。
  * `id` String - 菜单的唯一id。如果id已被使用,它将被用作这个菜单项的参考位置 `position` 属性。
  * `position` String - 定义菜单的具体位置信息。
  
### Roles任务

> `roles` 可使菜单项具有预定义行为.
 
在创建菜单项时,如果有匹配的方法,建议直接指定 `role` 属性而不是尝试在 `click`函数中手动实现该行为,这样可以给用户最好的使用体验。

当使用 `role`时, `label`和 `accelerator` 的值是可选的,默认将根据平台使用适当的值。

 `role`属性值可以为:

* `undo`
* `redo`
* `cut`
* `copy`
* `paste`
* `pasteandmatchstyle`
* `selectall`
* `delete`
* `minimize` -  最小化当前窗口
* `close` - 关闭当前窗口
* `quit`- 退出应用
* `reload` -重新加载当前窗口
* `forcereload`  - 重新加载当前窗口并忽略缓存。
* `toggledevtools`  - 在当前窗口中切换开发人员工具
* `togglefullscreen`  - 在当前窗口切换全屏模式
* `resetzoom`  - 聚焦页缩放级别重置为原始大小
* `zoomin`  - 聚焦页放大10％
* `zoomout`  - 聚焦页缩小10％
* `editMenu` - 默认的 `编辑`菜单 (撤销, 复制等等)
* `windowMenu` - 默认的 `窗口`菜单 (最小化, 关闭等等)

在 macOS 上也可使用下列 `role` :

* `about`  - 映射到 `orderFrontStandardAboutPanel`动作
* `hide`  - 映射到 `hide`动作
* `hideothers`  - 映射到 `hideOtherApplications`动作
* `unhide`  - 映射到 `unhideAllApplications`动作
* `startspeaking`  - 映射到 `startSpeaking`动作
* `stoppeaking`  - 映射到 `stopSpeaking`动作
* `front`  - 映射到 `arrangeInFront`动作
* `zoom`  - 映射到 `performZoom`动作
* `window`  - 子菜单是一个 `Window`菜单
* `help`  - 子菜单是一个 `帮助`菜单
* `services`  - 子菜单是一个 `服务`菜单

当在macOS上指定 `role`时, `label` 和 `accelerator`是唯一会影响MenuItem的选项。其他选项将被忽略。

### 实例属性

#### `menuItem.enabled`
> 属性:**是否启用该项**

此属性可以动态更改。

#### `menuItem.visible`
> 属性:**是否可见该项**

此属性可以动态更改。

#### `menuItem.checked`
> 属性:**该项是否已选中**

 `checkbox` 菜单项将在选中时打开或关闭 `checked`属性
 `radio`菜单项将在点击时打开其`checked`属性,并将关闭同一菜单中所有相邻项的该属性。
 
如果需要其他行为,您可以直接添加一个 `click`函数。

此属性可以动态更改。

#### `menuItem.label`
> 属性:**菜单项内容字符串**

#### `menuItem.click`
> 属性:**点击菜单项时需要触发的函数**