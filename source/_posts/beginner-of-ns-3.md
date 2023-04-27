---
title: 【笔记】ns-3 初入门
date: 2023-04-24 19:49:34
# updated: 2022-11-15 15:59:52
description: 又学习了新的东西
tags: 
    - 博文
    - 笔记
    - ns-3
    - 毕设
categories: 叶子的小知识
comment: true
keywords: ns3,ns-3,下载,安装,构建
---

![cover](/images/beginner-of-ns-3/cover.jpg)

最近因为在做**毕设**时需要学习一些更新的知识、与使用更复杂的工具，所以我想还是像以往那样，通过记下学习笔记的方式辅助自己学习，所以就开始写这篇笔记。

<!-- more -->

既然是笔记，那就不用那么多修饰的字词了，简单就好：

## 是什么

![ns3_web](/images/beginner-of-ns-3/ns3_web.jpg)

[官网](https://www.nsnam.org/)：https://www.nsnam.org/

> **ns-3** is a discrete-event network simulator for Internet systems, targeted primarily for research and educational use. ns-3 is free, open-source software, licensed under the GNU GPLv2 license, and maintained by a worldwide community.

[官方教程](https://www.nsnam.org/docs/tutorial/html/)：https://www.nsnam.org/docs/tutorial/html/

> _ns-3_ is a discrete-event simulator typically run from the command line. It is written directly in C++, not in a high-level modeling language; simulation events are simply C++ function calls, organized by a scheduler.

简单来说，**ns-3** 是一个网络协议的研究与仿真工具。

## 安装

### 先决条件

根据官方的[教程](https://www.nsnam.org/docs/tutorial/html/getting-started.html#downloading-ns-3-using-bake)，首先要检查 / 安装相关的第三方依赖软件包：

```cmd
# 检查 git
$ git --version

# 检查 tar
$ tar --version

# 安装 g++
$ sudo apt install g++
$ g++ --version

# 检查 python3
$ python3 -V

# 安装 cmake
$ sudo apt  install cmake
$ cmake --version
```

### 下载源码

```cmd
$ mkdir ns-3
$ cd ns-3

$ mkdir workspace
$ cd workspace

# 下载
$ wget https://www.nsnam.org/release/ns-allinone-3.39.tar.bz2

# 解压
$ tar xjf ns-allinone-3.38.tar.bz2
$ cd ns-allinone-3.38/
```

## 构建

我们会使用 `build.py` 进行 ns-3 的构建：

```cmd
# 在构建前先查看指令
$ ./build.py --help

# 编译与安装
$ ./build.py --enable-examples --enable-tests
```

然后就是**漫长的等待**。如果中途因故暂停或中断了编译进程，只需要在下次重新输入相同的命令即可。在重新开始编译时，编译器会自动跳过已编译的文件，并显示：

```cmd
Consolidate compiler generated dependencies of target xxx
```

⚠️ **血泪教训**：一定要为 Linux 分配足够的磁盘空间，建议 50G 左右。

## 测试

同样的，使用 `test.py` 进行测试：

```cmd
# 测试
$ ./test.py --no-build
```

如果没有问题，会显示如下内容云云：

```cmd
...
[74/758] PASS: TestSuite ipv6-ripng
[75/758] PASS: TestSuite ipv6-protocol
[76/758] PASS: TestSuite tcp-bbr-test
...
758 of 758 tests passed (758 passed, 0 skipped, 0 failed, 0 crashed, 0 valgrind errors)
```

## Hello World

在万事俱备后，我们照例跑一下实例程序：

```cmd
$ ./ns3 run hello-simulator

# 输出
Hello Simulator
```

就此，我们的安装步骤就完全结束了。

很喜欢 ns-3 官网的一句话：Congratulations! You are now an ns-3 user!

## 遇到的问题

作为一个 Linux 萌新，我在这个过程中，遇到了许多的问题。

### 安装方式出现问题

按照官方的教程，通过 Git 方式下载 ns-3：

```cmd
$ mkdir ns-3
$ cd ns-3

$ mkdir workspace
$ cd workspace

$ git clone https://gitlab.com/nsnam/ns-3-allinone.git
$ cd ns-3-allinone

$ python3 download.py
# 然后会下载大概 200M 左右的文件
# 包括 ns-3，NetAnim 和 bake
```

在通过 Git 下载完 ns-3 后，发现官方并没有针对这种情况给出构建方式，称：

> **Note:** This build step is only available from a source archive release described above; not from downloading via git or bake.

试图直接运行构建文件会报错：

```cmd
e$ ./build.py --enable-examples --enable-tests
# Build NetAnim
Entering directory `netanim'
 =>  qmake -v
Could not find qmake in the default path
 =>  qmake-qt5 -v
Could not find qmake-qt5 in the default path
 =>  qmake-qt4 -v
Could not find qmake-qt4 in the default path
 =>  qmake NetAnim.pro
Error building NetAnim. Ensure the path to qmake is correct.
Could not find qmake or qmake-qt5 in the default PATH.
Use ./build.py --qmake-path <Path-to-qmake>, if qmake is installed in a non-standard location
Note: Some systems use qmake-qt5 instead of qmake
Skipping NetAnim ....
Leaving directory `netanim'
# Building examples (by user request)
# Building tests (by user request)
# Build NS-3
Entering directory `/home/clover/ns-3/workspace/ns-3-allinone/./ns-3-dev'
 =>  /usr/bin/python3 ns3 configure --enable-examples --enable-tests
Traceback (most recent call last):
  File "/home/clover/ns-3/workspace/ns-3-allinone/ns-3-dev/ns3", line 1615, in <module>
    main()
  File "/home/clover/ns-3/workspace/ns-3-allinone/ns-3-dev/ns3", line 1508, in main
    current_cmake_cache_folder, current_cmake_generator = search_cmake_cache(build_profile)
  File "/home/clover/ns-3/workspace/ns-3-allinone/ns-3-dev/ns3", line 561, in search_cmake_cache
    raise Exception("No generator available.")
Exception: No generator available.
Traceback (most recent call last):
  File "/home/clover/ns-3/workspace/ns-3-allinone/./build.py", line 155, in <module>
    sys.exit(main(sys.argv))
  File "/home/clover/ns-3/workspace/ns-3-allinone/./build.py", line 146, in main
    build_ns3(config, build_examples, build_tests, args, build_options)
  File "/home/clover/ns-3/workspace/ns-3-allinone/./build.py", line 65, in build_ns3
    run_command(cmd) # ns3 configure ...
  File "/home/clover/ns-3/workspace/ns-3-allinone/util.py", line 25, in run_command
    raise CommandError("Command %r exited with code %i" % (argv, retval))
util.CommandError: Command ['/usr/bin/python3', 'ns3', 'configure', '--enable-examples', '--enable-tests'] exited with code 1
```

我纯小白，大概能理解到这样直接的方式不行，可我又不愿意使用 CMake，于是就回档重新开始了。

### 虚拟机注意事项

在创建 VMWare 虚拟机时，因为常听说 Linux 非常节省资源，所以就只分配了 20G 的磁盘空间。结果编译到一半中断了，系统提示我**磁盘空间不足**，这时我才知道才刚构建 50% 就已经快要把磁盘空间用完了……

另外，一定要为虚拟机分配**足够的 CPU 核心**，不要像我傻乎乎地只分配了两个小核，然后傻乎乎地等了半天……

所以说，Linux 节约资源根本就是误解，主要是取决于我们要拿它做什么。

## 其他参考资料：

* [ns-3网络模拟中文入门视频教程系列](https://www.bilibili.com/video/BV1mz4y1S7eZ/)，[**开源文摘**](https://space.bilibili.com/488796480/)，Bilibili。

