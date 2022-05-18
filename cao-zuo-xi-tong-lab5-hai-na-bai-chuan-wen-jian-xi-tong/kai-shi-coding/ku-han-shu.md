# 库函数

### open

open为Linux提供的系统原语，其用于打开（或创建）由路径path指定的文件，并返回文件描述符fd，fd为该文件对应的内核数据结构FCB的索引，参数flags用于对该文件的类型、访问控制进行设置。

```c
int open(char *path, int flags);
```

本实验只考虑四种打开权限：

```c
#define O_WRITE 0x01
#define O_READ 0x02
#define O_CREATE 0x04
#define O_DIRECTORY 0x08
```

这四种权限可以相互补充，设置了O\_WRITE才能写文件，设置了O\_READ才能读文件，设置了O\_CREAT才能在打 开的文件不存在时新建文件，设置O\_DIRECTORY表示读、写或新建的为文件夹，而不是文件。可以用或"|"运算符来组合。

### read

read为Linux提供的系统原语，其用于从fd索引的FCB中的文件读写偏移量处开始，从文件中读取size个字节至从buffer开始的内存中，并返回成功读取的字节数，若文件支持seek操作，则同时修改该FCB中的文件读写偏移量。

```c
int read(int fd, void *buffer, int size);
```

### write

write为Linux提供的系统原语，其用于向fd索引的FCB中的文件读写偏移量处开始，向文件中写入从buffer开始的内存中的size个字节，并返回成功写入的字节数，若文件支持seek操作，则同时修改该FCB中的文件读写偏移量。

```c
int write(int fd, void *buffer, int size);
```

### lseek

lseek为Linux提供的系统原语，其用于修改fd索引的FCB中的文件读写偏移量（若文件支持seek操作），返回修 改后的偏移量。

```c
int lseek(int fd, int offset, int whence);
```

所有打开的文件都有一个当前文件偏移量cfo，lseek参数 offset 的含义取决于参数 whence：

```c
#define SEEK_SET 0
#define SEEK_CUR 1
#define SEEK_END 2
```

* 如果 whence 是 SEEK\_SET，文件偏移量将被设置为 offset。&#x20;
* 如果 whence 是 SEEK\_CUR，文件偏移量将被设置为 cfo 加上 offset，offset 可以为正也可以为负。
* 如果 whence 是 SEEK\_END，文件偏移量将被设置为文件长度加上 offset，offset 可以为正也可以为 负。

### close

close为Linux提供的系统原语，其用于关闭由fd索引的FCB。

```c
int close(int fd);
```

### remove

remove为C标准库的函数，其用于删除path指定的文件。

```c
cint remove(char *path);
```





