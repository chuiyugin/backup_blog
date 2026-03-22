---
title: 侯捷 C++学习笔记之面向对象高级编程（一）
tags:
  - 面向对象
categories:
  - C++语言
date: 2024-10-27 22:20:00
excerpt: C++、编程语言、面向对象高级编程
---
# 侯捷 C++学习笔记之面向对象高级编程（一）
## 头文件与类的声明
### class template 类（模板）简介
+ 例如我们定义下面的一个类，我们可以不把一个类里面定义的变量类型（如：`double`）直接就写死，而是可以定义一个模板 `template`，具体如下面的例子：

```cpp
template<typename T>
class complex
{
public:
	complex (T r = 0, T i = 0)
	: re (r), im (i)
	{ }
	complex& operator += (const complex&);
	T real () const { return re; }
	T imag () const { return im; }
private:
	T re, im;
	friend complex& __doapl (complex*, const complex&);
};
```
+ 当我们具体需要使用上面的的类并定义确定的变量类型的时候可以进行如下操作：

```cpp
complex<double> c1(2.5,1.5);
complex<int> c2(2,6);
```

### inline （内联）函数
+ 若函数在 `class` 本体内部**定义**（并非声明）完成，就会进入内联函数的候选，但是该函数是否真正变成内联函数由编译器所决定。
+ 内联函数具有执行效率比较高的特性，但并非所有定义在 `class` 本体内的函数都能由编译器编译为内联函数。

```cpp
template<typename T>
class complex
{
public:
	complex (T r = 0, T i = 0)
	: re (r), im (i)
	{ }
	//在class本体内声明的函数无法作为内联函数
	complex& operator += (const complex&);
	//下面两个定义在class本体里面的函数会成为内联函数的候选
	T real () const { return re; }
	T imag () const { return im; }
private:
	T re, im;
	friend complex& __doapl (complex*, const complex&);
};
```

+ 要么在外部定义函数的时候加上 `inline` 前缀，如：
```cpp
inline double 
imag(const complex& x) 
{ 
	return x.imag (); 
}
```

## 构造函数
+ 构造函数是用来创建对象的函数，其有如下特点：
	+ 不需要返回类型
	+ 具有初始列（`initialization list`），别的函数没有

```cpp
class complex
{
public:
	complex (double r = 0, double i = 0)//默认实参
		: re (r), im (i) //initialization list 初始列
	{ }
	complex& operator += (const complex&);
	double real () const { return re; }
	double imag () const { return im; }
private:
	double re, im;
	friend complex& __doapl (complex*, const complex&);
};
```

+ 使用构造函数创建对象，在创建对象的过程中自然而然地使用构造函数，例：

```cpp
complex c1(2,1);
complex c2;
complex* p = new complex(4);
...
```

### 函数重载（overloading）
+ 同名的函数可以有多个，其传入的参数不一样，称为函数重载（在编译器看来是不同名的）。
+ 例如：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202410281102136.png)

+ 从上图可以看出，当同名的函数传入的参数**类型，数量**等不一致时，编译器可以区分出这两个函数，称为函数重载。
+ 函数的重载也一般发生在构造函数中。
+ 但是例如上面方框中的两个构造函数，由于第一个函数存在默认值，当使用该构造函数创建对象，并且采用默认值的时候，编译器将无法确认使用哪个函数，因此上面的写法时**错误**的。

## 参数传递与返回值
### 常量成员函数（const member functions）
+ `const` 定义这个参数的内容是不会改变的，在函数里面表示这个函数将不会改变任何参数。
+ 例如：

```cpp
class complex
{
public:
	complex (double r = 0, double i = 0)
		: re (r), im (i) 
	{ }
	complex& operator += (const complex&);
	double real () const { return re; }//加入const，表示将不会改变re的值
	double imag () const { return im; }//加入const，表示将不会改变im的值
private:
	double re, im;
	friend complex& __doapl (complex*, const complex&);
};
```

+ 对于上述类的创建和函数的调用，可能有以下两种情况：
1.  没加 `const`，运行没问题：
```cpp
{ 
	complex c1(2,1); 
	cout << c1.real(); 
	cout << c1.imag(); 
}
```
2. 加了 `const`，但是如果在类的函数中没加 const，将会编译失败。
```cpp
{ 
	const complex c1(2,1); 
	cout << c1.real(); 
	cout << c1.imag(); 
}
```

### 参数传递
+ 参数传递尽量都传引用，当所传的参数不希望被函数所修改可以在引用前面加上 `const`，当所传参数可能被函数所修改时就不在前面加上 `const`。
+ 例如：
```cpp
class complex
{
public:
	complex (double r = 0, double i = 0)//值传递
		: re (r), im (i) 
	{ }
	complex& operator += (const complex&);//引用传递
	double real () const { return re; }
	double imag () const { return im; }
private:
	double re, im;
	friend complex& __doapl (complex*, const complex&);
};
```

+ 引用传递的调用例子：
```cpp
{
	complex c1(2,1);
	complex c2;
	c2 += c1;
	cout << c2;
}
```

### 返回值传递
+ 与参数传递相似，返回值传递也**尽量**传引用，但返回值的传递有时候将无法传引用。
+ 例如：
```cpp
class complex
{
public:
	complex (double r = 0, double i = 0)
		: re (r), im (i) 
	{ }
	complex& operator += (const complex&);//返回值采用引用传递
	double real () const { return re; }//返回值采用值传递
	double imag () const { return im; }//返回值采用值传递
private:
	double re, im;
	friend complex& __doapl (complex*, const complex&);
};
```

#### 无法使用引用传递返回值的情况
+ 要返回的参数是在函数内部创建的将不能采用引用传递。
+ 例如：
```cpp
int my_add(int& a,int& b)
{
	int temp;
	temp = a + b;
	return temp;
}
```
+ 例如上面的例子，`temp` 是在函数内部创建的，因此不能够使用**引用传递来返回值**！
	+ 因为当这个函数运行结束后，`temp` 变量将会被释放。

#### 可以使用引用传递返回值的情况
+ 只要返回的参数不是在函数内部创建的，是在外部就有该参数空间的都可以采用引用传递来返回值。
+ 例如：
```cpp
inline complex& __doapl(complex* ths, const complex& r) 
{ 
	ths ->re += r.re ; 
	ths - >im += r.im ; 
	return *ths ; 
}
```
+ 从上面的例子可以看到，`ths` 是从函数外部传入的参数，其空间本来在函数外部就存在，因此可以使用**引用传递来返回值**。

### 友元（friend）
+  友元可以令表示的函数能够访问该类的**私有成员**，包括**私有变量和私有函数**。
+ 例如：
```cpp
class complex
{
public:
	complex (double r = 0, double i = 0)
		: re (r), im (i) 
	{ }
	complex& operator += (const complex&);
	double real () const { return re; }
	double imag () const { return im; }
private:
	double re, im;
	friend complex& __doapl (complex*, const complex&);//友元
};
```

+ 友元函数调用的例子：
```cpp
inline complex& 
__doapl (complex* ths, const complex& r) 
{ 
	ths - >re += r.re ; 
	ths - >im += r.im ; 
	return *ths ; 
}
```
+ 能够自由取得 `friend` 的 `private` 成员。

### 相同 class 的各个 objects 互为 friends（友元）
+ 如题，在创建了相同 `class` 的各个 `objects` 是互为友元的。
```cpp
class complex
{
public:
	complex (double r = 0, double i = 0)
		: re (r), im (i)
	{ }
	//并没有加friend
	int func(const complex& param)
		{ return param.re + param.im; }
		
private:
	double re, im;
};
```
+ 调用情况如下：
```cpp
{
	complex c1(2,1);
	complex c2;
	c2.func(c1);//没有加friend可以调用函数取private的内容
}
```
+ 解释如下：在创建了相同 `class` 的两个 `objects` （是 `c1` 和 `c2`）是互为友元的。

## 操作符重载与临时对象
### 操作符重载之一：成员函数（this）
+ 在 `class` 中实现操作符的重载：
```cpp
inline complex&
__doapl(complex* ths, const complex& r)
{
	ths->re += r.re;
	ths->im += r.im;
	return *ths;
}
inline complex&
complex::operator += (const complex& r)
{
	return __doapl (this, r);
}
```
+ 需要注意，`this` 是隐藏参数，不需要写在函数参数传递的过程中：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411031557563.png)

#### return by reference 语法分析
+ 传递者无需知道接收者是以引用的形式接收

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411031618944.png)

### 操作符重载之二：非成员函数（无 this）
+ 为了应付用户的三种用法，对应开发三种函数；
+ 与**成员函数**方式的区别在于**非成员函数**采用的是全局函数（`global`）：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411031718666.png)

+ 可以看出，上面的函数绝对不可以采用**返回引用**的形式，因为它们返回的必定是个本地对象（`local object`）。
+ 同时，`typename()` 创建的是**临时对象**，小括号中没有参数就是默认值。例如 `int(5)` 等，表明其是临时的，生命周期很短，在下一行便结束。在上述的例子中也有体现：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411031738262.png)

+ `“+”`表示正，`“-”`表示负的情况：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411031742123.png)

+ 判断两个复数是否相等（两种情况`==`和`!=`）：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411031746875.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411031746434.png)

+ 输出运算符 `“<<”` 也称为流插入运算符，是一个二目运算符，该操作符的重载具有特殊用法：
+ 该操作符重载的过程是将重载函数**右边的参数**作用到**左边的参数**中：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411031802200.png)

## 带指针的class用法
+ 前面的是**不带指针**的 `class` 的用法介绍，后续介绍**带指针**的 `class` 的用法。
### 以 string class 为例
#### 三个特殊函数（Big Three）
```cpp
class String
{
public:
	String(const char* cstr = 0);//构造函数
	String(const String& str);//拷贝构造函数
	String& operator=(const String& str);//操作符重载（拷贝赋值函数）
	~String();//析构函数（该类的对象死亡时调用）
	char* get_c_str() const { return m_data; }//一般的成员函数（属于内联函数，已经写在类里面）
private:
	char* m_data;//创建数组指针
};
```

#### 构造函数和析构函数
+ 带有指针的构造函数
```cpp
inline
String::String(const char* cstr = 0) {
	if (cstr) {
		m_data = new char[strlen(cstr)+1];//动态分配内存，称为array new
		strcpy(m_data, cstr);
	}
	else { // 未指定初值
		m_data = new char[1]; *m_data = '\0';
	}
}
```

+ 析构函数（释放掉不用的动态内存）
```cpp
inline
String::~String() {
	delete[] m_data;//前面new带有[]则delete时候也要带有[],称为array delete
```

+ 创建对象和使用
+ 大括号 `“{}”` 内表示其作用域，离开作用域后**析构函数**自然被调用
```cpp
{
	String s1(),
	String s2("hello");
	
	String* p = new String("hello");//动态创建对象
	delete p;
}
```

+ 总结

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411142139333.png)

#### class 内带有指针成员
+ `class` 内带有指针成员必须使用拷贝构造函数和拷贝赋值函数，不然会出现以下问题：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411142156017.png)

##### 拷贝构造函数
+ 拷贝构造函数代码
```cpp
inline
String::String(const String& str)
{
	m_data = new char[ strlen(str.m_data) + 1 ];
	strcpy(m_data, str.m_data);
}
```

+ 拷贝构造函数的调用
```cpp
{
	String s1("hello ");
	String s2(s1);
}
```

+ 特别之处：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411142228928.png)

##### 拷贝赋值函数
+ 拷贝赋值函数代码
```cpp
inline
String& String::operator=(const String& str)
{
	if (this == &str)//检测自我赋值
		return *this;
		
	delete[] m_data;
	m_data = new char[ strlen(str.m_data) + 1 ];
	strcpy(m_data, str.m_data);
	return *this;//保证可以连等，如 s1=s2=s3;
}
```

+ 一定要在拷贝赋值函数中加入**检测自我赋值**，检测自我赋值不止为了效率还有安全性：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411142248922.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411142249036.png)

+ 拷贝赋值函数的调用
```cpp
{
	String s1("hello ");
	String s2 = s1;
}
```
##### output 函数
+ `output` 函数，其中操作符 `<<` 的重载是将其重载函数的右边参数作用到左边参数，必须写成非成员函数（即全局函数）：
```cpp
#include <iostream.h>
ostream& operator << (ostream& os, const String& str)
{
	os << str.get_c_str();//传递字符串指针（该函数写在String类的内联函数中）
	return os;
}
```

+ `output` 函数的调用：
```cpp
{
	String s1("hello ");
	cout << s1;
}
```

#### 栈（stack）和堆（heap）
+ 栈（`stack`）是存在于是某作用域（`scope`）的一块內存空间（`memory space`）。例如在调用函数时，函数本身即会形成一个 `stack` 用来存放它所接收的参数，以及返回地址。在函数本体 (`function body`) 内声明的任何变量，其所使用的內存块都取自上述 `stack`。
+ 堆（`heap`），或 `system heap`，是指由操作系统提供的一块 `global` 內存空间，程序可动态分配 (`dynamic allocated`) 从某内存中获得若干区块 (`blocks`)。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411151446636.png)

+ `c1` 便是所谓的 `stack object`，其生命在作用域 (`scope`) 结束之际结束。这种作用域內的 `object`，又称为 `auto object`，因为它会被**析构函数**自动清理。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411151450878.png)

+ `c2` 便是所谓的 `static object`，其生命在作用域 (`scope`) 结束之后仍然存在，直到整个程序结束。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411151453950.png)

+ `c3` 便是所谓的 `global object`，其生命在整个程序结束之后才结束。也可以把它视为一种 `static object`，其作用域是**整个程序**。

##### heap objects 的生命周期

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411151500963.png)

+ `P` 所指的便是 `heap object`，其生命在它被 `delete` 之际结束。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411151501010.png)

+ 以上出现内存泄漏 ( `memory leak` )，因为当作用域结束，`p` 所指的 `heap object` 仍然存在，但指针 `p` 的生命却结束了，作用域之外再也看不到 `p` (也就没机会 `delete p`）。

##### new 和 delete 的具体机制
+ `new`：先分配 `memory`, 再调用构造函数

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411151509422.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411151517633.png)


+ `delete`：先调用析构函数，再释放 `memory`

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411151515975.png)

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411151517543.png)

##### array new 一定要搭配 array delete

![image.png](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411152240380.png)

## 细节扩展补充
### 静态（static）
+ 非静态的成员函数有 `this` 指针，能够通过 `this` 指针来区分不同的对象。
+ 而静态的成员函数没有 `this` 指针，只能处理静态数据。
+ 类中的成员函数，若未使用类中的成员变量，则可声明为静态成员函数，函数声明前加上 `static` 关键字。
+ 静态成员函数不属于对象，属于类。静态成员函数不包含编译器提供的 `this` 指针。 在类没有实例化对象前就存在，由于没有 `this` 指针，所以也就没有特定的成员变量可以使用。
+ 静态成员函数与成员函数用法上的主要不同为：
	+ 静态成员函数可以独立访问，调用静态成员函数时，不需要实例化任何对象。只需要使用**类名 + 命名空间标识符** (`::`) 加函数名即可调用。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411162145790.png)

+ 静态（`static`）函数的调用方法：
	+ 通过 `class` 名调用；
	+ 通过对象调用。
```cpp
class Account {
public:
	static double m_rate;//声明静态变量
	static void set_rate(const double& x) { m_rate = x; }
};
double Account::m_rate = 8.0;//定义静态变量，为其分配内存空间，初值可赋可不赋

int main() {
	Account::set_rate(5.0);//通过class名调用
	
	Account a;
	a.set_rate(7.0);//通过对象调用
}
```

### 把构造函数放在 private 区
+ 只有调用 `A::getInstance();` 这个函数对象 `a` 才会被创建出来，并通过类似 `A::getInstance().setup();` 函数的对外接口在外部被调用。
+ 这种方式也成为单例模式：单例模式是一种设计模式，其核心思想是确保一个类只有一个实例，并提供一个全局访问点来获取这个实例。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411162236741.png)

### cout 函数
+ cout 是 ostream 类的一个对象：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411162244217.png)

### 类模板（class template）
+ 类模板的作用：建立一个通用类，类中的成员数据类型可以不具体指定，用一个虚拟的类型来代表。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411162253922.png)

### 函数模板（function template）
+ 函数模板的作用：建立一个通用函数，其函数返回值类型和形参类型可以不具体制定，不用为每一个类型定义新的函数。
+ 下面例子中的 `<` 需要在 `stone` 类中进行操作符函数的重载，这样编译器才能知道如何对 `stone` 类进行比大小。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411162259978.png)

### 命名空间（namespace）
+ 命名空间（`namespace`）是一种将标识符组织在一起的方式，用于防止名字冲突。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411162304335.png)

## 组合、委托与继承
### 组合（composition）
+ 组合（`composition`）指的是一个对象**拥有**并可以控制另一个对象的生命周期。在 `class` 中的例子如下：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411181144192.png)

+ 利用设计模式中的**适配器模式**书写，表示 `queue` 类拥有 `deque` 类，且 `deque` 类的功能比 `queue` 类更加齐全，只需要把 `deque` 类稍加改造便可实现 `queue` 类的功能，例子如下：

![适配器模式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411181148548.png)
#### 组合的内存占用计算
+ 组合的内存占用需要把其拥有和部分的内存全部加上来进行计算。

![内存占用计算](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411181150532.png)

#### 组合关系下的构造函数和析构函数
+ 组合关系下构造函数为"由内而外"，析构函数为"由外而内"。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411181152957.png)

### 委托（delegation）
+ 两个类之间用指针相连称为委托（`delegation`），因此两个类之间的生命并不一致，而上述组合关系下两个类的生命是一致的。
+ 下面例子采用 `Pimpl`（`Pointer to Implementation`，指向实现的指针）的设计模式，也是一种惯用法，用于实现封装和隐藏类的实现细节。
+ `Pimpl` 模式通过将类的具体实现细节放在一个单独的类中，并在主类中使用指向该实现类的指针，从而实现类的接口与实现的分离。

![委托关系](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411181607642.png)

### 继承（inheritance）
+ 继承允许我们依据另一个类来定义一个类，这使得创建和维护一个应用程序变得更容易。当创建一个类时，不需要重新编写新的数据成员和成员函数，只需指定新建的类继承了一个已有的类的成员即可。
+ 这个已有的类称为**基类**，新建的类称为**派生类**。继承代表了 `is a` 关系。
+ 派生类可以得到父类的**成员变量**和**成员函数**。

![继承示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411181621554.png)

#### 从内存的角度看继承
+ 在继承关系下，派生类的构造函数为"由内而外"，析构函数为"由外而内"。

![继承关系下的构造和析构](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411181623036.png)

#### 继承下的虚函数（virtual functions）
+ 类中的函数有以下三种分类：
	+ 非虚函数（`non-virtual`）: 你不希望派生类（`derived class`）重新定义（`override`，覆写）它；
	+ 虚函数（`virtual`）：你希望派生类（`derived class`）重新定义（`override`，覆写）它，而且你对它已经有默认定义。语法是在函数名和函数类声明前面加上 `virtual` 字样；
	+ 纯虚函数（`pure virtual`）：你希望派生类（`derived class`）一定要重新定义（`override`，覆写）它，而且你对它没有有默认定义。语法除了在函数类声明前面加上 `virtual` 字样，还要在函数类声明的最后加上 `=0`。
+ 代码示例：
```cpp
class Shape {
public:
	virtual void draw( ) const = 0;//纯虚函数
	virtual void error(const std::string& msg);//虚函数
	int objectID( ) const;//非虚函数
	...
};

class Rectangle: public Shape { ... };
class Ellipse: public Shape { ... };
```

+ 虚函数的执行步骤，属于设计模式中的模板方法（`Template Method`）：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411211140693.png)

+ 具体实现代码示例：
```cpp
#include <iostream>
using namespace std;

//这是前期就写好的类
class CDocument
{
public:
	void OnFileOpen()
	{
		//每个cout输出代表一个实际动作
		cout << "dialog..." << endl;
		cout << "check file status..." << endl;
		cout << "open file..." << endl;
		Serialize();
		cout << "close file..." << endl;
		cout << "update all views..." << endl;
	}
	virtual void Serialize() { };
};

//后期根据要求对虚函数进行覆写
class CMyDoc : public CDocument
{
public:
	virtual void Serialize()
	{
		//对虚函数进行覆写
		cout << "CMyDoc::Serialize()" << endl;
	}
};

int main()
{
	CMyDoc myDoc; //假设对应[File/Open]
	myDoc.OnFileOpen();
}
```

#### 继承和组合关系下的构造和析构
+ 构造由内而外，析构由外而内。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411251510497.png)

#### 委托加继承的设计模式
+ 观察者模式 (`observer pattern`)：定义对象之间的一对多依赖关系, 这样当一个对象改变状态时, 它的所有依赖项都会自动得到通知和更新。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411251525156.png)

+ 组合模式（`Composite`）属于结构型模式（`Structural Pattern`）的一种。
+ 结构型模式（`Structural Pattern`）描述如何将类或者对象结合在一起形成更大的结构，就像搭积木，可以通过简单积木的组合形成复杂的、功能更为强大的结构。
+ 结构型模式可以分为类结构型模式和对象结构型模式：
	+ 类结构型模式关心类的组合，由多个类可以组合成一个更大的系统，在类结构型模式中一般只存在继承关系和实现关系。
	+ 对象结构型模式关心类与对象的组合，通过关联关系使得在一个类中定义另一个类的实例对象，然后通过该对象调用其方法。根据“合成复用原则”，在系统中尽量使用关联关系来替代继承关系，因此大部分结构型模式都是对象结构型模式。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411251533204.png)

+ 原型（`Prototype`）模式是一种创建型设计模式，用于通过复制（克隆）现有的对象来创建新对象，而不是通过直接实例化对象。该模式尤其适合在创建对象的成本较高或对象结构较复杂时使用。在原型模式中，每个对象都会有一个“原型”，通过克隆原型来创建新的对象，而不是通过类的构造函数。这样可以减少对构造函数的依赖，并且可以动态地生成新对象。

![原型模式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411251611721.png)

![原型模式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411251612407.png)

![原型模式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411251613443.png)

![原型模式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202411251613200.png)



