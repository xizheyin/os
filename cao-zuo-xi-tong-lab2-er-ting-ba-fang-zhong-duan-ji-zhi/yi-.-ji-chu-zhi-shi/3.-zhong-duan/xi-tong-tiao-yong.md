# 系统调用

系统调用的用户程序入口定义在`lib`下的`syscall.c`，在`syscall`函数里可以使用嵌入式汇编，先将各个参数分别赋值给`EAX`，`EBX`，`ECX`，`EDX`，`EDX`，`EDI`，`ESI`，然后约定将返回值放入`EAX`中（把返回值放入`EAX`的过程是我们需要在内核中实现的），接着使用`int`指令陷入内核，在`lib/syscall.c`中实现了如下的`syscall`函数：

```
int32_t syscall(int num, uint32_t a1,uint32_t a2,
		uint32_t a3, uint32_t a4, uint32_t a5)
{
	int32_t ret = 0;
	uint32_t eax, ecx, edx, ebx, esi, edi;
	asm volatile("movl %%eax, %0":"=m"(eax));
	asm volatile("movl %%ecx, %0":"=m"(ecx));
	asm volatile("movl %%edx, %0":"=m"(edx));
	asm volatile("movl %%ebx, %0":"=m"(ebx));
	asm volatile("movl %%esi, %0":"=m"(esi));
	asm volatile("movl %%edi, %0":"=m"(edi));
	asm volatile("movl %0, %%eax"::"m"(num));
	asm volatile("movl %0, %%ecx"::"m"(a1));
	asm volatile("movl %0, %%edx"::"m"(a2));
	asm volatile("movl %0, %%ebx"::"m"(a3));
	asm volatile("movl %0, %%esi"::"m"(a4));
	asm volatile("movl %0, %%edi"::"m"(a5));
	asm volatile("int $0x80");
	asm volatile("movl %%eax, %0":"=m"(ret));
	asm volatile("movl %0, %%eax"::"m"(eax));
	asm volatile("movl %0, %%ecx"::"m"(ecx));
	asm volatile("movl %0, %%edx"::"m"(edx));
	asm volatile("movl %0, %%ebx"::"m"(ebx));
	asm volatile("movl %0, %%esi"::"m"(esi));
	asm volatile("movl %0, %%edi"::"m"(edi));
	return ret;
}
```

{% hint style="info" %}
exercise10：我们在使用eax, ecx, edx, ebx, esi, edi前将寄存器的值保存到了栈中（注意第五行声明了6个局部变量），如果去掉保存和恢复的步骤，从内核返回之后会不会产生不可恢复的错误？
{% endhint %}

`int`指令接收一个8-Bits的立即数为参数，产生一个以该操作数为中断向量的软中断，其流程分为以下几步（**请看看这个流程和之前提到的哪个流程类似**。）：

1. 查找`IDTR`里面的IDT地址，根据这个地址找到IDT，然后根据IDT找到中断向量的门描述符
2. 检查CPL和门描述符的DPL，如果CPL数值上大于DPL，产生#GP异常，否则继续
3. 如果是一个ring3到ring0的陷入操作，则根据`TR`寄存器和GDT，找到TSS在内存中的位置，读取其中的`SS0`和`ESP0`并装载则向堆栈中压入`SS`和`ESP`，注意这个`SS`和`ESP`是之前用户态的数据
4. 压入`EFLAGS`，`CS`，`EIP`
5. 若门描述符为Interrupt Gate，则修改`EFLAGS`的`IF`位为0
6. 对于某些特定的中断向量，压入Error Code
7. 根据IDT表项设置`CS`和`EIP`，也就是跳转到中断处理程序执行

中断处理程序执行结束，需要从ring0返回ring3的用户态的程序时，使用`iret`指令

`iret`指令流程如下

1. `iret`指令将当前栈顶的数据依次Pop至`EIP`，`CS`，`EFLAGS`寄存器
2. 若Pop出的`CS`寄存器的CPL数值上大于当前的CPL，则继续将当前栈顶的数据依次Pop至`ESP`，`SS`寄存器
3. 恢复CPU的执行

{% hint style="info" %}
exercise11：我们会发现软中断的过程和硬件中断的过程类似，他们最终都会去查找IDT，然后找到对应的中断处理程序。为什么会这样设计？（可以做完实验再回答这个问题）
{% endhint %}

**系统调用的参数传递**

每个系统调用至少需要一个参数，即**系统调用号**，用以确定通过中断陷入内核后，该用哪个函数进行处理；普通 C 语言的函数的参数传递是通过将参数从右向左依次压入堆栈来实现；系统调用涉及到用戶堆栈至内核堆栈的切换，不能像普通函数一样直接使用堆栈传递参数；框架代码使用 `EAX`，`EBX` 等等这些通用寄存器从用戶态向内核态传递参数：

框架代码 `kernel/irqHandle.c` 中使用了`TrapFrame` 这一数据结构，其中保存了内核堆栈中存储的 7 个寄存器的值，其中的通用寄存器的取值即是通过上述方法从用戶态传递至内核态，并通过 `pushal` 指令压入内核堆栈的。
