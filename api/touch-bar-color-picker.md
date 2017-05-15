## 类: TouchBarColorPicker

>在本地MacOS应用程序的触摸条中创建颜色选择器

进程: [主进程](../tutorial/quick-start.md#主进程)            

### `new TouchBarColorPicker(options)` _实验功能_
>用途:**创建颜色选择器**

* `options` Object
  * `availableColors` String[] (可选) - 十六进制格式的可选颜色组成的字符串数组
  * `selectedColor` String (可选) - 选中项的十六进制格式颜色 如`#ABCDEF`.
  * `change` Function (可选) - 选择颜色时调用
  
### 实例属性
`TouchBarColorPicker`有以下属性:

#### `touchBarColorPicker.availableColors`
>属性:**颜色选择器的可选颜色**

#### `touchBarColorPicker.selectedColor`
>属性:**颜色选择器当前选择的颜色**