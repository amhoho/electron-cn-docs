## 类: TouchBarScrubber

>创建一个scrubber(一个可滚动的选择器)

进程: [主进程](../tutorial/quick-start.md#主进程)            

### `new TouchBarScrubber(options)` _实验功能_
> 用途: ** 创建一个新的scrubber**

* `options` Object
  * `items` [ScrubberItem[]](structures/scrubber-item.md) - 要放置在此scrubber中的项数组
  * `select` Function - 当用户点击的不是最后一个点击项时调用
    * `selectedIndex` Integer - 用户选择项的索引
  * `highlight` Function - 当用户点击任何项时调用
    * `highlightedIndex` Integer - 用户触摸项的索引
  * `selectedStyle` String -  所选项样式。默认为 `null`.
  * `overlayStyle` String - 选择的覆盖项样式 默认为 `null`.
  * `showArrowButtons` Boolean - 默认为 `false`.
  * `mode` String - 默认为 `free`.
  * `continuous` Boolean - 默认为 `true`.

### 实例属性
`TouchBarScrubber`有以下实例属性:

#### `touchBarSegmentedControl.items`
> 属性: ** scrubber中的项组成的数组( `ScrubberItem[]`)**

#### `touchBarSegmentedControl.selectedStyle`
> 属性: ** scrubber中的选择项的样式( `String`)**

可能的值:
* `background` - 映射到 `[NSScrubberSelectionStyle roundedBackgroundStyle]`
* `outline` - 映射到 `[NSScrubberSelectionStyle outlineOverlayStyle]`
* `null` - null时表示删除所有样式.

#### `touchBarSegmentedControl.overlayStyle`
> 属性: ** scrubber中的选择项的覆盖样式( `String`)**

可能的值:
 `background` - 映射到 `[NSScrubberSelectionStyle roundedBackgroundStyle]`
* `outline` - 映射到 `[NSScrubberSelectionStyle outlineOverlayStyle]`
* `null` - null时表示删除所有样式.

#### `touchBarSegmentedControl.showArrowButtons`
> 属性: ** 是否在scrubber中显示左右提示箭头( `Boolean`)**

#### `touchBarSegmentedControl.mode`
> 属性: ** scrubber的模式( `String`)**

* `fixed` - 映射到 `NSScrubberModeFixed`
* `free` - 映射到  `NSScrubberModeFree`

#### `touchBarSegmentedControl.continuous`
> 属性: ** scrubber是否是连续的( `Boolean`)**