# 离屏渲染

因为屏幕渲染可在位图中获取浏览器窗口内容，所以它可以呈现在任何地方，例如3D场景中的纹理。

Electron中的屏幕渲染的使用方法与 [Chromium Embedded Framework](https://bitbucket.org/chromiumembedded/cef)项目类似,都可以使用两种渲染模式，并且只有脏区域在 `'paint'` 事件中传递才能更有效率。可以停止或继续渲染，还可以设置帧速率。指定的帧速率是上限值，当网页上没有发生任何事情时，不会生成任何帧。最大帧速率为60，超过60并没有任何好处，反而是性能下降。

**注意：**屏幕外窗口总是创建为[无框窗口](../api/frameless-window.md).       

## 两种渲染模式

### GPU加速

GPU加速渲染意味着使用GPU用于合成。由于帧必须从需要更多性能的GPU中复制，因此这种模式比另一个模式慢得多,但这种模式的优点是支持WebGL和3D CSS动画。

### 软件输出设备

该模式使用软件输出设备在CPU中渲染，因此帧生成要快得多，因此该模式优于GPU加速模式。

要启用此模式，必须通过调用 [`app.disableHardwareAcceleration()`][disablehardwareacceleration] API 来禁用GPU加速。   

## 使用示例

```javascript
const {app, BrowserWindow} = require('electron')
app.disableHardwareAcceleration()
let win
app.once('ready', () => {
  win = new BrowserWindow({
    webPreferences: {
      offscreen: true
    }
  })
  win.loadURL('http://github.com')
  win.webContents.on('paint', (event, dirty, image) => {
    // updateBitmap(dirty, image.getBitmap())
  })
  win.webContents.setFrameRate(30)
})
```

[disablehardwareacceleration]: ../api/app.md#appdisablehardwareacceleration