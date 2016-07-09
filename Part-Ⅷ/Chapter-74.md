# 第七十四章

# 任务管理器上的实用玩笑(Windows Vista)

让我们试一试能否对任务管理器做点微小的修改，让它能够识别出更多的CPU内核。

首先让我们思考一下，任务管理器是怎么知道内核的数量的？

![](img\C74-1.png) 图 74.1: IDA: 对 NtQuerySystemInformation() 的交叉引用
