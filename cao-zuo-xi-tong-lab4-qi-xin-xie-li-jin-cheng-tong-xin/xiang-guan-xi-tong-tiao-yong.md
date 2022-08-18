# 相关系统调用

对于我们的实验，要实现下面这些系统调用，分别对应了信号量的创建，V操作，P操作，信号量销毁。

**sem\_init**

`sem_init`系统调用用于初始化信号量，其中参数`value`用于指定信号量的初始值，初始化成功则返回`0`，指针`sem`指向初始化成功的信号量，否则返回`-1`

```c
int sem_init(sem_t *sem, uint32_t value);
```

**sem\_post**

`sem_post`系统调用对应信号量的`V`操作，其使得`sem`指向的信号量的`value`增一，若`value`取值不大于`0`，则释放一个阻塞在该信号量上进程（即将该进程设置为就绪态），若操作成功则返回`0`，否则返回`-1`

```c
int sem_post(sem_t *sem);
```

**sem\_wait**

`sem_wait`系统调用对应信号量的`P`操作，其使得`sem`指向的信号量的`value`减一，若`value`取值小于`0`，则阻塞自身，否则进程继续执行，若操作成功则返回`0`，否则返回`-1`

```c
int sem_wait(sem_t *sem);
```

**sem\_destroy**

`sem_destroy`系统调用用于销毁`sem`指向的信号量，销毁成功则返回`0`，否则返回`-1`，若尚有进程阻塞在该信号量上，可带来未知错误

```c
int sem_destroy(sem_t *sem);
```
