# Task Object(任务对象)

* `program` String  - 要执行的程序的路径,通常你应该指定`process.execPath`打开当前程序。
* `arguments` String  - 执行`program`时的命令行参数。
* `title` String  - 要在JumpList中显示的字符串。
* `description` String  - 此任务的描述。
* `iconPath` String  - 要在JumpList中显示的图标的绝对路径,它可以是包含图标的任意资源文件。你通常可以指定`process.execPath`来显示程序的图标。
* `iconIndex` Number  - 图标文件中的图标索引。如果图标文件由两个或多个图标组成,请设置此值以标识图标。如果图标文件由一个图标组成,则此值为0。