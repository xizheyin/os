# 进程控制块

## 数据结构

在本课程和其它课程中，大家应该对进程控制块（Process Control Block，PCB）有了一定的了解，实验3开始操作系统提供对多进程的支 持，简单介绍一下lab3中的进程控制块。

```
struct ProcessTable {
	uint32_t stack[MAX_STACK_SIZE]; //内核堆栈
	struct StackFrame regs;		//陷阱帧，保存上下文信息
	uint32_t stackTop;		//栈顶信息
	uint32_t prevStackTop;		//嵌套时保存待恢复的栈顶信息
	int state;			//进程状态，就是上一节提到的
	int timeCount;			//时间片
	int sleepTime;			//需要阻塞的时间
	uint32_t pid;			//进程唯一标识，进程号
	char name[32];			//暂时用不到
};
```

对于实验二的TrapFrame，我们变成了StackFrame。

```
struct TrapFrame {
    uint32_t gs, fs, es, ds;
    uint32_t edi, esi, ebp, xxx, ebx, edx, ecx, eax;
    uint32_t irq, error;
    uint32_t eip, cs, eflags, esp, ss;
};
```

可以看出，StackFrame保存了陷入中断之前程序的状态（主要是硬件保存的内容）。

## 组织数据结构

### PCB数组

对于每个进程都会有一个进程控制块PCB。那么如何组织这个数据结构？

为了快速访问，我们可以把它设置成一个数组，如下：

```
struct ProcessTable pcb[MAX_PCB_NUM];
```

这样就可以通过PID作为索引，用数组下标快速访问相应的进程控制块。内核进程会占据0号pcb，剩下的分配给用户进程。同样为了简单，我们默认每个pcb 对应进程的内存空间固定， pcb\[i] 对应的进程内存起始地址为 (i + 1) \* 0x100000 ，大小为 0x100000

### 关于管理空闲进程

{% hint style="info" %}
exercise3：我们考虑这样一个问题：假如我们的系统中预留了100个进程，系统中运行了50个进程，其中某些结束了运行。这时我们又有一个进程想要开始运行（即需要分配给它一个空闲的PCB），那么如何能够以O(1)的时间和O(n)的空间快速地找到这样一个空闲PCB呢？&#x20;
{% endhint %}

为了回答上面的问题，这里给出一个时间复杂度O(n)的朴素做法。

```
for(int i = 0; i < MAX_PCB_NUM; i++){
    if(pcb[i]空闲){
        把pcb[i]分配给需要的进程;
        break;
    }
}
```
