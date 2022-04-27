# 经典同步问题

下面这三个问题是进程同步中非常经典的问题，**考察率也非常高。**

## 哲学家就餐问题 <a href="#231-e5-93-b2-e5-ad-a6-e5-ae-b6-e5-b0-b1-e9-a4-90-e9-97-ae-e9-a2-98" id="231-e5-93-b2-e5-ad-a6-e5-ae-b6-e5-b0-b1-e9-a4-90-e9-97-ae-e9-a2-98"></a>

问题描述：

* 5个哲学家围绕一张圆桌而坐
  * 桌子上放着5支叉子
  * 每两个哲学家之间放一支
* 哲学家的动作包括思考和进餐
  * 进餐时需要同时拿到左右两边的叉子
  * 思考时将两支叉子返回原处
* 如何保证哲学家们的动作有序进行？如：不出现有人永远拿不到叉子

方案1：

```c
#define N 5                // 哲学家个数
semaphore fork[5];         // 信号量初值为1
void philosopher(int i){   // 哲学家编号：0-4
  while(TRUE){
    think();               // 哲学家在思考
    P(fork[i]);            // 去拿左边的叉子
    P(fork[(i+1)%N]);      // 去拿右边的叉子
    eat();                 // 吃面条
    V(fork[i]);            // 放下左边的叉子
    V(fork[(i+1)%N]);      // 放下右边的叉子
  }
}
```

方案1看起来比较符合常识，先拿左手叉子，再拿右手叉子......可是你不要忘了，机器可不想人一样灵活变通，它会死锁......

{% hint style="info" %}
exercise1：请回答一下，什么情况下会出现死锁。
{% endhint %}

方案2：

```c
#define N 5                // 哲学家个数
semaphore fork[5];         // 信号量初值为1
semaphore mutex;           // 互斥信号量，初值1
void philosopher(int i){   // 哲学家编号：0-4
  while(TRUE){
    think();               // 哲学家在思考
    P(mutex);              // 进入临界区
    P(fork[i]);            // 去拿左边的叉子
    P(fork[(i+1)%N]);      // 去拿右边的叉子
    eat();                 // 吃面条
    V(fork[i]);            // 放下左边的叉子
    V(fork[(i+1)%N]);      // 放下右边的叉子
    V(mutex);              // 退出临界区
  }
}
```

{% hint style="info" %}
exercise2：说一下该方案有什么不足？（答出一点即可）
{% endhint %}

方案3：

{% hint style="info" %}
exercise3：正确且高效的解法有很多，请你利用信号量PV操作设计一种正确且相对高效（比方案2高效）的哲学家吃饭算法。（其实网上一堆答案，主要是让大家多看看不同的实现。）
{% endhint %}

## 生产者-消费者问题

生产者---->缓冲区---->消费者

有界缓冲区的生产者-消费者问题描述：

* 一个或多个生产者在生产数据后放在一个缓冲区里
* 单个消费者从缓冲区取出数据处理
* 任何时刻只能有一个生产者或消费者可访问缓冲区

问题分析：

* 任何时刻只能有一个线程操作缓冲区（互斥访问）
* 缓冲区空时，消费者必须等待生产者（条件同步）
* 缓冲区满时，生产者必须等待消费者（条件同步）

用信号量描述每个约束：

* 二进制信号量mutex
* 资源信号量fullBuffers
* 资源信号量emptyBuffers

伪代码描述一下：

```cpp
class BoundedBuffer { //buffer
	mutex = new Semaphore(1);
	fullBuffers = new Semaphore(0);
	emptyBuffers = new Semaphore(n);
}
```

```c
BoundedBuffer::Deposit(c){               BoundedBuffer::Remove(c){
  emptyBuffers->P();                       fullBuffers->P();
  mutex->P();                              mutex->P();
  Add c to the buffer;                     Remove c from buffer;
  mutex->V();                              mutex->V();
  fullBuffers->V();                        emptyBuffers->V();
}                                        }
```

{% hint style="info" %}
exercise4：为什么要用两个信号量呢？emptyBuffers和fullBuffer分别有什么直观含义？
{% endhint %}

## 读者-写者问题 <a href="#233-e8-af-bb-e8-80-85-e5-86-99-e8-80-85-e9-97-ae-e9-a2-98" id="233-e8-af-bb-e8-80-85-e5-86-99-e8-80-85-e9-97-ae-e9-a2-98"></a>



读者-写者问题主要出现在数据库等共享资源的访问当中，问题描述：

* 共享数据的两类使用者
  * 读者：只读取数据，不修改
  * 写者：读取和修改数据
* 对共享数据的读写
  * “读-读”允许，同一时刻，允许有多个读者同时读
  * “读-写”互斥，没有写者时读者才能读，没有读者时写者才能写
  * “写-写”互斥，没有其他写者，写者才能写

用信号量描述每个约束：

* 信号量WriteMutex，控制读写操作的互斥，初始化为1
* 读者计数Rcount，正在进行读操作的读者数目，初始化为0
* 信号量CountMutex，控制对读者计数的互斥修改，初始化为1，只允许一个线程修改Rcount计数

### 写者进程

```
P(WriteMutex);
write;
V(WriteMutex);
```

### 读者进程

```c
P(CountMutex);
if (Rcount == 0)P(WriteMutex);
++Rcount;
V(CountMutex);
read;
P(CountMutex);
--Rcount;
if (Rcount == 0)
  V(WriteMutex);
V(CountMutex);
```

可以看出，读者进程在跟写者进程抢写者锁！！！
