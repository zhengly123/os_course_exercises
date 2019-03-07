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
- 请描述在“计算机组成原理课”上，同学们做的MIPS CPU是从按复位键开始到可以接收按键输入之间的启动过程。

  PC初始化到Flash首地址，运行bootloader，将OS拷贝到SRAM中，然后PC进入OS起始位置，开始运行OS。当键盘键入时，触发IO中断，可以收到键入的信息。

- x86中BIOS从磁盘读入的第一个扇区是是什么内容？为什么没有直接读入操作系统内核映像？

  bootloader，因为操作系统可能写在不同的文件系统和分区上，BIOS并不能识别这些信息，故不能实现这些操作。

- 比较UEFI和BIOS的区别。

  UEFI启动方式更加多样，可以选择指定驱动和和启动文件；更加安全，有独立的分区；支持大容量的硬盘

- 理解rcore中的Berkeley BootLoader (BBL)的功能。

  提供底层输出，以不同模式导入系统内核。

## 3.2 系统启动流程

- x86中分区引导扇区的结束标志是什么？

  0X55AA

- x86中在UEFI中的可信启动有什么作用？

  通过检查启动设备的签名来防止不可信设备导致危害。

- RV中BBL的启动过程大致包括哪些内容？

  初始化、选择是否从kernel_start或者_payload_start启动系统。

## 3.3 中断、异常和系统调用比较
- 什么是中断、异常和系统调用？

  中断是外部设备产生的信号，异常是指令执行出现意外，系统调用是程序调用了系统提供的接口。

- 中断、异常和系统调用的处理流程有什么异同？

  他们的需要经过进入异常处理，通过中断处理向量找到对应的处理程序。

  不同之处：

  - 他们的处理程序不同，分别为系统调用库、硬件驱动和。
  - 系统调用是同步或异步的。中断是异步的，异常是同步的。
  - 系统调用来源于程序，中断来源于外部设备，异常来源于指令执行。

- 以ucore/rcore lab8的answer为例，ucore的系统调用有哪些？大致的功能分类有哪些？

  ```
  [SYS_exit]              
  [SYS_fork]              
  [SYS_wait]              
  [SYS_exec]              
  [SYS_yield]             
  [SYS_kill]              
  [SYS_getpid]            
  [SYS_putc]              
  [SYS_pgdir]             
  [SYS_gettime]           
  [SYS_lab6_set_priority] 
  [SYS_sleep]             
  [SYS_open]              
  [SYS_close]             
  [SYS_read]              
  [SYS_write]             
  [SYS_seek]              
  [SYS_fstat]             
  [SYS_fsync]             
  [SYS_getcwd]            
  [SYS_getdirentry]       
  [SYS_dup]               
  ```

  可以分为进程管理、文件操作、内存管理、外设输出。

## 3.4 linux系统调用分析
- 通过分析[lab1_ex0](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex0.md)了解Linux应用的系统调用编写和含义。(仅实践，不用回答)
- 通过调试[lab1_ex1](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex1.md)了解Linux应用的系统调用执行过程。(仅实践，不用回答)


## 3.5 ucore/rcore系统调用分析 （扩展练习，可选）
-  基于实验八的代码分析ucore的系统调用实现，说明指定系统调用的参数和返回值的传递方式和存放位置信息，以及内核中的系统调用功能实现函数。
- 以ucore/rcore lab8的answer为例，分析ucore 应用的系统调用编写和含义。
- 以ucore/rcore lab8的answer为例，尝试修改并运行ucore OS kernel代码，使其具有类似Linux应用工具`strace`的功能，即能够显示出应用程序发出的系统调用，从而可以分析ucore应用的系统调用执行过程。


## 3.6 请分析函数调用和系统调用的区别
- 系统调用与函数调用的区别是什么？

  - 权限不同：系统调用有特权级和特权堆栈的切换，但函数调用没有
  - 调用汇编指令：系统调用用了INT和IRET，但函数调用用了CALL和RET

- 通过分析x86中函数调用规范以及`int`、`iret`、`call`和`ret`的指令准确功能和调用代码，比较x86中函数调用与系统调用的堆栈操作有什么不同？

  int

  - 保存中断号
  - 标志寄存器入栈
  - CS,IP入栈
  - 设置新的IP和CS

  iret

  - 恢复IP
  - 恢复CS
  - 恢复标志寄存器
  - 恢复ESP和SS（返回权限发生变化）

  所以可以发现int和iret涉及权限和堆栈的转换，但函数调用并不会。

- 通过分析RV中函数调用规范以及`ecall`、`eret`、`jal`和`jalr`的指令准确功能和调用代码，比较x86中函数调用与系统调用的堆栈操作有什么不同？

  RISCV中的ecall产生了一个特殊的异常，通过进入exception handler来进入调用。

  eret从trap中返回，x RET sets the pc to the value stored in the x epc register.


## 课堂实践 （在课堂上根据老师安排完成，课后不用做）
### 练习一
通过静态代码分析，举例描述ucore/rcore键盘输入中断的响应过程。

### 练习二
通过静态代码分析，举例描述ucore/rcore系统调用过程，及调用参数和返回值的传递方法。
