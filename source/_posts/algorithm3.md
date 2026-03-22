---
title: 算法笔记之提高篇
tags:
  - 数据结构
  - 算法
categories:
  - 数据结构
date: 2024-9-6 10:30:00
excerpt: 算法笔记的学习与分享总结!
---
# 算法笔记之提高篇
## 数据结构专题（1）
### 栈的应用
+ 栈（`stack`）是一种**后进先出**的数据结构。

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202409061059275.png)

+ 栈顶指针是始终指向栈的最上方元素的一个标记。
	+ 当使用数组实现栈时，栈顶指针是一个 `int` 型变量（数组下标从 `0` 开始），通常记为 `TOP`；
	+ 而当使用链表实现栈时，则是一个 `int*` 型的指针。
+ 在图 7-1 中，从左到右每个操作之后，栈顶指针 `TOP` 的值分别为 `0`、`1`、`2`、`1`、`0`、`1`，而当栈中没有元素（即栈空）时令 `TOP` 为 `-1`。
+ 使用数组 `st[]` 来实现栈，`int` 型变量 `TOP` 表示栈顶元素的下标，这样栈空时 `TOP` 就是 `-1`，下面是常见操作的示范：

  1.  清空（`clear`）：栈的清空操作将栈顶指针 `TOP` 置为 `-1`，表示栈中没有元素。
```cpp
void clear()
{
	TOP = -1;
}
```
  2. 获取栈内元素个数（`size`）：由于栈顶指针 `TOP` 始终指向栈顶元素，而数组下标从 `0` 开始，因此栈内元素的数为 `TOP+1`。
```cpp
int size()
{
	return TOP+1;
}
```
  3. 判空（`empty`）：由栈顶指针 `TOP` 的定义可知，仅当 `TOP==-1` 时为栈空，返回 `true`，否则，返回 `false`。
  ```cpp
bool empty()
{
	if(TOP==-1)
		return true;
	else
		return false;
}
```
  4. 进栈（`push`）：`push(x)` 操作将元素 `x` 置于栈顶。由于栈顶指针 `TOP` 指向栈顶元素，因此需要先把 `TOP+1`，然后再把元素 `x` 存入 `TOP` 指向的位置。
```cpp
void push(int x)
{
	st[++TOP]=x;
}
```
  5. 出栈（`pop`）：`pop()` 操作将栈顶元素出栈，事实上可以直接将栈顶元素指针 `TOP-1` 来实现这个效果。
```cpp
void pop()
{
	TOP--;
}
```
  6. 取栈顶元素（`top`）：由于栈顶指针 `TOP` 始终指向栈顶元素，因此可以使得 `st[TOP]` 即为栈顶元素。
```cpp
int top()
{
	return st[TOP];
}
```
+ 需要特别注意的是，出栈操作和取栈顶元素操作必须在栈非空的情形下才能使用，因此在使用 `pop()` 函数和 `top()` 函数之前必须先使用 `empty()` 函数来判断栈是否为空。
+ 事实上，可以使用 C++ 的 STL 中的 stack 容器来非常容易地使用栈。STL 中的 stack 为实现好了栈的常用操作，当需要使用时只需要调用函数即可。
+ 需要指出的是，STL 中没有没有实现栈的清空，所以如果需要实现栈的清空，可以使用一个 `while` 循环反复 `pop` 出元素直至栈空：
```cpp
while(!st.empty())
{
	st.pop();
}
```
* 而事实上，更常用的方法是重新定义一个栈以变相实现栈的清空，因为这并不需要花费很多时间，STL 的 stack 进行定义的时间复杂度是 $O(1)$。
例题：**简单计算器**

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202409071557509.png)

+ **思路：**
+ 题目给出的是中缀表达式，所以要计算它的值主要是两个步骤：
	1. 中缀表达式转后缀表达式；
	2. 计算后缀表达式。
+ 下面分别介绍这两个步骤：
+ **步骤 1：中缀表达式转后缀表达式：**
1. 设立一个操作符，用以临时存放操作符；设立一个数组或者队列，用以存放后缀表达式。
2. 从左到右扫描中缀表达式，如果碰到操作数，就把操作数加入后缀表达式中。
3. 如果碰到操作符 `op`，就将其优先级与操作符栈的栈顶操作符优先级比较。
	1. 若 `op` 的优先级高于栈顶操作符的优先级，则压入操作符栈。
	2. 若 `op` 的优先级低于或等于栈顶操作符的优先级，则将操作符栈的操作符不断弹出到后缀表达式中，直到 `op` 的优先级高于栈顶操作符的优先级。
4. 重复上述操作，直到中缀表达式扫描完毕，之后若操作符栈中仍有元素，则将它们一次弹出至后缀表达式中。
+ 所谓的操作符优先级即它们的计算优先级，其中 `乘法==除法>加法==减法`，在具体实现上可以用 `map` 建立操作符和优先级的映射，优先级可以用数字表示，例如乘法和除法优先级为 1，加法和减法优先级为 0。
+ 本题没有括号，但是如果出现括号，处理方法也很简单，如果遇到是左括号 `'('`，就压入操作符栈；如果是右括号 `')'`，就把操作符栈里的元素不断弹出到后缀表达式中直至碰到左括号 `'('`。
+ **步骤 2：计算后缀表达式：**
1. 从左到右扫描后缀表达式，如果是操作数，就压入栈；如果是操作符，就连续弹出两个操作数（**注意：先入栈的先算**），然后进行操作符的计算，生成的新操作数压入栈中。反复上述操作直至后缀表达式扫描完毕，这时栈中只会存在一个数，就是最终答案。
	+ 注意除法可能导致浮点数，因此操作数类型要设成浮点型；
	+ 题目中说明是合法表达式，因此上述操作一定成功，但是如果题目表明可能出现非法表达式，那就要注意每一步使用的对象是否合法。
+ **注意点：**
	1. 用 `string` 的 `erase` 方法可以直接把表达式中的空格去掉。
+ 代码（使用队列 `queue` 来存放后缀表达式）：
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <stack>
#include <cstring>
#include <iostream>
#include <utility>
#include <map>
#include <algorithm>
#include <vector>
#include <climits>
#include <string>
#include <ctime>
#include <cmath>
#include <sstream>
#include <queue>
using namespace std;

struct node
{
    double num; //操作数
    char op;    //操作符
    bool flag;  // true表达操作数，false表示操作符
};

string str;
stack<node> s; //操作符栈
queue<node> q; //后缀表达式序列
map<char, int> op;

//将中缀表达式转换为后缀表达式
void change()
{
    double num;
    node temp;
    for (int i = 0; i < str.length();)
    {
        if (str[i] >= '0' && str[i] <= '9') //如果是数字
        {
            temp.flag = true;                                          //标记为数字
            temp.num = str[i++] - '0';                                 //记录这个操作数的第一个数位
            while (i < str.length() && str[i] >= '0' && str[i] <= '9') //可能大于9的数据
            {
                temp.num = temp.num * 10 + (str[i] - '0'); //更新这个操作数
                i++;
            }
            q.push(temp); //将这个操作数压入后缀表达式队列
        }
        else //如果是操作符
        {
            temp.flag = false;
            //只要操作符栈的栈顶元素比该操作符优先级高
            //就把操作符栈的栈顶元素弹出到后缀表达式的队列中
            while (!s.empty() && op[str[i]] <= op[s.top().op])
            {
                q.push(s.top());
                s.pop();
            }
            temp.op = str[i];
            s.push(temp); //把该操作符压入栈中
            i++;
        }
    }
    //如果操作符栈中还有操作符，就把它弹出到后缀表达式中
    while (!s.empty())
    {
        q.push(s.top());
        s.pop();
    }
}

//计算后缀表达式
double cal()
{
    double temp1, temp2;
    node cur, temp;
    while (!q.empty()) //后缀表达式队列非空
    {
        cur = q.front(); //记录队列头
        q.pop();
        if (cur.flag == true) //如果是操作数
        {
            s.push(cur); //直接入栈
        }
        else //如果是操作符
        {
            temp1 = s.top().num;
            s.pop();
            temp2 = s.top().num;
            s.pop();
            temp.flag = true; //记录运算后的数据
            if (cur.op == '+')
                temp.num = temp2 + temp1;
            else if (cur.op == '-')
                temp.num = temp2 - temp1;
            else if (cur.op == '*')
                temp.num = temp2 * temp1;
            else
                temp.num = temp2 / temp1;
            s.push(temp); //把计算后的数据压栈
        }
    }
    return s.top().num; //栈顶元素就是后缀表达式运算后的值
}

//主函数
int main()
{
    //设定操作符优先级
    op['+'] = op['-'] = 1;
    op['*'] = op['/'] = 2;
    while (getline(cin, str), str != "0")
    {
        for (string::iterator it = str.end(); it != str.begin(); it--)
        {
            if (*it == ' ')
                str.erase(it); //把表达式的空格全部去掉
        }
        while (!s.empty()) //初始化栈
            s.pop();
        change();                //将中缀表达式转换成后缀表达式
        printf("%.2f\n", cal()); //输出计算后缀表达式的结果
    }
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

例题：[合法的出栈序列](https://sunnywhy.com/sfbj/7/1/294)

+ 解释：
	+ **判断出栈序列合法性的规则**‌是：对于出栈序列中的每个数字，它后面的比它小的数字必须是按递减顺序排列的。例如，如果入栈顺序为 `1, 2, 3, 4`，那么合法的出栈序列可以是 `1, 2, 3, 4` 或者 `1, 3, 2, 4` 等，但不包括 `1, 4, 2, 3` 等，因为 `4` 后面的 `2` 和 `3` 不是按递减顺序排列的。
	+ **总结合法的出栈序列的特点**‌：合法的出栈序列要求每个数字后面的比它小的数字必须是按递减顺序排列的。这是判断出栈序列是否合法的基本规则。

+  代码：

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <stack>
#include <cstring>
#include <iostream>
#include <utility>
#include <map>
#include <algorithm>
#include <vector>
#include <climits>
#include <string>
#include <ctime>
#include <cmath>
#include <sstream>
#include <queue>
using namespace std;

//主函数
int main()
{
    int n, num, pointer = 0;
    int array[105];
    bool flag = true;
    scanf("%d", &n);
    for (int i = 0; i < n; i++)
    {
        scanf("%d", &array[i]);
    }
    for (int j = 0; j < n; j++)
    {
        if (pointer < array[j])
        {
            pointer = array[j];
        }
        else
        {
            num = array[j];
            for (int k = j + 1; k < n; k++)
            {
                if ((array[k - 1] < array[k] || num < array[k]) && pointer > array[k])
                {
                    flag = false;
                    break;
                }
            }
        }
    }
    if (flag == true)
        printf("Yes");
    else
        printf("No");
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

例题：[可能的出栈序列](https://sunnywhy.com/sfbj/7/1/295)

+ 解释：
	+ **总结合法的出栈序列的特点**‌：合法的出栈序列要求每个数字后面的比它小的数字必须是按递减顺序排列的。这是判断出栈序列是否合法的基本规则。
	+ 全排列的生成参考之前在**递归章节**学习的**递归生成全排列算法**。

+  代码：

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <stack>
#include <cstring>
#include <iostream>
#include <utility>
#include <map>
#include <algorithm>
#include <vector>
#include <climits>
#include <string>
#include <ctime>
#include <cmath>
#include <sstream>
#include <queue>
using namespace std;

//定义变量
const int MAXN = 105;
int n; //输出index-n的全排列
int p[MAXN];
bool HashTable[MAXN] = {false};

//判断是否合法出栈序列函数
bool isValidSeq(int *array)
{
    int num, pointer = 0;
    bool flag = true;
    for (int j = 1; j <= n; j++)
    {
        if (pointer < array[j])
        {
            pointer = array[j];
        }
        else
        {
            num = array[j];
            for (int k = j + 1; k <= n; k++)
            {
                if ((array[k - 1] < array[k] || num < array[k]) && pointer > array[k])
                {
                    flag = false;
                    break;
                }
            }
        }
    }
    return flag;
}

//生成全排列数据并判断是否合法出栈序列函数
void Full_permutation(int index)
{
    //递归边界
    if (index == n + 1)
    {
        if (isValidSeq(p) == true)
        {
            for (int i = 1; i < n; i++)
            {
                printf("%d ", p[i]);
            }
            printf("%d\n", p[n]);
        }
        return;
    }
    //递归式
    for (int k = 1; k <= n; k++)
    {
        if (!HashTable[k]) // HashTable[k]==false->说明该元素还没有被用上
        {
            p[index] = k;        //处理这一种情况
            HashTable[k] = true; //到这里说明假设1到index已经排好
            //递归进入函数再排index+1之后的部分
            Full_permutation(index + 1);
            //递归返回结束后循环还没有结束，继续处理下一循环的问题
            HashTable[k] = false; //已经处理完p[index]=k;这一种情况，还原状态
        }
    }
}

//主函数
int main()
{
    scanf("%d", &n);
    Full_permutation(1);
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

### 队列的应用
+ 队列是一种先进先出的数据结构。

![image.png](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202410282000775.png)

+ 一般而言，需要一个队首指针 `front` 来指向队首元素的前一个位置，而使用一个队尾指针 `rear` 来指向队尾元素。
	+ 当使用链表来实现队列时，则为 `int*` 型变量的指针。
	+ 当使用数组来实现队列时，队首指针 `front` 和队尾指针 `rear` 为 `int` 型变量（数组下标从 `0` 开始），其示例如下图所示：

![](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202410282028147.png)

+ 接下来将使用数组 `q[]` 来实现队列并介绍队列的常用操作：
#### 清空（clear）
+ 使用数组来实现队列时，初始状态为 `front=-1`、`rear=-1`，上图中第一步 `rear` 指向 `0` 是因为此时队列已经有一个元素了，如果没有元素，`rear` 应当是指向 `-1` 的位置。

```cpp
void clear()
{
	front = rear = -1;
}
```

####  获取队列内的元素个数（size）
+ 显然 `rear-front` 即为队列内的元素个数。

```cpp
int size()
{
	return rear - front;
}
```

#### 判空（empty）
+ 判定队列为空的条件为 `front==rear`.

```cpp
bool empty()
{
	if(front == rear)
		return true;
	else
		return false;
}
```

#### 入队（push）
+ 由于队尾指针 `rear` 指向队尾元素，因此把元素入队时，需要先 `++rear`，然后再将数据存放到 `rear` 指向的位置。

```cpp
void push(int x)
{
	q[++rear] = x;
}
```

#### 出队（pop）
+ 可以直接把队首指针 `front++` 来实现出队的效果。

```cpp
void pop()
{
	front++;
}
```

#### 取队首元素（get_front）
+ 由于队首指针 `front` 指向的是队首元素的前一个元素，因此 `front+1` 才是队首元素的位置。

```cpp
int get_front()
{
	return q[front+1];
}
```

#### 取队尾元素（get_rear）
+ 由于队尾指针 `rear` 指向的是队尾元素，因此可以直接访问 `rear` 的位置。

```cpp
int get_rear()
{
	return q[rear];
}
```

#### 总结
+ 与栈类似，`pop ()` 函数、`get_front()` 函数、`get_rear()` 函数之前必须先使用 `empty ()` 函数判断队列是否为空。
+ 同样的，可以使用 `C++ STL` 中的 `queue` 容器来非常容易地使用队列。
+ 另外，由于 `STL` 中没有实现队列的清空，因此需要使用一个 `while` 循环反复 `pop` 出元素直至队列为空。

```cpp
while(!q.empty())
{
	q.pop;
}
```
+ 更加常用的是重新定义一个队列以实现队列的清空，`STL` 中 `queue` 进行定义的时间复杂度和 `stack` 一样都是 $O(1)$ 。

### 链表处理
#### 链表的概念
