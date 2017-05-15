# DesktopCapturerSource Object(桌面捕获源对象)

* `id` String  - 调用[`navigator.webkitGetUserMedia`]时可用作 `chromeMediaSourceId`约束的窗口或屏幕的标识符。
   标识符的格式为 `window:XX`或 `screen:XX`,其中 `XX`是一个随机生成的数字。
* `name` String  - 屏幕源将被命名为 `Entire Screen` 或 `Screen <index>`,而窗口源的名称将匹配窗口标题。
* `thumbnail` [NativeImage](../ native-image.md) - 缩略图。

   **注意:**不能保证缩略图的大小与传递给 `desktopCapturer.getSources`的 `options`中指定的 `thumbnailSize`相同。
   
     实际大小取决于屏幕或窗口的比例。