# 时钟中断函数

{% hint style="info" %}
task2：完成时钟中断处理函数。
{% endhint %}

实验3的主要内容是进程管理，在现阶段的操作系统中，进程切换有两种情况，一个是sleep相关（阻塞 或恢复）；一个是进程时间片用完切换到下一个进程，其中sleep又可以转化为时间片用完，所以时钟中断处理 timerHandle 处是一个绝佳的进行进程切换的场所。时钟中断功能：

1. 遍历pcb，将状态为STATE\_BLOCKED的进程的sleepTime减一，如果进程的sleepTime变为0，重 新设为STATE\_RUNNABLE
2. 将当前进程的timeCount加一，如果时间片用完（timeCount==MAX\_TIME\_COUNT）且有其它 状态为STATE\_RUNNABLE的进程，切换，否则继续执行当前进程

进程切换代码已经在[《基础知识》](../ji-chu-zhi-shi/jin-cheng/jin-cheng-qie-huan-he-dui-zhan-qie-huan.md)部分给出，请理解后填写。
