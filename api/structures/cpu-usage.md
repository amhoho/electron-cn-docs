# CPUUsage Object(CPU使用率对象即程序占用的CPU资源)

* `percentCPUUsage` Number - 上一次调用getCPUUsage之后使用的CPU占比。
* `idleWakeupsPerSecond` Number - 上一次调用getCPUUsage之后每秒钟的平均空闲cpu唤醒次数。

以上两个参数首次获取均默认返回 `0`.