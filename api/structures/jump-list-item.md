# JumpListItem Object(常用列表项)

* `type` String(可选) - 以下之一:
  * `task`  - 启动具有特定参数的应用程序的任务。
  * `separator`  - 用来分隔标准 `Tasks`类别中的项目。
  * `file`  - 使用创建常用列表的应用程序打开的文件链接,为了这个工作应用程序必须注册为文件类型的处理程序(尽管它不必是默认处理程序) 。
* `path` String(可选) - 要打开的文件的路径, `type`是 `file`时才应该设置。
* `program` String(可选) - 要执行的程序的路径,通常你应该指定` process.execPath`打开当前程序。`type`是 `task`时才应该设置。
* `args` String(可选) - 执行 `program`时的命令行参数。`type`是 `task`时才应该设置。
* `title` String(可选) - 要在常用列表中显示的项目文本。`type`是 `task`时才应该设置。
* `description` String(可选) - 任务描述(显示在工具提示中)。`type`是 `task`时才应该设置。
* `iconPath` String(可选) - 要在常用列表中显示的图标的绝对路径,它可以是包含图标的任意资源文件(例如 `.ico`, `.exe`, `.dll`)。你通常可以指定 `process.execPath`来显示程序图标。
* `iconIndex` Number(可选) - 资源文件中图标的索引。如果资源文件包含多个图标,则此值可用于指定应为此任务显示的图标的从零开始的索引。如果资源文件只包含一个图标,则此属性应设置为`0`。