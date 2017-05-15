# 类:TouchBar触摸条

>为本机macOS应用程序创建TouchBar布局

进程: [主进程](../tutorial/quick-start.md#主进程)     

## `new TouchBar(options)` _实验功能_
>用途:**使用指定项目创建新的触摸条,使用 `BrowserWindow.setTouchBar`将 `TouchBar`加到窗口中**

* `options` - Object
  * `items` ([TouchBarButton](touch-bar-button.md) | [TouchBarColorPicker](touch-bar-color-picker.md) | [TouchBarGroup](touch-bar-group.md) | [TouchBarLabel](touch-bar-label.md) | [TouchBarPopover](touch-bar-popover.md) | [TouchBarSlider](touch-bar-slider.md) | [TouchBarSpacer](touch-bar-spacer.md))[]
  * `escapeItem` ([TouchBarButton](touch-bar-button.md) | [TouchBarColorPicker](touch-bar-color-picker.md) | [TouchBarGroup](touch-bar-group.md) | [TouchBarLabel](touch-bar-label.md) | [TouchBarPopover](touch-bar-popover.md) | [TouchBarScrubber](touch-bar-scrubber.md) | [TouchBarSegmentedControl](touch-bar-segmented-control.md) | [TouchBarSlider](touch-bar-slider.md) | [TouchBarSpacer](touch-bar-spacer.md)) (可选)


**提醒:**  如果您没有自带触控板的MacBook的话,可以使用这个[触控板模拟器](https://github.com/sindresorhus/touch-bar-simulator).         

**注意:**  TouchBar API 目前是实验功能,未来可能删除.

下面是个用在摇一摇或老虎机,贩卖机上的简单的带有按钮的触摸条例子:

```javascript
const {app, BrowserWindow, TouchBar} = require('electron')

const {TouchBarLabel, TouchBarButton, TouchBarSpacer} = TouchBar

let spinning = false

//卷轴标签
const reel1 = new TouchBarLabel()
const reel2 = new TouchBarLabel()
const reel3 = new TouchBarLabel()

//旋转结果标签
const result = new TouchBarLabel()

//旋转按钮
const spin = new TouchBarButton({
  label: '🎰 Spin',
  backgroundColor: '#7851A9',
  click: () => {
   //忽略已经旋转的点击
    if (spinning) {
      return
    }

    spinning = true
    result.label = ''

    let timeout = 10
    const spinLength = 4 * 1000 // 4 seconds
    const startTime = Date.now()

    const spinReels = () => {
      updateReels()

      if ((Date.now() - startTime) >= spinLength) {
        finishSpin()
      } else {
  //每次旋转减慢一点
        timeout *= 1.1
        setTimeout(spinReels, timeout)
      }
    }

    spinReels()
  }
})

const getRandomValue = () => {
  const values = ['🍒', '💎', '7️⃣', '🍊', '🔔', '⭐', '🍇', '🍀']
  return values[Math.floor(Math.random() * values.length)]
}

const updateReels = () => {
  reel1.label = getRandomValue()
  reel2.label = getRandomValue()
  reel3.label = getRandomValue()
}

const finishSpin = () => {
  const uniqueValues = new Set([reel1.label, reel2.label, reel3.label]).size
  if (uniqueValues === 1) {
 //3个相同值
    result.label = '💰 Jackpot!'
    result.textColor = '#FDFF00'
  } else if (uniqueValues === 2) {
    // 2个相同值
    result.label = '😍 Winner!'
    result.textColor = '#FDFF00'
  } else {
  //没有相同值
    result.label = '🙁 Spin Again'
    result.textColor = null
  }
  spinning = false
}

const touchBar = new TouchBar([
  spin,
  new TouchBarSpacer({size: 'large'}),
  reel1,
  new TouchBarSpacer({size: 'small'}),
  reel2,
  new TouchBarSpacer({size: 'small'}),
  reel3,
  new TouchBarSpacer({size: 'large'}),
  result
])

let window

app.once('ready', () => {
  window = new BrowserWindow({
    frame: false,
    titleBarStyle: 'hidden-inset',
    width: 200,
    height: 200,
    backgroundColor: '#000'
  })
  window.loadURL('about:blank')
  window.setTouchBar(touchBar)
})
```

### 上方代码的运行方法

假设已有终端已打开您期望的例子并打算运行上方的例子,那么你需要这么做:

1. 将上面的内容以 `touchbar.js`文件保存到计算机中.
2. 如果没有安装electron的话,则通过npm安装electron 如 `npm install electron`.
3. 在Electron中运行: `./node_modules/.bin/electron touchbar.js`

这样,您就能看到一个新窗口,而触摸条中也将运行相应程序.

