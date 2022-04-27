# 数据结构变化

在攻略之前，先带大家看一看实验4新增的或修改的数据结构等：

```c
struct Semaphore {
	int state;
	int value;
	struct ListHead pcb; // link to all pcb ListHead blocked on this semaphore
};
typedef struct Semaphore Semaphore;

struct Device {
	int state;
	int value;
	struct ListHead pcb; // link to all pcb ListHead blocked on this device
};
typedef struct Device Device;

Semaphore sem[MAX_SEM_NUM];
Device dev[MAX_DEV_NUM];
```

信号量`Semaphore`相信看完**相关资料**，大家都能理解，因为我们将信号量的定义成数组，所以添加了一个`state`成员，表示当前信号量是不是正在使用，1表示正在使用，0表示未使用

那么`Device`是干啥用的，为什么和信号量的定义这么相似，说这个之前我们需要想一下stdin标准输入，在操作系统中，我们不可能通过一直监听键盘中断来进行输入，这样太浪费系统资源了，所以我们需要一个键盘输入缓冲区和类似信号量的东西来实现条件同步，在键盘中断将输入存入缓冲区后再让用户程序读取，所以代码中定义了`Device`，他其实就是信号量，只不过不能由用户通过系统调用控制，而是直接和硬件绑定

在实验中，我们将stdin，stdout都抽象成了`Device`，其中

```c
#define STD_OUT 0
#define STD_IN 1
```

实际上，stdout是非阻塞式的，stdin上才会有进程阻塞。

```c
struct ListHead {
	struct ListHead *next;
	struct ListHead *prev;
};
```

`ListHead`是一个**头插法双向循环链表**（之前学的数据结构在这里有用了）。

如下两个函数用于初始化`sem`和`dev`

```c
void initSem() {
	int i;
	for (i = 0; i < MAX_SEM_NUM; i++) {
		sem[i].state = 0; // 0: not in use; 1: in use;
		sem[i].value = 0; // >=0: no process blocked; -1: 1 process blocked; -2: 2 process blocked;...
		sem[i].pcb.next = &(sem[i].pcb);
		sem[i].pcb.prev = &(sem[i].pcb);
	}
}

void initDev() {
	int i;
	for (i = 0; i < MAX_DEV_NUM; i++) {
		dev[i].state = 1; // 0: not in use; 1: in use;
		dev[i].value = 0; // >=0: no blocked; -1: 1 process blocked; -2: 2 process blocked;...
		dev[i].pcb.next = &(dev[i].pcb);
		dev[i].pcb.prev = &(dev[i].pcb);
	}
}
```

**修改的数据结构**：PCB中添加对应的双向链表结构

```c
 struct ProcessTable {
 	uint32_t stack[MAX_STACK_SIZE];
 	struct TrapFrame regs;
 	uint32_t stackTop;
 	uint32_t prevStackTop;
 	int state;
 	int timeCount;
 	int sleepTime;
 	uint32_t pid;
 	char name[32];
+	struct ListHead blocked; // sempahore, device, file blocked on
 };
 typedef struct ProcessTable ProcessTable;
```

这样将current线程加到信号量i的阻塞列表可以通过以下代码实现

```c
		pcb[current].blocked.next = sem[i].pcb.next;
		pcb[current].blocked.prev = &(sem[i].pcb);
		sem[i].pcb.next = &(pcb[current].blocked);
		(pcb[current].blocked.next)->prev = &(pcb[current].blocked);
```

以下代码可以从信号量i上阻塞的进程列表取出一个进程：

```c
		pt = (ProcessTable*)((uint32_t)(sem[i].pcb.prev) -
					(uint32_t)&(((ProcessTable*)0)->blocked));
		sem[i].pcb.prev = (sem[i].pcb.prev)->prev;
		(sem[i].pcb.prev)->next = &(sem[i].pcb);
```

irqHandle.c中的syscallWrite也有一些**变化**：

```c
 void syscallWrite(struct TrapFrame *sf) {
 	switch(sf->ecx) { // file descriptor
-		case 0:
-			syscallPrint(sf);
+		case STD_OUT:
+			if (dev[STD_OUT].state == 1) {
+			syscallWriteStdOut(sf);
+			}
 			break; // for STD_OUT
 		default:break;
 	}
 }
```

另外对syscallPrint进行了重命名

```
-void syscallPrint(struct TrapFrame *sf) {
+void syscallWriteStdOut(struct TrapFrame *sf) {
```

