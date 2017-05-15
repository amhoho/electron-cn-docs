# 使用 Node 原生模块

尽管Electron支持Node 原生模块, 但Electron与Node的V8版本可能不同，所以需要在构建原生模块时指定 Electron headers的位置。

## 如何安装原生模块?

有以下三种方法:

### 第一种: 使用 `npm`

只需设置少量的系统环境变量, 你就可以使用 `npm`直接安装原生模块.

为 Electron 安装所有依赖项的一个例子:

 ```bash
# Electron 的版本.
export npm_config_target=1.2.3
# Electron 的系统架构, 值为 ia32 或 x64.
export npm_config_arch=x64
export npm_config_target_arch=x64
# 下载 Electron 的 headers.
export npm_config_disturl=https://atom.io/download/electron
# 告诉 node-pre-gyp 是为 Electron 构建.
export npm_config_runtime=electron
# 告诉 node-pre-gyp 从源代码构建模块.
export npm_config_build_from_source=true
# 下载所有依赖，并缓存到 ~/.electron-gyp.
HOME=~/.electron-gyp npm install
 ```

### 第二种: 下载并重新编译模块

你可以像普通的Node项目一样下载并安装模块, 然后通过 [`electron-rebuild`][electron-rebuild] 包重新编译原生模块,它帮你自动完成了下载 headers, 编译原生模块等步骤.

安装了 `electron-rebuild`并利用它重建模块的示例:

```bash
npm install --save-dev electron-rebuild

# 每次运行"npm install"时，也运行这条命令:
./node_modules/.bin/electron-rebuild

# 在windows下如果上述命令遇到了问题，尝试这个:
.\node_modules\.bin\electron-rebuild.cmd
```

### 第三种: 手动编译

如果您是原生模块的作者并希望在Electron中进行测试，那么你可能需要手动重编译该Electron模块。 您可以直接使用 `node-gyp`来构建Electron:
 ```bash
cd /path-to-module/
HOME=~/.electron-gyp node-gyp rebuild --target=1.2.3 --arch=x64 --dist-url=https://atom.io/download/electron
 ```
 
- `HOME=~/.electron-gyp`  : 开发时的headers的位置. 
- `--target=1.2.3` : Electron的版本. 
- `--dist-url=...` : Electron 的 headers 的下载地址. 
- `--arch=x64`: 模块为适配64位操作系统而编译.

## 故障排除

你安装了原生模块后无法工作时,请尝试检查以下事项:

* 模块的架构必须与Electron的架构(ia32或x64)相匹配.
* 当你升级Electron后，通常也需要重编译模块.
* 有疑问时,先运行 `electron-rebuild` 试试.

## `prebuild` 依赖模块

[`prebuild`](https://github.com/mafintosh/prebuild) 为多个版本的 Node 和 Electron 提供了一种简单发布预编译二进制原生模块的方法。

如果为 Electron 提供二进制原生模块，请确保删除 `--build-from-source` 和 `npm_config_build_from_source` 环境变量来充分利用预编译的二进制文件。

## `node-pre-gyp` 依赖模块

[`node-pre-gyp` 工具][node-pre-gyp] 提供一种部署原生 Node 预编译二进制模块的方法，很多常用模块都是使用它。

通常这些模块在 Electron 中正常工作，但当 Electron 使用比 Node 更新的 V8 版本时，ABI会被更改并引起错误 。所以一般建议从源代码编译原生模块。

如果你通过 `npm` 的方式安装模块，默认情况下这就完成了

如果没有，你需要传入 `--build-from-source` 给 `npm`, 或者设置 `npm_config_build_from_source` 环境变量。

[electron-rebuild]: https://github.com/paulcbetts/electron-rebuild
[node-pre-gyp]: https://github.com/mapbox/node-pre-gyp
