# 真正的Kernel运行了！

{% hint style="info" %}
task2：完成Kernel的初始化，这应该是本实验最简单的地方了。

* [x] 在Kernel的main.c里，TODO标好了。
{% endhint %}

## 跳到Kernel

在加载完Kernel之后，调用kMainEntry（）函数，真正的Kernel运行了！！！

先别高兴地太早啊，你看看Kernel的代码中的main.c。诶？好像没有kMainEntry这个函数。究竟是怎么回事呢？

{% hint style="info" %}
exercise14：请查看Kernel的Makefile里面的链接指令，结合代码说明kMainEntry函数和Kernel的main.c里的kEntry有什么关系。kMainEntry函数究竟是啥？
{% endhint %}

## Kernel的一系列初始化

下面这个部分是相当简单的，请自行查看代码框架，**每个初始化只需要调用一个函数**\~
