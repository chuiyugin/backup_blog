---
title: C语言系统学习
tags:
  - C
  - 系统观
categories:
  - C语言
date: 2025-3-26 13:30:00
excerpt: C语言系统学习
---
# C 语言系统学习
## C 语言编译过程
### 预处理
+ `#include` ：头文件包含（把头文件中的内容复制到指令所在的位置）
+ `#define N 5`：宏定义（简单的文本替换）
+ `#define FOO(x) (1 + (x) * (x))`：带参数的宏（宏函数）（文本替换：用“实参”替换“形参”）
+ 宏函数注意事项：

![宏函数注意事项](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326143036.png)
+ 为什么要用宏函数？
	+ 避免函数调用的开销（频繁调用且简短的函数可以用宏函数）；
	+ 提供了一定的“宏编程”能力。

### 编译
+ 经过预处理器处理的文件会交给编译器进行编译；
+ 编译器会把程序翻译成对应平台的汇编代码。
### 汇编
+ 汇编器会把生成的汇编代码翻译成对应平台的机器代码（目标文件）；
+ 此时程序还不能运行，还需要经过最后一个步骤——链接。
### 链接
+ 在链接阶段，链接器会把由汇编器生成的目标代码和程序需要的其它附加代码整合在一起，生成最终可执行的程序；
+ 这些附加代码包括程序用到的库函数（如 `printf` 函数）。
### 汇总
+ 整个过程如下图所示：

![C语言的编译过程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326142719.png)
## 虚拟内存空间
+ 程序经过预处理、编译和链接，最终生成可执行文件；
+ 可执行文件被操作系统加载到内存，程序得以运行，运行中的程序称为**进程**（`process`）；
+ 每个进程都有自己的虚拟内存空间，如下图所示：

![进程的虚拟内存空间](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326151449.png)

## 变量
+ 变量要绑定一个值：

![变量](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326151924.png)

## 输入输出模型
+ 输入输出模型是为了平衡内存和外部设备的速度差异。

![输入输出模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326153610.png)

### printf
+  `printf = print + format` 打印＋格式化；
+ 作用：打印格式串中的内容，并用后面表达式的值替换转换说明（即 `%d` 、`%f` 等占位符）。

![printf](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326155245.png)

+ 格式串：

![格式串](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326155451.png)

### scanf
+ 原理：从左到右，依次匹配格式串中的每一项；

![scanf](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326160511.png)

+ `scanf` 返回匹配成功的转换说明的个数；
+ 能够匹配任意多个**空白字符**；
+ 如果有一项匹配不成功，`scanf` 会立刻返回。

![scanf](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326160605.png)

## 整数的编码
### 有/无符号整数
+ 无符号整数：

![无符号整数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326202710.png)

+ 有符号整数：

![有符号整数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326202741.png)
+ 两个性质：

![两个性质](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326203540.png)

+ 原码、补码、反码
	+ 正数的原码、反码、补码均相同。  
	+ 原码：用最高位表示符号位，其余位表示数值位的编码称为原码。其中，正数的符号位为 0，负数的符号位为 1。  
	+ 负数的反码：把原码的符号位保持不变，数值位逐位取反，即可得原码的反码。  
	+ 负数的补码：在反码的基础上加 1 即得该原码的补码。    
	+ 例如：  
		+ +11 的原码为: 0000 1011  
		+ +11 的反码为: 0000 1011  
		+ +11 的补码为: 0000 1011  
  
		+ -7 的原码为：1000 0111  
		+ -7 的反码为：1111 1000  
		+ -7 的补码为：1111 1001  
  
	+ 注意：
		+ 对补码再求一次补码操作就可得该补码对应的原码。
		+ 在计算机系统中，数值一律用补码来表示（存储）。
		+ 使用补码，可以将符号位和其他位统一处理;

## 浮点数
+ 浮点数是不精确的

![浮点数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326204701.png)

## 字符
+ 字符的编码为 ASCII：

![ASCII](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326210204.png)

+ 类型通用的两个特征：

![类型通用的两个特征](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326210319.png)

+ 表示值：

![表示值](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326210426.png)

+ 支持哪些操作：

![支持哪些操作](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326210509.png)

+ 读/写（和用户操作）

![读/写（和用户操作）](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326210602.png)

+ 惯用法：

![惯用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326210830.png)

## 类型转换
+ 隐式转换：不要将有符号整数和无符号整数混合运算

![隐式转换](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326211751.png)

+ 强制转换

![强制转换](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326212431.png)

## 定义别名
+ 定义别名的作用

![定义别名](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326213123.png)

## sizeof 运算符
+ `sizeof()` 运算符

![sizeof()运算符](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250326213219.png)

## void 类型
+ void 是一个类型
	+ 空类型
	+ 没有值

## 表达式
+ 表达式：计算某个值的公式
+ 最简单的表达式：变量和常量
+ 运算符：连接表达式，创建更复杂的表达式
+ 属性：优先级、结合性

## 位运算符
+ `i<<n`：如果没有溢出，左移 $n$ 位相当于乘以 $2^{n}$；
+ `i>>n`： 右移 $n$ 位，相当于除以 $2^{n}$（向下取整）；
	+ 如果 `i` 是无符号值或者非负值，则在左边补 `0`；
	+ 如果 `i` 是负值，其结果是由实现定义的，有可能在左边补 `0`，也有可能在左边补 `1`。
+ 为了可移植性，最好仅对无符号数进行移位运算。

![位运算符](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250329145055.png)

### 问题一
+ 判断一个数是否奇数：
	+ 需要注意 `-1 % 2 == -1` ，因此不能使用 `n % 2 == 1` 来判断。

```c
bool isOdd(int n)
{
	return (n % 2 != 0);
}
```

+ 采用二进制的形式：

```c
bool isOdd(int n)
{
	return (n & 0x1);//奇数的最后一位一定是1，掩码（mask）
}
```

### 问题二
+ 如何判断一个非 `0` 整数是否为 `2` 的幂 `（1，2，4，8，16，......）` ？
	+ 从二进制中能够看到只有一位为 `1`，其余都是 `0`。

![问题二](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250329150927.png)

+ 代码：

```c
bool isPowerOf2(int n)
{
	return (n & n-1) == 0;
}
```

### 问题三
+ 给定一个值不为 `0` 的整数，请找出值为 `1` 的最低有效位（`last set bit`）。

![问题三](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250407160135.png)
+ 代码：

```cpp
int lastSetBit(int n)
{
	return n & -n;
}
```

+ 原理：
	+ `n` 的原码（补码）：`0001 1000`
	+ `-n` 的补码：`0110 1000`
	+ `n & -n` 的码：`0000 1000`
	+ 在计算机系统中，数值一律用补码来表示（存储）。

### 问题四
+ 给定两个不同的整数 a 和 b，请交换它们两个的值（要求不使用中间临时变量）。
	+ 关键是找到一对逆运算，如 `a=a+b-b;`
+ 代码：

```cpp
void swap(int a,int b)
{
	a = a + b;
	b = a - b;
	a = a - b;
}
```

+ 但是使用加法容易造成溢出的情况，可以使用异或这个逆运算。

```cpp
void swap(int a,int b)
{
	a = a ^ b;
	b = a ^ b;
	a = a ^ b;
}
```

### 问题五
+ 一个数组中有一种数出现了奇数次，其他数都出现了偶数次，怎么找到并打印这种数？
+ 关键部分：

```cpp
//采用异或运算找到数组中出现奇数次的数
int yihuo_find(vector<int>& a)
{
    int eor = 0;
    for(int i=0;i<a.size();i++)
    {
        eor = eor ^ a[i];//用eor与数组中的所有元素相异或
    }
    return eor;
}
```

+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <vector>
using namespace std;

//采用异或运算找到数组中出现奇数次的数
int yihuo_find(vector<int>& a)
{
    int eor = 0;
    for(int i=0;i<a.size();i++)
    {
        eor = eor ^ a[i];//用eor与数组中的所有元素相异或
    }
    return eor;
}

int main()
{
    vector<int>  vec1= {1, 1, 3, 3, 3}; //3是奇数个
    int ans = yihuo_find(vec1);
    printf("%d",ans);
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

+ 证明：
	+ 1、具有 `a^a=0` 和 `a^0=a` 的性质；
	+ 2、偶数个数据累积异或后是 `0`，奇数个数据累积异或后就剩下它自身。

### 问题六
+ 一个数组中有两个不相等的数出现了奇数次，其他数都出现了偶数次，怎么找到并打印这两个数？
+ 关键代码：

```cpp
//采用异或运算找到数组中出现奇数次的两个数a、b
void yihuo_find(vector<int>& a,int& ans_1,int& ans_2)
{
    int eor = 0,eor_2 = 0;
    for(int i=0;i<a.size();i++)
    {
        eor = eor ^ a[i];//找到eor = a^b
    }
    //找到eor = a^b中的最右侧的1（问题三的函数）
    int OnlyOne = lastSetBit(eor);
    //由于eor = a^b中的最右侧为1，a和b在这一位一定不相等
    //只需要在数组中找到这一位只为1的数再做一次eor即可把a或者b找出来
    for(int i=0;i<a.size();i++)
    {
        if((OnlyOne & a[i]) != 0)
        {
            eor_2 = eor_2 ^ a[i];//找到eor_2 = a或者eor_2 = b
        }
    }
    ans_1 = eor_2;
    ans_2 = eor_2 ^ eor;
}
```

+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <vector>
using namespace std;

//把int类型最右侧的1找出来
int lastSetBit(int n)
{
	return n & -n;
}

//采用异或运算找到数组中出现奇数次的两个数a、b
void yihuo_find(vector<int>& a,int& ans_1,int& ans_2)
{
    int eor = 0,eor_2 = 0;
    for(int i=0;i<a.size();i++)
    {
        eor = eor ^ a[i];//找到eor = a^b
    }
    //找到eor = a^b中的最右侧的1
    int OnlyOne = yihuo_last_one(eor);
    //由于eor = a^b中的最右侧为1，a和b在这一位一定不相等
    //只需要在数组中找到这一位只为1的数再做一次eor即可把a或者b找出来
    for(int i=0;i<a.size();i++)
    {
        if((OnlyOne & a[i]) != 0)
        {
            eor_2 = eor_2 ^ a[i];//找到eor_2 = a或者eor_2 = b
        }
    }
    ans_1 = eor_2;
    ans_2 = eor_2 ^ eor;
}

int main()
{
    int ans_1=0,ans_2=0;
    vector<int>  vec1= {1, 1, 3, 3, 3, 6}; 
    yihuo_find(vec1,ans_1,ans_2);
    printf("%d、%d\n",ans_1,ans_2);
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

## 语句
+ 语句的分类：

![语句的分类](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250407194813.png)

## 数组
### 数组的内存模型
+ 连续的一片内存空间，并且会划分成大小相等的小空间。
+ 使用同一类型使得能够随机访问元素。

![数组的内存模型_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250407200257.png)

+ 数组的索引一般是从 `0` 开始的：

![数组的内存模型_2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250407200440.png)

+ 数组效率一般高于链表效率：
	+ 空间利用率更高；
	+ 空间局部性（数组是连续的）。

### 数组的定义
+ 数组的定义：
	+ 数组的操作只有取下标。

![数组的定义_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250407201301.png)

![数组的定义_2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250407201707.png)

+ 求数组的大小的宏函数：

```cpp
#define SIZE(a) (sizeof(a) / sizeof(a[0]))
```

+ 类型的判断：
	+ 先看标识符；
	+ 从标识符表示往右看；
	+ 再从标识符往左看得到完整的类型信息，例：

![类型的判断](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250407201452.png)

### 多维数组
+ 注意：C 语言只有一维数组，多维数组本质上也是一维数组。

![多维数组](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250407203805.png)

### 常量数组
+ 常量数组：元素不能够修改。
	+ 安全，用于存储静态数组；
	+ 效率高，编译器能够对常量数组做一些优化。

![常量数组](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250407204317.png)

## 函数
### 函数的特征
+ 数学上的函数：有返回值，没有副作用；
+ C 语言的函数：可以没有返回值，可以有副作用。

### 函数的准则
+ 函数的功能应该越单一越好（能够复用），函数的实现越高效越好；
+ 函数是 C 语言的“基本构建组件”，C 语言的本质就是函数之间的调用。

### 函数相关概念
+ 函数定义（隐含了声明）：`void foo(int a) {...}`
+ 函数声明：`void foo(int a);`
+ 函数调用：`foo(3);`
+ 函数指针：`foo` 或 `&foo`

#### 参数传递
+ 参数传递的内存调用示例：

![参数传递的内存调用](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409141732.png)

+ 特例：数组作为参数传递时，会退化成指向第一个元素的指针！

![参数传递的特例-数组](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409142358.png)

#### 局部变量和外部变量
+ 作用域：编译时变量名可以被引用的文本范围；
+ 存储期限：运行时变量绑定的值可以被引用的时间长度：

![存储期限](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409151729.png)

+ 局部变量：定义在函数里面的变量；
	+ 作用域：块作用域，从定义开始到块的末尾（块：大括号 `{}` ）。
	+ 存储期限：
		+ 默认——自动存储期限-栈
		+ `static` ——静态存储期限-数据段
+ 默认存储期限示例：

![默认存储期限](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409153043.png)

+ 静态存储期限示例：

![静态存储期限](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409153131.png)

+ 外部变量（全局变量）：定义在函数外面的变量。
	+ 作用域：文件作用域，从定义开始到文件末尾。
	+ 存储期限：静态存储期限。

##### 相关问题
+ 静态存储期限的局部变量和外部变量有什么区别？
	+ 答：作用域不同！
+ 静态储存期限的局部变量有什么作用？（使用场景）
	+ 答：能够存储上一次函数调用的状态！

![静态储存期限的局部变量使用场景](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409153759.png)

## 递归
### 递归的含义
+ 递归的含义：

![递归的含义](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409154119.png)

+ 具有递归结构的问题，不一定要用递归求解。
+ 递归可能存在大量重复计算的问题。
+ 可以采用**动态规划**来避免重复计算的问题：顺序求解子问题，并将子问题的解保存起来，从而避免重复计算，最终求解到大问题。

### 汉诺塔问题
+ 汉诺塔问题：

![汉诺塔问题_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409195250.png)

![汉诺塔问题_2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409195403.png)

### 约瑟夫环问题
+ 约瑟夫环问题：

![约瑟夫环问题_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409200726.png)

![约瑟夫环问题_2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409200946.png)

+ 更一般的约瑟夫环问题：

![更一般的约瑟夫环问题](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409201409.png)

### 递归三问
+ 递归三问：什么时候使用递归？

![递归三问](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250409201536.png)

## 指针基础
### 内存模型
+ 指针的内存模型：
	+ 计算机的最小寻址单位——字节
	+ 变量的地址——变量首字节的地址
	+ 指针——地址
	+ 指针变量——存储地址值的变量

![指针的内存模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250411131416.png)
### 指针的操作
+ 指针的定义：
	+ 变量名：`p`
	+ 类型：`int*`
	+ 注意事项：声明指针变量时，需要指定它指向对象的类型

![指针的定义](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250411131533.png)

+ 指针的两个基本操作：
	+ 取地址运算符：`&` —— `&i`
	+ 解引用运算符：`*` ——  `*p` 
		+ 指针变量 `p` 指向对象的别名
		+ 间接引用——访问内存两次

![指针的两个基本操作](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250411131714.png)

### 野指针
+ 野指针：不知道指向哪块数据。
	+ 对野指针进行解引用操作，是未定义行为。

![野指针](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250411155133.png)

+ 如何给指针变量赋值？
	+ `int *p = &i;`
	+ `int *q = p;` —— 指针变量 `p`，`q` 指向同一个对象。
	+ `p = NULL;`：空指针，不指向任何对象指针。

+ 指针赋值示例：

![指针赋值示例_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250411155759.png)

![指针赋值示例_2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250411155946.png)

### 指针的应用
+ 作为参数——在被调函数中修改主调函数的值。

![指针的应用_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250411160139.png)

+ 指针作为返回值。
	+ 教训：不要返回指向当前栈帧的指针！

![指针的应用_2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250411160254.png)

### 指针常量和常量指针
+ 指针变量 `p` 对内存 `1`，内存 `2` 都有写权限：

![指针变量](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416133938.png)

+ 常量指针，对内存 `1` 有写权限：

![常量指针](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416134812.png)

+ 指针常量，对内存 `2` 有写权限：

![指针常量](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416134930.png)

+ 两边加 `const` 修饰符，对内存 `1`，内存 `2` 都没有写权限：

![两边加const](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416135053.png)

+ 本质：限制变量的写权限！

### 传入参数和传出参数
+ 传入参数：在函数里面，不会（不能够）通过指针变量修改它指向的对象：

![传入参数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416135658.png)

+ 传出参数：在函数里面，可以通过指针变量修改它指向的对象！

![传出参数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416140224.png)

### 指针的算术运算
+ 画图表现数组和指针：

![数组和指针的画图表示](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416141535.png)

+ 指针加上一个整数，结果还是一个指针：
	+ 指针加上一个整数 `n`，其含义在于指针向右偏移了 `n` 个单位；
	+ 单位表示的是对象类型的大小。

![指针加上整数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416141818.png)

+ 指针减去一个整数 `n`，其含义在于指针向左偏移了 `n` 个单位：

![指针减去整数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416142057.png)

+ 两个指针相减，结果是一个整数：

![两个指针相减](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416142407.png)

+ 根据指针减法，可以定义指针的比较运算：

![指针的比较运算](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416142618.png)

### 指针和数组的关系
+ 采用指针处理数组：

![指针处理数组](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416143530.png)

+ `*` 和 `++` 的组合，`--` 同理：

![指针运算的组合](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416144140.png)

+ 指针运算的组合的示例：

![指针运算的组合的示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416144222.png)

+  在必要的时候，数组可以退化成指向它索引为 `0` 元素的指针：

![数组退化示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416144603.png)

+ 数组退化的几种情况总结
	+ 数组作为参数：`fun(arr);`
	+ 数组在赋值表达式的右边：`int* p = arr;`
	+ 数组参与算术运算：`arr+3;`

 + 指针也支持取下标运算：

![指针取下标运算](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416145337.png)

+ 下标可以放到中括号 `[]` 前面：

![下标可以放到中括号前面]( https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416145643.png )

## 字符串
### 字符串字面值
+ 三种书写方式：
	+ 如第三种方式，如果两个字符串字面值之间仅有空白字符，可以认为这两个字符串字面值是相邻的，相邻的字符串字面值在编译的时候就会拼接成一个字符串字面值。

![字符串字面值的三种书写方式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416150744.png)

+ 内存模型：
	+ 字符串字面值在代码段——不可被修改的。
	+ 可以把字符串字面值看作常量数组。

![字符串字面值的内存模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416151840.png)

+ 字符串字面值支持的操作：
	+ 常量数组能支持的操作，字符串字面值都支持，例：
	+ 十进制转换为十六进制的函数：

![十进制转换为十六进制的函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416152559.png)

### 字符串变量
+ 总纲：
	+ C 语言中没有字符串类型—— `string`、`String`
	+ C 语言中的字符串依赖字符数组存在！
	+ C 语言字符串是一种“逻辑类型”。

![字符串和字符数组](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416154414.png)

+ 注意事项：
	+ 字符串必须以 `'\0'` 结尾！
	+ 在 C 语言中求字符串的长度不是 $O(1)$ 的时间复杂度，不能写成下面的例子：

![不能写成该形式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416154620.png)

#### 声明和初始化
+ 注意数组的初始化和字符串的字面值。

![声明和初始化](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416191810.png)

#### 读和写（和用户交互）
+ `printf()+%s` 输出字符串

![输出字符串](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416192436.png)

+ `scanf()+%s` 读字符串
	+ `%s` 的匹配规则：
		+ 忽略前置空白的字符，读取字符填入字符数组；
		+ 遇到空白字符结束。
	+ 缺点：
		+ 不能够存储空白字符；
		+ 不会检查数组越界。

![scanf()+%s 读字符串](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416192753.png)

+ `puts()` 函数：`puts(str);` 等价于 `printf("%s\n",str);`

![puts() 函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416195429.png)

+ `gets()` 函数
	+ 从 `stdin`（标准输入流） 中读取一行数据，传入字符数组，并将 `'\n'` 替换为 `'\0'`；
	+ 缺陷：不会检查数组越界。

![gets() 函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416200223.png)

+ `fgets()` 函数
	+ 和 `gets()` 函数的区别：
		+ 会检查是否越界；
		+ 会存储 `'\n'` 符，并在后面添加 `'\0'`。

![fets() 函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416200843.png)

### 字符串库函数
+ 需要引入 `#include <string.h>` 库。
#### strlen
+ `strlen()` 的实现和遍历字符串的惯用法：

![遍历字符串的惯用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416204136.png)

+ `strlen()` 不会计算 `'\0'`：

![计算字符串的长度](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416204226.png)

#### strcpy() 和 strncpy() 函数
+ `strcpy()` 函数不检查数组越界，`strncpy()` 函数要设定最大拷贝的数量。

![strncpy()函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416205020.png)

+ `strcpy()` 函数的实现和惯用法：
	+ 将赋值运算符右侧的"表达式”的值赋给左侧的变量，赋值表达式的值就是被赋值的变量的值。
	+ 例如：`a=(b=5)` 相当于 `b=5` 和 `a=b` 两个赋值表达式，因此 `a` 的值等于 `5`，整个赋值表达式的值也等于 `5`。

![strcpy()的实现和惯用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416210657.png)

+ `strncpy()` 函数的实现：

![strncpy()的实现](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416211333.png)

#### strcat() 和 strncat() 函数
+ `strcat()` 函数不检查数组越界，`strncat()` 函数要设定最大拷贝的数量。

![strcat() 和 strncat() 函数的使用](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416212013.png)

+ `strcat()` 函数的实现：

![strcat()函数的实现](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416212212.png)

+ `strncat()` 函数的实现：

![strncat()函数的实现](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416212331.png)

#### strcmp()函数
+ 字符串的比较：`strcmp()` 函数
+ 比较规则：字典序（`ASCII`）

![字符串的比较](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416212727.png)

+ `strcmp()` 函数的实现：

![strcmp()函数的实现](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250416213027.png)

### 字符串数组
#### 字符串数组
+ 字符数组的数组——二维字符数组
	+ 缺陷：
		+ 浪费内存空间；
		+ 交换字符串不方便。
	+ 初始化的时候是数组初始化式。

![字符串数组](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250417152319.png)

#### 字符指针数组
+ 储存的是字符指针，比较灵活，不会造成内存空间的浪费；
+ 直接初始化的时候是字面值，字符串存储在代码段，无法修改；
+ 需要能够修改的字符串要先定义字符串变量再合并到字符指针数组。

![字符指针数组](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250417153006.png)

#### 命令行参数
+ 命令行参数可以认为是操作系有调用 `main()` 函数时传递的参数。
+ 命令行参数和从 `stdin` （标准输入）读取数据有什么区别？
	+ 命令行参数：程序还未执行；
	+ 从 `stdin` 读取数据：程序已经运行。
+ 命令行参数有什么作用？
	+ `cp src dst`：写一些非常通用的程序；
	+ `ls -l` ：改变程序的行为，参数不同，行为不同。

![操作系统与主函数的关系](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418152343.png)

+ `argc` : `argument count`，命令行参数的个数；
+ `argv`：`argument vector` ，命令行参数，命令行参数都是字符串。
	+ `argv[0]`：表示可执行程序的路径。

![命令行参数代码](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418152458.png)

+ 命令行参数的内存模型：

![命令行参数的内存模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418152616.png)

+ 命令行参数的类型转换（`sscanf()` 函数)：

![命令行参数的内存模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418153127.png)

+ 不同的指定格式（类型）读取函数，从不同的输入中读取数据：

![不同的指定格式（类型）读取函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418153308.png)

## 结构体
#### 结构体的含义
+ 对象的含义：
	+ 属性：静态数据；
	+ 方法：行为。
+ C 语言的结构体类似其它语言的“类”，不一样的是，结构体只有属性没有方法。

#### 结构体的语法
+ 结构体的语法：

![结构体的语法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418155636.png)

+ 一般来说，使用 `typedef` 给 `struct` 类型取别名 `Student`，这样就可以直接使用 `Student a;` 来定义结构体。

![结构体的typedef使用](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418164346.png)

+ 匿名结构体，只能被使用一次

![匿名结构体](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418164513.png)

#### 结构体的内存模型
+ 一片连续的内存空间；
+ 会按声明的顺序存放每一个成员；
+ 在结构体变量的中间或后面，可能会有填充（由于以前的数据总线只有 `32 bit` ，因此填充是为了对齐，对齐是为了更快地访问数据）。

![结构体的内存模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418160257.png)

#### 结构体变量的操作
+ 获取成员和赋值（结构体变量的复制），但是由于结构体的参数量可能很大，所以往往更习惯传递一个指向结构体变量的指针。

![获取成员和赋值](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418163457.png)

+ 结构体指针操作的语法糖：

![结构体指针操作的语法糖](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418163553.png)

## 枚举
+ 枚举的作用：表示一些离散值（类别或者状态）
+ 枚举的定义和用法：

![枚举的定义和用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418171111.png)

## 动态内存分配
+ 为什么要在堆上分配空间？
	+ 因为栈空间是受限的！
		+ 栈帧的大小是在编译期间确定的，而且栈不能存放动态大小的数据；
		+ 栈空间比较小：
			+ 主线程：8M；
			+ 其他线程：2M；
			+ 因此栈上不能存放很大的数据。
		+ 每个线程都有自己的栈，所以栈空间最好不要放多线程共享的数据。

### 堆空间
+ 堆空间的内存模型：

![堆空间的内存模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418172137.png)

+ 如何申请堆空间？

![申请堆空间的三个函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418203143.png)

+ 在堆上成功创建连续的内存空间后会返回指向新内存块的指针，创建失败则返回空指针。

![申请堆空间的三个函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250418203817.png)

+ `calloc()` 函数的示例代码：

```cpp
int main(void)
{
	int *p = calloc(100,sizeof(int));
	if(p==NULL)
	{
		printf("Error: calloc failed\n");
		exit(1);
	}
}
```

+ 其中 `void*` 是通用指针，作用是可以和其它类型指针相互转换。
	+ 如果 `void*` 指向对象的类型还不确定，不能够直接操作通用指针（例如对指针进行解引用等操作）。

### 动态数组
+ 自定义实现动态数组的模型：

![动态数组模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250424211336.png)

+ 依赖关系如下：
	+ 依赖接口，不需要依赖具体的实现！

![依赖关系图](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250424211453.png)

+ `realloc()` 函数的缩容和扩容原理

![realloc()函数的缩容和扩容原理](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250424211834.png)

+ 扩容代码：

```cpp
void grow_capacity(Vector* v)
{
    int new_capacity = v->capacity < PREALLOC_MAX ?
        v->capacity * 2 : v->capacity+PREALLOC_MAX;
    
    //下面代码有问题，realloc失败会返回NULL，原来的内存空间不会被释放，造成内存泄漏
    //v->elements = realloc(v->elements,new_capacity * sizeof(E));
    
    //正确代码写法
    E* p = realloc(v->elements,new_capacity * sizeof(E));
    if(p == NULL)
    {
        printf("Error: realloc failed in grow_capacity!");
        exit(1);
    }

    v->elements = p;
    v->capacity = new_capacity;
}
```

+ 大端法和小端法

![大端法和小端法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250424212052.png)

+ `free()` 函数的使用问题：
	+ `free()` 函数使用之后，其原本指针属于悬空指针（野指针的一种），因此
		+ 要避免 `free()` 函数使用多次的问题（`double free`）；
		+ `free()` 函数使用之后继续使用原本的指针（`use after free`）；
	+ 当堆上的数据不在使用时，应该有且只释放一次！

![free()函数的使用问题](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250424212751.png)

+ 垃圾回收器的优缺点：

![垃圾回收器的优缺点](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250424212858.png)

+ `main.c` 代码：

```c
#include "Vector.h" //""：搜索路径：当前目录 -> 系统的头文件包含的目录下
#include <stdio.h>  //<>：搜索路径：系统的头文件包含目录下
#include <stdlib.h>

int main(void)
{
    //创建空的动态数组
    Vector* v = vector_create();

    for(int i=0; i<200; i++)
    {
        push_back(v,i);
    }
    printf("push_finish\n");
    vector_destroy(v);
    printf("destroy_finish\n");
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

+ `Vector.h` 代码：

```c
//头文件：类型的定义和API声明
#include <stddef.h>
#include <stdlib.h>

typedef int E;

typedef struct
{
    E* elements;
    int capacity;
    int size;
}Vector;

//函数声明
//构造函数
Vector* vector_create(void);

//析构函数
Vector* vector_destroy(Vector* v);

void push_back(Vector* v,E val);
```

+ `Vector.c` 代码：

```c
#include "Vector.h"//""：搜索路径：当前目录 -> 系统的头文件包含的目录下

#define DEFAULT_CAPACITY 8
#define PREALLOC_MAX 1024 //提前申请的最大值

//构造函数（创建空的动态数组）
Vector* vector_create(void)
{
    Vector* v = malloc(sizeof(Vector));
    if(v==NULL)
    {
        printf("Error: malloc failed in vector_create!");
        exit(1);
    }
    E* p = malloc(DEFAULT_CAPACITY * sizeof(E));
    if(p == NULL)
    {
        free(v);//要将之前创建的v释放掉
        printf("Error: malloc failed in vector_create!");
        exit(1);
    }
    //设置基本参数
    v->elements = p;
    v->capacity = DEFAULT_CAPACITY;
    v->size = 0;

    return v;
}

//析构函数（释放动态数组）
Vector* vector_destroy(Vector* v)
{
    //从内到外释放（按照申请的相反顺序释放）
    free(v->elements);
    free(v);
}

void grow_capacity(Vector* v)
{
    int new_capacity = v->capacity < PREALLOC_MAX ?
        v->capacity * 2 : v->capacity+PREALLOC_MAX;
    
    //下面代码有问题，realloc失败会返回NULL，原来的内存空间不会被释放，造成内存泄漏
    //v->elements = realloc(v->elements,new_capacity * sizeof(E));
    
    //正确代码写法
    E* p = realloc(v->elements,new_capacity * sizeof(E));
    if(p == NULL)
    {
        printf("Error: realloc failed in grow_capacity!");
        exit(1);
    }

    v->elements = p;
    v->capacity = new_capacity;
}

void push_back(Vector* v,E val)
{
    //判断是否需要扩容
    if(v->size == v->capacity)
    {
        grow_capacity(v);
    }
    //添加元素val
    v->elements[v->size++] = val;
}
```

### 二级指针
+ 二级指针，即指向指针的指针。

![二级指针](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250425152831.png)

+ C 语言函数是的参数传递属于传值，因此下面例子中只是把指针 `head` 的值传入了 `addNode()` 函数的栈帧中，并没有实质性地修改指针 `head` 的指向。

![只传递了一级指针的值](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250425153224.png)

+ 传**二级指针**即可解决上述问题：

![二级指针](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250425153433.png)

+ 总结：怎么确定传一级指针还是二级指针？
	+ 想要修改哪个变量，就传那个变量的地址；
	+ 想修改指针指向的对象，传**一级指针**；
	+ 想修改指针的值（指向），传**二级指针**。

### 函数指针
+ 函数指针，即指向函数的指针，它保存了函数的地址，可以通过它调用指向的函数。

![函数指针](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250425154607.png)

+ 函数指针的语法和调用：

![函数指针的语法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250425154651.png)

![函数指针的语法和调用](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250425154817.png)

+ 函数指针的作用：
	+ 函数式编程（传递函数，返回函数），通过函数指针支持函数式编程。
		+ 分解任务，解耦合。
	+ 编写非常通用的函数（功能非常强大的函数）。
+ `qsort()` 函数示例：

![qsort()函数示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250425161711.png)

+ `qsort()` 函数利用函数指针（钩子函数）分解任务，解耦合：

![qsort()函数利用函数指针（钩子函数）分解任务，解耦合](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250425161959.png)

## 数据结构
### 链表
#### 单向链表
+ 增：在某个结点后面添加结点。

![单链表增加结点](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250425163131.png)

+ 删：在某个结点后面删除。

![单链表删除结点](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250425163238.png)

+ 查：
	+ 根据索引查找结点；
	+ 查找与特定值相等的结点。

![单链表查找结点](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250425163535.png)

+ 遍历：正向遍历。

![单链表的正向遍历和插入结点](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250425164651.png)

#### 双向链表
+ 双向链表的模型：

![双向链表的模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250426141149.png)

+ 基本操作：
	+ 增：能够在某个结点的前后添加；
	+ 删：删除当前结点；
	+ 查：
		+ 根据索引查找值：
			+ 单链表：$O(n)$，平均遍历 $\frac{n}{2}$；
			+ 双链表：$O(n)$，平均遍历 $\frac{n}{4}$；
		+ 查找与特定值相等的结点：
			+ 无序：$O(n)$；
			+ 有序：双链表比单链表效果更好（在多次查找的时候，通过记录上一次查找的结点来优化）；
		+ 查找前驱结点：$O(n)$；
	+ 遍历：
		+ 正向；
		+ 逆向。
+ 空间和时间：
	+ 空间换时间：缓存、缓冲；
	+ 时间换空间：压缩、交换区（ `swap area` ）；

### 栈
+ 栈的模型：栈是操作受限的线性表（数组、链表），在一端添加，在同一端删除元素。
	+ 特性：先进后出
+ 为什么需要栈这种数据结构？
	+ 安全；
	+ 可读性强；
	+ 和现实生活中的场景是对应的。

![栈的模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250426202955.png)

+ 栈的基本操作：
	+ 添加：`push()`;
	+ 删除：`pop()`;
	+ 查找：`peek()`;
	+ 判空：`empty()` ->遍历来实现；
+ 栈的实现：

![栈的实现](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250426203434.png)

####  栈的应用
##### 函数调用栈
+ 函数调用栈：

![函数调用栈](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250426203803.png)

##### 符号匹配问题
+ 符号匹配问题：
	+ 遍历字符串
		+ 遇到左括号，将对应的右括号入栈；
		+ 遇到右括号，出栈，判断是否和遇到的右括号一致：
			+ 否：不匹配
			+ 是：继续
		+ 遍历完字符串后，判断栈是否为空：
			+ 否：不匹配
			+ 是：匹配

![符号匹配问题](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250426204701.png)

##### 栈表示优先级
+ 栈表示优先级：
	+ 单调栈：从栈顶到栈底是单调递增或者单调递减的。
+ 中缀表达式和后缀表达式：

![中缀表达式和后缀表达式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250426205542.png)

+ 如何计算后缀表达式：

![计算后缀表达式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250426205729.png)

+ 如何将中缀表达式转换成后缀表达式：

![将中缀表达式转换成后缀表达式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250426210217.png)

##### 用栈记录轨迹
+ 浏览器的前进/后退功能：
	+ `http`：无状态协议，每一次请求是独立的。

![浏览器的前进/后退功能](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250427200407.png)

+ 深度优先搜索
+ 回溯问题

### 队列
#### 队列模型
+ 队列模型：受限的线性表，一端添加元素，另一端删除元素。

![队列模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250427204016.png)

#### 队列基本操作
+ 基本操作：
	+ 入队列：`push()`;
	+ 出队列：`pop()`;
	+ 查看队头：`peek()`；
	+ 判空：`empty()`;
	+ 判满：`full()`;

#### 队列的实现（动态数组）
##### 实现一
+ 队头为 `0` 位置，用 `rear` 标识队尾：

![队列的实现_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250427204904.png)

##### 实现二
+ 用 `front` 标识队头，用 `rear` 标识队尾：

![队列的实现_2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250427205206.png)

##### 实现三
+ 采用循环数组：

![采用循环数组实现队列](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250427205808.png)

+ 入队列（需要扩容的设计）：

![入队列（需要扩容的设计）](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250427210406.png)

+ 其它操作的实现：

![其它操作的实现](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250427210454.png)

#### 队列的应用
+ 缓冲（先进先出），具有公平性，一般采用有界队列。
+ 消息队列（中间件），避免集群之间耦合导致故障扩散。

![消息队列（中间件）](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250427211143.png)

+ 广度优先搜索（三度好友、队列）。

![广度优先搜索](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250427211308.png)

### 哈希表
#### 引入
+ 为什么需要哈希表？
+ 问题：统计一个文件中，字母出现的次数（不区分大小写）
	+ 可以采用数组来存储，数组下标表示字母，数组的值表示次数。这样的对应关系称为**键值对**（ `key-value` ）。
	+ 具有以下两点限制：
		+ 键的取值范围很小；
		+ 键可以很容易地转换成数组的下标。
	+ 核心问题在于：如果不满足上述的两个限制条件，该如何表示**键值对**（ `key-value` ）？
		+ 采用哈希表！

![哈希表的引入](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250428125717.png)

#### 模型
+ 同一个哈希表，哈希桶的数据结构可以不一样，但是 `key` 是唯一的！

![哈希表的模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250428125929.png)

#### 基本操作
+ 增：`put(key, val)`;
+ 删：`delete(key)`;
+ 查：`val = get(key)`；
+ 遍历：依次遍历每一个哈希桶。

#### 哈希表的实现
+ 哈希函数（数据的指纹）

![哈希函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250428141648.png)

+ 性能高的哈希函数（等概率随机映射）

![性能高的哈希函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250428141812.png)

+ 哈希桶（用于解决哈希冲突）
	+ 拉链法
	+ 开放地址法

![拉链法哈希桶](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250428142409.png)

![开放地址法哈希桶](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250428142439.png)

+ `main.c` 代码：

```c
#include "hashmap.h"

int main(void)
{
    //创建空的哈希表
    HashMap* map = create_hashmap();

    //向哈希表写入数据
    put(map, "test", "123");
    put(map, "test1", "234");
    put(map, "test2", "345");
    put(map, "test3", "567");
    put(map, "test4", "678");
    put(map, "test5", "789");
    put(map, "test6", "345");
    put(map, "test7", "567");
    put(map, "test8", "678");
    put(map, "test9", "789");

    V val_2 = get(map,"test9");
    puts(val_2);

    put(map, "test2", NULL);
    put(map, "test8", "456");

    V val_1 = get(map,"test8");
    puts(val_1);

    map_remove(map,"test6");

    V val_3 = get(map,"test7");
    puts(val_3);

    if(get(map,"test6")==NULL)
        printf("empty!\n");
    else
        puts(val_3);

    // 销毁哈希表
    destroy_hashmap(map);
    printf("sucess!\n");

    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

+ `hashmap.h` 代码：

```c
#include <stdint.h>
#include <stdbool.h>
#define CAPACITY 10

typedef char* K;
typedef char* V;

// 键值对结点
typedef struct node_s {
  K key;
  V val;
  struct node_s* next;
} KeyValueNode;

typedef struct {
  // 哈希桶
  KeyValueNode** table; // 二级指针
  // 存储的数量
  int size;
  // 容量大小
  int capacity;
  // 哈希函数需要的种子值
  uint32_t hash_seed; 
} HashMap;

// 创建一个固定容量的哈希表
HashMap* create_hashmap(void);
// 销毁一个哈希表
void destroy_hashmap(HashMap* map);

// 插入一个键值对
V put(HashMap* map, K key, V val);
// 查询一个键值对
V get(HashMap* map, K key);
// 删除某个键值对
bool map_remove(HashMap* map, K key);
```

+ `hashmap.c` 代码：

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include "hashmap.h"

#define DEFAULT_CAPACITY 4
#define LOAD_FACTOR 0.75
#define MAX_PREALLOCATE 256

// 创建空的哈希表
HashMap* create_hashmap(void)
{
    HashMap* map = malloc(sizeof(HashMap));
    if(map == NULL)
    {
        printf("ERROR:malloc failed in create_hashmap!");
        exit(1);
    }

    map->size = 0;
    map->capacity = DEFAULT_CAPACITY;
    map->hash_seed = time(NULL);
    map->table = calloc(DEFAULT_CAPACITY,sizeof(KeyValueNode*));
    if(map->table == NULL)
    {
        printf("ERROR:calloc failed in create_hashmap!");
        exit(1);
    }

    return map;
}

// MurmurHash2函数
uint32_t hash(const void* key, int len, uint32_t seed) {
    const uint32_t m = 0x5bd1e995;  // 修正十六进制拼写错误
    const int r = 24;
    uint32_t h = seed ^ len;        // 正确初始化方式

    const unsigned char* data = (const unsigned char*)key;

    while (len >= 4) {
        uint32_t k = *(uint32_t*)data;

        k *= m;
        k ^= k >> r;  // 正确运算顺序
        k *= m;

        h *= m;
        h ^= k;        // 异或操作替代原错误乘法

        data += 4;
        len -= 4;
    }

    // 处理剩余字节
    switch (len) {
        case 3: h ^= data[2] << 16;
                // fall through
        case 2: h ^= data[1] << 8;
                // fall through
        case 1: h ^= data[0];
                h *= m;
    }

    // 最终混合
    h ^= h >> 13;
    h *= m;
    h ^= h >> 15;

    return h;
}

//扩容函数的子函数
void rehash(KeyValueNode* cur,KeyValueNode** new_table,int new_capacity,uint32_t seed)
{
    //计算key的哈希值
    int len = strlen(cur->key);
    int idx = hash(cur->key,len,seed) % new_capacity;
    //头插法
    cur->next = new_table[idx];
    new_table[idx] = cur;
}

//对哈希表进行扩容
void grow_capacity(HashMap* map)
{
    //新的容量
    int new_capacity = (map->capacity <= MAX_PREALLOCATE)?
                    (map->capacity << 1) : (map->capacity + MAX_PREALLOCATE);
    //创建新的哈希数组
    KeyValueNode** new_table = calloc(new_capacity,sizeof(KeyValueNode*));
    if(new_table == NULL)
    {
        printf("ERROR:calloc failed in grow_capacity!");
        exit(1);
    }
    //重新设置哈希种子(更加安全)
    uint32_t new_seed = time(NULL);
    //将旧的哈希表迁移到新的哈希表上
    for(int i=0; i<map->capacity; i++)
    {
        KeyValueNode* cur = map->table[i];
        while(cur != NULL)
        {
            KeyValueNode* next = cur->next;
            //将旧表重映射到新表上
            rehash(cur,new_table,new_capacity,new_seed);
            cur = next;
        }
    }
    //释放旧的表
    free(map->table);

    //更新参数
    map->table = new_table;
    map->capacity = new_capacity;
    map->hash_seed = new_seed;
}


// 往哈希表添加元素
// a.如果key不存在，添加 key-val，并返回NULL
// b.如果key存在，更新key关联的val，返回原来的val
V put(HashMap* map, K key, V val)
{
    int idx = hash(key,strlen(key),map->table) % map->capacity;
    //遍历链表
    KeyValueNode* cur = map->table[idx];
    while(cur != NULL)
    {
        if(strcmp(cur->key,key) == 0)
        {
            //更新key关联的值，并返回原来的值
            V old_val = cur->val;
            cur->val = val;
            return old_val;
        }
        cur = cur->next;
    }
    //cur == NULL
    //a.如果key不存在，添加 key-val，并返回NULL
    KeyValueNode* new_node = malloc(sizeof(KeyValueNode));
    if(new_node == NULL)
    {
        printf("ERROR:malloc failed in put!");
        exit(1);
    }
    new_node->key = key;
    new_node->val = val;

    //判断是否需要扩容
    double table_load = (1.0 * map->size) / map->capacity;
    //如果超过负载因子就需要扩容
    if(table_load >= LOAD_FACTOR)
    {
        grow_capacity(map);
        //计算新的索引
        idx = hash(key,strlen(key),map->hash_seed) % map->capacity;
    }
    //头插法
    new_node->next = map->table[idx];//map->table[idx]哈希桶也存放着结点
    //链接
    map->table[idx] = new_node;
    //更新哈希表信息
    map->size++;

    return NULL;
}

// 根据key值，获取关联的值
// 如果key不存在，返回NULL
V get(HashMap* map, K key)
{
    //对key进行哈希，判断key在哪个哈希桶中
    int idx = hash(key,strlen(key),map->table) % map->capacity;
    //遍历链表
    KeyValueNode* cur = map->table[idx];
    while(cur != NULL)
    {
        if(strcmp(cur->key,key) == 0)
        {
            //找到了
            return cur->val;
        }
        cur = cur->next;
    }
    return NULL;
};

//删除键值对，如果key不存在，什么也不做
bool map_remove(HashMap* map, K key)
{
    int idx = hash(key,strlen(key),map->table) % map->capacity;
    //遍历链表
    KeyValueNode* pre = NULL;
    KeyValueNode* cur = map->table[idx];
    while(cur != NULL)
    {
        if(strcmp(cur->key,key) == 0)
        {
            //删除结点
            if(pre == NULL)
            {
                map->table[idx] = cur->next;
            }
            else
            {
                pre->next = cur->next;
            }
            free(cur);
            map->size--;
            return true;
        }
        pre = cur;
        cur = cur->next;
    }//cur == NULL,什么也不做
    return true;
}

// 销毁一个哈希表
void destroy_hashmap(HashMap* map)
{
    //释放所有结点(遍历哈希表)
    for(int i=0;i<map->capacity;i++)
    {
        KeyValueNode* cur = map->table[i];
        while(cur != NULL)
        {
            KeyValueNode* next = cur->next;
            free(cur);
            cur = next;
        }
    }
    //释放动态数组
    free(map->table);
    //释放HashMap结构体
    free(map);
}
```

#### 哈希表的扩容
+ 安全性：在扩容时，更改 `HashSeed`。

![哈希表的安全性](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250429162926.png)

+ 扩容方案：

![扩容方案](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250429163122.png)

#### 哈希表的性能
+ 哈希表的性能与所有哈希桶中链表的平均长度有关
	+ 一般设定负载因子在 0.75

![哈希表的性能](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250429162600.png)

#### 哈希表的应用
+ 存储键值对数据；
+ Redis（C 语言）—— 内存数据库（键值对数据库）—— 缓存；
	+ Redis 内部大量使用哈希表。

### 位图
#### 位图的模型
+ 位（`bit`）的数组，内存紧凑的数据结构，存储两种状态。
+ 为什么需要构建一个专门的数据结构来表示位的数组？
	+ 计算机最小的寻址单位是字节（`Byte`），而不是位（`bit`）。

![位图的模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250430150825.png)

#### 位图的基本操作
+ 增（`set`）：将某一位设置为 1；
+ 删（`uset`）：将某一位设置为 0；
+ 查（`isset`）：判断某一位是否为 1；
+ 遍历。

#### 位图的实现
+ 要求：
	+ 尽可能少地申请内存空间；
	+ 运算速度要快（采用位运算）。

![位图的实现_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250430151200.png)

+ 不同的运算对应的位运算和位图使用示例（注意是小端法）：

![不同的运算对应的位运算和位图使用示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250430151407.png)

#### 位图的应用
+ 假如有 10 亿的 QQ 用户，要存储每一个用户的在线状态（内存小于 1 G），需要使用什么数据结构？

![位图的应用_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250430151611.png)

+ 如何实现对数组排序并去重？
	+ 遍历原始数组，在对应的数字的位图上置为 1，最后遍历位图即可得到排序并去重的数组。

![位图的应用_2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250430151707.png)

### 二叉树
#### 二叉树的模型
+ 二叉树的每个结点最多有两棵子树，每个结点的度 `degree ≤ 2`：
	+ 二叉树的子树又分为左子树和右子树。

![二叉树的模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250430165607.png)

#### 二叉树的特殊形态
+ 二叉树有两种特殊的形态：完全二叉树和满二叉树。
	+ 完全二叉树：若二叉树的深度为 `h`，除了第 `h` 层外，其它各层（`1 ~ h-1`）层的结点数目都达到最大值，第 `h` 层的结点都连续排列在最左边，这样的二叉树就是完全二叉树；
	+ 满二叉树：每一层的结点数目都达到最大值（包括最下面一层）；
	+ 满二叉树包含完全二叉树。

![二叉树的两种特殊形态](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250430170111.png)

#### 二叉搜索树（Binary Search Tree，BST）
+ 二叉搜索树又叫二叉排序树，要求树中的结点可以按照某个规则进行比较，其定义如下：
	+ 左子树中所有结点的 `key` 值都比根节点的 `key` 值小，并且其左子树也是二叉搜索树；
	+ 右子树中所有结点的 `key` 值都比根节点的 `key` 值大，并且其右子树也是二叉搜索树。

![二叉搜索树（BST）](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250430170529.png)

#### 二叉搜索树的实现
##### 结构
+ 二叉搜索树的结构（ `bst.h` ）:

![二叉搜索树的结构](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250507163651.png)

##### 添加节点
+ 添加节点：

![添加节点](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250507163814.png)

+ 代码：

```c
//添加一个结点
//1、如果key存在则什么也不做
//2、如果key不存在，则添加一个结点
void bst_insert(BST* tree,K key)
{
    TreeNode* parent = NULL;
    TreeNode* cur = tree->root;
    int cmp;//用于比较，只需要比较一次
    while(cur!=NULL)
    {
        //比较
        cmp = key - cur->key;
        if(cmp < 0) //key < cur->key
        {
            parent = cur;
            cur = cur->left;
        }
        else if(cmp > 0) //key > cur->key
        {
            parent = cur;
            cur = cur->right;
        }
        else //key == cur->key，什么也不做
            return;
    }//此时cur == NULL
    //创建树结点
    TreeNode* new_node = calloc(1,sizeof(TreeNode));
    if(new_node == NULL)
    {
        printf("calloc failed in bst_insert!");
        exit(1);
    }
    //初始化树节点
    new_node->key = key;
    //链接到树中
    if(parent == NULL)
        tree->root = new_node;
    else if(cmp < 0) //在左边插入
        parent->left = new_node;
    else //在右边插入
    parent->right = new_node;
}
```

##### 查找节点
+ 查找结点与添加节点逻辑类似，代码如下：

```c
//查找一个结点
TreeNode* bst_search(BST* tree,K key)
{
    TreeNode* cur = tree->root;
    int cmp;//用于比较，只需要比较一次
    while(cur != NULL)
    {
        //比较
        cmp = key - cur->key;
        if(cmp < 0) //key < cur->key
        {
            cur = cur->left;
        }
        else if(cmp > 0) //key > cur->key
        {
            cur = cur->right;
        }
        else //key == cur->key
            return cur;
    }
    return NULL;
}
```

##### 遍历节点
###### 广度优先搜索遍历
+ 广度优先搜索步骤（层次遍历）：

![广度优先搜索步骤](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250507174224.png)

+ 代码：

```c
//层次遍历BST，广度优先搜索
void bst_levellorder(BST* tree)
{
    LinkedListQueue* q = create_queue(); 
    //将根节点入队列
    enqueue(q,tree->root);
    //判断队列是否为空
    while(!is_empty(q))
    {
        int level_size = q->size;
        for(int i=0;i<level_size;i++)
        {
            //出队列，遍历这个节点
            TreeNode* node = dequeue(q);
            printf("%d ",node->key);
            //判断是否有左孩子
            if(node->left)
            {
                enqueue(q,node->left);
            }
            //判断是否有右孩子
            if(node->right)
            {
                enqueue(q,node->right);
            }
        }
        printf("\n");
    }//队列为空了
    destroy_queue(q);
}
```

###### 深度优先搜索遍历
+ 深度优先搜索步骤（前序、中序（二叉排序树）、后序）：

![深度优先搜索步骤](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250507174458.png)

+ 代码：

```c
//中序遍历(二叉排序树)
void bst_inorder(TreeNode* root)
{
    //边界条件
    if(root == NULL)
        return;
    //递归公式
    //遍历左子树
    bst_inorder(root->left);
    //遍历根节点
    printf("%d ",root->key);
    //遍历右子树
    bst_inorder(root->right);
}

//先序遍历
void bst_preorder(TreeNode* root)
{
    //边界条件
    if(root == NULL)
        return;
    //递归公式
    //遍历根节点
    printf("%d ",root->key);
    //遍历左子树
    bst_preorder(root->left);
    //遍历右子树
    bst_preorder(root->right);
}

//后序遍历
void bst_backorder(TreeNode* root)
{
    //边界条件
    if(root == NULL)
        return;
    //递归公式
    //遍历左子树
    bst_backorder(root->left);
    //遍历右子树
    bst_backorder(root->right);
    //遍历根节点
    printf("%d ",root->key);
}
```

###### 删除二叉搜索树节点
+ 删除度为 0 的情况：

![删除度为 0 的情况](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250507201313.png)

+ 删除度为 1 的情况：

![删除度为 1 的情况](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250507201350.png)

+ 删除度为 2 的情况：
	+ 退化成度为 0 或者 1 的情况来处理；
	+ 采用要删除节点的右子树的最小节点来替代当前节点来实现退化！

![删除度为 2 的情况](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250507201559.png)

+ 特例：右子树最小节点就是右子树根节点

![特例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250507201641.png)

+ 代码：

```c
//删除节点
void bst_delete(BST* tree,K key)
{
    // 1.找到要删除的节点
    TreeNode* parent = NULL;
    TreeNode* cur = tree->root;
    K cmp;
    while(cur != NULL)
    {
        cmp = key - cur->key;
        if(cmp < 0) //key < cur->key,向左边找
        {
            parent = cur;
            cur = cur->left;
        }
        else if(cmp > 0)
        {
            parent = cur;
            cur = cur->right;
        }
        else
            break;
    }//cur == NULL || cur != NULL
    if(cur == NULL)
        return;
    // 2.删除节点
    if(cur->left && cur->right) // 度为2的情况
    {
        //退化成度为0或度为1的情况
        //先找右子树的最小节点
        TreeNode* pr_min = cur;
        TreeNode* r_min = cur->right;
        while(r_min->left != NULL)
        {
            pr_min = r_min;
            r_min = r_min->left;
        }
        //退化过程
        cur->key = r_min->key;
        cur = r_min;
        parent = pr_min;
    }
    //度为0或1的情况
    //找到唯一的孩子
    TreeNode* child = cur->left ? cur->left : cur->right;
    if(parent == NULL) // 删除的是根节点
    {
        tree->root = child;
    }
    else
    {
        //将child链接到parent正确的位置
        //必须重新比较，因为可能出现了退化
        K cmp = cur->key - parent->key;
        if(cmp<0)
        {
            parent->left = child;
        }
        else if(cmp>0)
        {
            parent->right = child;
        }
        else //注意：特例cmp==0，右子树最小节点就是右子树根节点
        {
            parent->right = child;
        }
    }
    free(cur);
}
```

#### 二叉搜索树的性能分析
+ 缺陷：不能保证 $O(log(n))$ 时间复杂度的插入，删除和查找。

![二叉搜索树的性能分析](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250507212637.png)

#### 平衡二叉搜索树
+ AVL树和红黑树的对比：

![AVL树和红黑树的对比](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250507212757.png)

#### 红黑树
+ 红黑树的基础模型是 **2-3-4树**，基于此在二叉搜索树的基础上实现红黑树。
+ 问题 1：如何表示2-3-4树中的 `3-node` 和 `4-node` ？

![如何表示 2-3-4 树中的 3-node 和 4-node？](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505081108145.png)

+ 问题 2：“边”是一种逻辑结构，是不存在的，如何表示“边”的颜色？
	+ 把颜色放入孩子节点中！

![如何表示“边”的颜色？](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505081110356.png)

+ 红黑树的性质：
	+ 2-3-4树的高度 ≤ 红黑树高度 ≤ 2×(2-3-4树的高度)
	+ 因为**黑高平衡**的原因！

![红黑树的性质](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505081112194.png)

## 文件流
### 流的模型
+ 流的模型：

![流模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505111548187.png)

+ 优点：
	+ 程序员读写文件时，不需要关心文件的位置；
	+ 数据源（ `data source` ）和数据汇（ `data sink` ）是解耦的。

+ 程序员视角的文件：

![程序员视角的文件](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505111553800.png)

### 前置知识
#### 缓冲区类型
+ 缓冲区是以**先进先出**的方式管理数据的，缓冲区分为三种类型：
	+ 满缓冲：当缓冲区为空时，从输入流中读取数据；当缓冲区满时，向输出流写入数据。
	+ 行缓冲：每次从输入流中读取一行数据；每次向输出流中写入一行数据（`stdin`、`stdout`）。
	+ 无缓冲：顾名思义，就是没有缓冲区（`stderr`，输出错误信息）。

![缓冲区](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505111559381.png)

#### 标准流
+ 标准流不需要手动创建，也不需要手动关闭（即其它文件流都是需要手动操作的）。

![标准流](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505111600959.png)

#### 二进制文件和文本文件
+ 二进制文件：字节

![二进制文件](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505111602043.png)

+ 文本文件：字符

![文本文件](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505111602529.png)

+ 二进制文件和文本文件的区别举例：

![二进制文件和文本文件的区别举例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505111605481.png)

### 文件流接口（API）
#### 文件流操作步骤
+ 文件流操作步骤如下：
	+ 打开文件流：`fopen()`；
	+ 读/写文件：统计、转换、加密、解密......
	+ 关闭文件流：`fclose()`。

#### 打开文件流
+ 打开文件流的函数：

![打开文件流的函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505111612301.png)

+ 文件的路径（`filename` ）
	+ 绝对路径和相对路径

![文件的路径](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505111614706.png)

+ 打开文件的模式
	+ 文件的类型
	+ 对文件的操作（`r`, `w`）
	+ 写模式和追加模式是不一样的，如果文件存在，写模式会清空原有数据，而追加模式会在原有数据的后面写入新的内容。
+ 以文本形式打开文件的模式：

![以文本形式打开文件的模式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505111619785.png)

+ 以二进制形式打开文件的模式：

![以二进制形式打开文件的模式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505111621104.png)

#### 关闭文件流
+ 关闭文件流的函数：

![关闭文件流的函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505111624460.png)

#### 打开和关闭文件流的程序
+ 打开和关闭文件流的程序示例：

```c
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    // 1.打开文件流
    FILE *stream = fopen("a.txt", "w");//可以调整路径和模式
    if (stream == NULL)
    {
        fprintf(stderr, "Open a.txt failed\n");
    }
    // 2.读写文件(统计，转换，加密，解密等业务逻辑)

    // 3.关闭文件流
    fclose(stream);

    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

#### 读写文本文件
##### 一个字符一个字符地读写
+ 一个字符一个字符地读写：

![一个字符一个字符地读写](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505112042697.png)
+ 代码：

```c
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>

int main(int argc, char *argv[])
{
    // xxx.exe src dst
    //  1.参数校验
    if (argc != 3)
    {
        fprintf(stderr, "Usage: %s src dst\n", argv[0]);
        exit(1);
    }

    // 2.打开文件流
    FILE *src = fopen(argv[1], "r");
    if (src == NULL)
    {
        fprintf(stderr, "Open %s failed\n", argv[1]);
        exit(1);
    }

    FILE *dst = fopen(argv[2], "w");
    if (src == NULL)
    {
        fprintf(stderr, "Open %s failed\n", argv[2]);
        fclose(src);
        exit(1);
    }

    // 3.读写文件
    // a.一个字符一个字符地读写: fgetc, fputc
    // 把大写转换为小写
    int c;
    while ((c = fgetc(src)) != EOF)
    {
        fputc(tolower(c), dst);
    }

    // 4.关闭文件流
    fclose(src);
    fclose(dst);

    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

##### 一行一行地读写
+ 一行一行地读写：

![一行一行地读写](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505112302826.png)

+ 代码：

```c
// b.一行一行地读写字符
    char line[MAXLINE];
    char buffer[MAXLINE];
    int line_num = 1;
    while (fgets(buffer, MAXLINE, src) != NULL)
    {
        sprintf(line, "%d. %s", line_num, buffer);
        fputs(line, dst);
        line_num++;
    }
```

##### 格式化地读写
+ 格式化地读写：

![格式化地读写](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505112307531.png)

#### 读写二进制文件
+ 读二进制文件：

![读二进制文件](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505112326481.png)

+ 写二进制文件：

![写二进制文件](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505112327687.png)

+ 代码示例：

```c
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>

#define BUFSIZE 4096

int main(int argc, char *argv[])
{
    // xxx.exe src dst
    //  1.参数校验
    if (argc != 3)
    {
        fprintf(stderr, "Usage: %s src dst\n", argv[0]);
        exit(1);
    }

    // 2.打开文件流
    FILE *src = fopen(argv[1], "rb");// 读二进制文件模式后面要加b
    if (src == NULL)
    {
        fprintf(stderr, "Open %s failed\n", argv[1]);
        exit(1);
    }

    FILE *dst = fopen(argv[2], "wb");
    if (src == NULL)
    {
        fprintf(stderr, "Open %s failed\n", argv[2]);// 读二进制文件模式后面要加b
        fclose(src);
        exit(1);
    }

    // 3.复制二进制文件
    char buffer[BUFSIZE];
    int bytes_num;
    while ((bytes_num = fread(buffer, 1, BUFSIZE, src)) > 0)
    {
        fwrite(buffer, 1, BUFSIZE, dst);
    }

    // 4.关闭文件流
    fclose(src);
    fclose(dst);

    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

#### 移动文件位置
+ `fseek()` 函数：可以改变与 stream 相关联的文件位置。

![fseek()函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505112346021.png)

+ 使用示例：

![fseek()函数使用示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505112347849.png)

+ `ftell()` 函数：

![ftell()函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505112348073.png)

+ `rewind()` 函数：

![rewind()函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505112349584.png)

+ 代码示例：

![代码实现示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505112351024.png)


```c
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>

char *readFILE(const char *path)
{
    // 2.打开文件流
    FILE *src = fopen(path, "rb"); // 以二进制形式
    if (src == NULL)
    {
        fprintf(stderr, "Open %s failed\n", path);
        exit(1);
    }

    // 3.确定文件大小
    fseek(src, 0, SEEK_END);
    long n = ftell(src);
    char *content = malloc(n + 1); // 1 for '\0'

    // 读文件内容，填充content数组
    rewind(src); // 回到文件开头
    int bytes_num = fread(content, 1, n, src);
    content[bytes_num] = '\0';

    // 4.关闭文件流
    fclose(src);

    return content;
}

int main(int argc, char *argv[])
{
    // xxx.exe src
    //  1.参数校验
    if (argc != 2)
    {
        fprintf(stderr, "Usage: %s filename\n", argv[0]);
        exit(1);
    }

    char *content = readFILE(argv[1]);

    // 输出内容
    puts(content);

    free(content);

    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

#### 错误处理
+ 需要引入 `#include <errno.h>` 和 `#include <string.h>` 两个头文件。
+ 错误处理示例：

![错误处理示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505120008076.png)








