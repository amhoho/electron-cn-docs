# Mac App Store应用提交指南

>如何提交应用至Mac App Store 和 MAS 构建的限制.

**注意:** 提交应用到MAS,你需要付费加入[开发者计划][developer-program].        

## 如何提交您的应用程序

以下介绍了如何将应用提交至MAS,但并不确保应用一定被Apple审核通过, 你需要详阅Apple的[提交指南][submitting-your-app],了解MAS的详尽要求.

### 获得证书

要将您的应用程序提交到Mac App Store，您首先必须从Apple获取证书。这里有个[现成指南][nwjs-guide]可供参考.    

### 获得 Team ID
在软件签名之前，您需要知道开发者账户的 Team ID,请登录 [Apple开发者中心](https://developer.apple.com/account/)并点击侧边栏的`Membership`,您的Team ID显示在团队名称下的 `Membership Information`里。

### 软件签名

完成准备工作后，您可以按照[应用程序分发](application-distribution.md)打包应用并进行签名.    

首先，你需要在软件包内的 `Info.plist` 中增添一项 `ElectronTeamID`：
```xml
<plist version="1.0">
<dict>
  ...
  <key>ElectronTeamID</key>
  <string>TEAM_ID</string>
</dict>
</plist>
```

然后，准备两个名为 `child.plist`和 `parent.plist`的授权文件.

`child.plist`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
    <key>com.apple.security.inherit</key>
    <true/>
  </dict>
</plist>
```

`parent.plist`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
    <key>com.apple.security.application-groups</key>
    <string>TEAM_ID.your.bundle.id</string>
  </dict>
</plist>
```

把 上面的 `TEAM_ID`改成你自己的 `Team ID`, 把 `your.bundle.id`改成应用的Bundle ID,然后使用以下脚本签名应用:

```bash
#!/bin/bash

# 应用名称
APP="YourApp"
# 应用路径
APP_PATH="/path/to/YourApp.app"
# 生成安装包路径
RESULT_PATH="~/Desktop/$APP.pkg"
# 开发者应用签名证书
APP_KEY="3rd Party Mac Developer Application: Company Name (APPIDENTITY)"
INSTALLER_KEY="3rd Party Mac Developer Installer: Company Name (APPIDENTITY)"
# 授权文件路径
CHILD_PLIST="/path/to/child.plist"
PARENT_PLIST="/path/to/parent.plist"

FRAMEWORKS_PATH="$APP_PATH/Contents/Frameworks"

codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework/Versions/A/Electron Framework"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework/Versions/A/Libraries/libffmpeg.dylib"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework/Versions/A/Libraries/libnode.dylib"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper.app/Contents/MacOS/$APP Helper"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper.app/"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper EH.app/Contents/MacOS/$APP Helper EH"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper EH.app/"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper NP.app/Contents/MacOS/$APP Helper NP"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper NP.app/"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$APP_PATH/Contents/MacOS/$APP"
codesign -s "$APP_KEY" -f --entitlements "$PARENT_PLIST" "$APP_PATH"

productbuild --component "$APP_PATH" /Applications --sign "$INSTALLER_KEY" "$RESULT_PATH"
```

如果你是 macOS 下的应用沙箱使用新手，应当仔细阅读 Apple 的这边 [启用应用程序沙箱][enable-app-sandbox] 了解一些基础，然后在授权文件 (entitlements files) 内添加你的应用所需密钥。

除了手动签名你的应用，你也可以选择使用[electron-osx-sign][electron-osx-sign] 模块来完成此工作。

#### 签名原生模块

应用程序中的原生模块也需要签名。如果使用electron-osx-sign，请确保在参数列表中包含已生成二进制文件的路径：

```bash
electron-osx-sign YourApp.app YourApp.app/Contents/Resources/app/node_modules/nativemodule/build/release/nativemodule
```

还要注意，原生模块可能产生的中间文件不包括在内（因为它们也需要被签名）。

如果你使用[electron-packager][electron-packager] 8.1.0 之前的版本，则需在构建步骤中添加[electron-packager][electron-packager]来忽略这些文件。

8.1.0及更高版本已默认忽略这些文件。

### 上传应用

签名完成后，您可以使用Application Loader将其上传到iTunes Connect进行处理，确保您在上传之前已创建了[创建记录][create-record].       

### 检查并提交你的应用

最后, 你可以[检查并提交你的应用][submit-for-review].                 

## MAS 构建限制

为了满足应用程序沙箱的所有要求，MAS构建中已禁用以下模块：

* `crashReporter`
* `autoUpdater`

并且下面的行为也改变了:

* 某些机器的视频采集功能可能无效.
* 某些辅助功能可能无效.
* 应用无法检测 DNS 变化.
* 在登录时启动应用程序的 API 会被禁用。详见[这里](https://github.com/electron/electron/issues/7312#issuecomment-249479237)        

另外,也由于应用沙箱的使用,应用可以访问的资源也被严格限制,更多请详细阅读[App Sandboxing][app-sandboxing]了解更多信息.

### 附加权利

根据应用使用的 Electron API，你可能需要在 `parent.plist` 文件添加附加授权，以便能够从应用程序的Mac App Store构建中使用这些API。

#### 网络访问

启用传出的网络连接，允许你的应用程序连接到服务器：
```xml
<key>com.apple.security.network.client</key>
<true/>
```

启用传入的网络连接，让你的应用程序打开网络 socket 监听:

```xml
<key>com.apple.security.network.server</key>
<true/>
```

详阅更多细节: [启用网络访问文档][network-access]                 

#### dialog.showOpenDialog

```xml
<key>com.apple.security.files.user-selected.read-only</key>
<true/>
```
详阅更多细节: [启用用户选择的文件访问文档][user-selected]                          

#### dialog.showSaveDialog

```xml
<key>com.apple.security.files.user-selected.read-write</key>
<true/>
```

详阅更多细节: [启用用户选择的文件访问文档][user-selected]                           

##  Electron使用的加密算法

根据您所在的国家和地区，Mac App Store可能需要记录应用程序中使用的加密算法，甚至要求您提交美国加密注册（ERN）批准的副本。

Electron 使用下列加密算法：

* AES - [NIST SP 800-38A](http://csrc.nist.gov/publications/nistpubs/800-38a/sp800-38a.pdf), [NIST SP 800-38D](http://csrc.nist.gov/publications/nistpubs/800-38D/SP-800-38D.pdf), [RFC 3394](http://www.ietf.org/rfc/rfc3394.txt)
* HMAC - [FIPS 198-1](http://csrc.nist.gov/publications/fips/fips198-1/FIPS-198-1_final.pdf)
* ECDSA - ANS X9.62–2005
* ECDH - ANS X9.63–2001
* HKDF - [NIST SP 800-56C](http://csrc.nist.gov/publications/nistpubs/800-56C/SP-800-56C.pdf)
* PBKDF2 - [RFC 2898](https://tools.ietf.org/html/rfc2898)
* RSA - [RFC 3447](http://www.ietf.org/rfc/rfc3447)
* SHA - [FIPS 180-4](http://csrc.nist.gov/publications/fips/fips180-4/fips-180-4.pdf)
* Blowfish - https://www.schneier.com/cryptography/blowfish/
* CAST - [RFC 2144](https://tools.ietf.org/html/rfc2144), [RFC 2612](https://tools.ietf.org/html/rfc2612)
* DES - [FIPS 46-3](http://csrc.nist.gov/publications/fips/fips46-3/fips46-3.pdf)
* DH - [RFC 2631](https://tools.ietf.org/html/rfc2631)
* DSA - [ANSI X9.30](http://webstore.ansi.org/RecordDetail.aspx?sku=ANSI+X9.30-1%3A1997)
* EC - [SEC 1](http://www.secg.org/sec1-v2.pdf)
* IDEA - "On the Design and Security of Block Ciphers" book by X. Lai
* MD2 - [RFC 1319](http://tools.ietf.org/html/rfc1319)
* MD4 - [RFC 6150](https://tools.ietf.org/html/rfc6150)
* MD5 - [RFC 1321](https://tools.ietf.org/html/rfc1321)
* MDC2 - [ISO/IEC 10118-2](https://www.openssl.org/docs/manmaster/crypto/mdc2.html)
* RC2 - [RFC 2268](https://tools.ietf.org/html/rfc2268)
* RC4 - [RFC 4345](https://tools.ietf.org/html/rfc4345)
* RC5 - http://people.csail.mit.edu/rivest/Rivest-rc5rev.pdf
* RIPEMD - [ISO/IEC 10118-3](http://webstore.ansi.org/RecordDetail.aspx?sku=ISO%2FIEC%2010118-3:2004)

关于如何获得ERN批准，您可以参考文章：[如何合法地向Apple App Store提交加密的应用程序 (或如何获得ERN)][ern-tutorial].              

[developer-program]: https://developer.apple.com/support/compare-memberships/
[submitting-your-app]: https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/SubmittingYourApp/SubmittingYourApp.html
[nwjs-guide]: https://github.com/nwjs/nw.js/wiki/Mac-App-Store-%28MAS%29-Submission-Guideline#first-steps
[enable-app-sandbox]: https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html
[create-record]: https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/CreatingiTunesConnectRecord.html
[electron-osx-sign]: https://github.com/electron-userland/electron-osx-sign
[electron-packager]: https://github.com/electron-userland/electron-packager
[submit-for-review]: https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/SubmittingTheApp.html
[app-sandboxing]: https://developer.apple.com/app-sandboxing/
[ern-tutorial]: https://carouselapps.com/2015/12/15/legally-submit-app-apples-app-store-uses-encryption-obtain-ern/
[temporary-exception]: https://developer.apple.com/library/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AppSandboxTemporaryExceptionEntitlements.html
[user-selected]: https://developer.apple.com/library/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW6
[network-access]: https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW9
