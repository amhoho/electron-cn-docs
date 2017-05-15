## 类: TouchBarSlider

>在本地MacOS应用程序的触摸栏中创建滑块

进程: [主进程](../tutorial/quick-start.md#主进程)            

### `new TouchBarSlider(options)` _实验功能_
>用途:**创建新滑块**

* `options` Object
  * `label` String (可选) - 标签文本.
  * `value` Integer (可选) - 所选值
  * `minValue` Integer (可选) - 最小值
  * `maxValue` Integer (可选) - 最大值
  * `change` Function (可选) - 滑块改变时调用的函数

### 实例属性

 `TouchBarSlider`有以下属性:

#### `touchBarSlider.label`
>属性:**滑块的当前文本**

#### `touchBarSlider.value`
>属性:**滑块的当前值**

#### `touchBarSlider.minValue`
>属性:**滑块的当前最小值**

#### `touchBarSlider.maxValue`
>属性:**滑块的当前最大值**