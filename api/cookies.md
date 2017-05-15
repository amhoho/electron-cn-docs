# 本文介绍:Cookies类

> 会话(Cookie)查询与修改操作类 

进程: [主进程](../glossary.md#主进程)                                                                          

```JavaScript
const {session} = require('electron')
//查询所有Cookie。
session.defaultSession.cookies.get({}, (error, cookies) => {
  console.log(error, cookies)
})
//查询与特定网址相关联的所有Cookie。
session.defaultSession.cookies.get({url: 'http://www.github.com'}, (error, cookies) => {
  console.log(error, cookies)
})
//设置具有给定Cookie数据的Cookie;
//如果存在相同的Cookie,则覆盖。
const cookie = {url: 'http://www.github.com', name: 'dummy_name', value: 'dummy'}
session.defaultSession.cookies.set(cookie, (error) => {
  if (error) console.error(error)
})
```

## 实例事件

#### 事件: 'changed'
> 触发:**当Cookie因增加,编辑,删除,过期等改变时**

* `event` Event
* `cookie` [Cookie](structures/cookie.md) - 被改变的Cookie
* `cause` String - 修改方式,可选以下:
  * `explicit` - 直接改变
  * `overwrite` - 自动擦除并覆盖
  * `expired` - 过期后自动删除
  * `evicted` - 跟随垃圾收集过程销毁
  * `expired-overwrite` - 用已过期的数据覆盖
* `removed` Boolean - `true` 已删除, `false` 其它.

## 实例方法

#### `cookies.get(filter, callback)`
> 用途:**获取所有匹配 `filter`的Cookie**

* `filter` Object
  * `url` String (可选) -关联Cookie的URL. 不存在则所有网址的Cookie。
  * `name` String (可选) - 按名称过滤Cookie。
  * `domain` String (可选) - 按 `domains`域或子域检索Cookie。
  * `path` String (可选) - 按 `path`路径检索Cookie。
  * `secure` Boolean (可选) -按照其Secure属性排序
  * `session` Boolean (可选) - 排除会话或持久Cookie
* `callback` Function
  * `error` Error
  * `cookies` Cookies[] - [`cookie`](structures/cookie.md) 对象的数组。

当 `callback(error, cookies)` 完成后调用 `callback`

#### `cookies.set(details, callback)`
> 用途:**用 `details`创建或设置一个Cookie**

* `details`对象
  * `url` String - 关联Cookie的URL。
  * `name` String(可选) - Cookie的名称。如果省略,默认为空。
  * `value` String(可选) - Cookie的值。如果省略,默认为空。
  * `domain` String(可选) - Cookie的域。如果省略,默认为空。
  * `path` String(可选) - Cookie的路径。如果省略,默认为空。
  * `secure` Boolean(可选) - Cookie是否应标记为安全。默认 为`false`。
  * `httpOnly` Boolean(可选) - Cookie是否应标记为仅HTTP。默认为 `false`。
  * `expirationDate` Double (可选) - Cookie的过期日期(秒级)。如果省略,则Cookie成为临时会话。
* `callback` Function
  * `error` Error
  
当 `callback(error)`完成后,用 `details`, `callback`设置Cookie

#### `cookies.remove(url, name, callback)`
> 用途:**删除匹配 `url`和 `name`的Cookie**

* `url` String - 关联Cookie的URL。
* `name` String - 要删除的Cookie的名称。
* `callback` Function

当 `callback()` 完成后,用 `url` 和 `name`, `callback`删除对应的Cookie

#### `cookies.flushStore(callback)`
> 用途:**清理存储数据,将未写入的Cookie数据写到磁盘中**

* `callback` Function