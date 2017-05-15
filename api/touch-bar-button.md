## 类:TouchBarButton

>在本地MacOS应用程序的触摸栏中创建一个按钮

进程: [主进程](../tutorial/quick-start.md#主进程)            

### `new TouchBarButton(options)` _实验功能_
>用途:**创建新按钮**

* `options` Object
  * `label` String (可选) - 按钮文本
  * `backgroundColor` String (可选) - 十六进制的按钮背景颜色如 `＃ABCDEF`
  * `icon` [NativeImage](native-image.md) (可选) - 按钮图标
  * `iconPosition` String - 可选 `left`, `right` 或 `overlay`.
  * `click` Function (可选) - 单击按钮时调用

### 实例属性

`TouchBarButton`实例有以下属性:

#### `touchBarButton.label`
>属性:**按钮的当前文本**

#### `touchBarButton.backgroundColor`
>属性:**按钮的当前背景颜色**

#### `touchBarButton.icon`
>属性:**按钮的当前图标**