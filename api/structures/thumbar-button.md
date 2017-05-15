# ThumbarButton Object(缩略图工具栏按钮对象)

* `icon` [NativeImage](../native-image.md)- 缩略图工具栏中显示的icon图标。
* `click`Function
* `tooltip` String(可选) - 按钮的提示文本。
* `flags` String [](可选) - 控制按钮的特定状态和行为。默认情况下,它是`['enabled']`。

`flags`是一个数组,可以包括以下 `String`:
* `enabled`  - 该按钮是活动的,可供用户使用。
* `disabled`  - 该按钮被禁用。如变灰等无法响应动作的外观。
* `dismissonclick`  - 当点击按钮时,缩略图窗口立即关闭。
* `nobackground`  - 不要绘制按钮边框,只使用图像。
* `hidden`  - 该按钮不向用户显示。
* `noninteractive`  - 按钮不是交互式也不绘制按下的状态。常用在通知中使用的按钮。