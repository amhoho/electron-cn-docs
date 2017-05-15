# API弃用说明

以下列表包括将在Electron 2.0中删除的API,虽然我们不确定准确的时间弃用,但我们会在至少90天前会发布警告.

## `BrowserWindow`

```js
// 弃用
let optionsA = {webPreferences: {blinkFeatures: ''}}
let windowA = new BrowserWindow(optionsA)
// 替代方案
let optionsB = {webPreferences: {enableBlinkFeatures: ''}}
let windowB = new BrowserWindow(optionsB)
```

## `clipboard`

```js
// 弃用
clipboard.readRtf()
// 替代方案
clipboard.readRTF()

// 弃用
clipboard.writeRtf()
// 替代方案
clipboard.writeRTF()

// 弃用
clipboard.readHtml()
// 替代方案
clipboard.readHTML()

// 弃用
clipboard.writeHtml()
// 替代方案
clipboard.writeHTML()
```

## `crashReporter`

```js
// 弃用
crashReporter.start({
  companyName: 'Crashly',
  submitURL: 'https://crash.server.com',
  autoSubmit: true
})
// 替代方案
crashReporter.start({
  companyName: 'Crashly',
  submitURL: 'https://crash.server.com',
  uploadToServer: true
})
```

## `menu`

```js
// 弃用
menu.popup(browserWindow, 100, 200, 2)
// 替代方案
menu.popup(browserWindow, {x: 100, y: 200, positioningItem: 2})
```

## `nativeImage`

```js
// 弃用
nativeImage.toPng()
// 替代方案
nativeImage.toPNG()

// 弃用
nativeImage.toJpeg()
// 替代方案
nativeImage.toJPEG()

// 弃用
nativeImage.createFromBuffer(buffer, 1.0)
// 替代方案
nativeImage.createFromBuffer(buffer, {
  scaleFactor: 1.0
})
```

## `process`

```js
// 弃用
process.versions['atom-shell']
// 替代方案
process.versions.electron
```

* `process.versions.electron` and `process.version.chrome` will be made read-only properties for consistency with the other `process.versions` properties set by Node.

## `session`

```js
// 弃用
ses.setCertificateVerifyProc(function (hostname, certificate, callback) {
  callback(true)
})
// 替代方案
ses.setCertificateVerifyProc(function (request, callback) {
  callback(0)
})
```

## `Tray`

```js
// 弃用
tray.setHighlightMode(true)
// 替代方案
tray.setHighlightMode('on')

// 弃用
tray.setHighlightMode(false)
// 替代方案
tray.setHighlightMode('off')
```

## `webContents`

```js
// 弃用
webContents.openDevTools({detach: true})
// 替代方案
webContents.openDevTools({mode: 'detach'})
```

```js
// 弃用
webContents.setZoomLevelLimits(1, 2)
// 替代方案
webContents.setVisualZoomLevelLimits(1, 2)
```

## `webFrame`

```js
// 弃用
webFrame.setZoomLevelLimits(1, 2)
// 替代方案
webFrame.setVisualZoomLevelLimits(1, 2)

// 弃用
webFrame.registerURLSchemeAsSecure('app')
// 替代方案
protocol.registerStandardSchemes(['app'], {secure: true})

// 弃用
webFrame.registerURLSchemeAsPrivileged('app', {secure: true})
// 替代方案
protocol.registerStandardSchemes(['app'], {secure: true})
```

## `<webview>`

```js
// 弃用
webview.setZoomLevelLimits(1, 2)
// 替代方案
webview.setVisualZoomLevelLimits(1, 2)
```

## Node Headers URL

弃用: https://atom.io/download/atom-shell
替代方案: https://atom.io/download/electron