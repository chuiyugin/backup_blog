---
title: 汇编语言
tags:
  - 汇编语言
categories:
  - 汇编语言
date: 2024-7-1 22:00:00
excerpt: 汇编语言的学习与分享总结!
---
# 汇编语言
## 第一章 基础知识

### 内存的读写和地址空间

#### CPU 对存储器的读写

CPU 想要进行数据的读写，必须和外部器件进行三类信息的交互：
+ 存储单元的地址（**地址信息**）
+ 器件的选择，读或写命令（**控制信息**）
+ 读或写的数据（**数据信息**）

#### 内存地址空间

+ 定义：CPU 地址总线宽度为 $N$，寻址空间为 $2^{N}$ 字节。
+ 举例：8086 CPU 的地址总线宽度为 20，那么可以寻址的内存单位个数为 $2^{20}$ =1 MB，称其内存地址空间为 1 MB。
+ 将各类存储器看作一个逻辑存储器（**统一编址**）
+ 所有的物理存储器被看作一个由若干存储单元组成的逻辑存储器；
+ 每个物理存储器在这个逻辑存储器中占有一个地址段，即一段地址空间。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240712162944.png)

+ 举例：8086 PC 机的内存地址空间分配基本情况：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240712163105.png)

## 第二章 寄存器

一个典型的CPU由**运算器**、**控制器**、**寄存器**等器件组成，这些器件靠**内部总线**相连。

区分**内部总线**与**外部总线**：

* **内部总线**实现CPU**内部各个器件**之间的联系。
* **外部总线**实现**CPU和主板上其他器件**的联系。

简单而言，在CPU中：

* 运算器进行信息处理；
* 寄存器进行信息存储；
* 控制器控制各种器件进行工作；
* 内部总线连接各种器件，在它们之间进行数据传送；

其中，寄存器是 CPU 中程序员可以用**指令读写**的部件，可以通过改变各种寄存器中的内容来实现对 CPU 的控制。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240712212819.png)

### 通用寄存器

8086CPU中所有寄存器都是16位的，可以存放**两个字节**的数据。

存放一般性数据的寄存器成为通用寄存器，有AX、BX、CX、DX四个通用寄存器。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202310101250059.png)

为了保证与上一代的8位寄存器兼容，8086CPU中的通用寄存器AX、BX、CX、DX都可以分为两个可以独立使用的8位寄存器。

* AX可分为AH和AL；
* BX可分为BH和BL；
* CX可分为CH和CL；
* DX可分为DH和DL；

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202310101250451.png)
### 字在寄存器的存储

+ 8086是16位CPU

  + 8086的字长(word size)为**16bit**
  + 字节：记为**Byte**，一个字节由**8个bit**组成，可以存在**8位寄存器**中；
  + 字：记为**Word**，对于**16位**的8086CPU而言，一个字由**两个字节**组成，这两个字节分别称为这个字的**高位字节**和**低位字节**。

+ 需要特别注意**字的定义**：

  + 计算机进行数据处理时，一次存取、加工和传送的数据长度称为字。然而，这可能会**根据系统的不同而变化**。例如，在`32`位的系统中，一个字由`32`位组成。在`64`位的系统中，一个字由`64`位组成。所以，虽然`16`位通常被称为一个字，但这可能会根据具体的计算机系统而有所不同。
### 几条汇编指令

+ 通过汇编指令控制 CPU 进行工作，下表有常见的几条指令：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240712160854.png)

+ 注意：汇编语言**不区分**大小写

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240713213811.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240713213948.png)

### 确定物理地址的方法
#### 物理地址
- CPU 访问内存单元时要给出内存单元的地址。
- 所有的内存单元构成的存储空间是一个一维的线性空间。
- 每一个内存单元在这个空间中都有唯一的地址，这个唯一的地址称为**物理地址**。
#### 8086 CPU 物理地址示例
##### 示例
  - 8086 CPU 有 `20位` 地址总线，可传送 `20位` 地址，寻址能力为 `1M`。
  - 8086 是 `16位` 结构的 CPU
	  -  运算器一次**最多**可以处理 `16位` 的数据，寄存器的最大宽度为 `16位`。
	  - 在 8086 CPU 内部处理的、传输、暂存的地址也是 `16位`，寻址能力也只有 `64KB`！

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240713215522.png)

##### 解决方案

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240713215903.png)

+ 方法：物理地址=段地址×16+偏移地址

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240713220232.png)

##### 本质含义
+ 要解决的问题：
	+ 用**两个 16 位**的地址（段地址、偏移地址），相加得到一个 `20位` 的物理地址。
+ 本质含义：
	+ CPU 在访问内存时，用一个基础地址（段地址×16）和一个相对于基础地址的偏移地址相加，给出内存单元的物理地址。

### 段的概念
#### 用分段的方式管理内存
+ 需要注意的是，**内存并没有分段**，段的划分来自于 **CPU**。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716104453.png)
#### 同一段内存，多种分段方案

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716104854.png)

#### 用不同的段地址和偏移地址形成同一个物理地址

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716105055.png)

### 段寄存器
+ 由上述章节我们知道，8086 CPU 在访问内存时要由相关部件提供内存单元的**段地址**和**偏移地址**，送入**地址加法器**合成**物理地址**。
+ 其中段地址在 8086 CPU 的 `4` 个**段寄存器**中存放，分别是：`CS`、`DS`、`SS`、`ES`，当 8086 CPU 要访问内存时由这 4 个段寄存器提供内存单元的段地址。
### CS 和 IP 寄存器
+ `CS` 和 `IP` 寄存器是 8086 CPU 中两个最关键的寄存器，它们指示了 CPU 当前要读取指令的地址。
+ `CS` 为代码段寄存器，`IP` 为指令指针寄存器。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716141922.png)

+ 在 8086 CPU 加电启动或者复位后，`CS` 和 `IP` 被设置为 `CS=FFFFH`，`IP=0000H`，即在 8086 PC 刚启动时，CPU 从内存 `FFFF0H` 单元中读取指令执行，`FFFF0H` 单元中的指令是 8086 PC 开机后执行的第一条指令。
+ 简而言之，`CS` 和 `IP` 寄存器提供了 CPU 要执行指令的地址。
+ 我们知道，在内存中，**指令**和**数据**没有任何区别，都是二进制信息，CPU 在工作时的时候把有的信息看作**指令**，有的信息看作**数据**。
+ 那么应该如何**区分**指令和数据呢？
+ 可以看到的是，CPU 将 `CS` 和 `IP` 寄存器指向的内存单元中的内容看作指令，因为在任何时候，`CS` 和 `IP` 寄存器中的内容分别代表指令的段地址和偏移地址，使用它们合成指令的物理地址，通过物理地址到内存中读取指令码并执行。
+ 那么，可以认为如果内存中的一段信息曾被 CPU 执行过的话，那么它所在的内存单元必然被 `CS` 和 `IP` 寄存器指向过。
### 修改 CS、IP 的指令
+ 事实：执行何处的指令，取决于 `CS` 和 `IP` 寄存器。
+ 应用：可以通过改变 `CS` 和 `IP` 寄存器中的内容，来控制 CPU 要执行的目标指令。
#### 转移指令 jmp

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716163546.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716163804.png)

### 内存中字的存储

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716164322.png)

#### 字单元

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716164406.png)

### DS 和 [address]
#### CPU 从内存单元中读取地址

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716171244.png)

#### 字的传送

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716171509.png)

+ 例题：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716171943.png)

+ 分析：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716172010.png)

#### 对内存单元数据的访问

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716213144.png)

### 三个常用指令总结
#### mov 指令

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716213223.png)

#### 加法 add 和减法 sub 指令

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716213436.png)

#### 用 DS 和[address]形式访问内存中数据段方法小结
+ 字在内存中存储时，要用两个地址连续的内存单元来存放，字的低位字节存放在低地址单元中，高位字节存放再高地址单元中。
+ 用 `mov` 指令要访问内存单元，可以在 `mov` 指令中只给出单元的偏移地址，此时，段地址默认在 `DS` 寄存器中。
+ `[address]` 表示一个偏移地址为 `address` 的内存单元。
+ 在内存和寄存器之间传送字型数据时，**高地址单元**和高 8 位寄存器、 **低地址单元**和低 8 位寄存器相对应。
+ `mov`、`add`、`sub` 是具有两个操作对象的指令，访问内存中的数据段（对照：`jmp` 是具有一个操作对象的指令，对应内存中的代码段）。
+ 可以根据自己的推测，在 `Debug` 模式中实验指令的新格式。

### 栈及栈操作的实现
#### 栈结构

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716220519.png)

#### 栈的操作
+ 问题：
	+ CPU 如何知道一段内存空间被当作栈来使用？
	+ 执行 `push` 和 `pop` 的时候，如何知道哪个单元是栈顶单元？
+ 回答：
	+ 8086 CPU 中，有两个与栈相关的寄存器：
		+ 栈段寄存器 `SS` 　　 - 存放栈顶的段地址；
		+ 栈顶指针寄存器 `SP`  - 存放栈顶的偏移地址；
		+ 任意时刻，`SS:SP` 指向栈顶元素。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716221517.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716221809.png)

#### 栈顶超界问题的解决
+ 8086 CPU **不保证**对栈的操作不会超界。
+ 8086 CPU 只知道**栈顶**在何处（由 `SS:SP` 指示），不知道程序安排的栈空间有多大。
+ 我们在编程的时候要自己操心栈顶超界的问题，要根据可能用到的最大栈空间，来安排栈的大小。
+ 防止**入栈**的数据太多而导致的超界；
+ 防止**出栈**时栈空了仍然继续出栈而导致的超界。

#### 栈的小结
+ `push`、`pop` 实质上就是一种内存传送指令，可以在寄存器和内存之间传送数据，与 `mov` 指令不同的是，`push` 和 `pop` 指令访问的内存单元的地址不是在指令中给出的，而是由 `SS:SP` 指出的。
+ 执行 `push` 和 `pop` 指令时，`SP` 中的内容自动改变。
+ 8086 CPU 提供的栈操作机制：
	+ 在 `SS`，`SP` 中存放栈顶的**段地址**和**偏移地址**，入栈和出栈指令根据 `SS:SP` 指示的地址，按照栈的方式访问内存单元。
	+ `push` 指令的执行步骤： 
		+ 1）`SP=SP-2`； 
		+ 2）向 `SS:SP` 指向的字单元中送入数据。
	+ `pop` 指令的执行步骤： 
		+ 1）从 `SS: SP` 指向的字单元中读取数据； 
		+ 2）`SP=SP+2`。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240716222610.png)

## 第三章 编写完整程序
### 汇编语言编写程序的工作过程
+ 第一步：编写汇编源程序。
	+ 采用文本编辑器（如 Edit、记事本等）
+ 第二步：对源程序进行编译产生目标文件（存储机器码），再用连接程序对目标文件进行连接，生成可在操作系统中直接运行的可执行文件。可执行文件包含以下两部分内容：
	+ 程序（从源程序中的汇编指令翻译过来的机器码）和数据（源程序中定义的数据）
	+ 相关的描述信息（比如，程序有多大、要占用多少内存空间等）
+ 第三步：执行可执行文件中的程序。
	+ 操作系统依照可执行文件中的描述信息，将可执行文件的机器码和数据加载入内存，并进行相关的初始化（比如设置 `CS:IP` 指向第一条要执行的指令），然后由 CPU 执行程序。
### 源程序
+ 汇编程序：包含**汇编指令**和**伪指令**的文本。
	+ 汇编指令：有对应机器码的指令，可以被编译为机器指令，最终为 CPU 所执行。
	+ 伪指令没有对应的机器指令，最终不被 CPU 所执行。伪指令是由编译器来执行的指令，编译器根据伪指令来进行相关的编译工作。
+ 汇编程序示例如下：
```assembly
assume cs:codesg 
codesg segment 

	mov ax,0123H 
	mov bx,0456H 
	add ax,bx 
	add ax,ax 

	mov ax,4c00h 
	int 21h 
	
codesg ends 
end
```
#### 伪指令

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240720110415.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240720110501.png)

#### 源程序经编译连接后变为机器码

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240720110757.png)

#### 汇编程序的结构

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240720111046.png)

#### 如何写出一个汇编程序？

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240720111238.png)

#### 程序中可能的错误

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240720111313.png)

#### 源程序到可执行文件的过程

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240720230021.png)

#### DOS 环境的挂载

```
mount c e:\masm
*********************************************************
c:
```

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721101529.png)

#### asm 文件的编译

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721112140.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721114331.png)

#### 汇编目标文件的连接

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721114212.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721114353.png)

#### 用 debug 跟踪程序执行

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721114916.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721114956.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721115152.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721115246.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721115613.png)

### 实验
+ 将下面程序保存为 `t1.asm` 文件，将其生成可执行文件 `t1.exe` 文件。
```assembly
assume cs:codesg 
codesg segment 

	mov ax,2000H 
	mov ss,ax
	mov sp,0
	add sp,10 ;开辟栈空间
	pop ax
	pop bx
	push ax
	push bx
	pop ax
	pop bx

	mov ax,4c00h 
	int 21h 
	
codesg ends 
end
```

+ 用 debug 跟踪 `t1.exe` 文件的执行过程，写出每一步执行后，相关寄存器中的内容和栈顶的内容。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721135936.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721140008.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721140034.png)

+ `PSP` 的头两个字节是 `CD 20`，用 debug 加载 `t1.exe` 文件，查看 `PSP` 的内容。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240721140401.png)

## 第四章 [BX]和 Loop 指令
### [...] 的规定与 (...) 的约定

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240723211951.png)

### 再约定：idata 表示常量
+ 例如：
	+ `mov ax,[idata]` ：代表 `mov ax,[1]` 、`mov ax,[2]` 、`mov ax,[3]` ...
	+ `mov bx,idata` ：代表 `mov bx,1` 、`mov bx,2` 、`mov bx,3` ...
	+ `mov ds,idata` ：代表 `mov ds,1` 、`mov ds,2` ... (但都是非法指令）

### Loop 指令
#### Loop 指令要点

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240723213103.png)

#### Loop 指令编程实例

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240723213434.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240723214406.png)

#### 运行示例程序
##### Debug 执行程序

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240723215237.png)

+ 标号 `s` ，用于标识地址，在程序写入内存中会直接转换为地址。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240723215155.png)

#### t 命令、p 命令和 g 命令的区别

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240723215518.png)

### 段前缀
#### 一个“异常”现象及对策
+ 现象：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240726141501.png)

+ 对策：
	+ 在 `[idata]` 前显式地写上段寄存器，例 `mov al,ds:[bx]`。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240726141818.png)

+ 这些出现在访问内存单元的指令中，用于**显式**地指明内存单元的段地址的 `ds:` 、`cs:` 、 `ss:` 或 `es:` ”，在汇编语言中称为**段前缀**。

#### 访问连续的内存单元 —— Loop 和 [bx] 联手
+ 问题：计算 `ffff:0~ffff:b` 字节单元中的数据的和，结果存储在 `dx` 中。
+ 分析：
	+ 运算后的结果是否会超出 `dx` 所能存储的范围？
		+ `ffff:0～ffff:b` 内存单元中的数据是字节型数据，范围在 `0～255` 之间，`12` 个这样的数据相加，结果不会大于 `65535`，可以在 `dx` 中存放下。
	+ 是否可以将 `ffff:0～ffff:b` 中的数据直接累加到 `dx` 中？`add dx,ds:[addr]` ; `(dx)=(dx)+`?
		+ 期望：取出内存中的 8 位数据进行相加；实际：取出的是内存中的 16 位数据。
	+ 是否可以将 `ffff:0～ffff:b` 中的数据直接累加到 `dl` 中？`add dl,ds:[addr]` ;`(dl)=(dl)+`?
		+ 期望：取出内存中的 8 位数据相加；实际：取出的是内存中的 8 位数据，但很有可能造成进位丢失。
+ 对策：取出 8 位数据，加到 16 位的寄存器

```assembly
mov al,ds:[addr] 
mov ah,0 
add dx,ax
```

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240726150728.png)
+ 代码：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240726150828.png)

#### 段前缀的使用
+ 问题：将内存 `ffff:0~ffff:b` 中的数据拷贝到 `0:200~0:20b` 单元中。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240726151542.png)
+ 代码：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240726151641.png)
+ 说明：
	+ 使用 `es` 存放目标空间 `0:200~0:20b` 的段地址，用 `ds` 存放原始空间 `ffff:0~ffff:b` 的段地址。
	+ 在访问内存单元的指令 `mov es:[bx],al` 中，显式地用附加段前缀 `es:` 给出单元的段地址，这样就不必在循环中重复设置 `ds`。

#### 上述程序的问题

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240726221031.png)

##### 解决方案
+ 将数据直接写在代码段中：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240726222057.png)

##### 上述程序存在问题

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240726222146.png)

##### 改进方案
+ 定义一个标号 `start`，指示代码开始的位置。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240726222311.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240726222335.png)

### 实验
+ 编程，向内存 `0:200~0:23F` 依次传送数据 `0~63(3FH)`，程序中只能使用 9 条指令，包括 `mov ax,4c00h` 和 `int 21h`。
```assembly
assume cs:codesg 
codesg segment 

    mov ax,0000h
    mov es,ax

    mov bx,0200h
    mov cx,0040h
    
s:  mov es:[bx],bl
    inc bx
    loop s

	mov ax,4c00h 
	int 21h 
	
codesg ends 
end
```

+ 结果：
![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240727222813.png)
## 第五章 包含多个段的程序
### 在代码段中使用栈

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240729172547.png)

#### 数据逆序存放程序

![image.png](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240729172622.png)

#### 在 Debug 中的执行结果

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240729172729.png)

### 将数据、代码、栈放入不同的段
#### 评价上述方案

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240729173335.png)

+ 特点：数据、栈和代码都在一个段。
+ 问题：
	+ 程序显得混乱，编程和阅读时都要注意何处是数据，何处是栈，何处是代码。
	+ 只应用于要处理的数据很少，用到的栈空间也小，加上没有多长的代码。
+ 对策：数据、栈和代码放在不同段。

#### 将数据、代码、栈放入不同的段

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240729185016.png)

#### 在 Debug 中的执行结果

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240729211331.png)

## 第六章 更灵活的定位内存地址的方法
### 处理字符问题
+ 在汇编程序中，用 `'……'` 的方式指明**数据是以字符的形式给出**的，编译器将把它们转化为相对应的 `ASCII` 码。
+ 代码：

```assembly
assume cs:code,ds:data

data segment
 db 'yuGin'
 db 'Chui'
data ends

code segment 
start: mov al,'a'
	   mov bl,'b'

	   mov ax,4c00h 
	   int 21h 
code ends 
end start
```
+ 编译为可执行文件，并用 `Debug` 加载查看 `data` 段中的内容：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240818123246.png)
+ 说明：
	+ 先用 `r` 命令分析一下 `data` 段的地址，因为 `DS=075A`，需要加上一个 `256` 字节称为**程序段前缀（PSP）的数据区**，从 `256` 字节处向后的空间存放的是程序。
	+ 用 `d` 命令查看 `data` 段，`Debug` 以十六进制数码和 `ASCII` 码字符形式展示其中的内容。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240818124008.png)

### 大小写转换问题

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240818125437.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240818125503.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240818125533.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240818125601.png)

### [bx+idata] 方式寻址
#### [bx+idata] 的含义
+ `[bx+idata]` 是表示一个内存单元，它的偏移地址为 `(bx)+idata`（`bx` 中的数值加上 `idata`）。
+ `mov ax,[bx+200]` / `mov ax, [200+bx]` 的含义：
	+ 将一个内存单元的内容送入 `ax`，
	+ 这个内存单元的长度为 `2` 字节（字单元），存放一个字，
	+ 内存单元的段地址在 `ds` 中，偏移地址为 `200` 加上 `bx` 中的数值，
	+ 数学化的描述为： `(ax)=((ds)*16+200+(bx))`；
+ 指令 `mov ax,[bx+200]` 的其他写法（常用）：
	+ `mov ax,[200+bx]`，
	+ `mov ax,200[bx]`，
	+ `mov ax,[bx].200`；

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819171406.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819172155.png)

### SI 寄存器和 DI 寄存器

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819211915.png)

#### SI 寄存器和 DI 寄存器常执行与地址有关的操作
+ `SI` 寄存器和 `DI` 寄存器是 8086 CPU 中和 `BX` 功能相近的寄存器
	+ 区别：`SI` 寄存器和 `DI` 寄存器不能够分成两个 8 位寄存器来使用。
	+ `BX` 寄存器：通用寄存器，在计算存储器地址时，常作为基址寄存器来使用，
	+ `SI` 寄存器：`source index`，源变址寄存器，
	+ `DI` 寄存器：`destination index`，目标变址寄存器；

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819212350.png)
#### 应用

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819212724.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819212805.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819212824.png)

### [bx+si] 和[bx+di] 方式寻址

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819215001.png)

### [bx+si+idata] 和[bx+di+idata] 方式指定地址

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819215138.png)

### 不同的寻址方式的灵活应用
#### 对内存的寻址方式

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819215606.png)

#### 案例 1

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819220030.png)

#### 案例 2

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819220355.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819220433.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20240819220617.png)

