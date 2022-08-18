# 🧐 开启保护模式

### 实验任务

{% hint style="info" %}
task1：以下任务点是我们在本节需要完成的（代码中已通过TODO注释）

* [x] 把cr0的低位设置为1。
* [x] 填写GDT。
* [x] 显示helloworld。
{% endhint %}

### 框架理解

我们先要开启保护模式，现在我们可以打开给出的代码文件。

文件的结构是这样的：

* bootloader（这个文件夹编译生成bootloader）
  * start.s（通过它开启保护模式）
  * boot.c（通过它来加载app，app即我们的微型“OS”）
  * boot.h（包含辅助函数，与硬件交互，了解即可，不必掌握）
  * Makefile（编译bootloader的makefile）
* app（微型os）
  * app.s（显示hello，world）
  * Makefile（编译app）
* utils
  * genboot.pl
* Makefile（总体的makefile，生成os.img）

我们目前需要修改的文件是**start.s。**

为了开启保护模式，我们要做如下几步：

1. 关中断
2. 开启A20地址线（请自行搜搜看A20地址线是什么）
3. 加载GDTR（请看一下代码中的gdtDesc是什么）
4. 把cr0的最低位设置成1（翻阅一下前面的教程，会告诉你为什么要置为1）
5. 长跳转切换到保护模式

对于长跳转指令，不知道大家还有没有印象。它有两个操作数，第一个操作数是**代码段选择子**，第二个操作数是**跳转地址**，具体是做什么的，请回顾PA学过的内容\~😎

我们需要实现的地方只有三处：

* 把cr0的最低位置为1
  * 思路：可以借助eax作为中转寄存器，先把cr0存入eax，然后把eax最低位置为1，最后存回cr0
  * 当然，如果有自己的思路更好
* 填写GDT
  * GDT的第一个描述符都是0，请自行搜索为什么。
  * base和limit的大小请参考Linux的实现。
  * 请先思考，**为什么我要把三个段描述符按照cs，ds，gs的顺序排列？（Hint：结合汇编代码，看看段选择子是多少**[#duan-xuan-ze-zi-ru-he-cha-zhao-duan-miao-shu-fu](../../ji-chu-zhi-shi/ia32-de-cun-chu-guan-li/bao-hu-mo-shi.md#duan-xuan-ze-zi-ru-he-cha-zhao-duan-miao-shu-fu "mention")**）**
  * 在注释里我已经把公式给出，请结合[#gdt](../../ji-chu-zhi-shi/ia32-de-cun-chu-guan-li/bao-hu-mo-shi.md#gdt "mention")来填写它。
* 输出helloworld
  * 这个提示的已经太明显了:joy:

{% hint style="info" %}
exercise11：请回答为什么三个段描述符要按照cs，ds，gs的顺序排列？
{% endhint %}

{% hint style="info" %}
exercise12：请回答app.s是怎么利用显存显示helloworld的。
{% endhint %}
