---
title: Linux 系统编程
tags:
  - Linux
  - ubuntu
categories:
  - Linux
date: 2025-5-18 16:00:00
excerpt: Linux 系统编程
---
# Linux 系统编程
## 通用 Makefile 文件
+ 通用 Makefile 文件的代码如下：

```shell
SRCS := $(wildcard *.c)
Outs := $(patsubst %.c, %, $(SRCS))

CC := gcc
CFLAGS = -Wall -g

all: $(Outs)

%: %.c
	$(CC) $(CFLAGS) $< -o $@

.PHONY: clean rebuild all

clean:
	$(RM) $(Outs)

rebuild: clean all
```

## 目录相关操作
### 获取当前工作目录
+ 我们可以调用库函数 `getcwd()` 获取当前工作目录的绝对路径：

![库函数 getcwd()](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250518165819.png)

+ 如果传入的 `buf` 为 `NULL` ，且 `size` 为 `0`，则 `getcwd()` 会调用 `malloc` 申请合适大小的内存空间，填入当前工作目录的绝对路径，然后返回 `malloc` 申请的空间的地址。
	+ **注意：** `getcwd()` 不负责 `free` 申请的空间， `free` 是调用者的职责。
+ 代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>

int main()
{
    char* cwd;
    if((cwd = getcwd(NULL,0)) == NULL)
    {
        //错误处理
        perror("getcwd");
        exit(1);
    }
	//一定处理成功
    puts(cwd);
    free(cwd);//由用户来free

    return 0;
}
```

+ 也可以与库函数 `error()` 来联动进行错误处理，从下图中的数据手册可以看到 `getcwd()` 出错的时候会设置 `errno` 这个参数：

![库函数 getcwd() 的返回值处理](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250518171625.png)

+ 库函数 `error()` 的用法：

![库函数 error() 的用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250518171744.png)

+ 代码如下：

```c
#include <unistd.h>
#include <error.h>
#include <errno.h>
#include <stdio.h>
#include <stdlib.h>

int main()
{
    char cwd[20];
    if(getcwd(cwd,20) == NULL)
    {
        //错误处理
        error(1, errno, "getcwd");
    }
    puts(cwd);
    
    return 0;
}
```

+ 运行结果：

![test_error 运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250518172857.png)

### 改变当前工作目录
+ 我们可以调用库函数 `chdir()` 改变当前工作目录的绝对路径：

![库函数 chdir()](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250518201356.png)

+ 代码：

```c
#include <stdio.h>
#include <unistd.h>
#include <error.h>
#include <errno.h>
#include <stdlib.h>

int main(int argc,char* argv[])
{
    if(argc != 2)
    {
        error(1, errno, "Usage: %s path",argv[0]);
    }
    
    char* cwd;
    if((cwd = getcwd(NULL,0)) == NULL)
    {
        error(1, errno, "getcwd");   
    }
    puts(cwd);
    free(cwd);
    
    //改变目录的惯用法
    if(chdir(argv[1]) == -1)
    {
        error(1, errno, "chdir %s",argv[1]);
    }
    
    if((cwd = getcwd(NULL,0)) == NULL)
    {
        error(1, errno, "getcwd");   
    }
    puts(cwd);
    free(cwd);
    
    return 0;
}
```

+ 注意：当前工作目录是进程的属性，也就是说每一个进程都有自己的当前工作目录。且父进程创建 ( `fork` )子进程的时候，子进程会继承父进程的当前工作目录。
+ 运行结果：

![chdir() 运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250518205754.png)

### 创建目录
+ `mkdir()` 函数可以用来创建目录。

![创建目录](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250519164757.png)

+ 代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>

int main(int argc, char* argv[])
{
    // ./test_mkdir dir mode(八进制)
    //参数校验
    if(argc != 3)
    {
        error(1, 0, "Usage %s dir mode",argv[0]);
    }
    
    //参数类型转换
    mode_t mode;
    sscanf(argv[2], "%o", &mode);
    int err = mkdir(argv[1], mode);
    if(err == -1)
    {
        error(1, errno, "mkdir %s",argv[1]);
    }
    return 0;
}
```

+ 当不知道 `mode_t` 的类型是什么的时候，可以使用以下方法来查看：

```sh
gcc -E test_mkdir.c -o test_mkdir.i
grep -nE "mode_t" test_mkdir.i
```

+ 运行结果：

![运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250519165619.png)

### 删除空目录
+ `rmdir()` 可以删除空目录。

![删除空目录](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250519171201.png)

+ 代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>

int main(int argc, char* argv[])
{
    // ./test_rmdir dir 
    //参数校验
    if(argc != 2)
    {
        error(1, 0, "Usage %s dir",argv[0]);
    }
    //功能实现
    if(rmdir(argv[1]) == -1)
    {
        error(1, errno, "rmdir %s",argv[1]);
    }
    
    return 0;
}
```

### 目录流
+ 使用目录流，可以查看目录中的内容。
	+ 流模型："流"类似"水流"，顺序访问流中的数据时，是不需要关注位置的。
	+ 目录流与文件流相比，文件流中的基本单位是字符或字节。而目录流中的基本单位是目录项。如下图所示：

![目录流模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250519195922.png)

#### 打开目录流
+ `opendir()` 可以打开一个目录，得到一个指向目录流的指针 `DIR*` 。

![打开一个目录](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250519200055.png)

#### 关闭目录流
+ `closedir()` 关闭目录流。

![关闭目录流](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250519200152.png)

#### 读取目录流
+ `readdir()` 读目录流，得到指向下一个目录项的指针。

![读取目录流](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250519200337.png)

+ 结构体 `dirent` 的定义：

![结构体 dirent 的定义](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250519200424.png)

+ 读取一个目录内的内容并打印的代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <dirent.h>

int main(int argc, char* argv[])
{
    // ./test_dirent dir
    //参数校验
    if(argc != 2)
    {
        error(1, 0, "Usage %s dir",argv[0]);
    }
    //打开目录流
    DIR* stream = opendir(argv[1]);
    if(!stream)
    {
        error(1, errno, "opendir %s",argv[1]);
    }
    //处理目录流
    errno = 0; // 等于0表示没出错
    struct dirent* pdirent;
    //readdir():读目录流，得到指向下一个目录项的指针。
    while((pdirent = readdir(stream)) != NULL)
    {
        printf("d_info=%ld, d_off=%ld, d_reclen=%hu, d_type=%d, d_name=%s\n",
        pdirent->d_ino,
        pdirent->d_off,
        pdirent->d_reclen,
        pdirent->d_type,
        pdirent->d_name);
    }
    if(errno != 0)
    {
        error(1, errno, "readdir %s",argv[1]);
    }
    //关闭目录流
    closedir(stream);
    
    return 0;
}
```

#### 递归地打印目录
+ 实现青春版 `tree` 命令：输出内容分为三部分 
	+ 1)目录的名字；
	+ 2)递归打印每一个目录项；
	+ 3)最后是统计信息。
+ 代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <dirent.h>
#include <string.h>

//width:缩进的空格数目
void dfs_print(const char* path, int width);

int directories = 0, files = 0;

int main(int argc, char* argv[])
{
    // 用法: ./young_tree dir
    if(argc != 2)
        error(1, 0, "Usage: %s dir", argv[0]);
    //遍历根节点
    puts(argv[1]);
    //递归打印每一个子树
    dfs_print(argv[1], 4);
    
    printf("\n%d directories, %d files\n", directories, files);
    
    return 0;
}

void dfs_print(const char* path, int width){
    //打开目录流
    DIR* stream = opendir(path);
    if(!stream){
        error(1, errno, "opendir %s", path);
    }
    //遍历每一个目录项
    errno = 0; // 等于0表示没出错
    struct dirent* pdirent;
    //readdir():读目录流，得到指向下一个目录项的指针。
    while((pdirent = readdir(stream)) != NULL){
        char* filename = pdirent->d_name;
        //忽略 . 和 ..
        if(strcmp(filename,".") == 0 || strcmp(filename,"..") == 0)
            continue;
        //打印这个目录项的名字
        for(int i=0; i<width; i++){
            putchar(' ');
        }
        puts(filename);
        //如果是目录的话递归地处理
        if(pdirent->d_type == DT_DIR){
            directories++;
            //拼接路径
            char subpath[128];
            sprintf(subpath, "%s/%s", path, filename);
            dfs_print(subpath, width+4);
        }
        else
            files++;
    }
    closedir(stream);
    if(errno){
        error(1, errno, "readdir");
    }
}
```

+ 效果：

![递归打印目录实验结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250528165342.png)

## 文件相关操作
### 打开文件
+ 系统调用 `open()` 打开文件。
	+ 打开成功：返回新的文件描述符（最小可用的文件描述符）；
	+ 打开失败：返回 `-1`，设置 `errno` 。

![open() 系统调用的用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250519204706.png)

+ 部分 `flags` 的含义：

![部分 flags 的含义](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250519204812.png)

+ 代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

int main(int argc, char* argv[])
{
    // ./test_open file
    //参数校验
    if(argc != 2)
    {
        error(1, 0, "Usage %s file",argv[0]);
    }
    //打开文件，获取文件描述符
    int fd = open(argv[1], O_RDWR | O_CREAT);
    if(fd == -1)
    {
        error(1, errno, "Usage %s", argv[1]);
    }
    //打印获取到的文件描述符
    printf("%d\n",fd);
    
    return 0;
}
```

+ 通过系统调用 `open()` 的过程介绍内核管理文件的数据结构：
	+ 文件描述符表的数组长度默认是 1024，并且可以进行设置： 

![文件描述符表的数组长度默认是 1024](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250603171236.png)

+ 系统调用 `open()` 的内核管理文件流程：

![open()系统调用内核管理文件流程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250519210649.png)

### 关闭文件
+ 系统调用 `close()` 关闭文件。
	+ 关闭成功：返回 `0` ；
	+ 关闭失败：返回 `-1`，设置 `errno` 。
+ 系统调用 `close()` 的内核管理文件流程：

![close()系统调用内核管理文件流程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250603195205.png)

### 读取文件
+ 系统调用 `read()` 读取文件。
	+ 读取成功：返回实际读取的字节数目（`0`，表示读取的起始位置在文件末尾）；
	+ 读取失败：返回 `-1`，设置 `errno` 。

![系统调用read()读取文件](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250603195627.png)

+ 系统调用 `read()` 的内核管理文件流程：

![read()系统调用内核管理文件流程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250603195750.png)

### 写入文件
+ 系统调用 `write()` 读取文件。
	+ 写入成功：返回实际写入字节数目（其中 `n≤count` ）；
	+ 写入失败：返回 `-1`，设置 `errno` 。

![write()系统调用](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250603200844.png)

+ 系统调用 `write()` 的内核管理文件流程：

![write()系统调用内核管理文件流程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250603200953.png)

### 修改文件偏移量
+ 系统调用 `lseek()` 修改文件偏移量，本质上就是修改 `current file offset (pos)` 的数值。
	+ 修改成功：返回文件的位置（距离文件开头的字节数目）；
	+ 修改失败：返回 `-1`，设置 `errno` 。

![系统调用 lseek() 修改文件偏移量](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250603205059.png)

+ 系统调用 `lseek()` 的内核管理文件流程：

![系统调用 lseek() 的内核管理文件流程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250603205158.png)

+ 文件库函数和文件系统调用之间的关系：

![文件库函数和文件系统调用之间的关系](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250603205358.png)

### 文件描述符和文件流的异同
+ 文件描述符的系统调用与文件流的函数：

![文件描述符的系统调用与文件流的函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250605145014.png)

+ 文件描述符和文件流的数据访问流程：

![文件描述符和文件流的数据访问流程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250605145112.png)

### 同步内存中所有已修改的文件数据到储存设备
+ 函数 `fsync()` 同步内存中所有已修改的文件数据到储存设备。
	+ 将和文件描述符相关联的脏页刷新到磁盘。
	+ 刷新成功：返回 `0`；
	+ 刷新失败：返回 `-1` 并设置 `errno`。

![系统调用 fsync()](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250605145633.png)

### 修改文件长度
+ 函数 `ftruncate()` 修改文件长度为 `length` 个字节大小。
+ 用法如下：

![系统调用 ftruncate() 的用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250605161558.png)

+ 修改文件的长度有两种情况：
	+ 情况二可能会出现文件空洞的情况，此时数据全为 `0` 的页不会分配磁盘空间。

![修改文件的长度的两种情况](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250605161818.png)

+ 使用代码示例：

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

int main(int argc, char* argv[])
{
    // ./test_ftruncate file length
    if(argc != 3){
        error(1, 0, "Usage: %s file length", argv[0]);
    }
    
    off_t length; // 要截断的文件长度
    sscanf(argv[2], "%ld", &length);
    
    int fd = open(argv[1], O_RDWR);
    if(fd == -1){
        error(1, errno, "open %s", argv[1]);
    }
    
    if(ftruncate(fd, length) == -1){
        error(1, errno, "ftruncate %d", fd);
    } // 截断成功
    
    close(fd);
    
    return 0;
}
```

+ 关于文件空洞的测试：

![关于文件空洞的测试](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250605162213.png)

### 获取文件状态信息
+ 函数 `fstat()` 用于获取文件状态信息。
	+ 获取成功：返回 `0`；
	+ 获取失败：返回 `-1`，设置 `errno`。
+ 其具体用法和状态信息结构体如下：

![fstat() 的具体用法和状态信息结构体](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250605165212.png)

+ 代码示例：

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

int main(int argc, char* argv[])
{
    // ./test_fstat file
    if(argc != 2){
        error(1, 0, "Usage %s file", argv[0]);
    }
    
    int fd = open(argv[1], O_RDONLY);
    if(fd == -1){
        error(1, errno, "open %s", argv[1]);
    }
    
    struct stat sb; // 存储文件元数据信息
    if(fstat(fd, &sb) == -1){
        error(1, errno, "fstat %d", fd);
    }
    
    // 打印 struct stat 里面的成员信息
    printf("st_ino=%ld\nst_mode=%lo\nst_nlink=%ld\nst_size=%ld\nst_blocks=%ld\n",
            (long)sb.st_ino,
            (long)sb.st_mode,
            (long)sb.st_nlink,
            (long)sb.st_size,
            (long)sb.st_blocks);
            
    return 0;
}
```

+ 打印结果：

![打印结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250605165347.png)

### 文件描述符的复制
+ 系统调用 `dup()` 用于对文件描述符的复制。
	+ 复制成功：返回新的文件描述符；
	+ 复制失败：返回 `-1`，设置 `errno`。
+ 其具体用法如下，分为 `dup()` 和 `dup2()` 两个系统调用：

![两个文件描述符系统调用的具体用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250606173642.png)

+ 系统调用 `dup()` 的内核管理文件流程：

![系统调用 dup() 的内核管理文件流程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250606174115.png)

+ 使用 `dup()` 函数实现重定向：

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

int main(int argc, char* argv[])
{
    // 对 stderr 重定向
    int fd = open("application.log", O_RDWR | O_CREAT | O_APPEND, 0664);
    if (fd == -1){
        error(1, errno, "open application.log");
    }
    
    // STDERR_FILENO 标准错误输出的文件描述符
    write(STDERR_FILENO, "The first error massage\n", 24);
    
    //对 stderr 进行重定向
    close(STDERR_FILENO);
    dup(fd);
    
    write(STDERR_FILENO, "The second error massage\n", 25);
    
    return 0;
}
```

+ 使用 `dup2()` 函数实现重定向：

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

int main(int argc, char* argv[])
{
    // 对 stderr 重定向
    int fd = open("application.log", O_RDWR | O_CREAT | O_APPEND, 0664);
    if (fd == -1){
        error(1, errno, "open application.log");
    }
    
    // STDERR_FILENO 标准错误输出的文件描述符
    write(STDERR_FILENO, "The first error massage\n", 24);
    
    //对 stderr 进行重定向
    //close(STDERR_FILENO);
    if(dup2(fd, STDERR_FILENO) == -1){
        error(1, errno, "dup2 %d %d", fd, STDERR_FILENO);
    }
    
    write(STDERR_FILENO, "The second error massage\n", 25);
    
    return 0;
}
```

+ 分别执行完两个代码的运行结果：
	+ 第一个代码重定向 `STDERR_FILENO` 文件描述符到 `application.log` 文件，写入一段语句；
	+ 第二个代码同样重定向 `STDERR_FILENO` 文件描述符到 `application.log` 文件，写入一段语句。

![分别执行完两个代码的运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250606174455.png)

### 零拷贝（mmap）
+ 系统调用零拷贝（ `mmap()` ）能够省去内核态和用户态的内存拷贝。

![零拷贝](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202506071540394.png)

+ 系统调用零拷贝（ `mmap()` ）的工作原理：
	+ 相当于将文件的一部分内存通过 `I/O` 操作拷贝到物理内存中，并且内核态和用户态共用这部分内存，不再需要重复拷贝。

![image.png](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202506071542883.png)

+ 系统调用零拷贝（ `mmap()` 和 `munmap()` ）的使用方法：

![系统调用零拷贝 mmap() 和 munmap() 的使用方法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202506071600326.png)

+ 系统调用零拷贝（ `mmap()` 和 `munmap()` ）的返回值：

![系统调用零拷贝 mmap() 和 munmap() 的返回值](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202506071602492.png)

+ 系统调用零拷贝（ `mmap()` 和 `munmap()` ）的复制大文件使用场景：

![系统调用零拷贝 mmap() 和 munmap() 的复制大文件使用场景](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202506071651605.png)

+ 代码示例：

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <sys/mman.h>

// offset 应该是页大小的整数倍
#define MMAP_SIZE (4096 * 10)

int main(int argc, char *argv[])
{
    // ./mmap_cp src dst
    if (argc != 3){
        error(1, 0, "Usage: %s src dst", argv[0]);
    }
    
    int srcfd = open(argv[1], O_RDONLY);
    if (srcfd == -1){
        error(1, errno, "open %s", argv[1]);
    }
    // 需要读写权限
    int dstfd = open(argv[2], O_RDWR | O_CREAT | O_TRUNC, 0666);
    if (dstfd == -1){
        close(srcfd);
        error(1, errno, "open %s", argv[2]);
    }
    // 1.需要事先知道文件大小
    // 获取src大小
    struct stat sb;
    fstat(srcfd, &sb);
    off_t fsize = sb.st_size;
    // 将dst文件大小设置为fsize
    ftruncate(dstfd, fsize); // 目标文件大小需要事先固定
    // 设置初始偏移量，表示已经复制的数据
    off_t offset = 0;
    while (offset < fsize)
    {
        // 计算映射区长度
        off_t length;
        if (fsize - offset >= MMAP_SIZE)
            length = MMAP_SIZE;
        else
            length = fsize - offset;
        // 映射
        void *addr1 = mmap(NULL, length, PROT_READ, MAP_SHARED, srcfd, offset);
        if (addr1 == MAP_FAILED){
            error(1, errno, "mmap %s", argv[1]);
        }
        
        void *addr2 = mmap(NULL, length, PROT_READ | PROT_WRITE, MAP_SHARED, dstfd, offset);
        if (addr2 == MAP_FAILED){
            error(1, errno, "mmap %s", argv[2]);
        }
        // 通过将addr1的内容复制到addr2中实现文件的复制
        memcpy(addr2, addr1, length);
        offset += length;
        // 解除映射
        int err = munmap(addr1, length);
        if (err == -1){
            error(1, errno, "munmap %s", argv[1]);
        }
        
        err = munmap(addr2, length);
        if (err == -1){
            error(1, errno, "munmap %s", argv[2]);
        }
    } // offset == fsize
    return 0;
}
```

## CPU 的虚拟化
### 前置知识
+ 内核的职责：管理硬件资源
+ 共享资源的方式：
	+ 时分共享（CPU）
	+ 空分共享（内存）
+ 操作系统通过让一个进程运行一段时间，然后切换到其它进程，缺点是会造成性能损失（需要进行上下文切换）。
+ 如何实现 CPU 的时分共享？
	+ 底层机制：如何进行上下文切换
	+ 上层策略：调度策略

### 认识进程
+ 用户角度：进程就是正在执行的程序
+ 内核角度：要执行的任务（ `struct task_t` ）
	+ 进程之间必须隔离，进程之间是相互看不到的，感知不到另外的进程存在。
	+ 以进程的角度看，就像它独占计算机的所有资源（抽象机制：CPU 的虚拟化）。
+ `xv6` 操作系统的进程相关结构体：

![xv6 操作系统的进程相关结构体](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608162423.png)

### 底层机制：实现上下文切换的三种方式及优缺点
+ 指标：
	+ 性能：不应该增加太多的系统开销
	+ 控制权：操作系统应该保留控制权

#### 方式一：直接运行（无限制）
+ 优点：简单、快
+ 缺点：没有控制权、不安全。

![直接运行（无限制）](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608170100.png)

+ 如何限制应用程序的权限？
	+ 不能让用户态应用程序访问非法的内存空间和执行一些特权指令。
	+ 需要硬件的协助即 CPU 的模态（模式）：
		+ 用户态：应用程序（不能访问非法的内存空间和执行一些特权指令）
		+ 内核态：操作系统（可以访问机器的所有资源）
#### 方式二：受限直接运行协议
+ 应用程序如何执行特权操作？
	+ 通过系统调用！

![应用程序通过系统调用执行特权操作](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608170707.png)

+ 通过特殊指令 `trap` 来切换用户态和内核态：
	+ 缺点在于控制权不是主动掌握在操作系统上。

![受限直接运行协议](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608171106.png)

#### 方式三：受限直接运行协议（时钟中断）
+ 方式二采用协作（ `yield()` ）的方式，等待系统调用。
+ 方式三采用非协作方式（抢占方式），操作系统能够主动进行控制。
+ 时钟中断的方式：

![时钟中断的方式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608171600.png)

+ 受限直接运行协议（时钟中断）：

![受限直接运行协议（时钟中断）](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608171645.png)

+ 进程之间是隔离的（感知不到内核和其他进程的存在）。
+ 进程是资源分配的最小单位（任务<-分配资源）。
+ 上下文切换：
	+ 调用系统调用
	+ 切换进程

### 和进程相关的常用命令
#### 显示进程
+ `ps` 命令显示和终端关联的进程：

![ps 命令显示和终端关联的进程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608200539.png)

+ `ps x` 显示和用户关联的进程：

![ps x 显示和用户关联的进程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608200642.png)

+ `ps aux` 显示所有用户相关的进程：

![ps aux 显示所有用户相关的进程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608200744.png)

+ `top` 每 3 秒统计一次进程信息：

![top 每 3 秒统计一次进程信息](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608200852.png)

+ `pstree` 打印进程树：

![pstree 打印进程树](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608200946.png)

+ 前台进程：

![前台进程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608201022.png)

+ 后台进程：

![后台进程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608201050.png)

### 获取进程的标识
+ 获取进程的标识的用法：

![获取进程的标识的用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608202024.png)

+ 代码示例：

```c
#include <unistd.h>
#include <stdio.h>

int main(int argc, char* argv[])
{
    // ./test_getpid
    printf("pid=%d\n",getpid());
    printf("ppid=%d\n",getppid());

    sleep(10);
    return 0;
}
```

+ 运行结果：

![运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608202612.png)

 + `Linux` 进程 `id` 的分配策略：

![image.png](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250608202721.png)

### 进程的基本操作
+ 创建进程：`fork()`
+ 终止进程：`exit()` 、`_exit()`、`abort()`、`wait()`、`waitpid()`
+ 执行程序：`exec` 函数簇

#### 创建进程：fork()
+ 系统调用 `fork()` 的用法：

![系统调用 fork() 的用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609155511.png)

+ 系统调用 `fork()` 的返回值：
	+ 成功：
		+ 父进程：子进程的 `pid`
		+ 子进程：`0`
	+ 失败：
		+ 父进程：`-1`，并且不会创建子进程，设置 `errno`。

+ 惯用法代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/types.h>
#include <error.h>
#include <errno.h>

int main(int argc, char* argv[])
{
    // ./test_fork1 
    printf("BEGIN:\n");
    
    //惯用法
    //父子进程都是从fork()返回
    //子进程不会执行前面的代码
    pid_t pid = fork(); 
    
    switch(pid){
        case -1:
            //出错
            error(1, errno, "fork");
        case 0:
            //子进程
            printf("I am a baby\n");
            printf("child:pid = %d, ppid = %d\n", getpid(), getppid());
            break;
        default:
            //父进程
            printf("Who's your daddy?\n");
            printf("parent:pid = %d, chilpid = %d\n", getpid(), pid);
            break;
    }
    printf("BYE BYE!\n"); // 父子进程
    
    return 0;
}
```

+ 测试结果：
	+ 到底是父进程先执行还是子进程先执行是不确定的（不能假定到底是谁先执行）。

![测试结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609155956.png)

+  系统调用 `fork()` 的原理：

![系统调用 fork() 的原理](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609160153.png)

##### 代码段：父子进程共享（不能修改）
+ 栈、堆、数据段（父子进程私有）
+ 测试代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <error.h>
#include <errno.h>

int g_value = 10; // 数据段

int main(int argc, char* argv[])
{
    // ./test_fork2
    int l_value = 20; // 栈
    int* d_value = (int*)malloc(sizeof(int)); // 堆
    *d_value = 30;
    
    //惯用法
    //父子进程都是从fork()返回
    //子进程不会执行前面的代码
    pid_t pid = fork(); 
    
    switch(pid){
        case -1:
            //出错
            error(1, errno, "fork");
        case 0:
            //子进程
            g_value += 100;
            l_value += 100;
            *d_value += 100;
            printf("g_value = %d, l_value = %d, d_value = %d\n", g_value, l_value, *d_value);
            exit(0);
        default:
            //父进程
            sleep(2);
            printf("g_value = %d, l_value = %d, d_value = %d\n", g_value, l_value, *d_value);
            exit(0);
    }
    return 0;
}
```

+ 测试结果：

![测试结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609161526.png)

##### 用户态缓冲区（文件流）：父子进程是私有的

![用户态缓冲区（文件流）：父子进程是私有的](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609161723.png)

+ 测试代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <error.h>
#include <errno.h>

int main(int argc, char* argv[])
{
    // ./test_fork3
    printf("BEGIN:"); // stdout是行缓冲区
    
    //惯用法
    //父子进程都是从fork()返回
    //子进程不会执行前面的代码
    pid_t pid = fork(); 
    
    switch(pid){
        case -1:
            //出错
            error(1, errno, "fork");
        case 0:
            //子进程
            printf("I am a baby\n");
            exit(0);
        default:
            //父进程
            sleep(2);
            printf("Who's your daddy?\n");
            exit(0);
    }
    return 0;
}
```

+ 测试结果：

![测试结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609162127.png)

+ 用户缓冲区父子进程私有经典题目一：
	+ 总共输出 24 个 `a`。

![经典题目一](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609164244.png)

+ 用户缓冲区父子进程私有经典题目二：
	+ 总共输出 14 个 `a`。

![经典题目二](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609164333.png)

##### 打开文件（共享的）、文件描述符列表（私有的）
+ 对于父子进程，打开文件（共享的）、文件描述符列表（私有的）
+ 测试代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

int main(int argc, char* argv[])
{
    // ./test_fork4
    int fd = open("test.txt", O_RDWR | O_CREAT | O_TRUNC, 0666);
    printf("origin_pos: %ld\n", lseek(fd, 0, SEEK_CUR));

    //惯用法
    //父子进程都是从fork()返回
    //子进程不会执行前面的代码
    pid_t pid = fork(); 
    int newfd;

    switch(pid){
        case -1:
            //出错
            error(1, errno, "fork");
        case 0:
            //子进程
            write(fd, "hello world", 11);
            printf("child_pos: %ld\n", lseek(fd, 0, SEEK_CUR));
            close(STDERR_FILENO);

            newfd = dup(fd); // newfd = 2
            printf("child_newfd = %d\n", newfd);
            exit(0);
        default:
            //父进程
            sleep(2);
            printf("parent_pos: %ld\n", lseek(fd, 0, SEEK_CUR));
            newfd = dup(fd); // newfd = 4
            printf("parent_newfd = %d\n", newfd);
            exit(0);
    }
    return 0;
}
```

+ 测试结果：

![测试结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609165658.png)

#### 终止进程
+ 基本概念：

![终止进程的基本概念](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609170714.png)
##### 正常终止
+ 库函数 `exit()` 的步骤以及系统调用 `atexit()` 的用法和返回值：

![库函数 exit() 的步骤和返回值](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609180501.png)

+ 示例代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

//执行一些资源清理操作
void func(void){
    printf("Something going to die...");
}

int main(int argc, char* argv[])
{
    // ./test_exit
    // 调用atexit()注册函数
    int err = atexit(func); // 仅注册，不会执行func函数
    if(err != 0){
        error(1, 0, "atexit()");
    }
    
    //正常执行程序...
    printf("Hello world");
    
    exit(123); //前面分析的执行exit函数后第一步执行atexit()注册的func函数
    
    return 0;
}
```

+ 测试结果：

![测试结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609181435.png)

+ 系统调用 `_exit()` 的用法：
	+ 退出状态码传至操作系统。

![系统调用 _exit() 的用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609181614.png)

+ 测试程序：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

//执行一些资源清理操作
void func(void){
    printf("Something going to die...");
}

int main(int argc, char* argv[])
{
    // ./test_exit
    // 调用atexit()注册函数
    int err = atexit(func);
    if(err != 0){
        error(1, 0, "atexit()");
    }
    
    //正常执行程序...
    printf("Hello world");
    
    _exit(123);
    
    return 0;
}
```

+ 测试结果：

![测试结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609181901.png)

##### 异常终止
+ 系统调用 `abort()` 的用法：

![系统调用 abort() 的用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609182101.png)

+ 测试代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

//执行一些资源清理操作
void func(void){
    printf("Something going to die...");
}

int main(int argc, char* argv[])
{
    // ./test__exit
    // 调用atexit()注册函数
    int err = atexit(func);
    if(err != 0){
        error(1, 0, "atexit()");
    }
    
    //正常执行程序...
    printf("Hello world");
    
    abort();
    
    printf("You can't see me!!!");
    
    return 0;
}
```

+ 测试结果：

![测试结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609182315.png)

+ 内核给该进程发送 `SIGABRT` 信号

![内核给该进程发送 SIGABRT 信号](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250609182437.png)

### 孤儿进程和僵尸进程
+ 孤儿进程：子进程存活，父进程终止了
+ 测试代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

int main(int argc, char* argv[])
{
    // ./orphen
    pid_t pid = fork();
    switch(pid){
        case -1:
            error(1, errno, "fork");
        case 0:
            //子进程
            sleep(2);
            printf("pid = %d, ppid = %d\n", getpid(), getppid());
            exit(0);
        default:
            //父进程
            printf("Parent: pid = %d, childpid = %d\n", getpid(), pid);
            exit(0);
    }
    return 0;
}
```

+ 测试结果：

![僵尸进程测试结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250610164343.png)

+ 分析：孤儿进程会被 `1` 号进程（`init` 进程）收养，该进程一直循环执行 `wait` 函数。

![孤儿进程分析](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250610164541.png)

+ 僵尸进程：子进程死亡时，有一些信息会保存在内核（`pid` 、退出状态、CPU 时间......），方便父进程以后查看这个信息，并且给父进程发送 `SIGCHLD` 信号，但父进程默认会忽略信号。
	+ 如何给僵尸进程收尸：`wait`、`waitpid`。

#### wait()
+ 系统调用 `wait()` 的用法和返回值：

![系统调用 wait() 的用法和返回值](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250610165007.png)

+ 测试程序：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

void print_wstatus(int status){
    if(WIFEXITED(status)){
        int exit_code = WEXITSTATUS(status);
        printf("exit_code = %d", exit_code);
    }
    else if(WIFSIGNALED(status)){
        int signo = WTERMSIG(status);
        printf("term_sig = %d", signo);
    }
#ifdef WCOREDUMP
    if(WCOREDUMP(status)){
        printf(" (core dump) ");
    }
#endif
    printf("\n");
}

int main(int argc, char* argv[])
{
    pid_t pid = fork();
    switch(pid){
        case -1:
            error(1, errno, "fork");
        case 0:
            //子进程
            printf("CHILD: pid = %d\n", getpid());
            return 123;
        default:
            //父进程
            int status; // 保存子进程的终止状态信息，位图。
            pid_t childPid = wait(&status); // 阻塞点：一直等待，直到有子进程终止
            if(childPid > 0){
                printf("PARENT: %d terminated\n", childPid);
                print_wstatus(status);
            }
            exit(0);
    }
    return 0;
}
```

+ 测试结果：

![测试结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250610172230.png)

#### waitpid()
+ 系统调用 `waitpid()` 的用法：

![系统调用 waitpid() 的用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250610180607.png)

+ 系统调用 `waitpid()` 的返回值：

![系统调用 waitpid() 的返回值](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250610180707.png)

+ 测试程序：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

void print_wstatus(int status){
    if(WIFEXITED(status)){
        int exit_code = WEXITSTATUS(status);
        printf("exit_code = %d", exit_code);
    }
    else if(WIFSIGNALED(status)){
        int signo = WTERMSIG(status);
        printf("term_sig = %d", signo);
    }
#ifdef WCOREDUMP
    if(WCOREDUMP(status)){
        printf(" (core dump) ");
    }
#endif
    printf("\n");
}

int main(int argc, char* argv[])
{
    pid_t pid = fork();
    switch(pid){
        case -1:
            error(1, errno, "fork");
        case 0:
            //子进程
            printf("CHILD: pid = %d\n", getpid());
            return 123;
        default:
            //父进程
            int status; // 保存子进程的终止状态信息，位图。
            pid_t childPid = waitpid(-1, &status, WNOHANG); // 阻塞点：一直等待，直到有子进程终止
            if(childPid > 0){
                printf("PARENT: %d terminated\n", childPid);
                print_wstatus(status);
            }
            else if(childPid == 0){
                printf("PARENT: no child changed state!\n");
            }
            else{
                error(1, 0, "waitpid");
            }
            exit(0);
    }
    return 0;
}
```

+ 测试结果：

![测试结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250610181037.png)

### exec 函数簇
#### 环境变量 env 的定义：

![环境变量 env 的内容](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250611190306.png)

+ 打印环境变量 `env` 测试程序：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

int main(int argc, char* argv[])
{
    // ./test_environ
    // 打印进程的pid
    printf("pid = %d, ppid = %d\n", getpid(), getppid());
    
    // 打印命令行参数
    for(int i=0; i<argc; i++){
        puts(argv[i]);
    }
    printf("-----------------------------\n");
    // 打印环境变量
    // 声明外部变量(引用其它文件定义的environ变量)
    extern char** environ; 
    char** cur = environ;
    while(*cur){
        puts(*cur);
        cur++;
    }
    return 0;
}
```

+ 测试结果：

![打印环境变量测试结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250611190428.png)

#### exec 函数簇
+ `exec` 函数簇的用法：

![exec 函数簇的用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250611190641.png)

+ `exec` 函数簇的测试程序：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>

// 定义新的环境变量
char* const new_env[] = {"USER=yugin", "TEST=0123", NULL};
// 使用execv函数簇的命令行参数向量
char* const args[] = {"./test_environ", "aaa", "bbb", "ccc", NULL};

int main(int argc, char* argv[])
{
    // ./test_exce
    // 打印进程的pid
    printf("pid = %d, ppid = %d\n", getpid(), getppid());
    
    printf("BEGIN:\n");

    //execl("test_environ", "./test_environ", "aaa", "bbb", "ccc", NULL);
    //p:会根据path环境变量查找可执行程序
    //execlp("test_environ", "./test_environ", "aaa", "bbb", "ccc", NULL);
    execle("test_environ", "./test_environ", "aaa", "bbb", "ccc", NULL, new_env);
    //execv("test_environ", args);
    //p:会根据path环境变量查找可执行程序
    //execvp("test_environ", args);
    //execve("test_environ", args, new_env);

	printf("You cannot see here!");
    error(1, errno, "exce");

    return 0;
}
```

+ 测试结果：

![exce 函数簇测试结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250611193253.png)

+ `exec` 现象和原理：
	+ 从上图可以看到，`pid` 和 `ppid` 没有改变，因此没有创建新的进程，并且在新的可执行程序 `mian` 函数的第一行开始执行。
	+ 原理在于：
		+ 执行 `exec` 函数簇会清除进程的代码段、数据段、堆、栈、上下文；
		+ 加载新的可执行程序（设置代码段、数据段）；
		+ 从新的可执行程序 `mian` 函数的第一行开始执行。
+ 清除进程的代码段、数据段、堆、栈、上下文如下图所示：

![清除进程的代码段、数据段、堆、栈、上下文](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250611194231.png)

#### system 的实现和惯用法
+ `system()` 系统调用的用法：

![system() 系统调用的用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250612161213.png)

+ 简易 `system()` 的实现代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <sys/types.h>
#include <sys/wait.h>

int simple_system(const char* command){
    pid_t childPid = fork();
    int status;

    switch(childPid){
        case -1: // 出错
            return -1;
        case 0:
            execl("/bin/sh", "sh", "-c", command, NULL);
            _exit(127); // 出错了直接退出，避免stdout输出两次
        default:
            if(waitpid(childPid, &status, 0) == -1){
                return -1;
            }
            else{
                return status;
            }
    }
}

int main(int argc, char* argv[])
{
    // ./test_system
    simple_system("ls");
    return 0;
}
```

+ 惯用法说明：

![惯用法说明](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250612161400.png)

#### Simple_shell 的实现
+ 使用 `exec` 函数簇实现建议 `shell`：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>

#define MAXLINE 1024
#define MAXRGS 128

void print_wstatus(int status){
    if(WIFEXITED(status)){
        int exit_code = WEXITSTATUS(status);
        printf("exit_code = %d", exit_code);
    }
    else if(WIFSIGNALED(status)){
        int signo = WTERMSIG(status);
        printf("term_sig = %d", signo);
    }
#ifdef WCOREDUMP
    if(WCOREDUMP(status)){
        printf(" (core dump) ");
    }
#endif
    printf("\n");
}

// 解析命令行参数
void parse_parameters(char* message, char* argv[]){
    int i=0;
    argv[i] = strtok(message, " \t\n");
    while(argv[i] != NULL){
        i++;
        argv[i] = strtok(NULL, " \t\n");
    } // argv[i] == NULL
}

int main(int argc, char* argv[])
{
    char cmd[MAXLINE];
    char* parameters[MAXRGS];

    int status;

    for(;;){
        // 读取用户命令
        printf("Simple_shell > ");
        fgets(cmd, MAXLINE, stdin);
        // 如果cmd是exit，终止程序
        if(strcmp(cmd, "exit\n") == 0){
            exit(0);
        }
        // 创建子进程，让子进程执行命令
        pid_t pid = fork();

        switch(pid){
            case -1:
                error(1, errno, "fork");
            case 0: // 子进程
                // 解析命令行参数
                parse_parameters(cmd, parameters);
                if(execvp(parameters[0], parameters) == -1){
                    error(1, errno, "exevp");
                }
                break;
            default:
                if(waitpid(pid, &status, 0) == -1){
                    error(1, errno, "waitpid");
                }
                // 打印子进程的终止信息
                printf("%d terminated\n", pid);
                print_wstatus(status);
        }
    }
    return 0;
}
```

### 进程间通信
#### 管道 pipe
+ 管道：内核管理的一个数据结构
	+ 管道需要读端和写端都就绪，`open` 才会返回。
	+ 当写端写入数据时，`read` 才会返回，否则是阻塞状态。
	+ 如果写端关闭，读端是可以读到剩余数据，如果数据读完了，读端会读到 `EOF`（`read` 会返回 `0`）；
+ 创建管道：

![创建管道](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250701193043.png)

+ 进程间管道通信惯用法：
	+ 先 `pipe`
	+ 再 `fork`
	+ 父进程关闭管道一端
	+ 子进程关闭管道的另一端

![进程间管道通信惯用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250701193221.png)

+ 代码：

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>

#define MAXLINE 1024

int main(int argc, char* argv[])
{
    // 先创建管道pipe
    int fields[2]; // fields[0]读端、fields[1]写端
    if(pipe(fields) == -1)
        error(1, errno, "pipe");
    // 再fork
    pid_t pid = fork();
    switch(pid){
        case -1:
            error(1, errno, "fork");
        case 0: // 子进程
            close(fields[1]);
            char message[MAXLINE];
            read(fields[0], message, MAXLINE);
            printf("child: %s",message);
            break;
        default: // 父进程
            close(fields[0]);
            sleep(5); // 管道为空会阻塞子进程
            write(fields[1], "This is parent!\n", 16+1); // +1 for '\0'
    }
    return 0;
}
```

#### 有名管道 mkfifo
+ 有名管道 `mkfifo`：
	+ 管道需要读端和写端都就绪，`open` 才会返回。
	+ 当写端写入数据时，`read` 才会返回，否则是阻塞状态。
	+ 如果写端关闭，读端是可以读到剩余数据，如果数据读完了，读端会读到 `EOF`（`read` 会返回 `0`）；
	+ 如果读端关闭，往管道写数据，内核发送 `SIGPIPE` 信号。

![有名管道的用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702172701.png)

#### 五种 I/O 模型
+ 五种 `I/O` 模型:

![五种 I/O 模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702173022.png)

#### 多路 I/O 复用
##### select 系统调用
+ `select` 系统调用用法：
	+ 作用：将多个阻塞点变成一个阻塞点！

![select 系统调用用法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702173423.png)

+ `select` 系统调用参数：

![select 系统调用参数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702173648.png)

+ `select` 系统调用详细参数和使用方法：

![select 系统调用详细参数和使用方法](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702173850.png)

+ `select` 系统调用工作原理：

![select 系统调用工作原理](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702174025.png)

+ 使用 `select` 系统调用实现点对点聊天系统：
	+ 需要注意的点：如果一方将管道写端关闭了，`read` 系统调用会一直读，但返回值是 0，即读到 0 个 Bytes。
+ 用户 1 代码：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>

#define MAXLINE 1024

int main(int argc, char* argv[])
{
    int fd1 = open("./pipe1", O_WRONLY);
    if(fd1 == -1){
        error(1, errno, "open pipe1");
    }
    int fd2 = open("./pipe2", O_RDONLY);
    if(fd2 == -1){
        error(1, errno, "open pipe2");
    }
    printf("Established!\n");

    char recvline[MAXLINE];
    char sendline[MAXLINE];

    fd_set mainfds; //局部变量，1024的位图
    FD_ZERO(&mainfds); //将所有位置都置为0
    // 将感兴趣的文件描述符加入到mainfds中
    FD_SET(STDIN_FILENO, &mainfds);
    int max_fds = STDIN_FILENO;
    FD_SET(fd2, &mainfds);
    if(fd2 > max_fds){
        max_fds = fd2;
    }

    for(;;){
        fd_set readfds = mainfds; // 结构体的复制
        int events = select(max_fds+1, &readfds, NULL, NULL, NULL);
        switch(events){
        case -1: 
            error(1, errno, "select");
        case 0:
            //超时
            printf("TIMEOUT");
            continue;
        default:
            // STDIN_FILENO 就绪
            if(FD_ISSET(STDIN_FILENO, &readfds)){
                // 一定不会阻塞
                fgets(sendline, MAXLINE, stdin);
                write(fd1, sendline, strlen(sendline)+1); // +1 for '\0'
            }
            //fd2 就绪
            if(FD_ISSET(fd2, &readfds)){
                // 一定不会阻塞
                int nbytes = read(fd2, recvline, MAXLINE);
                if(nbytes == 0) // 管道写端关闭了
                    goto end;
                else if(nbytes == -1)
                    error(1, errno, "read pipe2");
                printf("From p2: %s", recvline);
            }
        }
    }
    end:
        close(fd1);
        close(fd2);
    return 0;
}
```

+ 用户 2 代码：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>

#define MAXLINE 1024

int main(int argc, char* argv[])
{
    int fd1 = open("./pipe1", O_RDONLY);
    if(fd1 == -1){
        error(1, errno, "open pipe1");
    }
    int fd2 = open("./pipe2", O_WRONLY);
    if(fd2 == -1){
        error(1, errno, "open pipe2");
    }
    printf("Established!\n");

    char recvline[MAXLINE];
    char sendline[MAXLINE];

    fd_set mainfds; //局部变量，1024的位图
    FD_ZERO(&mainfds); //将所有位置都置为0
    // 将感兴趣的文件描述符加入到mainfds中
    FD_SET(STDIN_FILENO, &mainfds);
    int max_fds = STDIN_FILENO;
    FD_SET(fd1, &mainfds);
    if(fd1 > max_fds){
        max_fds = fd2;
    }

    for(;;){
        fd_set readfds = mainfds; // 结构体的复制
        int events = select(max_fds+1, &readfds, NULL, NULL, NULL);
        switch(events){
        case -1: 
            error(1, errno, "select");
        case 0:
            //超时
            printf("TIMEOUT");
            continue;
        default:
            // STDIN_FILENO 就绪
            if(FD_ISSET(STDIN_FILENO, &readfds)){
                // 一定不会阻塞
                fgets(sendline, MAXLINE, stdin);
                write(fd2, sendline, strlen(sendline)+1); // +1 for '\0'
            }
            //fd1 就绪
            if(FD_ISSET(fd1, &readfds)){
                // 一定不会阻塞
                int nbytes = read(fd1, recvline, MAXLINE);
                if(nbytes == 0) // 管道写端关闭了
                    goto end;
                else if(nbytes == -1)
                    error(1, errno, "read pipe1");
                printf("From p1: %s", recvline);
            }
        }
    }
    end:
        close(fd1);
        close(fd2);
    return 0;
}
```

+ `select` 系统调用的缺陷：
	+ 监听的文件描述符的个数是有限的；
	+ 当 `select` 系统调用返回时，还需要遍历 `fd_set`，找到就绪的文件描述符。

### 信号
#### 基本概念
+ 信号是内核通知应用程序外部事件的一种机制。

![事件通知机制](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702202437.png)

+ 事件源：

![事件源](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702202616.png)

+ 内核会感知事件，并给进程发送相应的信号。

![内核会感知事件，并给进程发送相应的信号](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702202714.png)

+ 信号的处理方式：

![信号的处理方式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702202807.png)

+ 标准信号 1：

![标准信号 1](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702202912.png)

+ 标准信号 2：

![标准信号 2](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702202957.png)

#### 信号的执行流程
+ 注册信号处理函数：

![注册信号处理函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702210529.png)

+ 示例代码：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>

void handler(int signo){
    switch(signo){
        case SIGINT:
            printf("Caught SIGINT\n");
            break;
        case SIGTSTP:
            printf("Caught SIGTSTP\n");
            break;
        default:
            printf("Unknown %d\n", signo);
    }
}

int main(int argc, char* argv[])
{
    //注册信号处理函数（捕获信号）
    void (*oldhandler)(int);  // 声明一个函数指针
    oldhandler = signal(SIGINT, handler);
    if(oldhandler == SIG_ERR){
        error(1, errno, "signal %d", SIGINT);
    }
    oldhandler = signal(SIGTSTP, handler);
    if(oldhandler == SIG_ERR){
        error(1, errno, "signal %d", SIGTSTP);
    }
    
    for(;;){
    }
    
    return 0;
}
```

+ 信号的处理流程：
	+ 注册函数是跑在用户态的。

![信号的处理流程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702210656.png)

+ 信号的特点：
	+ 不稳定；
	+ 异步的（什么时候收到信号是不确定的，收到信号后，会立刻马上执行信号处理函数）；
	+ 不同心态关于信号的语义也不一样。

#### 注册信号处理函数
+ 注册信号处理函数：

![注册信号处理函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702213625.png)

+ 示例代码 1：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>

int main(int argc, char* argv[])
{
    printf("pid = %d\n", getpid());

    // 忽略 SIGINT 信号
    void (*oldhandler)(int);  // 声明一个函数指针
    oldhandler = signal(SIGINT, SIG_IGN);
    if(oldhandler == SIG_ERR){
        error(1, errno, "signal %d", SIGINT);
    }
    sleep(5);

    printf("Wake up\n");

    signal(SIGINT, SIG_DFL);

    for(;;){
    }

    return 0;
}
```

+ 示例代码 2：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>

void handler(int signo){
    switch(signo){
        case SIGKILL:
            printf("Caught SIGKILL\n");
            break;
        case SIGSTOP:
            printf("Caught SIGSTOP\n");
            break;
        default:
            printf("Unknown %d\n", signo);
    }
}

int main(int argc, char* argv[])
{
    //注册信号处理函数（捕获信号）
    void (*oldhandler)(int);  // 声明一个函数指针
    oldhandler = signal(SIGKILL, handler);
    if(oldhandler == SIG_ERR){
        error(0, errno, "signal SIGKILL");
    }
    oldhandler = signal(SIGSTOP, handler);
    if(oldhandler == SIG_ERR){
        error(0, errno, "signal SIGSTOP");
    }

    for(;;){
    }

    return 0;
}
```

#### 发送信号
+ `kill` 命令：

![kill 命令](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702215628.png)

+ `pid` 相关权限和返回值：

![pid 相关权限和返回值](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250702215753.png)

+ 示例代码：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>

int main(int argc, char* argv[])
{
    // ./test_kill signo pid ...
    if(argc < 3){
        error(1, 0, "Usage: %s signo pid ...", argv[0]);
    }

    int signo;
    sscanf(argv[1], "%d", &signo);

    for(int i=2; i<argc; i++){
        pid_t pid;
        sscanf(argv[i], "%d", &pid);
        if(kill(pid, signo) == -1){
            error(0, errno, "kill(%d %d)", pid, signo);
        }
    }
    return 0;
}
```

#### 线程
+ 线程：一条执行的流程。

![线程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250703174335.png)

+ 引入线程：
	+ 进程是资源分配的最小单位；
	+ 线程是调度的最小单位；
	+ 线程共享进程的所有资源。
+ 为什么要引入线程？
	+ 进程之间的切换（`CPU` 的高速缓存，`TLB` 失效），开销大。用进程中的线程之间切换，开销较小。
	+ 进程之间通信，需要打破隔离避障，线程之间的通信，开销较小。
	+ 进程的创建和销毁比较耗时，而线程的创建和销毁要轻量很多。
##### 线程的基本操作和创建线程
+ 获取线程的标识：

![获取线程的标识](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250703185042.png)

+ 创建线程：
	+ `pthread` 库设计原则：
		+ 返回值是 `int` 类型，表示调用成功或失败。
			+ 成功：`0`；
			+ 失败：错误码，不会设置 `errno`。
		+ `thread_t` ：返回时，存放创建线程 `ID`。
		+ `attr_t` ：线程属性，一般填 `NULL`，表示用默认属性。
		+ `start_routine` ：线程的入口函数。
		+ `arg`：线程的入口函数的参数。

![创建线程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250703185116.png)

+ 示例代码：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>
#include <pthread.h>

void print_ids(const char* prefix){
    printf("%s: ", prefix);
    printf("pid = %d, ppid = %d ", getpid(), getppid());
    printf("tid = %lu\n", pthread_self());
}

// 子线程的执行流程
void* start_routine(void* args){
    print_ids("child_thread");
    return NULL;
}

int main(int argc, char* argv[])
{
    // 主线程
    print_ids("main_thread");

    pthread_t tid;
    int err = pthread_create(&tid, NULL, start_routine, NULL);
    if(err){
        error(1, err, "pthread_create");
    }

    printf("main: new_thread = %lu\n", tid);

    // 注意事项：当主线程终止时，整个进程就终止了
    sleep(2);
    return 0;
}
```

+ 运行结果：

![运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250703185815.png)

+ 向线程的入口函数传递参数（`void*`）代码：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>
#include <pthread.h>

typedef struct {
    int id;
    char name[25];
    char gender;
} Student;

void print_ids(const char* prefix){
    printf("%s: ", prefix);
    printf("pid = %d, ppid = %d ", getpid(), getppid());
    printf("tid = %lu\n", pthread_self());
}

// 子线程的执行流程
void* start_routine(void* args){
    Student* p = (Student*) args;
    // 在子线程中访问主线程栈里面的数据
    printf("%d %s %c\n",
        p->id,
        p->name,
        p->gender);
    print_ids("child_thread");
    return NULL;
}

int main(int argc, char* argv[])
{
    // 主线程
    print_ids("main_thread");

    Student s = {1, "yugin", 'm'};

    pthread_t tid;
    int err = pthread_create(&tid, NULL, start_routine, (void*)&s);
    if(err){
        error(1, err, "pthread_create");
    }

    printf("main: new_thread = %lu\n", tid);

    // 注意事项：当主线程终止时，整个进程就终止了
    sleep(2);
    return 0;
}
```

+ 运行结果：

![向线程的入口函数传递参数运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250705174257.png)

##### 终止线程
+ 进程的终止：
	+ 从 `main` 返回
	+ `exit()`
	+ 收到信号
+ 线程的终止：
	+ 从 `start_routine` 返回
	+ `pthread_exit()`
	+ `pthread_cancel()`
###### 线程显式终止函数
+ `pthread_exit()` 线程显式终止函数：

![线程显式终止函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250705211436.png)

+ `pthread_join()` 等待一个线程的结束：

![等待一个线程的结束](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250705211644.png)

+ 示例代码（`1` 到 `100` 求和，分两个线程执行）：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>
#include <pthread.h>

typedef struct {
    int* arr;
    int left;
    int right;
} Section;

// 子线程的执行流程
void* start_routine(void* args){
    Section* sec = (Section*) args;
    // 在子线程中访问主线程栈里面的数据
    int sum = 0;
    for(int i=sec->left; i<=sec->right; i++){
        sum += sec->arr[i];
    }
    // 子线程两种返回方式（返回即终止）
    pthread_exit((void*)sum);
    //return (void*)sum;
    return NULL;
}

int main(int argc, char* argv[])
{
    // 主线程
    int arr[100];
    for(int i=1; i<=100; i++){
        arr[i-1] = i;
    }

    pthread_t tid1, tid2;

    Section sec1 = {arr, 0, 49};
    int err = pthread_create(&tid1, NULL, start_routine, (void*)&sec1);
    if(err){
        error(1, err, "pthread_create");
    }

    Section sec2 = {arr, 50, 99};
    err = pthread_create(&tid2, NULL, start_routine, (void*)&sec2);
    if(err){
        error(1, err, "pthread_create");
    }

    // 主线程：等待子线程结束，并接收返回值
    int result1;
    err = pthread_join(tid1, (void**)&result1);
    if(err){
        error(1, err, "pthread_join %lu\n", tid1);
    }
    int result2;
    err = pthread_join(tid2, (void**)&result2);
    if(err){
        error(1, err, "pthread_join %lu\n", tid2);
    }

    printf("main: sum = %d\n", result1+result2);

    // 注意事项：当主线程终止时，整个进程就终止了
    return 0;
}
```

+ 运行结果：

![运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250705211817.png)

+ 注意：不能返回指向该线程栈上数据的指针，因为当线程退出时，该线程的栈会销毁。

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>
#include <pthread.h>

typedef struct {
    int id;
    char name[25];
    char gender;
} Student;

// 子线程的执行流程
void* start_routine(void* args){
    // 不能返回指向该线程栈上数据的指针，因为当线程退出时，该线程的栈会销毁
    //Student* p = (Student*) args;
    Student* p = (Student*) malloc(sizeof(Student));
    // 在子线程中访问主线程栈里面的数据
    p->id = 1;
    strcpy(p->name, "yugin");
    p->gender = 'm';
    pthread_exit((void**) p);
    return NULL;
}

int main(int argc, char* argv[])
{
    // 主线程
    pthread_t tid;
    int err = pthread_create(&tid, NULL, start_routine, NULL);
    if(err){
        error(1, err, "pthread_create");
    }
    Student* result;
    pthread_join(tid, (void**) &result);
    printf("%d %s %c\n",
        result->id,
        result->name,
        result->gender);
    printf("main: child_thread = %lu\n", tid);

    // 注意事项：当主线程终止时，整个进程就终止了
    return 0;
}
```

###### 游离线程
+ `pthread_detach()` 用于将线程设置为游离状态的函数，使线程在终止时自动释放资源：

![将线程设置为游离状态](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250705213222.png)

+ 示例代码：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>
#include <pthread.h>

typedef struct {
    int id;
    char name[25];
    char gender;
} Student;

// 子线程的执行流程
void* start_routine(void* args){
    // 不能返回指向该线程栈上数据的指针，因为当线程退出时，该线程的栈会销毁
    //Student* p = (Student*) args;
    Student* p = (Student*) malloc(sizeof(Student));
    // 在子线程中访问主线程栈里面的数据
    p->id = 1;
    strcpy(p->name, "yugin");
    p->gender = 'm';
    pthread_exit((void**) p);
    return NULL;
}

int main(int argc, char* argv[])
{
    // 主线程
    pthread_t tid;
    int err = pthread_create(&tid, NULL, start_routine, NULL);
    if(err){
        error(1, err, "pthread_create");
    }
    
    // 当线程主动调用 pthread_detach
    err = pthread_detach(tid);
    if(err){
        error(1, err, "pthread_detach");
    }

    Student* result;
    pthread_join(tid, (void**) &result);
    printf("%d %s %c\n",
        result->id,
        result->name,
        result->gender);

    // 注意事项：当主线程终止时，整个进程就终止了
    return 0;
}
```

+ 主线程无法再获取游离线程的返回结果：

![主线程无法再获取游离线程的返回结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250705213915.png)

###### 线程清理函数
+ 线程清理函数：用于在线程退出时执行预定义的清理操作。
	+ `execute` 参数：
		+ `0`：栈中的 `args` 参数出栈但不执行 `cleanup` 线程清理函数。
		+ 非 `0`：栈中的 `args` 参数出栈并执行 `cleanup` 线程清理函数。

![线程清理函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706133555.png)

+ 与进程之间的对比：

![与进程之间的对比](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706133657.png)

+ 示例代码：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>
#include <pthread.h>

void cleanup(void* args){
    char* msg = (char*) args;
    printf("cleanup: %s\n", msg);
}

// 子线程的执行流程
void* start_routine(void* args){
    // 注册线程清理函数
    pthread_cleanup_push(cleanup, "first");
    pthread_cleanup_push(cleanup, "second");
    pthread_cleanup_push(cleanup, "third");

    pthread_cleanup_pop(1); // 执行线程清理函数，打印third，执行顺序与注册顺序相反
    pthread_cleanup_pop(0); // 

    sleep(2);
    printf("child thread is going to return!\n");

    pthread_exit(NULL);
    // 后面的代码肯定不会执行
    pthread_cleanup_pop(0);

    return NULL;
}

int main(int argc, char* argv[])
{
    // 主线程
    pthread_t tid;
    int err = pthread_create(&tid, NULL, start_routine, NULL);
    if(err){
        error(1, err, "pthread_create");
    }
    // 主线程等待子线程
    err = pthread_join(tid, NULL);
    if(err){
        error(1, err, "pthread_join");
    }
    // 注意事项：当主线程终止时，整个进程就终止了
    return 0;
}
```

+ 运行结果：

![运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706141101.png)

+ 注意事项：
	+ 从 `start_routine` 返回，不会执行线程清理函数。
	+ `pthread_cleanup_push` 和 `pthread_cleanup_pop` 必须成对出现。
		+ 必须成对出现的原因在于源码中采用了宏函数的特性。

![宏函数的特性](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706141824.png)

##### 线程的同步
+ 原子性：CPU 指令是原子性的。
+ 相关术语：
	+ 竞态条件（`race condition`）：
		+ 多个执行流程
		+ 共享资源
		+ 程序的结果（状态取决于执行流程调度的情况）
	+ 异步和同步：
		+ 异步：任何调度情况都可以出现、两个执行流程不做任何交流。
		+ 同步：让一些调度不可能出现（同步会有一些开销）。
			+ 互斥锁、条件变量。
	+ 并发和并行：
		+ 并发：一种现象，在一个时间段中，执行流程可以交替执行。
		+ 并行：一种技术，同一时刻，可以执行多个执行流程（并行是并发的一种）。
+ 线程的同步：
	+ 互斥地访问资源
	+ 等待某个条件成立

![线程的同步](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706155605.png)

+ 互斥锁函数：

![互斥锁函数](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706160444.png)

+ 互斥锁的使用代码：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>
#include <pthread.h>

// 具有以下状态：未初始化、初始化、上锁、没上锁、销毁..
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER; // 静态初始化，默认属性

// 子线程的执行流程
void* start_routine(void* args){
    long* value = (long*) args;
    for(int i=0; i<10000000; i++){
        pthread_mutex_lock(&mutex);
        (*value)++; // 临界区，对共享资源操作
        pthread_mutex_unlock(&mutex);
    }
    return NULL;
}

int main(int argc, char* argv[])
{
    // 主线程
    long* value = (long*)calloc(1, sizeof(long)); // *value = 0

    pthread_t tid1, tid2;

    int err = pthread_create(&tid1, NULL, start_routine, value);
    if(err){
        error(1, err, "pthread_create");
    }

    err = pthread_create(&tid2, NULL, start_routine, value);
    if(err){
        error(1, err, "pthread_create");
    }

    // 主线程：等待子线程结束，并接收返回值
    err = pthread_join(tid1, NULL);
    if(err){
        error(1, err, "pthread_join %lu\n", tid1);
    }
    err = pthread_join(tid2, NULL);
    if(err){
        error(1, err, "pthread_join %lu\n", tid2);
    }

    // 销毁互斥锁
    pthread_mutex_destroy(&mutex);

    printf("value = %ld\n", *value);

    // 注意事项：当主线程终止时，整个进程就终止了
    return 0;
}
```

+ 运行结果：

![运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706162054.png)

+ 银行例子（细粒度锁）：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>
#include <pthread.h>

typedef struct{
    int id;
    int balance;
    pthread_mutex_t mutex;
} Account;

// 具有以下状态：未初始化、初始化、上锁、没上锁、销毁..
Account acct1 = {1, 100, PTHREAD_MUTEX_INITIALIZER};

// 扣款函数
int withdraw(Account* acct, int money){
    pthread_mutex_lock(&acct->mutex);
    // 临界区，对共享资源操作
    if(acct->balance < money){
        return 0;
    }
    sleep(1);// 让某种调度出现的概率最大化
    acct->balance -= money;
    pthread_mutex_unlock(&acct->mutex);
    printf("%lu: withdraw %d\n",pthread_self(), money);
    return money;
}

// 子线程的执行流程
void* start_routine(void* args){
    withdraw(&acct1, 100);
    return NULL;
}

int main(int argc, char* argv[])
{
    // 主线程
    pthread_t tid1, tid2;
    int err = pthread_create(&tid1, NULL, start_routine, NULL);
    if(err){
        error(1, err, "pthread_create");
    }
    err = pthread_create(&tid2, NULL, start_routine, NULL);
    if(err){
        error(1, err, "pthread_create");
    }

    // 主线程：等待子线程结束
    err = pthread_join(tid1, NULL);
    if(err){
        error(1, err, "pthread_join %lu\n", tid1);
    }
    err = pthread_join(tid2, NULL);
    if(err){
        error(1, err, "pthread_join %lu\n", tid2);
    }

    // 销毁互斥锁
    pthread_mutex_destroy(&acct1.mutex);
    // 打印账户余额
    printf("balance = %d\n", acct1.balance);

    // 注意事项：当主线程终止时，整个进程就终止了
    return 0;
}
```

+ 运行结果：

![运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706163256.png)

##### 死锁
+ 以下四个条件同时成立会造成死锁：
	+ 互斥
	+ 持有并等待
	+ 不能抢占
	+ 循环等待

![造成死锁的四个条件](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706203309.png)

+ 死锁代码例子：

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>
#include <pthread.h>

typedef struct{
    int id;
    char name[25];
    int balance;
    // 细粒度锁
    pthread_mutex_t mutex;
} Account;

// 具有以下状态：未初始化、初始化、上锁、没上锁、销毁..
Account acct1 = {1, "yugin", 10000, PTHREAD_MUTEX_INITIALIZER};
Account acct2 = {2, "jokic", 100, PTHREAD_MUTEX_INITIALIZER};

// 转账函数，A转账给B
int transfer(Account* acctA, Account* acctB, int money){
    pthread_mutex_lock(&acctA->mutex);
    sleep(1);// 让某种调度出现的概率最大化
    pthread_mutex_lock(&acctB->mutex);
    // 临界区，对共享资源操作
    if(acctA->balance < money){
        pthread_mutex_unlock(&acctA->mutex);
        pthread_mutex_unlock(&acctB->mutex);
        return 0;
    }
    
    acctA->balance -= money;
    acctB->balance += money;

    pthread_mutex_unlock(&acctA->mutex);
    pthread_mutex_unlock(&acctB->mutex);
    
    return money;
}

// 子线程的执行流程
void* start_routine1(void* args){
    int money = (int) args;
    int ret = transfer(&acct1, &acct2, money);
    printf("%s -> %s: %d\n", acct1.name, acct2.name, ret);
    return NULL;
}
void* start_routine2(void* args){
    int money = (int) args;
    int ret = transfer(&acct2, &acct1, money);
    printf("%s -> %s: %d\n", acct2.name, acct1.name, ret);
    return NULL;
}

int main(int argc, char* argv[])
{
    // 主线程
    pthread_t tid1, tid2;
    int err = pthread_create(&tid1, NULL, start_routine1, (void*)900);
    if(err){
        error(1, err, "pthread_create");
    }
    err = pthread_create(&tid2, NULL, start_routine2, (void*)100);
    if(err){
        error(1, err, "pthread_create");
    }

    // 主线程：等待子线程结束
    err = pthread_join(tid1, NULL);
    if(err){
        error(1, err, "pthread_join %lu\n", tid1);
    }
    err = pthread_join(tid2, NULL);
    if(err){
        error(1, err, "pthread_join %lu\n", tid2);
    }

    // 销毁互斥锁
    pthread_mutex_destroy(&acct1.mutex);
    pthread_mutex_destroy(&acct2.mutex);
    // 打印账户余额
    printf("balance = %d\n", acct1.balance);
    printf("balance = %d\n", acct2.balance);

    // 注意事项：当主线程终止时，整个进程就终止了
    return 0;
}
```

+ 三个线程都处于阻塞状态：

![三个线程都处于阻塞状态](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706203523.png)

+ 解决方案 1：破坏循环等待
	+ 必须按照固定的顺序，依次获取锁：

```c
// 破坏循环等待
if(acctA->id < acctB->id){
    pthread_mutex_lock(&acctA->mutex);
    sleep(1);// 让某种调度出现的概率最大化
    pthread_mutex_lock(&acctB->mutex);
}
else{
    pthread_mutex_lock(&acctB->mutex);
    sleep(1);// 让某种调度出现的概率最大化
    pthread_mutex_lock(&acctA->mutex);
}
```

![破坏循环等待](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706204040.png)

+ 运行结果：

![运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706204949.png)

+ 解决方案 2：不能抢占

![不能抢占](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706204827.png)

```c
// 不能抢占
start:
    pthread_mutex_lock(&acctA->mutex);
    sleep(1);
    int err = pthread_mutex_trylock(&acctB->mutex);
    if(err){
        // 主动释放获取的锁
        pthread_mutex_unlock(&acctA->mutex);
        int seconds = rand() % 2;
        sleep(seconds);
        goto start;
    }
```

+ 解决方案 3：持有并等待

```c
//持有并等待
pthread_mutex_lock(&protection);
pthread_mutex_lock(&acctA->mutex);
sleep(1);
pthread_mutex_lock(&acctB->mutex);
pthread_mutex_unlock(&protection);
```

+ 解决方案 4：解决互斥，实现原子性

![解决互斥，实现原子性](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250706205923.png)

##### 等待条件成立
+ 条件变量（`pthread_cond_t`）：
	+ 条件变量只是提供了一个等待、唤醒机制；
	+ 至于条件何时成立，何时不成立，取决于业务。
+ 1、初始化：

![条件变量初始化](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250707210657.png)

+ 2、当条件不成立，等待：

![当条件不成立，等待](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250707210847.png)

+ `pthread_cond_wait()` 返回时，条件一定成立吗？
	+ 不一定！

![`pthread_cond_wait()` 返回时，条件不一定成立](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250707211013.png)

+ 3、当条件成立时，唤醒等待的线程：

![当条件成立时，唤醒等待的线程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250707211149.png)

+ 4、销毁

![销毁](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250707211215.png)

##### 生产者消费者模型
+ 生产者消费者模型：
	+ 阻塞队列：
		+ 当队列满时，如果线程往阻塞队列中添加东西，线程会陷入阻塞；
		+ 当队列空时，如果线程往阻塞队列中取出东西，线程会陷入阻塞；
	+ 生产者，产生任务：
		+ 如果队列满了，生产者陷入阻塞，等待队列不满（`not_full`）;
		+ 如果队列不满，将任务添加到阻塞队列，队列非空，唤醒消费者（`not_empty`）;
	+ 消费者，完成任务：
		+ 如果队列空了，消费者陷入阻塞，等待队列非空（`not_empty`）;
		+ 如果队列非空，从阻塞队列中获取任务，队列不满，唤醒生产者（`not_full`）;

![生产者消费者模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250707211858.png)

##### 阻塞队列
+ 阻塞队列（有界队列）：

![阻塞队列（有界队列）](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250707211938.png)

+ `blockQ.h` 文件：

```c
#ifndef __WD_BLOCKQ_H
#define __WD_BLOCKQ_H

#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <error.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>
#include <signal.h>
#include <pthread.h>
#include <stdbool.h>

#define N 1024

typedef int E;

// 循环数组实现队列
typedef struct {
    E elements[N];
    int front;
    int rear;
    int size;

    pthread_mutex_t mutex;
    pthread_cond_t not_empty; // 非空
    pthread_cond_t not_full; // 不满
} BlockQ;

// API 
BlockQ* blockq_create(void);
void blockq_destroy(BlockQ* q);

void blockq_push(BlockQ* q, E val);
E blockq_pop(BlockQ* q);
E blockq_peek(BlockQ* q);
bool blockq_empty(BlockQ* q);
bool blockq_full(BlockQ* q);

#endif
```

+ `blockQ.c` 文件：

```c
#include "blockQ.h"

// 创建空的阻塞队列
BlockQ* blockq_create(void){
    BlockQ* q = (BlockQ*) calloc(1, sizeof(BlockQ));

    pthread_mutex_init(&q->mutex, NULL);
    pthread_cond_init(&q->not_empty, NULL);
    pthread_cond_init(&q->not_full, NULL);

    return q;
}

void blockq_destroy(BlockQ* q){
    pthread_mutex_destroy(&q->mutex);
    pthread_cond_destroy(&q->not_empty);
    pthread_cond_destroy(&q->not_full);

    free(q);
}

void blockq_push(BlockQ* q, E val){
    // 获取锁
    pthread_mutex_lock(&q->mutex);
    // 注意事项：一定要写成while，当线程返回时再次检查队列未满
    while(q->size == N){
        // 1. 释放q->mutex
        // 2. 该线程陷入阻塞状态
        // 3. 当pthread_cond_wait返回时，一定再一次获取了q->mutex
        pthread_cond_wait(&q->not_full, &q->mutex);
    } // a. 获取了q->mutex; b. q->size != N

    q->elements[q->rear] = val;
    q->rear = (q->rear + 1) % N;
    q->size++;
    // 此时not_empty的条件成立，唤醒not_empty条件的线程
    pthread_cond_signal(&q->not_empty);
    // 释放锁
    pthread_mutex_unlock(&q->mutex);
}

E blockq_pop(BlockQ* q){
    // 获取锁
    pthread_mutex_lock(&q->mutex);
    // 注意事项：一定要写成while，当线程返回时再次检查队列未满
    while(q->size == 0){
        // 1. 释放q->mutex
        // 2. 该线程陷入阻塞状态
        // 3. 当pthread_cond_wait返回时，一定再一次获取了q->mutex
        pthread_cond_wait(&q->not_empty, &q->mutex);
    } // a. 获取了q->mutex; b. q->size != 0

    E ret = q->elements[q->front];
    q->front = (q->front + 1) % N;
    q->size++;
    // 此时not_full的条件成立，唤醒not_full条件的线程
    pthread_cond_signal(&q->not_full);
    // 释放锁
    pthread_mutex_unlock(&q->mutex);

    return ret;
}

bool blockq_full(BlockQ* q){
    pthread_mutex_lock(&q->mutex);
    int size = q->size;
    pthread_mutex_unlock(&q->mutex);
    return size == N;
}

bool blockq_empty(BlockQ* q){
    pthread_mutex_lock(&q->mutex);
    int size = q->size;
    pthread_mutex_unlock(&q->mutex);
    return size == 0;
}
```

##### 实现生产者消费者模型
+ 通过线程池（能够避免频繁地创建和销毁线程）实现生产者消费者模型：

![实现生产者消费者模型](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250707212319.png)


+ 应用程序应该包含多少个线程要通过两个方面考虑：
	+ CPU 的核数
	+ 任务的负载
		+ `I/O` 密集型任务
		+ 计算密集型任务

![应用程序应该包含多少个线程要通过两个方面考虑](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250707212345.png)

+ 代码示例：

```c
#include "blockQ.h"

typedef struct {
    pthread_t* threads; // 线程数组
    int nums; // 线程数目
    BlockQ* q;
} ThreadPool;

void* start_routine(void* args){
    ThreadPool* pool = (ThreadPool*) args;
    pthread_t tid = pthread_self();
    
    // 从阻塞队列中获取任务
    for(;;){
        E task_id = blockq_pop(pool->q);
        if(task_id == -1){
            pthread_exit(NULL);
        }
        printf("%lx: execute task %d\n", tid, task_id);
        sleep(1); // 模拟执行任务
        printf("%lx: task %d done!\n", tid, task_id);
    }
    return NULL;
}

ThreadPool* threadpool_create(int n){
    ThreadPool* pool = (ThreadPool*) malloc(sizeof(ThreadPool));
    pool->threads = (pthread_t*) malloc(n * sizeof(ThreadPool));
    pool->q = blockq_create();
    // 创建线程
    for(int i=0; i<n; i++){
        pthread_create(pool->threads+i, NULL, start_routine, (void*)pool);
    }
    return pool;
}

void threadpool_destroy(ThreadPool* pool){
    blockq_destroy(pool->q);
    free(pool->threads);
    free(pool);
}

int main(int argc, char* argv[]){
    // 1.创建线程池，并初始化
    ThreadPool* pool = threadpool_create(8);

    // 2.主线程生产任务
    for(int i=0; i<100; i++){
        blockq_push(pool->q, i+1);
    }
    sleep(13);

    // 3. 退出线程池
    for(int i=0; i<pool->nums; i++){
        blockq_push(pool->q, -1); // -1表示退出任务
    }
    for(int i=0; i<pool->nums; i++){
        pthread_join(pool->threads[i], NULL); // -1表示退出任务
    }

    // 4.销毁线程池
    threadpool_destroy(pool);

    return 0;
}
```

+ 生产者消费者模型运行结果：

![生产者消费者模型运行结果](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250707212446.png)
