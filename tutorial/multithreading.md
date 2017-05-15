# 多线程

使用 [Web Workers][web-workers]可以在系统级线程中运行JavaScript。

## 多线程Node.js

在 `webPreferences`中将 `n​​odeIntegrationInWorker`选项设置为 `true`,可以在Electron的Web Workers中使用Node.js功能:

```javascript
let win = new BrowserWindow({
  webPreferences: {
    nodeIntegrationInWorker: true
  }
})
```

 如果要把 `nodeIntegrationInWorker`独立于 `nodeIntegration` 使用， `sandbox`就不能设置为 `true`。

## 可用API

WebWork虽然支持Node.js的所有内置模块，也可以使用Node.js API读取`asar`档案。但Electron的内置模块并不能在多线程环境中使用。

## 原生的 Node.js 模块


由于大多数现有的原生模块已经被假设为单线程环境，在Web Workers中使用它们可能会导致崩溃和内存损坏,所以强烈建议你不要直接将本地Node.js模块加载到Web Workers中。

还要注意的是,虽然本地Node.js模块是安全的线程，但将其加载到Web中仍然是不安全的,因为 `process.dlopen`功能并不是一个安全线程。

现在安装原生模块的唯一方法是确保应用程序在Web Workers启动后绝不加载本地模块。
```javascript
process.dlopen = () => {
  throw new Error('Load native module is not safe')
}
let worker = new Worker('script.js')
```

[web-workers]: https://developer.mozilla.org/en/docs/Web/API/Web_Workers_API/Using_web_workers