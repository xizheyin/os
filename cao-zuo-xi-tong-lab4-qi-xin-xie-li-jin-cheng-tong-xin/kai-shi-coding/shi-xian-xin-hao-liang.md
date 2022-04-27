# 实现信号量

{% hint style="info" %}
task2：实现下面四个函数
{% endhint %}

这一部分也只需要完善处理例程，其它部分已经实现，所有的信号量相关调用有一个总的处理：

```
void syscallSem(struct StackFrame *sf) {
	switch(sf->ecx) {
		case SEM_INIT:
			syscallSemInit(sf);
			break;
		case SEM_WAIT:
			syscallSemWait(sf);
			break;
		case SEM_POST:
			syscallSemPost(sf);
			break;
		case SEM_DESTROY:
			syscallSemDestroy(sf);
			break;
		default:break;
	}
}
```

需要完成的是4个子例程：`syscallSemInit`、`syscallSemWait`、`syscallSemPost`和`syscallSemDestroy`

在实现时，因为信号量以数组形式存在，所以只要一个**下标**就可以定位信号量

完成后请进行测试，并在实验报告中展示结果
