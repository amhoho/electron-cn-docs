# DevTools扩展

Electron支持[Chrome 的 DevTools 扩展][devtools-extension], 可扩展常用于调试Web的Devtools功能.

## 如何加载DevTools扩展

除了本文概述的手动加载扩展的过程,您也可以尝试从Chrome WebStore直接下载扩展的第三方工具:[electron-devtools-installer](https://github.com/GPMDP/electron-devtools-installer),.

想要Electron中加载扩展,你要先在Chrome中下载并找到它的所在路径,然后通过调用 `BrowserWindow.addDevToolsExtension(extension)` API加载它.

以[React Developer Tools][react-devtools]这个扩展为例:

1. Chrome浏览器中安装扩展.
2. Chrome导航至 `chrome://extensions`, 找到类似 `fmkadmapgofadopljbjfkapdkoienihi` 的扩展名.
3. 找出Chrome存储扩展的所在路径:
   * Windows中可能是:`%LOCALAPPDATA%\Google\Chrome\User Data\Default\Extensions`;
   * Linux中可能是:
     * `~/.config/google-chrome/Default/Extensions/`
     * `~/.config/google-chrome-beta/Default/Extensions/`
     * `~/.config/google-chrome-canary/Default/Extensions/`
     * `~/.config/chromium/Default/Extensions/`
   * macOS中可能是: `~/Library/Application Support/Google/Chrome/Default/Extensions`.
4. 将扩展所在路径作为参数传递给 `BrowserWindow.addDevToolsExtension` API, 相对于React Developer Tools,它可能类似于: `~/Library/Application Support/Google/Chrome/Default/Extensions/fmkadmapgofadopljbjfkapdkoienihi/0.15.0_0`

这样, `BrowserWindow.addDevToolsExtension`将返回扩展名, 而通过将扩展名传递给 `BrowserWindow.removeDevToolsExtension` API则进行卸载.

 ```bash
const BrowserWindow = require('electron').remote.BrowserWindow;
//返回扩展名
BrowserWindow.addDevToolsExtension('/some-directory/react-devtools/shells/chrome');
//卸载扩展
BrowserWindow.removeDevToolsExtension('React Developer Tools');
 ```
 
**注意:**  `BrowserWindow.addDevToolsExtension` API 必须触发于ready事件之后.


## 支持的DevTools扩展

由于Electron仅支持使用了 `chrome.*` APIs的扩展, 所以那些不支持 `chrome.*` APIs的扩展可能不被支持.

以下扩展已被证实可使用在Electron中:

* [Ember Inspector](https://chrome.google.com/webstore/detail/ember-inspector/bmdblncegkenkacieihfhpjfppoconhi)
* [React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
* [Backbone Debugger](https://chrome.google.com/webstore/detail/backbone-debugger/bhljhndlimiafopmmhjlgfpnnchjjbhd)
* [jQuery Debugger](https://chrome.google.com/webstore/detail/jquery-debugger/dbhhnnnpaeobfddmlalhnehgclcmjimi)
* [AngularJS Batarang](https://chrome.google.com/webstore/detail/angularjs-batarang/ighdmehidhipcmcojjgiloacoafjmpfk)
* [Vue.js devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
* [Cerebral Debugger](http://www.cerebraljs.com/documentation/the_debugger)
* [Redux DevTools Extension](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd)

### DevTools扩展故障了,我该怎么办?

首先,要确保扩展仍然有作者在维护,某些无人维护的扩展甚至无法适用于最新版的Chrome,这样我们就无能为力了.
其次,在Electron[提出issues](https://github.com/electron/electron/issues)并详诉如哪个地方出现了故障等具体情况.

[devtools-extension]: https://developer.chrome.com/extensions/devtools
[react-devtools]: https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi