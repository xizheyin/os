# 四个系统调用

《直观感受》章节已经提供了一个样例程序运行三个系统调用。下面还是再介绍一点。

## fork系统调用

**fork系统调用**用于创建子进程，内核需要为子进程分配一块独立的内存，将父进程的地址空间、用户态堆栈完全拷贝至子进程的内存中（温馨提示，不要用结构体整体赋值！！！因为这是老版本c语言未定义行为），并为子进程分配独立的进程控制块，完成对子进程的进程控制块的设置。

若子进程创建成功，则对于父进程，该系统调用的返回值为子进程的 pid ，对于子进程，其返回值为 0 ；若子进程创建失败，该系统调用的返回值为 -1。

```
pid_t fork();
```

## Exec系统调用

相比于fork，我们可能更少去用exec系统调用，但实际上他们常常配合使用。wikipedia是这样介绍exec的：

> In [computing](https://en.wikipedia.org/wiki/Computing), **exec** is a functionality of an [operating system](https://en.wikipedia.org/wiki/Operating\_system) that runs an [executable file](https://en.wikipedia.org/wiki/Executable\_file) in the context of an already existing [process](https://en.wikipedia.org/wiki/Process\_\(computing\)), replacing the previous executable. This act is also referred to as an **overlay**. It is especially important in [Unix-like](https://en.wikipedia.org/wiki/Unix-like) systems, although it exists elsewhere. As no new process is created, the [process identifier](https://en.wikipedia.org/wiki/Process\_identifier) (PID) does not change, but the [machine code](https://en.wikipedia.org/wiki/Machine\_code), [data](https://en.wikipedia.org/wiki/Data\_\(computing\)), [heap](https://en.wikipedia.org/wiki/Heap\_\(programming\)), and [stack](https://en.wikipedia.org/wiki/Run-time\_stack) of the process are replaced by those of the new program.
>
> The _exec_ call is available for many [programming languages](https://en.wikipedia.org/wiki/Programming\_language) including [compilable](https://en.wikipedia.org/wiki/Compiler) languages and some [scripting languages](https://en.wikipedia.org/wiki/Scripting\_language). In [OS command interpreters](https://en.wikipedia.org/wiki/Operating\_system\_shell), the exec [built-in command](https://en.wikipedia.org/wiki/Shell\_builtin) replaces the shell process with the specified program.[\[1\]](https://en.wikipedia.org/wiki/Exec\_\(system\_call\)#cite\_note-bashref-1)

在Linux中，exec的实现比较复杂，在这里，我们实现一个简化版本。我们的exec把一个新的程序**装载到当前进程中，替换掉当前进程**。

在我们的实验中，exec系统调用接受两个参数：`secstart`和`secnum`。第一个参数表明新程序在磁盘中的位置，第二个参数表明新程序在磁盘中占据的扇区数。

{% hint style="danger" %}
challenge5：你是否可以完善你的exec，第三个参数接受变长参数，用来模拟带有参数程序执行。

举个例子，在shell里面输入cat a.txt b.txt，实际上shell会fork出一个新的shell（假设称为shell0），并执行exec("cat", "a.txt","b.txt")运行cat程序，覆盖shell0的进程。

不必完全参照Unix，可以有自己的思考与设计。
{% endhint %}

## sleep系统调用

sleep系统调用用于进程主动阻塞自身，内核需要将该进程由 RUNNING 状态转换为 BLOCKED 状态，设置 该进程的 SLEEP 时间片，并切换运行其他 RUNNABLE 状态的进程。

```
int sleep(uint32_t time);
```

## exit系统调用

exit系统调用用于进程主动销毁自身，内核需要将该进程由 RUNNING 状态转换为 DEAD 状态，回收分配 给该进程的内存、进程控制块等资源，并切换运行其他 RUNNABLE 状态的进程。我们实验中实现的是一个简化版本，不需要参数（你用C语言使用\`exit()\`的时候是需要一个参数的）。

```
int exit();
```

{% hint style="info" %}
exercise8：请用fork，sleep，exit自行编写一些并发程序，运行一下，贴上你的截图。（自行理解，不用解释含义，帮助理解这三个函数）
{% endhint %}
