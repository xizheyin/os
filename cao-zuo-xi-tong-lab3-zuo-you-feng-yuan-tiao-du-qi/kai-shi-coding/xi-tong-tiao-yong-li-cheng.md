# 系统调用例程

{% hint style="info" %}
task3：完成系统调用处理函数。

* [x] syscallFork
* [x] syscallExec
* [x] syscallSleep
* [x] syscallExit
{% endhint %}

## syscallFork

syscallFork要做的是在寻找一个空闲的pcb做为子进程的进程控制块，将父进程的资源复制给子进程。 如果没有空闲pcb，则fork失败，父进程返回-1，成功则子进程返回0，父进程返回子进程pid

在处理fork时有以下几点注意事项：

1. 代码段和数据段可以按照进行完全拷贝
2. pcb的复制时，需要考虑哪些内容可以直接复制，哪些内容通过计算得到，哪些内容和父进程无关
3. 返回值放在哪&#x20;

Hint： initProc 中有初始化 pcb\[0] 和 pcb\[1] 的经验可供参考。

## syscallExec

调用loadelf把新的程序加载到当前用户进程的地址空间，然后返回0即可（用eax装返回值）。

{% hint style="info" %}
exercise9：请问，我使用loadelf把程序装载到当前用户程序的地址空间，那不会把我loadelf函数的代码覆盖掉吗？（很傻的问题，但是容易产生疑惑）
{% endhint %}

## syscallSleep&#x20;

将当前的进程的sleepTime设置为传入的参数，将当前进程的状态设置为STATE\_BLOCKED，然后利用&#x20;

```
asm volatile("int $0x20");
```

模拟时钟中断，利用 timerHandle 进行进程切换 需要注意的是判断传入参数的合法性

## syscallExit

将当前进程的状态设置为STATE\_DEAD，然后模拟时钟中断进行进程切换。
