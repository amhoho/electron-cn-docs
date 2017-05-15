# Chrome升级清单

> 概述Electron中每次升级Chrome所需的步骤

除了更新任何Chrome / Node API更改的Electron代码之外,还有这些事情需要处理:
- 验证新版本的可用性 https://github.com/zcbenz/chromium-source-tarball/releases
- 更新 `electron/libchromiumcontent`存储库根目录下的 `VERSION`文件
- 更新 `script/update-clang.sh`里的 `CLANG_REVISION`,确保和 `libchromiumcontent/src/tools/clang/scripts/update.py`中使用的版本一致
- 更新 `vendor/node`到与新版Chrome版本中使用的v8版本相对应的Node版本. 有关更多详细信息，请参阅https://nodejs.org/en/download/releases 的Node中的v8版本
- 更新 `vendor/crashpad`
- 更新 `vendor/depot_tools`
- 更新  `libchromiumcontent`  为 `script/lib/config.py`中下载的SHA-1
- 在更改的 `electron/libchromiumcontent`打开一个pull请求
- 在更改的 `electron/electron`打开一个pull请求
  - 包括 `vendor/`中所需子模块的升级
- V验证调试生成成功:
  - macOS
  - 32-bit Windows
  - 64-bit Window
  - 32-bit Linux
  - 64-bit Linux
  - ARM Linux
- 验证版本构建成功:
  - macOS
  - 32-bit Windows
  - 64-bit Window
  - 32-bit Linux
  - 64-bit Linux
  - ARM Linux
- 验证测试通过:
  - macOS
  - 32-bit Windows
  - 64-bit Window
  - 32-bit Linux
  - 64-bit Linux
  - ARM Linux

## 验证是否支持 `ffmpeg`

Electron 的发行版已默认包含了具有专有编解码器的 `ffmpeg`. 但没有这类编解码器的版本也是与所有版本共同构建与分发的. 所以每次进行 Chrome 更新时,你都应该验证该版本是否继续支持.

通过加载以下页面可验证Electron对 `ffmpeg` 的支持.它仅仅支持使用于Electron自带的含有专有编解码器的默认 `ffmpeg`库,并不支持使用于未包含专有编解码器的 `ffmpeg`库.
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Proprietary Codec Check</title>
  </head>
  <body>
    <p>通过加载mp4(http://www.quirksmode.org/html5/videos/big_buck_bunny.mp4)进行监测Electron是否使用了专有编解码器</p>
    <p id="outcome"></p>
    <video style="display:none" src="http://www.quirksmode.org/html5/videos/big_buck_bunny.mp4" autoplay></video>
    <script>
      const video = document.querySelector('video')
      video.addEventListener('error', ({target}) => {
        if (target.error.code === target.error.MEDIA_ERR_SRC_NOT_SUPPORTED) {
          document.querySelector('#outcome').textContent = '没有使用专有编解码器.视频源不支持错误事件.'
        } else {
          document.querySelector('#outcome').textContent = `未知错误: ${target.error.code}`
        }
      })
      video.addEventListener('playing', () => {
        document.querySelector('#outcome').textContent = '使用了专有解码器,视频已开始播放.'
      })
    </script>
  </body>
</html>
```

## 链接

- [Chrome更新](https://www.chromium.org/developers/calendar)
