---
title: 【QT 学习笔记01】从 Hello World 开始
date: 2022-04-26 17:25:34
updated: 2022-05-01 15:59:52
description: 又来开个新坑
tags: 
    - 博文
    - QT
    - 笔记
    - 知识
categories: 叶子的小知识
comment: true
keywords: QT,学习,笔记
---

<img src="/images/QT-learning-note-01/iloveprogramming.jpg" width="450" alt="iloveprogramming" />

> 本节以经典的 C++ Hello World 程序介绍一下使用 MinGW （Linux 上是 GNU 工具集）环境下编译程序的过程，并示范一下简单 Makefile 的使用。
>
> ——奇先生

<!-- more -->

由于导师的项目需要，并且负责项目平台界面的师兄要毕业了，所以导师安排我接手那位师兄的工作，以后继续负责开发界面。我这就找了几篇互联网上评价比较好的系列教程，争取能快速的上手，赶在师兄毕业前把工作接过来。由于知识点在不同的教程中比较散，我向来也是不相信自己记忆力的，就一边学、一边将其中的要点记录下来，作为笔记供我速查之用。如果您也想要学习 QT 的话，建议还是通过参考资料里的链接，阅读原始的教程，可以参看最后的[参考资料](#参考资料)。

我目前是在 `Windows 10` 上做的开发，我的 QT 版本为 `5.14.2`，有关 QT 的下载与系统环境配置，请参看 [Qt 在Windows 下安装](https://qtguide.ustclug.org/ch01-03.htm) 与 [Qt 在 Linux 下安装](https://mskclover.com/404.html/)，我在此就不再班门弄斧了。

众所周知，每一个初学者都会从编写 `helloworld` 开始，我也不例外，那就让我们开始吧（

## Hello MinGW

### 简单的 helloworld.cpp

首先在示例路径 `D:\QtProjects\ch02\helloworld` 中新建一个 `helloworld.C++`，代码如下：

```c++
//helloworld.cpp

#include <iostream>
using namespace std;

//有关下列 main 的定义或可参见：
//https://www.ibm.com/docs/en/i/7.1?topic=functions-main-function
int main(int argc, char **argv)
{
  cout << "Hello world!" << endl;
  return 0;
}
```

接下来打开 MinGW 命令行环境，但或许是我的版本与作者不一样吧，我打开的方式为：`开始菜单`  --> ` Qt 5.14.2` --> `Qt 5.14.2(MinGW 7.3.0 64-bit)`。（注：后文的所有“命令行”均指 MinGW 命令行环境）

然后，向命令行中输入指令进入目标文件夹：

```c++
//注：因为我 QT 安装在 D 盘的，而相关的学习文件目前还放在桌面上的，所以得切换磁盘

D:\QT\5.14.2\mingw73_64>cd /d C:\Users\***\Desktop\QT\QT_projects\ch02\helloworld
//将磁盘从 D 盘切换至 C 盘，并改变工作目录至目标文件夹
```

然后在工作目录下输入如下的指令：

```c++
//从这里开始就省略目录了

>g++ helloworld.cpp -o helloworld
//使用 g++ 编译 helloworld.cpp，-o 代表输出文件名为 helloworld（默认扩展名为 .exe）

>helloworld
//在此目录下运行生成的 exe 程序
```

但如果直接双击该 `.exe` 文件，会显示缺少 dll 库，无法运行。这是因为它现在还是一个 56KB 的“动态链接程序”，需要相应的 dll 程序才能运行，而在 MinGW 命令行中已经配好了环境变量 PATH，所以 MinGW 的环境里能找到该 dll 程序，而操作系统（Windows）的 PATH 环境变量中是找不到该 dll 的。解决方法有两种：将报错的 dll 库复制到这个文件夹里，或者使用如下的“静态编译命令”：

```
>g++ helloworld.cpp -static -o hellostatic 
//静态编译命令 -static
```

在编译为“静态链接程序”后，体积一下子膨胀至 2337KB，现在可以直接双击运行了。

**MinGW 编译程序的过程大概是，先用 g++ 编译器生成目标文件 `*.o`，然后 g++ 自动调用 ld 工具链接 `*.o` 与库文件，完成后就生成了 `.exe` 文件。** 

g++ 在默认情况下生成的都是 Release 版的可执行程序，若希望在可执行程序里加入 Debug 调试信息以供 gdb 调试，则需要在 g++ 编译时加入 -g 选项：

```c++
>g++ helloworld.cpp -g -static -o hellodebug 
//debug 编译 -g
```

![helloworld-cmd](/images/QT-learning-note-01/helloworld-cmd.jpg)_命令行截图示意_

![helloworld-list](/images/QT-learning-note-01/helloworld-list.jpg)_文件夹截图示意_

### 稍微不简单的 Hello Rect

在下一个教程里，作者应该是想要我们做一个简单的多工程文件程序，现在在示例路径 `D:\QtProjects\ch02\helloworld` 中新建下列文件：

```c++
//rect.h

class Rect
{
    private:
        double m_dblWidth;
        double m_dblHeight;
    
    public:
        Rect(double dblWidth, double dblHeight);
        double GetWidth(){ return m_dblWidth; }
        double GetHeight(){ return m_dblHeight; }

        //Functions
        double CalcDiagonal();  //计算对角线
        double CalcArea();      //计算面积
};
```

```c++
//rect.cpp

#include <cmath>
#include "rect.h"
using namespace std;

Rect::Rect(double dblWidth, double dblHeight)
{
    m_dblWidth = abs(dblWidth);
    m_dblHeight = abs(dblHeight);
}

//定义成员函数
//计算对角线
double Rect::CalcDiagonal()
{
    return sqrt(m_dblWidth*m_dblWidth + m_dblHeight*m_dblHeight);
    //sqrt() 开平方根
}

//计算面积
double Rect::CalcArea()
{
    return m_dblWidth * m_dblHeight;
}
```

```c++
//hellorect.cpp

#include <iostream>
#include "rect.h"
using namespace std;

int main(int argc, char **argv)
{
    Rect arect(3.0, 4.0);
    cout << "Diagonal Length is " << arect.CalcDiagonal() << endl;
    cout << "Area is " << arect.CalcArea() << endl;
    return 0;
}
```

这段代码相当于在 `rect.h` 定义了一个简单的 `Rect` 类，在 `rect.cpp` 中定义了 `Rect` 类的两个函数，而在 `hellorect.cpp` 中写了 `main` 函数做事情：计算输入矩形的对角线长度与面积。

下面在 MinGW 命令行中进入 hellorect 工作目录，先编译两个 cpp 文件 `rect.cpp` 与 `hellorect.cpp` ：

```c++
>g++ -c rect.cpp -o rect.o
>g++ -c hellorect.cpp -o hellorect.o

//使用 g++ -c 编译生成目标代码文件 *.o，却没有进行连接
//可以看到，因为在编译的时候指定了拓展名，所以不是 *.exe
```

下面将 `*.o` 文件与链接库一并生成为可执行程序：

```c++
>g++ rect.o hellorect.o -lm -static -o hellorect

//其中：
    //g++ 会自动调用链接器链接 *.o 文件与系统里的链接库
    //-lm 表示链接数学库
    //-static 表示生成静态链接的程序
    //-o 指定了生成的输出文件名
```

接着执行 `hellorect` 程序就可以获得函数输出的结果，命令行显示如下图：

![hellorect-cmd](/images/QT-learning-note-01/hellorect-cmd.jpg)_命令行截图示意_

![hellorect-list](/images/QT-learning-note-01/hellorect-list.jpg)_文件夹截图示意_

### 为 Hello Rect 编写简单的 Makefile

简单的程序每次生成的时候一句句敲 `g++` 命令图开心也没问题，但如果项目变得十分的复杂，为每个文件分别敲代码编译就很麻烦。并且 `.cpp` 每修改一次，就需要重新生成目标文件 `*.o`。

> 因此实际开发项目时都是借助 make 工具（MinGW 的是 mingw32-make），编写好 Makefile 之后，只需要在项目文件夹执行一句  make 命令，其他生成目标文件、链接目标文件和库以及自动根据源代码改动重新生成等，这些事情全交给 make ，而程序员就不用操心构建程序的具体过程。

虽然 `Makefile` 是可以自己编写的，不过现在几乎所有的 IDE 都可以根据项目文件自行生成相应的 `Makefile`，所以这也为我们省下了许多功夫。下面是作者给出的一个“生成器 `make` 工作”的简单示范，体验一下吧：

```makefile
# Makefile for building: hellorect

# 定义变量
CC			= gcc			# C 语言编辑器
CXX			= g++			# C 语言编译器
LINKER		= g++			# 链接器
LFLAGS		= -lm -static	# 链接器的参数

OBJECTS		= rect.o hellorect.o	# 编译后得到的目标文件
DSTTARGET	= hellorect				# 可执行的目标程序

# 定义 Makefile 的生成规则
# 引用变量的方式：$(变量名)

# Makefile 的基本规则：
# 生成目标：依赖文件
# [tab字符] 系统命令
# 注意：tab 不能用 4 个空格代替，否则 make 时会出现没有分隔符（separator）的错误

# Defult rule
all: $(DSTTARGET)
# 默认生成规则，依赖文件 $(DSTTARGET)
# 但下一行没有命令，生成的命令得继续往下找

# 生成可执行文件：
$(DSTTARGET): $(OBJECTS)
		$(LINKER)	$(LFLAGS)	-o $@	$(OBJECTS)
# 在有目标文件时执行命令：
# 即调用链接器 $(LINKER)，根据链接器的参数 $(LFLAGS) 与依赖文件 $(OBJECTS) 生成 $@
# $@ 指第一行左边的生成目标

# 使用编译器生成两个目标文件 hellorect.o 与 rect.o：
# hellorect.o 和 rect.o 就是链接器需要的 $(OBJECTS)
hellorect.o: hellorect.cpp
		$(CXX)	-c	-o	$@	$<
# $< 是上一行冒号右边的第一个依赖文件

rect.o: rect.cpp
		$(CXX)	-c	-o	$@	$<


# 清除规则 clean：
clean:
		rm	$(OBJECTS)	hellorect.exe
# rm 是删除命令，使 clean 删除项目生成的 .o 与 .exe 文件
```

以上代码编辑好 `Makefile` 后，便可使用 `make` 工具了：

```c++
//在命令行的工作

>mingw32-make
//生成项目

>mingw32-make clean
//清理项目
```

#### 为 Windows 增添 `rm` 命令

这里有个问题，`clean` 中需要用到 `rm` 命令，但 Windows 系统里可能会没有这个命令，直接输入 `mingw32-make clean` 会得到如下的结果：

![make-cmd1](/images/QT-learning-note-01/make-cmd1.jpg)_运行 mingw32-make clean 后报错_

作者给出了一个工具，安装后就可以将 `rm` 工具加入到系统环境中了。但可能是因为作者发表那篇文章的时间太久，作者给出的方法现在已经不再适用了。我去查了下资料，发现有一个很简单的方式。

首先，创建一个文本文档，输入以下两行代码：

```
@echo off
del %*
```

然后将该文档重命名为 `rm.bat`，再将它移动到 `C:\Windows` 的目录下，就可以愉快地使用 `rm` 命令啦！

![make-cmd2](/images/QT-learning-note-01/make-cmd2.jpg)_成功运行 mingw32-make clean_

![make-list](/images/QT-learning-note-01/make-list.jpg)_可以看到目标文件 .o 与可执行程序 .exe 被删去了_

## 结束

初入 QT 的过程就这样结束啦，在第一次接触 QT 后，我们首先接触了使用 MinGW 命令行环境编译 C++ 程序的过程，并简单了解 `Makefile` 生成器的使用。从下一节起，就会真正地开始编写 QT 的图形程序了。


## 参考资料

[1] [《Qt 编程指南 (QT Guide)》](https://qtguide.ustclug.org/)，奇先生，年份不详（主要）；

[2] [《Qt 学习之路 2》](https://www.devbean.net/2012/08/qt-study-road-2-catelog/)，豆子，2012 —— 2016；