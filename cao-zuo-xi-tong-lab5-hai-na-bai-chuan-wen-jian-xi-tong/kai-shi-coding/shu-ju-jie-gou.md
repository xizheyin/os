# 数据结构

### FCB

我们使用文件控制块（FCB）这一数据结构对进程打开的文件进行管理，FCB中需要记录其对应的是哪个文件，该文件是以哪种方式打开的（读、写），该文件的读写偏移量。

FCB的索引号称为文件描述符，每个进程的PCB中对其打开的文件的文件描述符进行记录：

```c
struct File {
int state;       // 0是没用 1是在使用
int inodeOffset; //inodeOffset in filesystem, for syscall open
int offset;      //offset from SEEK_SET
int flags;
//可以像device一样放个链表来存放进程......不过本次实验不要求
};
typedef struct File File;
```

推荐的文件控制块数据结构如上，同学们可根据需求自行修改。

### Inode

跟Inode有关的数据结构都放在了ext.h里面了，可以自行阅读理解。
