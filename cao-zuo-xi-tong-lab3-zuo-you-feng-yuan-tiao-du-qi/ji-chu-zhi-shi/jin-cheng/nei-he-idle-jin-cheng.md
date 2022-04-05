# 内核IDLE进程

我们知道，如果有处于RUNNABLE状态的用户进程，我们就可以切换过去；但若没有处于 RUNNABLE 状态的进程可供切换，则需要切换至以下**内核IDLE进程（进程0）**，该进程调用 waitForInterrupt() 执行 hlt 指令， hlt 会使得CPU进入暂停状态（而不是一直空转），直到外部硬件中断产生，在实验 中不需要手动创建IDLE进程，还记得系统启动的执行流吗？执行流在bootloader中加载并跳转到内核， 然后执行一系列的初始化工作，等到初始化结束后将会打开中断，此时执行流摇身一变，成为了实验中 的IDLE进程，等待中断的到来。

```c
static inline void waitForInterrupt() {
    asm volatile("hlt");
}
...
while(1) {
    waitForInterrupt();
}
```
