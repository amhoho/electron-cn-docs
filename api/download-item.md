## 本文介绍: 应用中的文件下载

> 控制从远程源的文件下载。

进程: [主进程](../glossary.md#主进程)    

 `DownloadItem`(下载项)是一个在Electron中展示下载项的[EventEmitter](http://nodejs.org/api/events.html#events_class_events_eventemitter)
   
它被用于 `Session`类中的 `will-download`事件,并允许用户控制下载项。

```JavaScript
//主进程中
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.webContents.session.on('will-download', (event, item, webContents) => {
//设置保存路径,使Electron不提示保存对话框。
  item.setSavePath('/tmp/save.pdf')

  item.on('updated', (event, state) => {
    if (state === 'interrupted') {
      console.log('下载已中断，但可以恢复')
    } else if (state === 'progressing') {
      if (item.isPaused()) {
        console.log('下载已暂停')
      } else {
        console.log(`Received bytes: ${item.getReceivedBytes()}`)
      }
    }
  })
  item.once('done', (event, state) => {
    if (state === 'completed') {
      console.log('下载成功')
    } else {
      console.log(`下载失败: ${state}`)
    }
  })
})
```

### 实例事件

#### 事件: 'updated'
> 触发:**下载获得更新且未完成时**

* `event` Event
* `state` String,可选以下状态:
 * `progressing`  - 下载正在进行中。
 * `interrupted`  - 下载已中断但可以恢复。

#### 事件: 'done'
> 触发:**下载处于完成状态(如已完成,被 `downloadItem.cancel()`取消,意外中断)**

* `event` Event
* `state` String,可选以下状态:
 * `completed`  - 下载已成功完成。
 * `cancelled`  - 下载已取消。
 * `interrupted`- 下载已中断,无法恢复。
 
### 实例方法

#### `downloadItem.setSavePath(path)`
> 用途:**设置下载目录**

* `path` String - 下载项目的保存路径。

API仅在会话的 `will-download` 回调函数中可用。
如果用户未通过API设置保存路径,Electron将使用原始保存路径(通常提示保存对话框)。

#### `downloadItem.getSavePath()`
> 用途:**返回下载目录**

通过 `downloadItem.setSavePath(path)` 设置的路径或从保存对话框中选择的路径。

#### `downloadItem.pause()`
> 用途:**暂停下载**

#### `downloadItem.isPaused()`
> 用途:**判断下载是否已被暂停**

#### `downloadItem.resume()`
> 用途:**恢复已暂停的下载**

**注意:** 要启用可继续的下载项，您正在下载的服务器必须支持范围请求，并提供 `Last-Modified`和 `ETag`头值。 否则 `resume()`将关闭以前收到的字节，并从头开始重新下载。

#### `downloadItem.canResume()`
> 用途:**判断下载是否可以恢复**

#### `downloadItem.cancel()`
> 用途:**取消下载**

#### `downloadItem.getURL()`
> 用途:**获取下载项的链接地址**

#### `downloadItem.getMimeType()`
> 用途:**获取下载项的文件mime类型**

#### `downloadItem.hasUserGesture()`
> 用途:**判断下载是否具有用户手势**

#### `downloadItem.getFilename()`
> 用途:**获取下载项的文件名**

 **注意:**文件名不一定与保存在本地磁盘中的实际文件名相同。如果用户在提示的下载保存对话框中更改文件名,则保存文件的实际名称将不同。

#### `downloadItem.getTotalBytes()`
> 用途:**获取下载项的文件大小(以bytes字节为单位)**
如果大小未知,则返回0。

#### `downloadItem.getReceivedBytes()`
> 用途:**获取下载项的已接收字节数(以bytes字节为单位)**

#### `downloadItem.getContentDisposition()`
> 用途:**获取下载项的响应头的Content-Disposition字段(字符串)**

#### `downloadItem.getState()`
> 用途:**获取下载项的当前状态**

可以是 `progressing`下载中, `completed`已完成, `cancelled`已取消, `interrupted`中断且无法恢复.

**注意:**以下方法适用于在会话重新启动时恢复 `cancelled`项。

#### `downloadItem.getURLChain()`
> 用途:**获取下载项的完整下载链接(包含任何重定向)(字符串)**

#### `downloadItem.getLastModifiedTime()`
> 用途:**获取下载项的Last-Modified标头值(字符串)**

#### `downloadItem.getETag()`
> 用途:**获取下载项的ETag头值(字符串)**

#### `downloadItem.getStartTime()`
> 用途:**获取下载项的开始下载时间(秒级)(字符串)**