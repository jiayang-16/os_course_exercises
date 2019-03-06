# lec 3 SPOC Discussion

## **提前准备**
（请在上课前完成）


 - 完成lec3的视频学习和提交对应的在线练习
 - git pull ucore_os_lab, v9_cpu, os_course_spoc_exercises  　in github repos。这样可以在本机上完成课堂练习。
 - 仔细观察自己使用的计算机的启动过程和linux/ucore操作系统运行后的情况。搜索“80386　开机　启动”
 - 了解控制流，异常控制流，函数调用,中断，异常(故障)，系统调用（陷阱）,切换，用户态（用户模式），内核态（内核模式）等基本概念。思考一下这些基本概念在linux, ucore, v9-cpu中的os*.c中是如何具体体现的。
 - 思考为什么操作系统需要处理中断，异常，系统调用。这些是必须要有的吗？有哪些好处？有哪些不好的地方？
 - 了解在PC机上有啥中断和异常。搜索“80386　中断　异常”
 - 安装好ucore实验环境，能够编译运行lab8的answer
 - 了解Linux和ucore有哪些系统调用。搜索“linux 系统调用", 搜索lab8中的syscall关键字相关内容。在linux下执行命令: ```man syscalls```
 - 会使用linux中的命令:objdump，nm，file, strace，man, 了解这些命令的用途。
 - 了解如何OS是如何实现中断，异常，或系统调用的。会使用v9-cpu的dis,xc, xem命令（包括启动参数），分析v9-cpu中的os0.c, os2.c，了解与异常，中断，系统调用相关的os设计实现。阅读v9-cpu中的cpu.md文档，了解汇编指令的类型和含义等，了解v9-cpu的细节。
 - 在piazza上就lec3学习中不理解问题进行提问。

## 第三讲 启动、中断、异常和系统调用-思考题

## 3.1 BIOS
- x86中BIOS从磁盘读入的第一个扇区是是什么内容？为什么没有直接读入操作系统内核映像？
  * BIOS读取硬盘的0磁头、0柱面、1扇区的内容，在启动硬盘时，文件系统没有建立，并且一个硬盘可能存在多个分区，每个都可能有一个操作系统，无法判断从哪个操作系统启动，所以BIOS无法直接启动操作系统。
- 比较UEFI和BIOS的区别。
  * 统一可扩展固件接口 (Unified Extensible Firmware Interface, UEFI) 是一种个人电脑系统规格，用来定义操作系统与系统固件之间的软件界面，作为BIOS的替代方案。
  * 与BIOS相比，UEFI编码99%都是由C语言完成
  * UEFI弥补BIOS对新硬件的支持不足的问题
  * UEFI启动的安全性更强，启动配置更灵活，支持容量更大
- 理解rcore中的Berkeley BootLoader (BBL)的功能。
  * 把操作系统映像文件拷贝到RAM中去，然后跳转到它的入口处去执行。
## 3.2 系统启动流程

- x86中分区引导扇区的结束标志是什么？
  * 0x55AA
- x86中在UEFI中的可信启动有什么作用？
  * 通过启动前的数字签名检查来保证启动介质的安全性。
- RV中BBL的启动过程大致包括哪些内容？
  * 第一阶段：硬件设备初始化 为加载Bootloader的第二阶段代码准备RAM空间 复制Bootloader的第二阶段代码到RAM空间中 设置好栈 跳转到第二阶段代码的C入口点
  * 第二阶段：初始化本阶段要用到的硬件设备 检测系统内存映射 将内核映像和根文件系统映像从Flash上读到RAM空间中 为内核设置启动参数 调用内核
## 3.3 中断、异常和系统调用比较
- 什么是中断、异常和系统调用？
  * 中断是外部意外的响应，异常是指令执行意外的响应，系统调用是系统调用指令的响应。
- 中断、异常和系统调用的处理流程有什么异同？
  * 相同：都会进入异常服务例程，切换为内核态。
  * 不同：中断是外部设备引起的，异常和系统调用是应用程序引起的。中断是异步的，异常是同步的，系统调用异步和同步都可以。
- 以ucore/rcore lab8的answer为例，ucore的系统调用有哪些？大致的功能分类有哪些？
  * 进程管理：fork/exit/wait/exec/yield/kill/getpid/sleep
  * 文件操作：open/close/read/write/seek/fstat/fsync/getcwd/getdirentry/dup
  * 内存管理：pgdir
  * 外设输出：putc
## 3.4 linux系统调用分析
- 通过分析[lab1_ex0](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex0.md)了解Linux应用的系统调用编写和含义。(仅实践，不用回答)
- 通过调试[lab1_ex1](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex1.md)了解Linux应用的系统调用执行过程。(仅实践，不用回答)


## 3.5 ucore/rcore系统调用分析 （扩展练习，可选）
-  基于实验八的代码分析ucore的系统调用实现，说明指定系统调用的参数和返回值的传递方式和存放位置信息，以及内核中的系统调用功能实现函数。
- 以ucore/rcore lab8的answer为例，分析ucore 应用的系统调用编写和含义。
- 以ucore/rcore lab8的answer为例，尝试修改并运行ucore OS kernel代码，使其具有类似Linux应用工具`strace`的功能，即能够显示出应用程序发出的系统调用，从而可以分析ucore应用的系统调用执行过程。

 
## 3.6 请分析函数调用和系统调用的区别
- 系统调用与函数调用的区别是什么？
- 通过分析x86中函数调用规范以及`int`、`iret`、`call`和`ret`的指令准确功能和调用代码，比较x86中函数调用与系统调用的堆栈操作有什么不同？
- 通过分析RV中函数调用规范以及`ecall`、`eret`、`jal`和`jalr`的指令准确功能和调用代码，比较x86中函数调用与系统调用的堆栈操作有什么不同？


## 课堂实践 （在课堂上根据老师安排完成，课后不用做）
### 练习一
通过静态代码分析，举例描述ucore/rcore键盘输入中断的响应过程。

### 练习二
通过静态代码分析，举例描述ucore/rcore系统调用过程，及调用参数和返回值的传递方法。
