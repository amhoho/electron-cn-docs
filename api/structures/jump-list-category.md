# JumpListCategory Object( 最近使用的项目)

* `type` String(可选) - 以下之一:
  * `tasks`  - 此类别中的项目将被放置到标准的 `Tasks` 类别中。只能有一个这样的类别而且总是显示在跳转列表的底部。
  * `frequent`  - 显示由应用程序频繁打开的文件的列表,类别的名称及其项目由Windows设置。
  * `recent`  - 显示由应用程序最近打开的文件的列表,类别的名称及其项目由Windows设置。项目可以使用`app.addRecentDocument(path)`间接添加到此类别。
  * `custom`  - 显示任务或文件链接, `name`必须由应用程序设置。
* `name` String(可选) - 如果 `type`是 `custom`,则必须设置,否则应该省略。
* `items` JumpListItem [](可选) -   [`JumpListItem`](jump-list-item.md) 对象的数组,如果 `type`不是 `tasks`或 `custom`则忽略

 **注意:**
 
如果 `JumpListCategory`对象没有 `type`和 `name`属性,那么它的 `type`被假定为 `tasks`。

如果设置了 `name`属性,但省略了 `type`属性,则 `type`被假定为 `custom`。