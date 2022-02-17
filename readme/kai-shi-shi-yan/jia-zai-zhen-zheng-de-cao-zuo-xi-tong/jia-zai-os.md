---
description: 这部分只要写两行代码
---

# 🤓 加载“OS”

我们最后一步，是加载app（即OS）

请在完成[《开启保护模式》](kai-qi-bao-hu-mo-shi.md)之后，在实验根目录命令行里输入，切换到本节的分支：

```
git checkout load-os
```

boot.c里面的bootMain函数的作用有如下两个：

1. 把app.bin里面的内容读到0x8c00（在本实验我们这样规定，实际上不一定非是0x8c00）
2. 跳转到0x8c00（Hint：使用内联汇编）

readSect函数是将第offset块磁盘读出，读到物理地址为dst的内存中。

> exercise9：请阅读项目里的3个Makefile，解释一下根目录的Makefile文件里
>
> &#x20;                 cat bootloader/bootloader.bin app/app.bin > os.img
>
> &#x20;                 这行命令是什么意思。

> exercise10：如果把app读到0x7c20，再跳转到这个地方可以不？为什么？

如果你成功加载app，就可以看到下图。恭喜你，成功完成了实验！:tada::tada::tada:

![](../../../.gitbook/assets/image10.png)

