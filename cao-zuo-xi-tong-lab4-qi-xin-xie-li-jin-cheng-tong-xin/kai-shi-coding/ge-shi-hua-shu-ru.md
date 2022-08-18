# 格式化输入

{% hint style="info" %}
task1：完成格式化输入
{% endhint %}

为了降低实验难度，`syscall.c`中的`scanf`已经完成，同学们只需要完成对应的中断处理例程

irqHandle.c中添加了syscallRead函数处理各种读数据：

```
void syscallRead(struct StackFrame *sf) {
	switch(sf->ecx) {
		case STD_IN:
			if (dev[STD_IN].state == 1)
				syscallReadStdIn(sf);
			break; // for STD_IN
		default:
			break;
	}
}
```

在这一节主要关注的就是`syscallReadStdIn`，同学们需要去完成它，那么如何完成呢，它是和键盘中断有条件同步的，所以的这一步还要结合`keyboardHandle`一起完成

在实验2中，有很多同学不知道下面的代码是干啥的

```
extern uint32_t keyBuffer[MAX_KEYBUFFER_SIZE];
extern int bufferHead;
extern int bufferTail;
```

其实这就是键盘输入的缓冲区，把所有零碎的知识拼凑在一起，`keyboardHandle`要做的事情就两件：

1. 将读取到的`keyCode`放入到`keyBuffer`中
2. 唤醒阻塞在`dev[STD_IN]`上的一个进程

接下来安排`syscallReadStdIn`，它要做的事情也就两件：

1. 如果`dev[STD_IN].value == 0`，将当前进程阻塞在`dev[STD_IN]`上
2. 进程被唤醒，读`keyBuffer`中的所有数据

值得注意的就是最多只能有一个进程被阻塞在`dev[STD_IN]`上，多个进程想读，那么后来的进程会返回`-1`，其他情况`scanf`的返回值应该是实际读取的字节数

和实验2中printf的处理例程类似，以下代码可以将读取的字符`character`传到用户进程

```
int sel = sf->ds;
char *str = (char *)sf->edx;
int i = 0;
asm volatile("movw %0, %%es"::"m"(sel));
asm volatile("movb %0, %%es:(%1)"::"r"(character),"r"(str + i));
```

完成这一步后请测试`scanf`，并在实验报告展示结果
