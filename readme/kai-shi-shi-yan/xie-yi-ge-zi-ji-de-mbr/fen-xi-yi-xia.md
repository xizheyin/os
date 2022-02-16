# 😀 分析一下

上一小节，大家一脸懵逼的进行了操作，下面来解释一下。

先编写`mbr.s`，然后通过gcc编译成**可链接目标文件mbr.o**

然后用ld进行链接：

```shell
$ld -m elf_i386 -e start -Ttext 0x7c00 mbr.o -o mbr.elf
```

这句指令的意思是可执行文件的格式是**i386架构的ELF格式**。程序的入口是**start函数，并且**
