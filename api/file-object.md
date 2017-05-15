# 本文介绍: `File` 的应用(本地文件操作接口)

>使用H5的 `File` API操作本地文件

Electron增添了 `path`属性的 `File`接口,用来获得文件真实路径。

拖入文件到app并显示真实路径的例子:

```html
<div id=`holder`>
拖拽文件到这里
</div>

<script>
  const holder = document.getElementById('holder')
  holder.ondragover = () => {
    return false;
  }
  holder.ondragleave = holder.ondragend = () => {
    return false;
  }
  holder.ondrop = (e) => {
    e.preventDefault()
    for (let f of e.dataTransfer.files) {
      console.log('File(s) you dragged here: ', f.path)
    }
    return false;
  }
</script>
```