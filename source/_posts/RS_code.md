---
title: 里德-所罗门(Reed-Solomon)编译码实现
tags:
  - Reed-Solomon
categories:
  - Reed-Solomon
  - 里德-所罗门
date: 2025-4-5 14:00:00
excerpt: 里德-所罗门(Reed-Solomon)编译码实现
---
# 里德-所罗门 (Reed-Solomon) 的编译码实现
## 伽罗华域 (Galois Fields)
+ 有限域，也称为伽罗华域（Galois Fields，简写为 GF，该命名是为纪念法国数学家 Evariste Galois）。其有本元、生成多项式等相关概念，在实现上通过本元和生成多项式产生数据表，随后通过查表来进行运算。

+ 例子：
+ 不同的 $GF(2^m)$ 伽罗华域的生成多项式：

![生成多项式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051429847.png)

+ $GF(2^3)$ 伽罗华域通过生成多项式的参数表实例如下：

![GF(8)生成的参数表](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051432843.png)

+ 一般在实现上需要生成对数表和指数表用于后续加减乘除运算！
+ $GF(2^{10})$ 伽罗华域通过生成多项式生成对数表和指数表的 `C++` 代码实现如下：

```cpp
// 定义伽罗华域 GF(2^10)
/*
poly = 1 + x^3 + x^10
alpha = 2
alpha^10 = b'0000001001 = 0x009
*/

int exp[2048];          // 指数表
int log[1024];          // 对数表
int data[512];          // 产生的数据
int codeWord[1023];     // 对数据编码后的码字
int ded_codeWord[1024]; // 解码后的码字
int syndrome[511];      // 症状码（syndrome）多项式
int alpha = 2;          // 本元

// 在GF(2^10)有限域内乘2运算的函数, 0<=num<1024
int Mul2(int num)
{
    if (num < 0 || num >= 1024)
    {
        printf("This number %d not in GF(2^10) field Mul2!\n", num);
        return -1;
    }
    else
    {
        int ans;
        if (num & 0x200)
        {
            ans = (((num ^ 0x200) << 1) ^ 0x009);
        }
        else
        {
            ans = num << 1;
        }
        return ans;
    }
}

// 在GF(2^10)有限域内生成指数表和对数表
void GenerateTables(int *my_exp, int *my_log)
{
    int x = 1;
    for (int i = 0; i < 1024; i++)
    {
        my_exp[i] = x;
        my_exp[i + 1023] = x;
        my_log[x] = i;
        x = Mul2(x);
    }
    // 特殊值处理
    my_exp[2047] = my_exp[1024];
    my_log[0] = NULL;
    my_log[1] = 0;
}
```

+ 生成对数表和指数表后进行加减乘除等相关运算的实现，在 $GF(2^{10})$ 伽罗华域下的 `C++` 代码实现如下：

```cpp
// 在GF(2^10)有限域内加法运算的函数, 0<=a<1024,0<=b<1024
int Add(int a, int b)
{
    if ((a < 0 || a >= 1024) || (b < 0 || b >= 1024))
    {
        printf("This number %d or %d not in GF(2^10) field Add!\n", a, b);
        return -1;
    }
    return a ^ b;
}

// 在GF(2^10)有限域内减法运算的函数, 0<=a<1024,0<=b<1024
int Sub(int a, int b)
{
    return Add(a, b);
}

// 在GF(2^10)有限域内乘法运算的函数, 0<=a<1024,0<=b<1024
int Mul(int a, int b)
{
    if ((a < 0 || a >= 1024) || (b < 0 || b >= 1024))
    {
        printf("This number %d or %d not in GF(2^10) field Mul!\n", a, b);
        return -1;
    }
    else
    {
        if (a == 0 || b == 0)
            return 0;
        else
            return exp[(log[a] + log[b])];
    }
}

// 在GF(2^10)有限域内次方运算的函数, 0<=a<1024
int Pow(int a, int b)
{
    if (a < 0 || a >= 1024)
    {
        printf("This number %d not in GF(2^10) field Pow!\n", a);
        return -1;
    }
    else
    {
        if (a == 0)
            return 0;
        else if (a != 0 && b == 0)
            return 1;
        else
        {
            int ans = 1; // 初始化
            for (int i = 0; i < b; i++)
            {
                ans = Mul(ans, a); // 迭代运算
            }
            return ans;
        }
    }
}

// 在GF(2^10)有限域内相反数运算的函数, 0<=a<1024
int Inv(int a)
{
    if (a < 0 || a >= 1024)
    {
        printf("This number %d not in GF(2^10) field Inv!\n", a);
        return -1;
    }
    else
    {
        return exp[1023 - log[a]];
    }
}

// 在GF(2^10)有限域内除法运算的函数, 0<=a<1024，0<b<1024
int Div(int a, int b)
{
    if ((a < 0 || a >= 1024) || (b <= 0 || b >= 1024))
    {
        printf("This number %d or %d not in GF(2^10) field Div!\n", a, b);
        return -1;
    }
    else
    {
        if (a == 0)
            return 0;
        else
            return exp[(log[a] + 1023 - log[b])];
    }
}
```

## 里德-所罗门 (Reed-Solomon) 编码实现
+ 里德-所罗门 ( `Reed-Solomon` ) 的编码实现在 $GF(2^{3})$ 伽罗华域的实现实例如下：
	+ 在 $GF(2^{3})$ 伽罗华域下的 `n`，`k` 分组码为 `RS(7,3)`，即 `n=7`，`k=3`。
	+ 原理简单而言就是将消息序列作为 $u(x)$ 多项式的系数，然后从 1 到 $α^6$ 代入到 $u(x)$ 多项式中得到码字。

![GF(8)伽罗华域下的编码实现](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051441241.png)

+ 在 $GF(2^{10})$ 伽罗华域下的 `RS(1023,512)` 里德-所罗门 ( `Reed-Solomon` ) 编码的 `C++` 实现如下：

```cpp
// 编码程序函数，n=1023，k=512
void rs_encoder(int *codeword, int n, int *message, int k)
{
    for (int i = 0; i < n; i++)
    {
        codeword[i] = 0; // 清零
        for (int j = 0; j < k; j++)
        {
            codeword[i] = Add(codeword[i], (Mul(message[j], Pow(exp[i], j)))); // 多项式生成码字
        }
    }
}
```

## 里德-所罗门 (Reed-Solomon) 译码实现
+ 里德-所罗门 (Reed-Solomon) 译码有多种方法，例如辗转相除法 ( `Euclidean Algorithm` )、`Berlekamp-Massey Algorithm` 方法、基于 `FFT` 的方法。
+ 本实现采用辗转相除法 ( `Euclidean Algorithm` )，核心步骤分为以下 `4` 步：
	+ 计算症状码 ( `syndrome` );
	+ 辗转相除法 ( `Euclidean Algorithm` ) 计算得到错误定位多项式和错误纠正多项式；
	+ 钱搜索 ( `Chein Search` ) 定位到错误的地方；
	+ `Forney` 算法根据错误的地方纠正错误。

### 计算症状码 (syndrome)
+ 令 $t=\frac{n-k}{2}$，原理如下：

![症状码(syndrome)计算原理_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051503420.png)

![症状码(syndrome)计算原理_2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051512519.png)


+ 在 $GF(2^{3})$ 伽罗华域下的症状码 ( `syndrome` ) 计算示例：

![症状码(syndrome)在GF(8)下的计算示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051505015.png)

 +  $GF(2^{10})$ 伽罗华域下的 `RS(1023,512)` 症状码 ( `syndrome` )的 `C++` 代码实现如下：

```cpp
// 症状码（syndrome）计算函数，n=1023，k=512
void cal_syn(int *rec, int n, int *syn, int k)
{
    int total = n - k;
    for (int i = 1; i <= total; i++)
    {
        syn[i - 1] = 0; // 清零
        for (int j = 0; j < n; j++)
        {
            syn[i - 1] = Add(syn[i - 1], (Mul(rec[j], Pow(exp[i], j)))); // 接收数据多项式生成症状码
        }
    }
}
```

### 辗转相除法 (Euclidean Algorithm) 
+ 辗转相除法( `Euclidean Algorithm` ) 用于计算错误定位多项式和错误纠正多项式，原理如下：

![辗转相除法(Euclidean Algorithm)原理](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051515554.png)

+ 设置初值：

![设置初值_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051516513.png)

![设置初值_2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051517427.png)

+ 迭代计算：

![迭代计算公式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051519585.png)

+ 迭代停止条件：多项式 $r$ 的次数小于 $t=\frac{n-k}{2}$：

![迭代停止条件](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051521658.png)

+ 得到最终结果：

![得到最终结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051522543.png)

+ 在 $GF(2^{3})$ 伽罗华域下的辗转相除法 ( `Euclidean Algorithm` ) 计算示例：

![辗转相除法 ( Euclidean Algorithm ) 在GF(8)下的计算示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051523856.png)

+ $GF(2^{10})$ 伽罗华域下的 `RS(1023,512)` 辗转相除法 ( `Euclidean Algorithm` ) 的 `C++` 代码实现如下：

```cpp
// 定义多项式结构体
struct poly
{
    int ci = 0;         // 系数非零的最高次项是多少
    int num[512] = {0}; // 多项式参数数组
};

// 多项式乘法函数
poly poly_Mul(poly a, poly b)
{
    poly temp;
    int max_index = a.ci + b.ci;
    for (int i = 0; i <= a.ci; i++)
    {
        for (int j = 0; j <= b.ci; j++)
        {
            if (a.num[i] != 0 && b.num[j] != 0)
            {
                // 对相同次数项的系数进行异或操作
                temp.num[i + j] = Add(temp.num[i + j], Mul(a.num[i], b.num[j]));
            }
        }
    }
    for (int j = 0; j <= max_index; j++)
    {
        if (temp.num[j] != 0)
            temp.ci = j;
    }
    return temp;
}

// 多项式加减法函数（有限域内加法和减法相同）
poly poly_Sub(poly a, poly b)
{
    int max_index = max(a.ci, b.ci);
    poly temp;
    for (int i = 0; i <= max_index; i++)
    {
        temp.num[i] = Add(a.num[i], b.num[i]); // 有限域内加法和减法相同
    }
    for (int j = 0; j <= max_index; j++)
    {
        if (temp.num[j] != 0)
            temp.ci = j;
    }
    return temp;
}

// 多项式除法函数，a/b，传入的商和余数poly要保证是空的
void poly_Div(poly a, poly b, poly &shang, poly &yu)
{
    shang.ci = 0;
    memset(shang.num, 0, sizeof(shang.num));
    yu.ci = 0;
    memset(yu.num, 0, sizeof(yu.num));
    if (a.ci < b.ci)
        return; // 商为0，余数为a
    while (a.ci >= b.ci)
    {
        poly max_num;
        max_num.ci = a.ci - b.ci;
        max_num.num[max_num.ci] = Div(a.num[a.ci], b.num[b.ci]); // 得到最高次项的数据
        poly temp;
        temp = poly_Mul(max_num, b);
        a = poly_Sub(a, temp);
        shang = poly_Sub(shang, max_num);
        yu = a;
    }
}

// Euclidean_Algorithm 循环条件
poly Euclidean_Algorithm(poly a, poly s, poly &r_ans)
{
    int t = 255; // 纠错能力t=255
    poly q, yu;
    poly_Div(a, s, q, yu); // 初始除法
    poly g1 = {0, {0}}, g2 = {0, {1}}, r1 = a, r2 = s;

    while (r2.ci >= t)
    {                            // 余数次数≥t时继续
        poly_Div(r1, r2, q, yu); // 计算商q和余数yu
        r_ans = yu;
        poly temp_g = poly_Sub(g1, poly_Mul(q, g2));

        // 更新变量
        r1 = r2;
        r2 = yu;
        g1 = g2;
        g2 = temp_g;
    }
    return g2; // 返回错误位置多项式
}
```

### 钱搜索 (Chein Search) 
+ 钱搜索 ( `Chein Search` ) 原理如下：

![钱搜索 (Chein Search) 原理](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051527754.png)

+ 钱搜索 ( `Chein Search` ) 步骤如下：

![钱搜索 (Chein Search) 步骤](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051528379.png)

+ 在 $GF(2^{3})$ 伽罗华域下的钱搜索 ( `Chein Search` ) 计算示例：

![钱搜索 (Chein Search) 在GF(8)下的计算示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051530647.png)


+ $GF(2^{10})$ 伽罗华域下的 `RS(1023,512)` 钱搜索 ( `Chein Search` ) 的 `C++` 代码实现如下：

```cpp
// 钱搜索函数
int CheinSearch(poly g, int *ans)
{
    int index = 0;
    int temp = 0;
    for (int i = 1; i <= 1023; i++)
    {
        temp = 0;
        for (int j = 0; j <= g.ci; j++)
        {
            temp = Add(temp, (Mul(g.num[j], Pow(exp[i], j))));
        }
        if (temp == 0)
            ans[index++] = 1023 - i; // 钱搜索数组
    }
    return index;
}
```

### Forney 算法
+ `Forney` 算法原理如下：

![Forney 算法原理_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051532487.png)

![Forney 算法原理_2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051533035.png)

+ 在 $GF(2^{3})$ 伽罗华域下的 `Forney` 算法计算示例：

![Forney 算法在GF(8)下的计算示例](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051534291.png)

+ $GF(2^{10})$ 伽罗华域下的 `RS(1023,512)` 码的 `Forney` 算法的 `C++` 代码实现如下：

```cpp
// Forney算法实现
void Forney(int *ded_codeword, int *rec_codeword, poly g, poly r_ans, int *chein)
{
    int fenzi, fenmu, err;
    for (int i = 0; i < 1024; i++)
    {
        if (chein[i] == -1)
            break;
        else
        {
            fenzi = 0;
            fenmu = 0;
            for (int j = 0; j <= r_ans.ci; j++)
            {
                fenzi = Add(fenzi, (Mul(r_ans.num[j], Pow(Inv(exp[chein[i]]), j))));
            }
            for (int k = 1; k <= g.ci; k++)
            {
                if (k % 2 != 0)
                    fenmu = Add(fenmu, (Mul(g.num[k], Pow(Inv(exp[chein[i]]), k - 1))));
            }
            err = Div(fenzi, fenmu);
            ded_codeword[chein[i]] = Add(rec_codeword[chein[i]], err);
        }
    }
}
```

## 总结
+ 至此，已经能够完整实现里德-所罗门 ( `Reed-Solomon` ) 的编译码；
+ 下面给出 $GF(2^{10})$ 伽罗华域下 `RS(1023,512)` 的编译码完整可运行 `C++` 程序：

```cpp
#include <cstdio>
#include <cstdlib>
#include <vector>
#include <algorithm>
#include <cstring>
#include <ctime>
using namespace std;

// 定义伽罗华域 GF(2^10)
/*
poly = 1 + x^3 + x^10
alpha = 2
alpha^10 = b'0000001001 = 0x009
*/

int exp[2048];          // 指数表
int log[1024];          // 对数表
int data[512];          // 产生的数据
int codeWord[1023];     // 对数据编码后的码字
int ded_codeWord[1024]; // 解码后的码字
int syndrome[511];      // 症状码（syndrome）多项式
int alpha = 2;          // 本元

// 定义多项式结构体
struct poly
{
    int ci = 0;         // 系数非零的最高次项是多少
    int num[512] = {0}; // 多项式参数数组
};

// 在GF(2^10)有限域内乘2运算的函数, 0<=num<1024
int Mul2(int num)
{
    if (num < 0 || num >= 1024)
    {
        printf("This number %d not in GF(2^10) field Mul2!\n", num);
        return -1;
    }
    else
    {
        int ans;
        if (num & 0x200)
        {
            ans = (((num ^ 0x200) << 1) ^ 0x009);
        }
        else
        {
            ans = num << 1;
        }
        return ans;
    }
}

// 在GF(2^10)有限域内生成指数表和对数表
void GenerateTables(int *my_exp, int *my_log)
{
    int x = 1;
    for (int i = 0; i < 1024; i++)
    {
        my_exp[i] = x;
        my_exp[i + 1023] = x;
        my_log[x] = i;
        x = Mul2(x);
    }
    // 特殊值处理
    my_exp[2047] = my_exp[1024];
    my_log[0] = NULL;
    my_log[1] = 0;
}

// 在GF(2^10)有限域内加法运算的函数, 0<=a<1024,0<=b<1024
int Add(int a, int b)
{
    if ((a < 0 || a >= 1024) || (b < 0 || b >= 1024))
    {
        printf("This number %d or %d not in GF(2^10) field Add!\n", a, b);
        return -1;
    }
    return a ^ b;
}

// 在GF(2^10)有限域内减法运算的函数, 0<=a<1024,0<=b<1024
int Sub(int a, int b)
{
    return Add(a, b);
}

// 在GF(2^10)有限域内乘法运算的函数, 0<=a<1024,0<=b<1024
int Mul(int a, int b)
{
    if ((a < 0 || a >= 1024) || (b < 0 || b >= 1024))
    {
        printf("This number %d or %d not in GF(2^10) field Mul!\n", a, b);
        return -1;
    }
    else
    {
        if (a == 0 || b == 0)
            return 0;
        else
            return exp[(log[a] + log[b])];
    }
}

// 在GF(2^10)有限域内次方运算的函数, 0<=a<1024
int Pow(int a, int b)
{
    if (a < 0 || a >= 1024)
    {
        printf("This number %d not in GF(2^10) field Pow!\n", a);
        return -1;
    }
    else
    {
        if (a == 0)
            return 0;
        else if (a != 0 && b == 0)
            return 1;
        else
        {
            int ans = 1; // 初始化
            for (int i = 0; i < b; i++)
            {
                ans = Mul(ans, a); // 迭代运算
            }
            return ans;
        }
    }
}

// 在GF(2^10)有限域内相反数运算的函数, 0<=a<1024
int Inv(int a)
{
    if (a < 0 || a >= 1024)
    {
        printf("This number %d not in GF(2^10) field Inv!\n", a);
        return -1;
    }
    else
    {
        return exp[1023 - log[a]];
    }
}

// 在GF(2^10)有限域内除法运算的函数, 0<=a<1024，0<b<1024
int Div(int a, int b)
{
    if ((a < 0 || a >= 1024) || (b <= 0 || b >= 1024))
    {
        printf("This number %d or %d not in GF(2^10) field Div!\n", a, b);
        return -1;
    }
    else
    {
        if (a == 0)
            return 0;
        else
            return exp[(log[a] + 1023 - log[b])];
    }
}

// 编码程序函数，n=1023，k=512
void rs_encoder(int *codeword, int n, int *message, int k)
{
    for (int i = 0; i < n; i++)
    {
        codeword[i] = 0; // 清零
        for (int j = 0; j < k; j++)
        {
            codeword[i] = Add(codeword[i], (Mul(message[j], Pow(exp[i], j)))); // 多项式生成码字
        }
    }
}

// 症状码（syndrome）计算函数，n=1023，k=512
void cal_syn(int *rec, int n, int *syn, int k)
{
    int total = n - k;
    for (int i = 1; i <= total; i++)
    {
        syn[i - 1] = 0; // 清零
        for (int j = 0; j < n; j++)
        {
            syn[i - 1] = Add(syn[i - 1], (Mul(rec[j], Pow(exp[i], j)))); // 接收数据多项式生成症状码
        }
    }
}

// 多项式乘法函数
poly poly_Mul(poly a, poly b)
{
    poly temp;
    int max_index = a.ci + b.ci;
    for (int i = 0; i <= a.ci; i++)
    {
        for (int j = 0; j <= b.ci; j++)
        {
            if (a.num[i] != 0 && b.num[j] != 0)
            {
                // 对相同次数项的系数进行异或操作
                temp.num[i + j] = Add(temp.num[i + j], Mul(a.num[i], b.num[j]));
            }
        }
    }
    for (int j = 0; j <= max_index; j++)
    {
        if (temp.num[j] != 0)
            temp.ci = j;
    }
    return temp;
}

// 多项式加减法函数（有限域内加法和减法相同）
poly poly_Sub(poly a, poly b)
{
    int max_index = max(a.ci, b.ci);
    poly temp;
    for (int i = 0; i <= max_index; i++)
    {
        temp.num[i] = Add(a.num[i], b.num[i]); // 有限域内加法和减法相同
    }
    for (int j = 0; j <= max_index; j++)
    {
        if (temp.num[j] != 0)
            temp.ci = j;
    }
    return temp;
}

// 多项式除法函数，a/b，传入的商和余数poly要保证是空的
void poly_Div(poly a, poly b, poly &shang, poly &yu)
{
    shang.ci = 0;
    memset(shang.num, 0, sizeof(shang.num));
    yu.ci = 0;
    memset(yu.num, 0, sizeof(yu.num));
    if (a.ci < b.ci)
        return; // 商为0，余数为a
    while (a.ci >= b.ci)
    {
        poly max_num;
        max_num.ci = a.ci - b.ci;
        max_num.num[max_num.ci] = Div(a.num[a.ci], b.num[b.ci]); // 得到最高次项的数据
        poly temp;
        temp = poly_Mul(max_num, b);
        a = poly_Sub(a, temp);
        shang = poly_Sub(shang, max_num);
        yu = a;
    }
}

// Euclidean_Algorithm 循环条件
poly Euclidean_Algorithm(poly a, poly s, poly &r_ans)
{
    int t = 255; // 纠错能力t=255
    poly q, yu;
    poly_Div(a, s, q, yu); // 初始除法
    poly g1 = {0, {0}}, g2 = {0, {1}}, r1 = a, r2 = s;

    while (r2.ci >= t)
    {                            // 余数次数≥t时继续
        poly_Div(r1, r2, q, yu); // 计算商q和余数yu
        r_ans = yu;
        poly temp_g = poly_Sub(g1, poly_Mul(q, g2));

        // 更新变量
        r1 = r2;
        r2 = yu;
        g1 = g2;
        g2 = temp_g;
    }
    return g2; // 返回错误位置多项式
}

// 钱搜索函数
int CheinSearch(poly g, int *ans)
{
    int index = 0;
    int temp = 0;
    for (int i = 1; i <= 1023; i++)
    {
        temp = 0;
        for (int j = 0; j <= g.ci; j++)
        {
            temp = Add(temp, (Mul(g.num[j], Pow(exp[i], j))));
        }
        if (temp == 0)
            ans[index++] = 1023 - i; // 钱搜索数组
    }
    return index;
}

// Forney算法实现
void Forney(int *ded_codeword, int *rec_codeword, poly g, poly r_ans, int *chein)
{
    int fenzi, fenmu, err;
    for (int i = 0; i < 1024; i++)
    {
        if (chein[i] == -1)
            break;
        else
        {
            fenzi = 0;
            fenmu = 0;
            for (int j = 0; j <= r_ans.ci; j++)
            {
                fenzi = Add(fenzi, (Mul(r_ans.num[j], Pow(Inv(exp[chein[i]]), j))));
            }
            for (int k = 1; k <= g.ci; k++)
            {
                if (k % 2 != 0)
                    fenmu = Add(fenmu, (Mul(g.num[k], Pow(Inv(exp[chein[i]]), k - 1))));
            }
            err = Div(fenzi, fenmu);
            ded_codeword[chein[i]] = Add(rec_codeword[chein[i]], err);
        }
    }
}

// BSC 信道函数
void bsc_channel(int *codeWord, double p)
{
    int num = 0;
    printf("\n");
    // 初始化随机数种子
    std::srand(static_cast<unsigned int>(std::time(nullptr)));

    // 遍历所有位置
    for (int i = 0; i < 1023; i++)
    {
        // 生成一个 [0, 1) 之间的随机数
        double random_value = static_cast<double>(std::rand()) / RAND_MAX;
        if (random_value < p)
        {
            // 以概率 p 发生错误
            printf("BSC 信道函数在位置 %d 处造成了传输错误。\n", i);
            printf("BSC 信道函数修改前的值: %d\n", codeWord[i]);

            // 假设在 GF(2^10) 有限域内随机生成一个新值
            int new_value = std::rand() % 1024;
            codeWord[i] = new_value;

            printf("BSC 信道函数修改后的值: %d\n", codeWord[i]);
            num++;
        }
    }
    printf("BSC 信道总共造成 %d 处错误\n", num);
}

// 辗转相除法译码函数
void rs_decoder_EA_algorithm(int *ded_codeWord, int *rec_codeWord, int n, int k)
{
    // 计算症状码(syndrome)
    poly test_syn;
    memset(syndrome, 0, sizeof(syndrome));
    bool flag = false;
    cal_syn(rec_codeWord, 1023, syndrome, 512);
    for (int i = (sizeof(syndrome) / 4 - 1); i >= 0; i--)
    {
        if ((syndrome[i] != 0) && (flag == false))
        {
            test_syn.ci = i;
            flag = true;
        }
    }
    for (int i = 0; i < 511; i++)
    {
        test_syn.num[i] = syndrome[i];
    }

    // 执行辗转相除法
    poly a, ans, r_ans;
    a.ci = 511;
    a.num[511] = 1;
    ans = Euclidean_Algorithm(a, test_syn, r_ans); // 辗转相除法

    // 执行钱搜索算法
    int chein[1024] = {-1};
    int num = 0;
    memset(chein, -1, sizeof(chein));
    num = CheinSearch(ans, chein);
    printf("\n钱搜索共找到 %d 个传输错误的地方:\n", num);
    for (int i = 0; i <= 1023; i++)
    {
        if (chein[i] != -1)
            printf("%d:%d\n", i + 1, chein[i]);
    }

    // 执行forney算法
    for (int i = 0; i < 1023; i++)
    {
        ded_codeWord[i] = rec_codeWord[i];
    }
    Forney(ded_codeWord, rec_codeWord, ans, r_ans, chein);
}

// 主函数
int main()
{
    // 生成指数表和对数表
    GenerateTables(exp, log);

    // 生成消息数据
    for (int i = 0; i < 512; i++)
    {
        data[i] = i + 1;
    }

    // 执行RS(1023,512)编码函数
    rs_encoder(codeWord, 1023, data, 512);

    // 打印编码数据
    printf("RS(1023,512)编码后的数据:\n");
    for (int i = 0; i < 1023; i++)
    {
        printf("%d:%d\n", i, codeWord[i]);
    }

    // BSC信道传输
    bsc_channel(codeWord, 0.01);

    // 辗转相除译码算法
    rs_decoder_EA_algorithm(ded_codeWord, codeWord, 1023, 512);

    // 打印译码和编码数据的对比
    printf("\nRS(1023,512)译码后的对比:\n");
    for (int i = 0; i < 1023; i++)
    {
        printf("rec:%d:%d\n", i, codeWord[i]);
        printf("ded:%d:%d\n", i, ded_codeWord[i]);
    }

    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

+ 运行结果剪影：

![运行结果_1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051540703.png)

![运行结果_2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051541881.png)

![运行结果_3](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202504051542813.png)












