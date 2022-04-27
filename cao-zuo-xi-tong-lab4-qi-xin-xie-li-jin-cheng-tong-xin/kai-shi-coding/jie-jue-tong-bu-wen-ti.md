# 解决同步问题

{% hint style="info" %}
task3：完成app里面的下列问题，在报告里放上运行截图（注意在写其中一个问题时，把别的代码注释掉）。
{% endhint %}

为了方便区分进程，你可以实现`getpid`系统调用，用来返回当前进程的`pid`

## 哲学家就餐问题 <a href="#331-e5-93-b2-e5-ad-a6-e5-ae-b6-e5-b0-b1-e9-a4-90-e9-97-ae-e9-a2-98" id="331-e5-93-b2-e5-ad-a6-e5-ae-b6-e5-b0-b1-e9-a4-90-e9-97-ae-e9-a2-98"></a>

同学们需要在`lab4/app/main.c`中实现哲学家就餐问题

要求：

* 5个哲学家同时运行
* 哲学家思考，`printf("Philosopher %d: think\n", id);`
* 哲学家就餐，`printf("Philosopher %d: eat\n", id);`
* 任意P、V及思考、就餐动作之间添加`sleep(128);`

## 生产者-消费者问题和读者-写者问题（选做） <a href="#332-e7-94-9f-e4-ba-a7-e8-80-85-e6-b6-88-e8-b4-b9-e8-80-85-e9-97-ae-e9-a2-98-e5-92-8c-e8-af-bb-e8-80" id="332-e7-94-9f-e4-ba-a7-e8-80-85-e6-b6-88-e8-b4-b9-e8-80-85-e9-97-ae-e9-a2-98-e5-92-8c-e8-af-bb-e8-80"></a>

如果你有多余的精力和兴趣，你可以选择额外完成其它两个进程同步问题。实现方式不限，问题要求如下：

生产者-消费者问题：

* 4个生产者，1个消费者同时运行
* 生产者生产，`printf("Producer %d: produce\n", id);`
* 消费者消费，`printf("Consumer : consume\n");`
* 任意P、V及生产、消费动作之间添加`sleep(128);`

读者-写者问题：

* 3个读者，3个写者同时运行
* 读者读数据，`printf("Reader %d: read, total %d reader\n", id, Rcount);`
* 写者写数据，`printf("Writer %d: write\n", id);`
* 任意P、V及读、写动作之间添加`sleep(128);`
