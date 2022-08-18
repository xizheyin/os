# 加载Kernel和用户程序

{% hint style="info" %}
task1：迎来我们第一处代码，已经用TODO标记

* [x] 填写boot.c，加载内核代码。
{% endhint %}

## 用start.S来热身

做实验一定要明白代码运行的全过程，达到庖丁解牛的程度。想要达到这种程度，就通读代码框架！！！

我们打开bootloader/start.S文件夹，我们发现在跳转到bootMain之前，设置了esp寄存器。

{% hint style="info" %}
exercise12：为什么要设置esp？不设置会有什么后果？我是否可以把esp设置成别的呢？请举一个例子，并且解释一下。（你可以在**写完实验确定正确**之后，把此处的esp设置成别的实验一下，看看哪些可以哪些不可以）
{% endhint %}

我们发现，start.S中包含了上次实验的一些代码，同学们也可以通过这个来检查一下上次实验是否正确\~

## 加载Kernel

下面我们来到boot.c，发现有一处TODO。

这里要求我们加载Kernel，有没有人觉得很耳熟？加载......似乎在NEMU里面做过......是的，不过即使做过也不一定在这里会写对......还是认真复习和思考一下\~

请翻阅前面的基础知识，思考如何利用readSect函数和后续的一些操作把Kernel加载到内存，并将控制权交给Kernel。这里有几个提示：

* 请翻阅Kernel的Makefile（**井号的意思是注释**，不要以为每一句都执行）或者简单编译一下Kernel并反汇编，看看Kernel的代码段是从哪里开始的。
* 解析ELF文件需要的结构体已经在boot.h里给出。

## 一个很离奇的问题

会不会有同学已经在github上面进行了ctrl+c的操作呢？在你ctrl+v之前，我要提醒一下，**github上很多写法都是错误的，但是却阴差阳错的能够运行（意思是，如果你抄往年代码很容易被发现）**。下面这段代码就是错误的：

```
	// TODO: 填写kMainEntry、phoff、offset
	ELFHeader* eh=(ELFHeader*)elf;
	kMainEntry=(void(*)(void))(eh->entry);
	phoff=eh->phoff;
	ProgramHeader* ph=(ProgramHeader*)(elf+phoff);
	offset=ph->off;

	for (i = 0; i < 200 * 512; i++) {
		*(unsigned char *)(elf + i) = *(unsigned char *)(elf + i + offset);
	}
	kMainEntry();
```

下面提问两个问题：

{% hint style="info" %}
exercise13：上面那样写为什么错了？
{% endhint %}

{% hint style="danger" %}
challenge1：既然错了，为什么不影响实验代码运行呢？

这个问题设置为challenge说明它比较难，回答这个问题需要对ELF加载有一定掌握，并且愿意动手去探索。Hint：可以在写完所有内容并保证正确后，改成这段错误代码，进行探索，并回答该问题。（做出来加分，做不出来不扣分）
{% endhint %}
