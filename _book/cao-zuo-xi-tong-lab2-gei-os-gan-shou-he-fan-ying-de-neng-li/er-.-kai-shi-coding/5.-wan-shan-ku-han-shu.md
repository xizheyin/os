# 完善库函数

{% hint style="info" %}
task5：完成lib/syscall.c（不是kernel的lib，是外部库函数）里的库函数，分别对应输入和输出函数。

* [x] 完成getChar
* [x] 完成getStr
* [x] 完成printf

提示：他们都要调用syscall函数。不要忘记getChar有返回值。
{% endhint %}

相信大家在学习ICS的时候都背过“scanf或printf执行的过程”吧？下面我们就可以亲身体会了。这一部分，我不会逐行代码去解释，但是会给予适当提示，更多需要自己去阅读。

## 完成printf的格式化输出

还记得C语言的printf吧，我们现在要通过系统调用实现自己的printf。在框架代码中已经提供了`printf`最基本的功能，我们要做的是：对格式化输出进行解析和处理，比如：printf(“sum=%d”，a);

对于下面的框架代码，我给予一些提示：

* buffer是一个临时存放缓冲区，最后通过syscall来统一输出到标准输出上。
* count用来对缓冲区里存放的字符进行计数。
*   index是指printf函数参数的索引，注意printf的第一个参数是一个格式化字符串，后面是格式字符对应的变量。

    ```
    printf("<格式化字符串>", <参量表>);
    ```
* paraList是一个很关键的点，需要你自己去想，请认真思考并完成下面的exercise。
* state是状态机的状态，我们这里给出三个状态：
  * 0代表合法的字面字符。
  * 1代表遇到个格式字符，这时需要进行解析出`%d`, `%x`, `%s`,`%c`四个中的一个。
  * 2代表非法字符（非法字符处理不必实现，测试样例中没有对非法字符的处理）。

```c
void printf(const char *format,...){
	int i=0; // format index
	char buffer[MAX_BUFFER_SIZE];
	int count=0; // buffer index
	int index=0; // parameter index
	void *paraList=(void*)&format; // address of format in stack
	int state=0; // 0: legal character; 1: '%'; 2: illegal format
	int decimal=0;
	uint32_t hexadecimal=0;
	char *string=0;
	char character=0;
	while(format[i]!=0){
		buffer[count]=format[i];
		count++;
		//TODO in lab2
		...
	}
	if(count!=0)
		syscall(SYS_WRITE, STD_OUT, (uint32_t)buffer, (uint32_t)count, 0, 0);
}
```

为了方便使用, 我们需要**实现`%d`, `%x`, `%s`,`%c`四种格式转换说明符**, 如果你不清楚它们的含义, 请自行查阅相关资料。

我们已经帮忙实现好了几个辅助函数：dec2Str、hex2Str、str2Str。他们的作用应该很清楚了，“2”的意思是“to”，比如dec2Str，实际上就是把整数转化成字符串填充在buffer里面。

这里有一个比较困难的点：

{% hint style="info" %}
exercise20：paraList是printf的参数，为什么初始值设置为\&format？

假设我调用**`printf("%d = %d + %d", 3, 2, 1);`**，那么数字2的地址应该是多少？

所以当我解析format遇到%的时候，需要怎么做？
{% endhint %}

{% hint style="info" %}
exercise21：关于系统调用号，我们在printf的实现里给出了样例，请找到阅读这一行代码

syscall(SYS\_WRITE, STD\_OUT, (uint32\_t)buffer, (uint32\_t)count, 0, 0);

说一说这些参数都是什么（比如SYS\_WRITE在什么时候会用到，又是怎么传递到需要的地方呢？）。
{% endhint %}

## 实现`getChar`, `getStr`的处理例程 <a href="#44-e5-ae-9e-e7-8e-b0getchar-getstr-e7-9a-84-e5-a4-84-e7-90-86-e4-be-8b-e7-a8-8b" id="44-e5-ae-9e-e7-8e-b0getchar-getstr-e7-9a-84-e5-a4-84-e7-90-86-e4-be-8b-e7-a8-8b"></a>

这两个函数

这两个函数的处理方式比较灵活，`getChar`相对来说要容易一些，你可以等按键输入完成的时候，将末尾字符通过eax寄存器传递回来。需要注意的是，在用户态向内核态传递参数的过程中，eax既扮演了参数的角色，又扮演了返回值的角色。这一段在汇编代码lab2/lib/syscall.c中有体现

```
asm volatile("int $0x80");
asm volatile("movl %%eax, %0":"=m"(ret));
```

`getStr`的实现要更为麻烦一些，涉及到字符串的传递。我们要知道的是，即使采用了通用寄存器做为中间桥梁，字符串地址作为参数仍然涉及到了不同的数据段。实际上，在处理printf的时候我们就遇见过这个问题，它的解决方式是，在内核态进行了段描述子的切换。内核态可以访问用户态的数据段，但是用户态不可以越级访问内核态的数据。这就要求我们不能采用把内核获取的显存字符串地址传出来，而得是在内核态提前开辟一块字符串地址，将这个地址传进内核态。

当然，`getStr`也可以不局限于这一种实现思路，能奏效的实现方式都被接受。

{% hint style="info" %}
exercise22：记得前面关于串口输出的地方也有getChar和getStr吗？这里的两个函数和串口那里的两个函数有什么区别？请详细描述它们分别在什么时候能用。
{% endhint %}

## 格式化输入scanf

{% hint style="danger" %}
challenge3：如果你读懂了系统调用的实现，请仿照printf，实现一个scanf库函数。并在app里面编写代码自行测试。最后录一个视频，展示你的scanf。（写出来加分，不写不扣分）
{% endhint %}

## 参考资料

在这里要读懂内联汇编，我们提供一些[内联汇编教程](https://blog.csdn.net/lyndon\_li/article/details/118471845)。
