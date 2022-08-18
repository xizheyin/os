# 写代码

{% hint style="info" %}
task1：完成irqHandle.c里面有关文件的系统调用的内容，都用TODO标好了，并且添加了提示。
{% endhint %}

这里需要提示的是，我们需要使用几个fs.c里面的函数

最重要的是如下几个：

* readInode：这个用法在代码里面标注了，我们常常通过这个找到父亲inode，而父亲目录的路径，可以通过**util.c里的一些字符串函数（已经封装好了）**来获取。
* allocInode：已经在代码里标注，注意通过readInode先找到父目录的inode。
* freeInode：同样在代码里注释了，释放inode。

在读写文件的时候，还需要用到readBlock和writeBlock函数。

字符串处理请参考util.c。

{% hint style="info" %}
task2：在app里面完善简易的ls和cat函数。
{% endhint %}

完结撒花！！！:tada:
