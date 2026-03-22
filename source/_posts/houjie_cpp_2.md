---
title: 侯捷 C++学习笔记之面向对象高级编程（二）
tags:
  - 面向对象
categories:
  - C++语言
date: 2024-10-28 14:00:00
excerpt: C++、编程语言、面向对象高级编程
---
# 侯捷 C++ 学习笔记之面向对象高级编程（二）
## conversion function 转换函数
+ 转换函数（分数转为 `double`）程序示例：

```cpp
class Fraction
{
public：
	Fraction(int num , int den=1) //构造函数
	: m_numerator(num) , m_denominator(den) { }

	operator double() const //相当于操作符重载（double属于显式类型转换运算符）
	{
		return (double) (m_numerator/m_denominator);
	}

private:
	int m_numerator; //分子
	int m_denominator; //分母
}
```

+ **说明**：对于上述示例的转换函数，可以看到其相当于操作符重载（`double` 属于显式类型转换运算符），该转换函数不需要返回类型，因为本身转换为 `double` 类型，属于 `C++` 语言特性，能够避免返回类型与转换类型不一致的问题。

+ 上述示例的调用示例如下：

```cpp
Fraction f(3,5);
double d = 4+f; //调用operator double() const{}函数将f转为0.6
```

### non-explicit-one-argument 构造函数
+ `one-argument` 是指该构造函数只需要一个实参就足够了，其中以下示例的构造函数有 `den=1` 这个默认参数，因此是属于 `one-argument` 的。

```cpp
class Fraction
{
public：
	Fraction(int num , int den=1) //non-explicit-one-argument构造函数
	: m_numerator(num) , m_denominator(den) { }

	Fraction operator+(const Fraction& f)
	{
		return Fraction(......);
	}

private:
	int m_numerator; //分子
	int m_denominator; //分母
}
```

+ 上述示例的调用示例如下：

```cpp
Fraction f(3,5);
Fraction d2 = f+4; //调用 non-explicit-one-argument 构造函数将4转为Fraction类型然后执行operator+
```

### explicit（明确的） 构造函数
+ 将上述两种方式组合一下可能会造成歧义，来看下述例子：

```cpp
class Fraction
{
public：
	Fraction(int num , int den=1) //non-explicit-one-argument构造函数
	: m_numerator(num) , m_denominator(den) { }

	//Fraction转double
	operator double() const //相当于操作符重载（double属于显式类型转换运算符）
	{
		return (double) (m_numerator/m_denominator);
	}

	//int转Fraction
	Fraction operator+(const Fraction& f)
	{
		return Fraction(......);
	}

private:
	int m_numerator; //分子
	int m_denominator; //分母
}
```

+ 使用示例：
```cpp
Fraction f(3,5);
Fraction d2 = f+4; //报错[Error]，编译器提示ambiguous（存在歧义的）
```

+ 存在两种可能性：
	+ 一是将 `4` 转为 `Fraction` 类型进行相加；
	+ 二是将 `f` 转为 `double` 类型进行相加。
+ 两种错误就会导致编译器报错，提示 ambiguous（存在歧义的），解决方法是在构造函数前加入 `explicit` 关键字，如下示例：

```cpp
class Fraction
{
public：
	explict Fraction(int num , int den=1) //non-explicit-one-argument构造函数
	: m_numerator(num) , m_denominator(den) { }

	//Fraction转double
	operator double() const //相当于操作符重载（double属于显式类型转换运算符）
	{
		return (double) (m_numerator/m_denominator);
	}

	//int转Fraction
	Fraction operator+(const Fraction& f)
	{
		return Fraction(......);
	}

private:
	int m_numerator; //分子
	int m_denominator; //分母
}
```

+ 使用示例：
```cpp
Fraction f(3,5);
Fraction d2 = f+4; //报错[Error]，编译器提示需要从double转为Fraction
double d = 4+f; //调用operator double() const{}函数将f转为0.6，不报错
```

+ **说明**：加入 `explicit` 关键字后说明不让编译器自动将 `4` 转为 `Fraction` 类型。因此示例的第一个使用方法会报错，提示需要从 `double` 类型转为 `Fraction` 类型。而第二个使用方法能够将 `f` 转为 `double` 类型并相加就不会报错。

## pointer-like classes
### 智能指针
+ 较为简单的智能指针的示例（`shared_ptr`）:

![shared_ptr](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250301210258.png)

### 迭代器
+ 迭代器作为容器中的一种用法，以链表迭代器为例：

![链表迭代器 1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250301211615.png)

![链表迭代器 2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250301211659.png)

### 注意点
+ 关于 `C++` 语言中的指针，需要注意运算符 `.` 和 `->` 用法的区别，例子如下：

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <iostream>
#include <memory>

using namespace std;

//主函数
int main()
{
    //智能指针：共享指针
    shared_ptr<string> name(new string("yugin"));
    cout << "*name:  " << *name << endl;
    cout << "(*name).size():  " << (*name).size() << endl;
    cout << "name->size():  " << name->size() << endl;
    
    cout << "---------------" << endl;
    cout << "name.use_count():  " << name.use_count() << endl;
    
    system("pause");// 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

+ 输出结果：

![输出结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250301212429.png)

+ 从上述输出结果中不难看出，`(*name).size()` 与 `name->size()` 的输出结果一致，使用的是 `string` 这个对象里面的 `size()` 函数。
+ 而 `name.use_count()` 输出的是数值 1，使用的是智能指针 `shared_ptr<string>` 里面的 `use_count()` 函数。
+ `use_count()` 函数表示有多少个智能指针指向某个对象，主要用于调试目的。例如：

```cpp
#include <iostream>
using namespace std;

int main()
{
	shared_ptr<int> p1(new int(100));
	cout << p1.use_count() << endl; // 1
	
	shared_ptr<int> p2(p1);
	cout << p1.use_count() << endl; // 2
	
	shared_ptr<int> p3;
	p3 = p2;
	cout << p1.use_count() << endl; // 3
	cout << p3.use_count() << endl; // 3
	
	return 0;
}
```

## function-like classes，仿函数
+ 在 C++中，函数调用运算符（`operator()`）允许一个类的对象表现得像函数一样被调用。这种特性通过重载类的圆括号操作符 `operator()` 实现。
+ 注：小括号 `()` 就是函数调用操作符，`operator()` 就是对函数调用操作符进行重载。
+ 这对于实现类似于函数的对象非常有用，例如，你可以创建一个对象，该对象封装了一组操作，而这些操作可以通过调用对象来实现。
+ 例如创建一个简单的计算器类，该类可以执行加法和减法操作：

```cpp
#include <iostream>

class Calculator {
public:
    // 定义函数调用运算符，接受两个int参数并返回它们的和
    int operator()(int a, int b) {
        return a + b;
    }
    
    // 定义另一个函数调用运算符，接受两个int参数并返回它们的差
    int operator()(int a, int b, const char* op) {
        if (op == "subtract") {
            return a - b;
        } else {
            return a + b; // 默认返回和，如果op不是"subtract"
        }
    }
};

int main() {
    Calculator calc;
    std::cout << "Sum: " << calc(5, 3) << std::endl; // 使用第一个重载的operator()
    std::cout << "Difference: " << calc(5, 3, "subtract") << std::endl; // 使用第二个重载的operator()
    return 0;
}
```

## function template，函数模板
+ 编译器会对函数模板（`function template`）进行实参推导 ( `argument deduction` )。

![函数模板](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250306134454.png)

## member template，成员模板
+ 在模板类里面还有一份模板，成为成员模板：

![成员模板1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250306164753.png)

+ 成员模板中的继承关系：

![成员模板2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250306171840.png)

![成员模板3](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250306172314.png)

## specialization，模板特化
+ 全特化：特定类型的模板执行特定类型的函数或事情。

![模板特化1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250306211554.png)

### 模板的偏特化
+ 个数的偏：

![模板个数的偏特化](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250306212509.png)

+ 范围的偏：

![模板范围的偏特化](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250306212806.png)

## template template parameter，模板模板参数
+ 模板模板参数，即允许一个模板接受另一个模板作为参数。
+ 可以使用 `template<template<...> class...>` 语法来定义一个模板模板参数。

![模板模板参数1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250306214228.png)

![模板模板参数2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250306215019.png)

+ 不是模板模板参数的情况：

![不是模板模板参数的情况](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250306215404.png)

## C++ 11 新特性三个主题简介
### variadic templates，数量不定的模板参数
+ 数量不定的模板参数的语法是在模板声明中使用省略号（`...`）来表示参数的数量是不确定的。例如：

```cpp
template<typename... Types>
void function(const Types&... args) {
    // 函数体
}
```

+ 完整代码示例：

```cpp
#include<iostream>
using namespace std;

template<typename T, typename... Types>
void print(const T& firstArg, const Types&... args) {
    cout << firstArg << endl;
    print(args...); // 递归调用自身，传递剩余参数
}

int main() {
    print(7.5, "hello", 100, 43); // 输出: 7.5 hello 100 43
    return 0;
}
```

+ 可以用 `sizeof... (args)` 来获取 `args` 内具多少元素：

![数量不定的模板参数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250307135147.png)

### auto
+ `C++` 中的 `auto` 关键字主要用于自动推导变量的类型。在 `C++ 11` 及以后的版本中，`auto` 不再表示存储类型，而是作为一个类型指示符，用于指示编译器自动推导变量的类型。使用 `auto` 可以简化代码，尤其是在处理复杂类型时，能够减少代码量并提高可读性。

![auto](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250307135625.png)

### ranged-base for，基于范围的 for 循环
+ 基于范围的 `for` 循环是一种简洁而强大的语法，用于遍历容器（如数组、向量、列表、集合等）。

![基于范围的for循环](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250307140826.png)

## reference，再谈引用
+ `object` 和其 `reference` 的大小相同，地址也相同。

![再谈引用1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250307143420.png)

![再谈引用2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250307143447.png)

+ 引用（`reference`）通常不用于声明变量，而用于参数类型（`parameters type`）和返回类型（`return type`）的描述。

![再谈引用3](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250307144134.png)

## 对象模型（Object Model）
### 关于虚指针（vptr）和虚函数表（vtbl）
+ 在 C++中，虚函数和多态是通过虚指针（`vptr`）和虚函数表（`vtbl`）来实现的。这种机制允许在运行时根据对象的实际类型来调用相应的函数，而不是在编译时确定。这对于实现多态行为非常关键，比如在基类指针或引用调用派生类的方法时。
+ 虚指针（`vptr`）：
	+ 每个包含虚函数的类都会有一个或多个虚指针（`vptr`）；
	+ 虚指针是一个指向虚函数表的指针，该表包含了指向各个虚函数的指针。这个表对于类的每一个对象来说是唯一的，确保了多态性的实现。
+ 虚函数表（`vtbl`）：
	+ 虚函数表是一个静态的数据结构，它存储了类中所有虚函数的地址；
	+ 对于每个包含虚函数的类，编译器都会生成一个虚函数表，当类的对象被创建时，其虚指针（`vptr`）被初始化为指向该类的虚函数表的地址。
+ 代码示例：

```cpp
class Base {
public:
    virtual void func() {
        cout << "Base func" << endl;
    }
};
 
class Derived : public Base {
public:
    void func() override {
        cout << "Derived func" << endl;
    }
};
```

+ 在这个例子中，`Base` 类有一个虚函数 `func`。当 `Derived` 类继承 `Base` 并重写 `func` 函数时，编译器会做以下事情：

	1. 为 `Base` 类生成一个虚函数表，其中包含 `func` 函数的地址。
    
	2. 为 `Derived` 类生成一个新的虚函数表，其中包含 `func` 函数的地址（即 `Derived` 类中的新实现）。
    
	3. 在 `Derived` 类的每个对象中，其虚指针（`vptr`）被初始化为指向 `Derived` 的虚函数表。

+ 访问和调用过程：当通过基类指针调用虚函数时，实际调用的函数取决于对象的实际类型。

```cpp
Base* b = new Derived();
b->func();  // 输出 "Derived func"
```

+ 在这个例子中，尽管 `b` 是 `Base` 类型的指针，但由于 `b` 指向一个 `Derived` 类型的对象，所以调用的是 `Derived` 的 `func` 方法。这是因为编译器会根据对象的实际类型（通过其虚指针找到正确的虚函数表），从而确定并调用正确的函数。
+ 图片示例：

![虚指针和虚函数表1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250309150806.png)

![虚指针和虚函数表2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250309151059.png)

### 多态
+ 多态（`Polymorphism`）是面向对象编程中的一个核心概念，允许一个接口被多种不同的实现使用。在 C++中，多态主要通过虚函数（`virtual functions`）和继承来实现。
#### 虚函数
+ 在 C++中，虚函数允许你在派生类中重写基类的函数。这是实现多态的基础：

```cpp
class Base {
public:
    virtual void show() {
        cout << "Base class show function" << endl;
    }
};
 
class Derived : public Base {
public:
    void show() override {  // 使用override关键字是C++11标准的一部分，用于确保正确的重写
        cout << "Derived class show function" << endl;
    }
};
```

#### 指针和引用的多态性
+ 通过基类指针或引用调用虚函数时，会根据对象的实际类型来调用相应的函数，这称为动态绑定（`Dynamic Binding`）。

```cpp
Base* basePtr;
Derived derivedObj;
basePtr = &derivedObj;
basePtr->show();  // 输出：Derived class show function
```

#### 纯虚函数和抽象类
+ 纯虚函数是一种特殊的虚函数，它在基类中没有实现，要求任何继承该类的子类必须提供具体实现。含有纯虚函数的类是抽象类，不能直接实例化。

```cpp
class AbstractBase {
public:
    virtual void show() = 0;  // 纯虚函数
};
 
class Concrete : public AbstractBase {
public:
    void show() override {
        cout << "Concrete class show function" << endl;
    }
};
```

### 关于 this 和动态绑定（Dynamic Binding）
+ 动态绑定（`Dynamic Binding`），`this` 指针所指的对象（`Object`）称为 `this Object`；
+ 虚函数的执行步骤，属于设计模式中的模板方法（`Template Method`）：

![动态绑定](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250309153050.png)

+ 动态绑定需要满足三个条件：
	+ 通过指针调用；
	+ 向上转型的动作（如 `this` 指针的对象是子类对象）；
	+ 调用 `this` 指针的子类对象的虚函数。

+ 静态绑定：

![静态绑定汇编示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250309161038.png)

+ 动态绑定：

![动态绑定汇编示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250309161849.png)

### 谈谈 const
+ 常量成员函数：

![常量成员函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250310144213.png)

+ 一个函数通过在其后面加关键字 `const`，它将被声明为常量函数；
+ 在 C++，只有将成员函数声明为常量函数才有意义。带有 `const` 作后缀的常量成员函数又被称为视察者 (`inspector`)，没有 `const` 作后缀的非常量成员函数被称为变异者 (`mutator`)；
+ 常量函数可以被任何对象调用，而非常量函数则只能被非常量对象调用，不能被常量对象调用；
+ 在 C++中，一个对象的所有方法都接收一个指向对象本身的隐含的 `this` 指针，常量方法则获取了一个隐含的常量 `this` 指针；
+ 在类中允许存在同名的常量函数和非常量函数，编译器根据调用该函数的对象选择合适的函数：
	+ 当非常量对象调用该函数时，先调用非常量函数；
	+ 当常量对象调用该函数时，只能调用常量函数；
	+ 如果在类中只有常量函数而没有与其同名的非常量函数，则非常量与常量对象都可调用该常量函数。
    
### 关于 new 和 delete
+ 第一部分关于 `new` 和 `delete` 的汇总：

![关于 new 和 delete](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250310145853.png)

+ 重载全局 `new` 和全局 `delete` 以及全局 `new[]` 和全局 `delete[]`：

![重载全局new和全局delete](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250310150432.png)

+ 重载成员 `new` 和成员 `delete`：

![重载成员 new 和成员 delete 1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250310151326.png)

+ 重载成员 `new[]` 和成员 `delete[]`：

![重载成员 new 和成员 delete 2]( https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250310151431.png )

+ 设计示例和接口：

![设计示例和接口1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250310152522.png)

![设计示例和接口2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250310153157.png)

![设计示例和接口3](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250310153411.png)

+ 重载 `new()`、`delete()` :

![重载 new()、delete() 1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250310154749.png)

![重载 new()、delete() 2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250310154820.png)

![重载 new()、delete() 3](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250310154900.png)

+ 标准库重载 `new()` 的示例：

![标准库重载 new() 的示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250310155449.png)

