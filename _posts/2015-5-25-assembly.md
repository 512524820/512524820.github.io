---
layout: post
title: 底层程序执行原理及汇编语言
date: 2018-5-25
categories: blog
tags: [汇编语言,程序执行原理，底层实现]
description: 。
---

**学会高级语言，并不等于理解计算机实际的运行步骤**
![photo](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018012204.png)

# 汇编语言是什么
汇编是二进制指令的文本形式，方便开发人员阅读
我们知道，CPU 只负责计算，本身不具备智能。你输入一条指令（instruction），它就运行一次，然后停下来，等待下一条指令


# 来历
最早的时候，编写程序就是手写二进制指令，然后通过各种开关输入计算机，比如要做加法了，就按一下加法开关。后来，发明了纸带打孔机，通过在纸带上打孔，将二进制指令自动输入计算机。

为了解决二进制指令的可读性问题，工程师将那些指令写成了八进制。二进制转八进制是轻而易举的，但是八进制的可读性也不行。很自然地，最后还是用文字表达，加法指令写成 ADD。内存地址也不再直接引用，而是用标签表示。

这样的话，就多出一个步骤，要把这些文字指令翻译成二进制，这个步骤就称为 assembling，完成这个步骤的程序就叫做 assembler。它处理的文本，自然就叫做 aseembly code。标准化以后，称为 assembly language，缩写为 asm，中文译为汇编语言。

目前最常见的 x86 汇编语言，即 Intel 公司的 CPU 使用的那一种。

# 寄存器
学习汇编语言，首先必须了解两个知识点：寄存器和内存模型。

先来看寄存器。**CPU 本身只负责运算**，不负责储存数据。数据一般都储存在内存之中，CPU 要用的时候就去内存读写数据。但是，CPU 的运算速度远高于内存的读写速度，为了避免被拖慢，CPU 都自带一级缓存和二级缓存。基本上，CPU 缓存可以看作是读写速度较快的内存。

但是，CPU 缓存还是不够快，另外数据在缓存里面的地址是不固定的，CPU 每次读写都要寻址也会拖慢速度。因此，除了缓存之外，CPU 还自带了寄存器（register），**用来储存最常用的数据**。也就是说，那些最频繁读写的数据（比如循环变量），都会放在寄存器里面，**CPU 优先读写寄存器，再由寄存器跟内存交换数据**。

# 寄存器的种类
早期的 x86 CPU 只有8个寄存器，而且每个都有不同的用途。现在的寄存器已经有100多个了，都变成通用寄存器，不特别指定用途了，但是早期寄存器的名字都被保存了下来。
```
- EAX
- EBX
- ECX
- EDX
- EDI
- ESI
- EBP
- ESP
```
上面这8个寄存器之中，前面七个都是通用的。ESP 寄存器有特定用途，保存当前 Stack 的地址

**我们常说的32位和64位系统其实就是寄存器的不同，代表寄存器的大小，32位寄存器就是4字节，64位的寄存器就是8字节**


# 内存模型--Heap(堆)

程序运行的时候，操作系统会给它分配一段内存，用来储存程序和运行产生的数据。这段内存有起始地址和结束地址
![photo](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018012208.png)

程序运行过程中，对于动态的内存占用请求（比如新建对象，或者使用malloc命令），系统就会从预先分配好的那段内存之中，划出一部分给用户，具体规则是从起始地址开始划分

![photo](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018012209.png)

这种因为用户主动请求而划分出来的内存区域，叫做 Heap（堆）

# 内存模型--stack（栈）

除了 Heap 以外，其他的内存占用叫做 Stack（栈）
![photo](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018012210.png)

```
int main() {
   int a = 2;
   int b = 3;
}
```

系统开始执行main函数时，会为它在内存里面建立一个帧,所有main的内部变量（比如a和b）都保存在这个帧里面,main函数执行结束后,释放所有的内部变量，不再占用空间
![photo](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018012211.png)

```
int main() {
   int a = 2;
   int b = 3;
   return add_a_and_b(a, b);
}
```
如果在函数中再调用函数的话，系统会新建一个帧
![photo](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018012212.png)


生成新的帧，叫做"入栈"，英文是 push；栈的回收叫做"出栈"，英文是 pop!
**栈的结构是后进先出**
![photo](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018012213.jpg)
![photo](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018012214.jpg)

Stack是从高位地址向低位地址分配

# CPU指令
程序文件example.c
```
int add_a_and_b(int a, int b) {
   return a + b;
}

int main() {
   return add_a_and_b(2, 3);
}
```

`gcc -S example.c`将程序编译成汇编文件example.s,内容为汇编语言，大概内容为

```
_add_a_and_b:
   push   %ebx
   mov    %eax, [%esp+8] 
   mov    %ebx, [%esp+12]
   add    %eax, %ebx 
   pop    %ebx 
   ret  

_main:
   push   3
   push   2
   call   _add_a_and_b 
   add    %esp, 8
   ret
```
每一行就是 CPU 执行的一次操作。它又分成两部分，就以其中一行为例。
`push   %ebx`
这一行里面，push是 CPU 指令，%ebx是该指令要用到的运算子。一个 CPU 指令可以有零个到多个运算子。





