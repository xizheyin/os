# 完善中断处理部分

{% hint style="info" %}
task4：填充中断处理程序，保证系统在发生中断是能合理处理

* [x] irqHandle通过相应的irq号跳转到对应的Handle函数。
{% endhint %}

我们打开irqHandle.c，这里是中断处理的部分。

我们只需要填充一个函数即可。但是对于代码的其他部分，需要同学们去理解。

{% hint style="info" %}
exercise19：请看syscallPrint函数的第一行：

int sel = USEL(SEG\_UDATA);

请说说sel变量有什么用。（请自行搜索）
{% endhint %}

{% hint style="danger" %}
challenge2：比较关键的几个函数

1. KeyboardHandle函数是处理键盘中断的函数
2. syscallPrint函数是对应于“写”系统调用
3. syscallGetChar和syscallGetStr对应于“read”系统调用

有以下两个问题

1. 请解释框架代码在干什么。
2. 阅读前面人写的烂代码是我们专业同学必备的技能。很多同学会觉得这三个函数给的框架代码写的非常烂，你是否有更好的实现方法？可以替换掉它（此题目难度很大，修改哪个都行）。这一问可以不做，但是如果有同学实现得好，可能会有隐藏奖励（也可能没有）。


{% endhint %}
