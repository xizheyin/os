# Debug利器-串口输出

相信很多同学在编程的时候，都会通过printf在屏幕上输出程序的一些信息用于调试。但是现在做操作系统实验，还没有实现printf等屏幕输出函数（实验的内容之一就是实现printf），那么在完成实验之前，如何通过类似手段进行调试呢？不要慌，在lab2的Makefile我们发现这样的内容：

```
play: os.img
	$(QEMU) -serial stdio os.img
```

我相信做过lab1的同学应该已经知道如何搜索“-serial”参数的含义：其中`-serial stdio`表示将qemu模拟的串口数据即时输出到stdio（即宿主机的标准输出）。

在`kernel/main.c`的第一行就是初始化串口设备`initSerial()`，在之后的代码中，我们就可以通过调用`putChar，putStr，putNum`（我们帮你写好的，定义在`kernel/include/device/serial.h`，实现在`kernel/kernel/serial.c`）等串口输出函数进行调试或输出log，同时在框架代码中基于putChar提供了一个调试接口`assert`（定义在`kernel/include/common/assert.h`)

有兴趣的同学可以进一步对`putChar`进行封装，**实现一个类似printf的串口格式化输出`sprintf（不做要求，主要是为了你自己debug方便哈哈）`**。:joy:
