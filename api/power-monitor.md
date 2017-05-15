# 本文介绍:监视电源状态

>监视电源状态

进程: [主进程](../glossary.md#主进程)               

该模块在主进程 `app` 模块的 `ready` 事件前使用.
```JavaScript
const electron = require('electron')
const {app} = electron

app.on('ready', () => {
  electron.powerMonitor.on('suspend', () => {
    console.log('系统即将进入睡眠')
  })
})
```

## 事件列表

### 事件:'suspend'
> 触发:**系统挂起时**

### 事件:`resume`
> 触发:**系统恢复时**

### 事件:'on-ac'_Windows_
> 触发:**系统更改为交流电源时**

### 事件:'on-battery'_Windows_
> 触发:**系统更改为电池电源时**