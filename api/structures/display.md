# Display Object(显示器对象)

* `id` Number  - 与显示相关联的唯一标识符。
* `rotation`Number -顺时针方向的屏幕旋转角度, 可选`0`,`90`,`180`,`270`。
* `scaleFactor` Number  - 输出设备的像素比例因子。
* `touchSupport` String - 是否支持触摸,可选 `available`, `unavailable`, `unknown`.
* `bounds` Object [Rectangle](rectangle.md)
* `size` Object
  * `height` Number
  * `width` Number
* `workArea` [Rectangle](rectangle.md)
* `workAreaSize` Object
  * `height` Number
  * `width` Number
  
`Display`对象表示连接到系统的物理显示器。虚拟 `Display` 可以存在于无头系统上, `Display` 也可以是对应于远程的虚拟显示器。