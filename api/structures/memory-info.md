# MemoryInfo Object(内存信息对象)

* `pid` Integer - 进程id
* `workingSetSize` Integer - 当前固定在实际物理RAM中的内存量
* `peakWorkingSetSize` Integer - 已经被固定到实际物理RAM的最大内存量
* `privateBytes` Integer - 其他进程不共享的内存量，如JS或HTML内容
* `sharedBytes` Integer - 进程之间共享的内存量，通常是Electron代码本身消耗的内存

注意,所有单位以Kb为单位