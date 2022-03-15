# 特权级

我们来介绍一下关于x86平台的特权级代码保护，这是CPU状态切换很重要的内容。而详细切换过程请结合[下面一章](broken-reference)来思考。

**特权级代码的保护**：

* x86 平台 CPU 有 0、1、2、3 四个特权级，其中 level0 是最高特权级，可以执行所有指令。
* level3 是最低特权级，只能执行算数逻辑指令，很多特殊操作（例如 CPU 模式转换，I/O 操作指令。这会影响系统安全！！！）都不能在这个级别下进行。
* 现代操作系统往往只使用到 level0 和 level3 两个特权级，操作系统内核运行时，系统处于 level0（即 CS 寄存器的低两位为 `00b`），而用戶程序运行时系统处于 level3（即 CS 寄存器的低两位为 `11b`）。
* x86 平台使用 CPL、DPL、RPL 来对代码、数据的访存进行特权级检测：
  * CPL（current privilege level）为 `CS` 寄存器的低两位，表示当前指令的特权级。
  * DPL（discriptor privilege level）为描述符中的 DPL 字段，表示访存该内存段的最低特权级（有时表示访存该段的最高特权级，比如 Conforming-Code Segment）。
  * RPL（requested privilege level）为 `DS`、`ES`、`FS`、`GS`、`SS`寄存器的低两位，用于对 CPL 表示的特权级进行补充。
  * 一般情况下，同时满足 CPL ≤ DPL，RPL ≤ DPL 才能实现对内存段的访存，否则产生 `#GP` 异常。
* 基于中断机制可以实现对特权级代码的保护。
