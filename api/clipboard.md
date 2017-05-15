# 本文介绍:剪贴板操作
>在系统剪贴板上执行复制和剪贴操作

进程: [主进程](../glossary.md#主进程), [渲染进程](../glossary.md#渲染进程)

复制字符串例子:
```JavaScript
const {clipboard} = require('electron')
clipboard.writeText('Example String')
```
在X Window系统上，还有一个选择剪贴板。要操作它，你需要传递`selection`到每个方法：
```JavaScript
const {clipboard} = require('electron')
clipboard.writeText('Example String', 'selection')
console.log(clipboard.readText('selection'))
```

## 方法
 `clipboard` 模块有以下方法:

### `clipboard.readText([type])`
> 用途:**读取剪贴文本**

* `type` String (可选)

### `clipboard.writeText(text[, type])`
> 用途:**将文本添加到剪贴板**

* `text` String
* `type` String (可选)

### `clipboard.readHTML([type])`
> 用途:**读取剪贴板中的 `markup` 内容**

* `type` String (可选)

### `clipboard.writeHTML(markup[, type])`
> 用途:**向剪贴板中添加 `markup` 内容**

* `markup` String
* `type` String (可选)

### `clipboard.readImage([type])`
> 用途:**读取剪贴板中的`[NativeImage](native-image.md)` 内容**

* `type` String (可选)

### `clipboard.writeImage(image[, type])`
> 用途:**向剪贴板中添加`[NativeImage](native-image.md)` 内容**

* `image` [NativeImage](native-image.md)
* `type` String (可选)

### `clipboard.readRTF([type])`
> 用途:**读取剪贴板中的`RTF` 内容**

* `type` String (可选)

### `clipboard.writeRTF(text[, type])`
> 用途:**向剪贴板中添加 `RTF` 内容**

* `text` String
* `type` String (可选)

### `clipboard.readBookmark()` _macOS_ _Windows_
> 用途:**读取剪贴板中的包含表示书签的 `title` 和 `url` 键的对象**

* `title` String
* `url` String

 如果书签不可用, `title'和 `url`值将是空字符串

### `clipboard.writeBookmark(title, url[, type])` _macOS_ _Windows_
> 用途:**向剪贴板中添加表示书签的 `title` 和 `url` 键的对象**

* `title` String
* `url` String
* `type` String (可选)

 **注意:** 大部分WIN程序都不支持直接复制或剪贴书签,你可以使用 `clipboard.write`来写入.
 
```JavaScript
clipboard.write({
  text: 'http://electron.atom.io',
  bookmark: 'Electron Homepage'
})
```

### `clipboard.readFindText()` _macOS_
> 用途:**从剪贴板中查找文本**

在渲染进程中使用IPC调用该方法进行查找时,每当程序被激活,缓存文本都将被重读.

### `clipboard.writeFindText(text)` _macOS_
> 用途:**以文本形式写入剪贴板**

* `text` String

此方法在从渲染器进程调用时使用同步IPC。

### `clipboard.clear([type])`
> 用途:**清除剪贴板内容**

* `type` String (可选)

### `clipboard.availableFormats([type])`
> 用途:**返回剪贴板 `type`支持的格式数组**

* `type` String (可选)

### `clipboard.has(format[, type])` _实验功能_
> 用途:**判断剪剪贴板是否支持指定 `format`的格式( `Boolean`)**

* `format` String
* `type` String (可选)

```JavaScript
const {clipboard} = require('electron')
console.log(clipboard.has('<p>selection</p>'))
```

### `clipboard.read(format)` _实验功能_
> 用途:**从剪贴板读取 `format`格式内容( `String`)**

* `format` String

### `clipboard.readBuffer(format)` _实验功能_
> 用途:**以输入流形式从剪贴板读取 `format`格式内容( `Buffer`)**

* `format` String

### `clipboard.write(data[, type])`
> 用途:**向剪贴板中添加 `data`**

* `data` Object
  * `text` String (可选)
  * `html` String (可选)
  * `image` [NativeImage](native-image.md) (可选)
  * `rtf` String (可选)
  * `bookmark` String (可选) -  `text` 里url链接的标题.
* `type` String (可选)
 
```JavaScript
const {clipboard} = require('electron')
clipboard.write({text: 'test', html: '<b>test</b>'})
```