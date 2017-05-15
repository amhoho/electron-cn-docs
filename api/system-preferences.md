# 本文介绍:获取系统首选项

> 获取系统首选项

进程: [主进程](../glossary.md#主进程)               
例子:
```JavaScript
const {systemPreferences} = require('electron')
console.log(systemPreferences.isDarkMode())
```

## 事件列表
 `systemPreferences` 对象提供以下事件:

### 事件: 'accent-color-changed' _Windows_
> 触发:**用户在个性化中更改了系统基本色调时**

* `event` Event
* `newColor` String 

### 事件: 'color-changed' _Windows_
> 触发:**用户在个性化中更改了系统颜色时**

* `event` Event

### 事件: 'inverted-color-scheme-changed' _Windows_
> 触发:**用户切换为反色方案(高对比度主题等)时**

* `event` Event
* `invertedColorScheme`  Boolean - `true` 代表的是正在使用反色方案, 否则为 `false`.

## 方法列表

### `systemPreferences.isDarkMode()` _macOS_
> 用途:**判断系统是否处于深色或黑暗模式( `Boolean`)**

### `systemPreferences.isSwipeTrackingFromScrollEventsEnabled()` _macOS_
> 用途:**判断系统是否开启页面间滑动功能( `Boolean`)**

### `systemPreferences.postNotification(event, userInfo)` _macOS_
> 用途:**在 macOS 上使用系统通知来发布 `event`**

* `event` String
* `userInfo` Object 包含了用户向通知发送的信息字典

### `systemPreferences.postLocalNotification(event, userInfo)` _macOS_
> 用途:**在 macOS 上使用系统本地通知来发布 `event`**

* `event` String
* `userInfo` Object 包含了用户向通知发送的信息字典

### `systemPreferences.subscribeNotification(event, callback)` _macOS_
> 用途:**订阅macOS的本地通知并调用 `callback(event, userInfo)`返回订阅者的 `id`**

* `event` String
* `callback` Function
  * `event` String
  * `userInfo` Object 包含了用户向通知发送的信息字典

订阅者的 `id` 可用于取消订阅 `event` 。

这个API在底层上订阅于 `NSDistributedNotificationCenter`, `event` 的参考值如下:

* `AppleInterfaceThemeChangedNotification`
* `AppleAquaColorVariantChanged`
* `AppleColorPreferencesChangedNotification`
* `AppleShowScrollBarsSettingChanged`

### `systemPreferences.unsubscribeNotification(id)` _macOS_
> 用途:**使用 `id`删除订阅**

* `id` Integer

### `systemPreferences.subscribeLocalNotification(event, callback)` _macOS_
> 用途:**订阅macOS的本地通知并调用 `callback(event, userInfo)`返回订阅者的 `id`,但从 `NSNotificationCenter`订阅**

* `event` String
* `callback` Function
  * `event` String
  * `userInfo` Object
  
对于诸如 `NSUserDefaultsDidChangeNotification`的事件是必需的.

### `systemPreferences.unsubscribeLocalNotification(id)` _macOS_
> 用途:**使用 `id`删除订阅,但从 `NSNotificationCenter`删除**

* `id` Integer

### `systemPreferences.getUserDefault(key, type)` _macOS_
> 用途:**获取系统偏好中相应 `key`的值**

* `key` String
* `type` String - 类型,可选 `string`, `boolean`, `integer`, `float`, `double`, `url`, `array`, `dictionary`

这个API在macOS上使用的是 `NSUserDefaults` 。以下是部分常用的 `key` 和 `type`:
* `AppleInterfaceStyle`:  `string`
* `AppleAquaColorVariant`:  `integer`
* `AppleHighlightColor`:  `string`
* `AppleShowScrollBars`:  `string`
* `NSNavRecentPlaces`:  `array`
* `NSPreferredWebServices`:  `dictionary`
* `NSUserDictionaryReplacementItems`:  `array`

### `systemPreferences.setUserDefault(key, type, value)` _macOS_
> 用途:**设置系统偏好中相应 `key`的值**

* `key` String
* `type` String -  类型,可选 `string`, `boolean`, `integer`, `float`, `double`, `url`, `array`, `dictionary`
* `value` String

需要注意的是 `type` 需要与实际的类型的 `value` 对应。不然会抛出一个异常。

这个API在macOS上使用的是 `NSUserDefaults` 。以下是部分常用的 `key` 和 `type`:
* `ApplePressAndHoldEnabled`:  `boolean`

### `systemPreferences.isAeroGlassEnabled()` _Windows_
> 用途:**判断是否开启了[毛玻璃效果][dwm-composition]( `Boolean`)**

使用它来确定是否可创建透明窗口的示例(因为DWM组合被禁用时，透明窗口将无法正常工作):

```JavaScript
const {BrowserWindow, systemPreferences} = require('electron')
let browserOptions = {width: 1000, height: 800}

// 如果平台支持的话便新建一个透明的窗口。
if (process.platform !== 'win32' || systemPreferences.isAeroGlassEnabled()) {
  browserOptions.transparent = true
  browserOptions.frame = false
}

// 创建窗口。
let win = new BrowserWindow(browserOptions)

// 区分环境进行加载。
if (browserOptions.transparent) {
  win.loadURL(`file://${__dirname}/index.html`)
} else {
//没有透明度则加载基本样式
  win.loadURL(`file://${__dirname}/fallback.html`)
}
```

### `systemPreferences.getAccentColor()` _Windows_
> 用途:**获取当前系统范围的16进制 RGBA 色值( `String`)**

```JavaScript
const color = systemPreferences.getAccentColor() // `aabbccdd`
const red = color.substr(0, 2) // `aa`
const green = color.substr(2, 2) // `bb`
const blue = color.substr(4, 2) // `cc`
const alpha = color.substr(6, 2) // `dd`
```

### `systemPreferences.getColor(color)` _Windows_
> 用途:**获取当前系统范围的16进制 RGB 色值.如 `＃ABCDEF`( `String`)**

* `color` String - 可选值:
  * `3d-dark-shadow` - 3D暗影颜色。
  * `3d-face` - 3D边框或对话框背景色。
  * `3d-highlight` - 高亮的3D元素颜色。
  * `3d-light` - 3D 元素亮色。
  * `3d-shadow` - 3D 元素的阴影颜色。
  * `active-border` - 活动窗口边框。
  * `active-caption` - 活动窗口标题栏。如果启用渐变效果，则指定活动窗口标题栏的颜色渐变中的左侧颜色。
  * `active-caption-gradient` - 活动窗口标题栏颜色渐变中的右侧颜色。
  * `app-workspace` - 多文档(MDI)应用界面背景颜色。
  * `button-text` - 按钮上的文本。
  * `caption-text` - 标题栏,尺寸框,滚动条尖头框上的文本。
  * `desktop` - 桌面背景颜色。
  * `disabled-text` - 灰色(禁止的)文本。
  * `highlight` - 在控件中选择的项目。
  * `highlight-text` - 在控件中选择的项目的文本。
  * `hotlight` - 超链接或热跟踪项的颜色。
  * `inactive-border` - 非活动窗口边框。
  * `inactive-caption` - 非活动窗口标题。如果启用渐变效果，则指定非活动窗口标题栏的颜色渐变中的左侧颜色。非活动窗口的标题栏。
  * `inactive-caption-text` -  无效标题文本的颜色。
  * `info-background` - 工具提示控件的背景颜色。
  * `info-text` - 工具提示控件的文本颜色。
  * `menu` - 菜单背景。
  * `menu-highlight` - 当菜单作为平面菜单时高亮的菜单颜色。
  * `menubar` - 当菜单作为平面菜单时菜单栏背景颜色。
  * `menu-text` - 菜单中的文本。
  * `scrollbar` - 滚动条灰色区域。
  * `window` - 窗口背景。
  * `window-frame` - 窗口框架。
  * `window-text` -  在窗口中的文本。

详见 [Windows docs][windows-colors] 获取更多细节。

### `systemPreferences.isInvertedColorScheme()` _Windows_
> 用途:**判断系统是否处于反色方案(高对比度主题等)( `Boolean`)**

[dwm-composition]:https://msdn.microsoft.com/en-us/library/windows/desktop/aa969540.aspx
[windows-colors]:https://msdn.microsoft.com/en-us/library/windows/desktop/ms724371(v=vs.85).aspx