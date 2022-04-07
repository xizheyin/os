# 直观感受

我们先来看看测试用例是什么样的（exec没有放上去，可以自己加上去）。

```
int uEntry(void) {
	int data = 0;	
	
	int ret = fork();
	int i = 8;
	if (ret == 0) {
		data = 2;
		while( i != 0) {
			i --;
			printf("Child Process: Pong %d, %d;\n", data, i);
			//sleep(1);
			sleep(128);
		}
		exit();
	}
	else if (ret != -1) {
		data = 1;
		while( i != 0) {
			i --;
			printf("Father Process: Ping %d, %d;\n", data, i);
			//sleep(1);
			sleep(128);
		}
		exit();
	}

	return 0;
}

```

这段代码的执行过程如下：

1. 先fork出一个子进程，在代码里我们叫它Child Process
2. 这时，父子进程就是并发进程：
   1. 子进程每输出一个i，就会通过sleep函数睡眠一段时间。
   2. 父进程每输出一个i，就会通过sleep函数睡眠一段时间。
3. 在父子进程全部减为0之后，两个进程就会执行exit函数，结束并释放自己！

我们根据上面猜想，父子进程可能会每隔一段时间同时输出同一个i的值，并且逐步递减，直到为0......

当然，瞎猜没用！

{% hint style="info" %}
exercise1：请把上面的程序，用gcc编译，在你的Linux上面运行，看看真实结果是啥（有一些细节需要改，请根据实际情况进行更改）。
{% endhint %}
