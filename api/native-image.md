# 本文介绍:NativeImage图标创建与应用

>使用PNG或JPG文件创建托盘，dock和应用程序图标。

进程: [主进程](../glossary.md#main-process), [渲染进程](../glossary.md#renderer-process)

在 Electron 中, 对所有创建 images 的 api 来说, 你可以传递文件路径或 `NativeImage` 实例. 如果传递 `null` ,将创建一个空的image 对象.

例如, 当创建托盘图标或设置窗口图标时,你可以传递 `String`格式的文件路径 :
```JavaScript
const {BrowserWindow, Tray} = require('electron')
const appIcon = new Tray('/Users/somebody/images/icon.png')
let win = new BrowserWindow({icon: '/Users/somebody/images/window.png'})
console.log(appIcon, win)
```

或者从返回 `NativeImage`的剪贴板中读图 :
```JavaScript
const {clipboard, Tray} = require('electron')
const image = clipboard.readImage()
const appIcon = new Tray(image)
console.log(appIcon)
```

## 支持的格式

当前支持 `PNG` 和 `JPEG` 图片格式. 推荐 `PNG` ,因为它支持透明和无损压缩.

在 Windows, 你也可以使用 `ICO` 图标的格式.
* 常用小图标尺寸
 * 16x16 (100% DPI scale)
 * 20x20 (125% DPI scale)
 * 24x24 (150% DPI scale)
 * 32x32 (200% DPI scale)
* 常用大图标尺寸
 * 32x32 (100% DPI scale)
 * 40x40 (125% DPI scale)
 * 48x48 (150% DPI scale)
 * 64x64 (200% DPI scale)
* 256x256

[在这里查看微软关于尺寸的详细说明][icons] 
[icons]:https://msdn.microsoft.com/en-us/library/windows/desktop/dn742485(v=vs.85).aspx

## 高分辨率图片

在具有高DPI支持的平台(如Apple Retina显示器)上，您可以在图像的基本文件名后面加上 `@2x`，以将其标记为高分辨率图像。

如果 `icon.png`是具有标准分辨率的正常图像， `icon@2x.png`则表示具有双DPI密度的高分辨率图像。

如果要同时支持具有不同DPI密度的显示，则可以将不同大小的图像放在同一文件夹中，并使用不带DPI后缀的文件名。例如：

```text
images/
├── icon.png
├── icon@2x.png
└── icon@3x.png
```

```JavaScript
const {Tray} = require('electron')
let appIcon = new Tray('/Users/somebody/images/icon.png')
console.log(appIcon)
```

还支持以下后缀DPI：

* `@1x`
* `@1.25x`
* `@1.33x`
* `@1.4x`
* `@1.5x`
* `@1.8x`
* `@2x`
* `@2.5x`
* `@3x`
* `@4x`
* `@5x`

## 模板图像

模板图像由黑色和透明色(或alpha 通道)组成.

模板图像并不用作独立图片,它通常与其他内容混合以创建期望的最终效果。

最常见的情况是当做菜单栏图标,因此它可以适应亮和暗色菜单栏。

 **注意:** 仅在macOS上支持模板图像。
 
如果希望标记为模板图像,其文件名应以单词 `Template`结尾。例如:

* `xxxTemplate.png`
* `xxxTemplate@2x.png`

## 方法
 `nativeImage`模块有以下方法，所有方法都返回 `NativeImage`类的一个实例：
 
### `nativeImage.createEmpty()`
> 用途:**创建一个空的 `NativeImage` 实例**

### `nativeImage.createFromPath(path)`
> 用途:**从位于 `path`的文件创建一个新的 `NativeImage`实例**

* `path` String - `path`不存在,无法读取或不是有效图像,此方法将返回空图像。

示例:

```JavaScript
const nativeImage = require('electron').nativeImage
let image = nativeImage.createFromPath('/Users/somebody/images/icon.png')
console.log(image)
```

### `nativeImage.createFromBuffer(buffer[, options])`
> 用途:**从 `buffer`中创建一个 `NativeImage` 实例**

* `buffer` [Buffer][buffer]
* `options` Object (可选)
  * `width` Integer (可选) - 对于位图bitmap,缓冲区(buffers)是必需的。
  * `height` Integer (可选) -  对于位图bitmap,缓冲区(buffers)是必需的。
  * `scaleFactor` Double (可选) - 默认1.0.

### `nativeImage.createFromDataURL(dataURL)`
> 用途:**从 `dataURL`创建一个 `NativeImage` 实例( `NativeImage`)**

* `dataURL` String

#### `image.toPNG([options])`

> 用途:**返回包含图片 `PNG` 编码数据( `Buffer` [Buffer][buffer])**

* `options` Object (可选)
* `scaleFactor` Double (可选) - 默认 1.0.

#### `image.toJPEG(quality)`
> 用途:**返回包含图片 `JPEG` 编码数据( `Buffer` [Buffer][buffer])**

* `quality` Integer (**必须**) - 质量可选 0 - 100.

#### `image.toBitmap([options])`
> 用途:**返回包含图片 `Bitmap` 编码数据副本( `Buffer` [Buffer][buffer])**

* `options` Object (可选)
* `scaleFactor` Double (可选) - 默认 1.0.

#### `image.toDataURL([options])`
> 用途:**返回图片数据的 URL( `String`)**

* `options` Object (可选)
* `scaleFactor` Double (可选) - 默认 1.0.

#### `image.getBitmap([options])`
> 用途:**返回包含图片 `Bitmap` 编码数据( `Buffer` [Buffer][buffer])**

* `options` Object (可选)
* `scaleFactor` Double (可选) - 默认 1.0.

 `getBitmap()`和 `toBitmap()`之间的区别是, `getBitmap()` 不会复制位图数据,所以你必须在当前事件中立即使用返回的Buffer,否则数据可能会被改变,销毁。

#### `image.getNativeHandle()` _macOS_
> 用途:**返回带有指向了图像底层本地句柄的C指针的 [Buffer][buffer]**

在macOS上，返回的是指向 `NSImage`实例的指针。

注意，返回的指针是一个指向底层本地映像而不是副本的弱指针，所以你_必须_确保关联的`nativeImage`实例被保留。

#### `image.isEmpty()`
> 用途:**判断图片是否为空( `Boolean`)**

#### `image.getSize()`
> 用途:**获得尺寸信息( `Object`)**

* `width` Integer
* `height` Integer

#### `image.setTemplateImage(option)`
> 用途:**将图片标识为模板图像**

* `option` Boolean

#### `image.isTemplateImage()`
> 用途:**判断图片是否为模板图像( `Boolean`)**

#### `image.crop(rect)`
> 用途:**按区域裁剪并返回指定尺寸的图片( `NativeImage`)**

* `rect` Object - 要裁剪的图像区域
  * `x` Integer
  * `y` Integer
  * `width` Integer
  * `height` Integer


#### `image.resize(options)`
> 用途:**重新调整图片尺寸( `NativeImage`)**

* `options` Object
  * `width` Integer (可选)- 默认为图像宽度
  * `height` Integer (可选)- 默认为图像高度
  * `quality` String (可选) - 调整图像大小的所需质量,可能的值是 `good`, `better` 或者 `best`。默认值是 `best`

如果只指定 `height`或 `width`,那么图片将保持原始宽高比。

#### `image.getAspectRatio()`
> 用途:**获取图像的宽高比例( `Float`)**


#### `image.addRepresentation(options)`
> 用途:**自定义图片缩放比例**

* `options` Object
  * `scaleFactor` Double - 默认1.0
  * `width` Integer(可选) - 默认为0.如果位图缓冲区指定为 `buffer`,则为必需。
  * `height` Integer(可选) - 默认为0.如果位图缓冲区指定为 `buffer`,则为必需。
  * `buffer` Buffer(可选) - 包含原始图像数据的缓冲区。
  * `dataURL` String(可选) - 数据URL，包含基本64编码的PNG或JPEG图像。

[buffer]: https://nodejs.org/api/buffer.html#buffer_class_buffer