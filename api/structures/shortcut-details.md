# ShortcutDetails Object(快捷方式对象)

* `target` String  - 从这个快捷方式启动的目标。
* `cwd` String(可选) - 工作目录。默认值为空。
* `args` String(可选) - 从此快捷方式启动时应用于`target'的参数。默认值为空。
* `description` String(可选) - 快捷方式的描述。默认值为空。
* `icon` String(可选) - 图标的路径,可以是DLL或EXE。 `icon`和`iconIndex`必须设置在一起。默认值为空,它使用目标的图标。
* `iconIndex` Number(可选) - 当`icon`是DLL或EXE时,图标的资源ID。默认值为0。
* `appUserModelId` String(可选) - 应用程序用户模型ID。默认值为空。