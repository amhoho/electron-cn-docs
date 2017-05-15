## 类: TouchBarPopover

> 在本地MacOS应用程序的触摸栏中创建一个Popover弹出框

进程: [主进程](../tutorial/quick-start.md#主进程)            

### `new TouchBarPopover(options)` _实验功能_
>用途:**创建新Popover弹出框**

* `options` Object
  * `label` String (可选) - Popover按钮文本
  * `icon` [NativeImage](native-image.md) (可选) - Popover按钮图标
  * `items` [TouchBar](touch-bar.md) (可选) - 在Popover弹出框中显示的项目
  * `showCloseButton` Boolean (可选) - 是否在左侧显示关闭文本,默认为 `false`.

### 实例属性
 `TouchBarPopover`有以下属性:
 
#### `touchBarPopover.label`
>属性:**Popover的当前按钮文本**

#### `touchBarPopover.icon`
>属性:**Popover的当前按钮图标**
