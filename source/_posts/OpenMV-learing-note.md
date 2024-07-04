---
title: 国赛上篇：学习OpenMV的七日
date: 2021-07-17 17:25:34
updated: 2021-07-22 15:59:52
description: 记述我近日学到的一些知识
tags: 
    - 知识
    - 学业
    - 博文
    - 电赛
    - OpenMV
categories: 叶子的小知识
comment: true
katex: true
keywords: OpenMV,电子设计大赛,电赛,国赛,教程,机器视觉
---

![cover](/images/OpenMV-learing-note/cover.jpg)_封面图来自加零（@jia0kelvin），欢迎大家在Twitter上关注他！_

<!-- more -->

## 前言

由于 2020 年初疫情，大一的下学期只能居家上课的缘故，我没有参加去年的省赛。所以，如果不算上学院和学校自己举办的那些比赛的话，今年的国赛还是我第一次打电赛。

以前我很少有机会能够有这么一段时间，为了一个目标集中地学习某样东西，我也没有做过大型项目的经历。所以这一次，我可得好好地参与一番！

从考试完到现在，我们已经准备了快十天。实际上我们从大二的上学期就已经开始准备了，但之前一直都比较懈怠，平日也都在忙其他的东西。真正说得上大家开始同心准备的时候，是从大二下开始的，我们在这个学期确定了飞机的选型，购买相关零件，和搭建好了飞机，并使她完成了遥控飞行。在期末考试结束以后，我们立刻开始继续准备，这一次我们想要先实现受控的自动飞行，于是 OpenMV 就成为了其中的一个很关键的元件。

学到现在，我想效仿 [**果子酱**](https://cak.moe/) 的这篇文章（[2019 电赛 --OpenMV 学习笔记](https://blog.moej.cn/archives/405/)）那样，在学习到一定程度的时候，写一篇文章，以回顾这段时间学习情况，并且还可以停下来思考一下，接下来又应该准备些什么。

所以，我就写了这篇，希望能给大家一点帮助。

## OpenMV简介

OpenMV 是由美国的同名公司 OpenMV, LLC 制造的一个小型、嵌入式、低成本、可拓展的基于 Micro Python 的机器视觉模块，该公司想要其成为“机器视觉世界的 Arduino ” [[1]](https://openmv.io) 。简单来说，这就是一个可编程的摄像头 [[2]](https://singtown.com/openmv/) ，或者你可以理解为带摄像头模组的单片机，而且官方还贴心的为你附上了庞大的机器视觉和图像处理的算法库，开箱 10 分钟内就能上手。

![OpenMV](/images/OpenMV-learing-note/OpenMV.jpg)_这是我们队买的 OpenMV_

其实在19年电赛禁用搭载 OpenCV 的树莓派后，简单的图像处理大都只能靠 OpenMV 来做了，当然，不排除有自己造轮子的大佬（

重要的是， OpenMV 是少有的开源硬件——不是像匿名 / 无名那样的假开源—— OpenMV 是真正的硬件结构和软件驱动都开源的商业硬件。既然如此，我们有能力有机会自然也想支持一下，虽然官方的卖得超贵，但也比其他仿造的 OpenMV 质量好得多啊！

总之，正如官方的描述： OpenMV 适合做 DIY 或者低成本的项目，而不适合做复杂的算法工作 [[3]](https://book.openmv.cc) 。

所以，对于经费少的大学生和目标功能简单的项目（相对于成熟工业界）而言，用 OpenMV 来做电赛真是再合适不过了。

## 学习笔记

我们的计划是，首先实现无人机的手动自稳飞行，再实现简单的自动飞行，最后实现可靠的复杂自动飞行——也就是能够完成一道往年的赛题。因此，我们就从 19 年国赛的 B 题开始入手了。

前年国赛的 B 题需要 OpenMV 的地方，大概有四个：

- 识别杆 / 线和测距（视无人机方案而定）；
- 识别黄色条形码标记；
- 识别二维码；
- 识别地上的红色起飞标记；

这几项其实蛮简单的，基本上都可以用 OpenMV 的内置函数解决，例如识别颜色标记，可以参考其“色块识别”的例程 [[4]](https://book.openmv.cc/image/blob.Html) ；二维码 / 条形码的识别，也可以参照其“QR Code”和“Bar Code”的相关例程 [[5]](https://book.openmv.cc/image/code.Html)。

所以， OpenMV 编程多少能算是偏向软件的项目，我有大量的往年源码和例程可以参考。与此相比，我那写 MSP432 的队友才是真的头疼：虽然TI给了很详细的芯片手册和大量例程，但苦于都是英文，并且庞大的内置库函数让人难以下手，而硬件方面又缺少开源的传统，少有往年的源码给我们参考，所以面对这种情况是会感到有些手足无措的。 OpenMV 相比于这个，大量例程让我们能够 10 分钟内上手，可是相当的友好。

参考官方的例程和各位前辈、学长的源码和思路，我将学习到的综合起来，照猫画虎地写了一套我自己的实现。虽然仍有一些瑕疵，但在我的测试中多少还是能完成任务了，希望能给大家一些参考。

**注意**：因为一些原因，我的写作顺序是 初始化——识别色块——识别条形码和二维码——识别竖杆——测距——储存照片，不过实际上的逻辑还是文章段落的顺序。

### 初始化

OpenMV 作为带相机模组的单片机，每次启动都需要初始化。我大概摘一部分在这里，思路很简单，看注释就可以了：

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# By: Misaka_Clover

import sensor, image, time  # 引入OpenMV的三个常用库
import pyb			# 引入单片机的库
import math			# 以防万一做数学运算
from pyb import LED	# 准备LED
from pyb import UART    # 准备通信

# 定义各颜色的LED
red_led = LED(1)
green_led = LED(2)
blue_led = LED(3)

# 开启串行通信
# 默认/只能串口3，波特率常用115200或9600
uart = UART(3, 115200)

# 镜头初始化，具体参见官方文档
sensor.reset()
sensor.set_pixformat(sensor.RGB565)
sensor.set_framesize(sensor.QVGA)
sensor.skip_frames(10)
sensor.set_auto_gain(False)
sensor.set_auto_whitebal(False)

clock = time.clock()

while(True):
    clock.tick()
    Light()
    img = sensor.snapshot()	# 不断地拍照送入Buffer

```

可以像我这样，自己建立个模版，每次直接调用或者复制过去都行。

我的源码在 [这里](https://github.com/Misaka-9936/OpenMV_note/blob/main/my_note/00-01-模板.py) 。

### 识别竖杆

这个功能在几分钟前我才调试好，趁着队友们在飞机上测试的时候，我才有时间在旁边把博文写完。

识别竖杆，实际上就是识别黑色的色块。由于上文提到过，这是我最后写的部分之一，所以色块识别请参见后面，这里我们只需要一起看看更准确地识别竖杆的逻辑思路就好。

![useful](/images/OpenMV-learing-note/useful.jpg)_不能想着凑合啊喂_

根据 B 站这位 UP 的 [分享](https://www.bilibili.com/video/BV1M4411277Y) ，可以看出，在大于 30 cm 的情况下，直接应用官方教程得到的效果非常地不理想。但他们非常的天才，通过“使用被横向裁剪的 VGA 画幅”解决了识别效果不好的问题。 VGA 画幅的影像广，可以探测到更多的区域，但受限于 OpenMV 孱弱的性能，其在这种画幅下的帧率仅有 7 帧左右，远远小于 OpenMV 默认状态下的 42 帧。所以，他们将画幅进行了裁剪，这样不仅能减少干扰，更准确地识别黑杆，而且能够大幅提高帧率至 24 帧左右，一举两得。

不过，这些奇技淫巧也主要是为测距服务的，下面就让我们来看看 OpenMV 如何测距吧。

### 测距

测量距离，其实按照常理而言，在不使用超声波或者激光雷达这种直接测距模块的前提下，我们至少需要两个摄像头（双目）才能测量出距离。可我们并没有那么土豪，并且用两个摄像头的话，不仅难以协调二者，并且在工程上冗余，既昂贵也不美观。

#### 思路

不过，工程师们是最擅长解决问题的。工程数学告诉我们，即使是 OpenMV 这样的单目摄像头，我们依旧可以通过一个简单比例变换计算出距离。其中的原理是霍夫变换 [[7]](https://en.wikipedia.org/wiki/Hough_transform) ，或者，我们可以直接看星瞳科技的教程：如何利用 OpenMV 进行测距 [[8]](https://book.openmv.cc/image/ranging.html) 。

In brief ，“实际长度和摄像头里的像素成反比”，直观一点便是：距离 = 一个常数 / 直径的像素。

所以，识别竖杆的核心思想便是：选取一个已知长度 / 宽度的参考，通过现在已知的距离和参考处的像素情况计算出常数 K 。当实际飞行时已知参考处的像素情况，我们便可以通过之前计算好的比例常数 K ，算出现在与参考物之间的距离。

根据官方的教程和前辈们的代码，我们可以编写如下程序：

```python
# 黑杆测距

K = 1470		# 我们计算出的常数K

black_threshold = (3, 24, -24, -2, -5, 21)	# 我们测量出的黑杆阈值

sensor.reset()
sensor.set_pixformat(sensor.RGB565)
sensor.set_framesize(sensor.VGA)	# 设置VGA画幅
sensor.set_windowing(640, 100)		# 裁剪窗口，以降低负荷，提升帧率
sensor.skip_frames(time = 500)
sensor.set_auto_gain(False)
sensor.set_auto_whitebal(False)

while(code_step == 1):
    clock.tick()    # 开始计频
    blue_led.on()	# 指示灯

    img = sensor.snapshot()
    # 识别黑色色块
    blobs = img.find_blobs([black_threshold], area_threshold=400, merge=True)
    for blob in blobs:
        if len(blobs) > 0:
            x = blob[0]
            y = blob[1]
            width = blob[2]
            height = blob[3]
            cx = blob[5]
            cy = blob[6]
            img.draw_rectangle([x, y, width, height])   # rect
            img.draw_cross(cx, cy)
            
            # 测距，以不会改变的宽度为参考
            Lm = width
            length = K / Lm
            print("length =", length, "width =", width, "FPS =", clock.fps())
    # K = 物体放置的距离 * 打印出的物体像素大小
    # K = 30cm * 47(width) = 1410
    # K = 30cm * 49(width) = 1470
```

好了，下面让我们来看看效果吧。

#### 效果

![black_distance](/images/OpenMV-learing-note/black_distance.jpg)_黑杆测距示例_

可见，在竖杆识别准确无误的情况下，我在30 cm 处测得的距离非常准确，几乎没有误差，而且帧率 FPS 也很好看。

不过，这只是在“实验室条件下”测得的数据。因为我们的飞机目前只能完成自稳，其定高定点尚未调试好，所以还没办法将 OpenMV 搭载在上面，也暂时无法测试这些方案的实际效果。但考虑到这些方案都是前辈们在国赛中使用过的，所以，想来效果应该也不错吧。

识别竖杆和测距的源码我就没有单独整理出来了，大家想参考的话，可以来看看我现在的 [主函数](https://github.com/Misaka-9936/OpenMV_note/blob/main/my_code/try_for_2019/main.py) 。

### 识别色块

对于色块识别，从现成的色块阈值测量工具，到色块识别丰富的内置函数和其较高准确率，都可以看出 OpenMV 的官方在这方面花费了很多精力优化。

整个流程并不难，我简略地说下吧：

#### 选择阈值

先将我们需要识别的颜色放在 OpenMV 的下方，并使用 ``` sensor.snapshot()  ``` 函数拍照，创建图像对象，并将其送入缓冲区（Buffer）。

然后，按照 [官方的教程](https://book.openmv.cc/image/blob.Html)，打开 OpenMV IDE 中内置的“阈值选择工具”（Threshold Editor），再如下图一样，通过滑动滑块将目标颜色选出即可：

![Threshold_Editor](/images/OpenMV-learing-note/Threshold_Editor.jpg)_“阈值选择工具”（Threshold Editor）示例图，来自 OpenMV 官方教程_

选择出阈值以后，我们会得到一个元组，就是上图中的 LAB Threshold ，这个元组表示 (minL, maxL, minA, maxA, minB, maxB) ，代表了颜色空间 / 颜色标准 LAB 的最大值和最小值。（注意：OpenMV 使用的颜色空间并非 RGB ！）

#### 调用 find_blobs 函数

这个函数的参数有些多：

```python
image.find_blobs(thresholds[, invert=False[, roi[, x_stride=2[, y_stride=1[, area_threshold=10[, pixels_threshold=10[, merge=False[, margin=0[, threshold_cb=None[, merge_cb=None]]]]]]]]]])
```

看起来相当的复杂，对吧？

不过按照前辈的经验，我们只需要使用这些参数就可以了，若我们的阈值名为 ```green_threshold``` ，则可写为：

```python
red_blobs = img.find_blobs([green_threshold], area_threshold=100, merge=True, roi=(120,0,80,240))
```

这行我们将函数的结果赋给 ```red_blobs``` 对象，第一个参数 ```thresholds``` 是阈值，,它必须是元组列表，你可以在其中送入多个阈值，以识别多种颜色；第二个参数 ```area_threshold``` 规定了色块的最小像素，若小于此值，则会被当作噪声过滤掉；第三个参数 ```merge``` 为 ```True``` 的时候，可以合并所有没有被过滤掉的色块，他们的边界矩形互相交错重叠；第四个参数 ```roi``` 表示感兴趣区域的矩形元组 (x，y，w，h) ，如果未指定，ROI 即整个图像的图像矩形，其他操作的范围也仅限于 `roi` 区域内的像素。至于其他的参数，可以参考 OpenMV 官方文档，这里就不再详述了。

当我们得到返回的色块对象以后，可以在每一帧图像上都用循环 for 将我们需要的信息返回：

```python
if green_blobs:
    for blob in green_blobs:
        x = blob[0]		# 色块在左上角的x值
        y = blob[1]		# y值
        width = blob[2]		# 宽
        height = blob[3]	# 高
        cx = blob[5]		# 中心位置的x坐标
        cy = blob[6]		# y坐标
        color_code = blob[8]    # 返回一个32位二进制数字
        # 上述变量的详细释义请参照注释和官方文档

        # 画出边框与中心十字
        img.draw_rectangle([x, y, width, height])
        img.draw_cross(center_x, center_y)
```

这里我们重点看一下 ```blob[8]``` ：

“返回一个 32 位的二进制数字，其中为每个颜色阈值设置一个位，那么颜色阈值不同的多个色块就可以合并在一起了。 您可以用这个方法以及多个阈值来实现颜色代码跟踪，您也可以通过索引 `[8]` 取得这个值。”

看不懂吗？没关系，当时我看这个也看不懂，直到我看到了一个例程 [[6]](https://www.bilibili.com/video/BV1b7411m7Pc) 。这位UP想要识别多种搭配在一起的颜色，于是他这样使用  ```color_code``` ：

```python
red_threshold = (58, 40, 77, -46, 72, 28)       # 红色颜色阈值
green_threshold = (39, 93, -71, -28, -22, 67)   # 绿色颜色阈值
blue_threshold = (30, 59, 101, -53, -71, -21)   # 蓝色颜色阈值

# 程序的核心部分：
red_color_code = 1          # code = 2^0 = 1 = 0001H
green_color_code = 2        # code = 2^1 = 2 = 0010H
blue_color_code = 4         # code = 2^2 = 4 = 0100H
black_color_code = 8        # code = 2^3 = 8 = 1000H
                            # 红色、绿色、蓝色和没有识别到的，对应着2的幂次

blobs = img.find_blobs([red_threshold,green_threshold,blue_threshold], area_threshold=200, merge=True, roi=(5,55,50,73))

if blob_left:
    for blob in blob_left:              
        x = blob[0]             # 赋值官方给的色块对象
        y = blob[1]
        width = blob[2]
        height = blob[3]
        center_x = blob[5]
        center_y = blob[6]
        color_code = blob[8]
```

可见，他定义了三个阈值，并且同时使用 ```find_blobs``` 函数寻找合并的色块，取返回的 ```color_code``` 值。

如果色块是红色加绿色，则返回的 ```color_code = 0011H``` ，即 ```color_code``` 等于 3 ；同理，如果色块是绿色加蓝色，则返回的 ```color_code = 0110H``` ，即 ```color_code``` 等于 6 。最后对 ```color_code``` 进行判定，则可知道色块为哪些颜色的组合。

需要注意的是，色块最原始定义的 ```color_code``` 需要是 **2 的幂次方**，这样才能保证组合后色块的 ```color_code``` 能被还原。

#### 效果

不好意思，我的 IDE 等都在另外一台工作的电脑上，所以只能先用照片凑合一下，见谅。

![blob](/images/OpenMV-learing-note/blob.jpg)_色块识别示例_

细心点会发现，下方盒子上的红色并没有被识别，原因有两点：

1. 我设定了 ```area_threshold``` 大小阈值参数，所以小色块会被滤除；
2. 我划定了 `roi` 的区域，虽然我并没有把区域在图像上显示。

差不多就是这样了，而我参考的大佬源代码我抄录在自己的GitHub上了，[点此即达](https://github.com/Misaka-9936/OpenMV_note/blob/main/others_codes/多色块识别以及二维码识别%20-%20from%20LJYLJ.py)。

### 识别条形码和二维码

#### 思路

OpenMV 对条形码（BarCode）和二维码（QR Code）的识别和读取都内置了相关函数，在识别之前，我们需要先找到目标码：

```python
# 条形码的识别
img = sensor.snapshot()
bar_code = img.find_barcodes()
```

```python
# 二维码的识别
img = sensor.snapshot()
img.lens_corr(1.8) # 镜头畸变矫正，官方认为1.8的强度参数对于2.8mm镜头来说是不错的。
QR_code = img.find_qrcodes()
```

分别调用这两个函数我们就能够找到目标码，并将它们分别赋给变量。接下来，我们可通过 ```rect()``` 函数，为识别到的码标出边框，使其在  IDE 中更加明显：

```python
# 画出矩形边框
img.draw_rectangle(xx_code.rect(), color = (255, 0, 0))	
# xx_code代表上述任一码
```

至于识别它们的内容，我们可以用如下函数得到：

```python
code_means = xx_code.payload()
print("code_means =", code_means)
```

然后其他的操作就完全靠自己发挥啦，比如根据码的内容选择执行分支什么的。

#### 效果

![QR_code](/images/OpenMV-learing-note/QR_code.jpg)_二维码识别示例_

源码请参见 [这里](https://github.com/Misaka-9936/OpenMV_note/blob/main/my_note/01-11-扫码识别.py) 。

### 拍摄并储存照片

首先贴出官方的例程： [snapshot 保存图片](https://book.openmv.cc/example/05-Snapshot/snapshot.Html) 。

可见，我们通过 ```sensor.snapshot().save()``` 函数，将送入缓存中的图像保存在SD卡中。

代码如下：

```python
sensor.reset()
sensor.set_pixformat(sensor.RGB565)
sensor.set_framesize(sensor.QVGA)
sensor.skip_frames(time = 500)

img = sensor.snapshot()
img.save("QR_code_", 1, ".jpg")
print("Done")
```

## 后记

![past_and_future](/images/OpenMV-learing-note/past_and_future.jpg)

虽然题目是七日，到现在也已经过了十余日了。

因为在写这篇博文的同时，一边要学习其他新的策略，例如巡线；一边又需要参与到项目中去，根据实际的进度做一些调整，所以写了好些天，想来标题不如改名为“学习 OpenMV 的七八九十日”吧（

开始写的时候，我对其中的一些策略还不太满意，也在边写边改。到写完的时候，情况变化很大，或许到现在我才能说我是真正地掌握了这些吧，而不再是像刚开始时候那样，对着官方例程和别人的代码照猫画虎了。

现在我这里的进展还算是顺利，可我队友们，他们那里真的，真的太难了。

跟硬件打交到可不容易，至少我是这么认为的。学习 OpenMV ，官方给出了完备的函数和示例，从 Micro Python 到单片机的 pyb 库都有着详细的文档，中华区的代理商还贴心地将其中最重要的部分翻译为中文， GitHub 上还能找到往年的前辈开源出的项目源码。这样一来，要文档有文档，要实例有实例，想做项目还有完备的源码可以参考，哪怕是一头猪都不可能学不会。

但我的两位队友，他们就不像我这么容易了。

我们以往只是按部就班地学习，接触过一点点单片机，比如 51 之流，或者像我一样折腾过 Arduino 那种最简单的玩具单片机，连 STM32 都没怎么用过。现在直接使用 TI 公司赞助的 MSP432 ，难度自然不小。并且，由于我们选用的芯片不常出现在电赛中，所以我们没有往年的参考源码，没有例程，也缺乏相关资料的帮助，唯一有的，只是配套的芯片手册而已。但他们还是对着全英的芯片手册从头开始啃，下载了 TI 官网的各种函数实例挨个地看，而我我真的很佩服他们。

最开始的时候，我们什么都不了解，分工也只是三人在飞控、视觉和主控传感器里各选了一部分而已。那时我们普遍认为视觉和飞控都很难，身为队长，我当然不好意思避重就轻，而且对机器视觉比较感兴趣，所以我能负责视觉这部分也算是他们照顾我。

我这段时间常常在想：要是回到最开始各选分工的时候，我们能清楚的知道这些分别代表着什么，它们的难易程度又如何，让我们再来选，我是会迎难而上，选最难的飞控 / 主控，还是依旧选“较为简单”的视觉呢？我不知道。要是换做是我来做飞控 / 主控，我能做到像他们这种程度吗？我不知道——大概不行吧。

但现在，我们在这上面遇到了难题，进度已经卡住好久了，我却只能做做最基本的调试飞机的工作，帮不上什么忙…… 而且他们遇到困难的部分原因，也是因为我在最开始的方案没选好——我应该选更常见的芯片的。如果当时选的是另外一种方案，或许我们就会顺利得多，不会卡在这里了。

我有些愧疚，特别是想到之前他们开始遇到问题的时候，我还想得很简单，去指手画脚，而现在却什么忙都帮不上…… 

有时候又在想，如果当时我没有选择无人机这个方向，我们的结果会更好呢？

谁知道呢？

反正我只知道，在我看到 [这个视频](https://www.bilibili.com/video/BV1at411r7gn) 的时候，我脑子里就只剩下无人机了。

希望我们接下来能够一切顺利吧。

## 附录

这里照常附上KaTeX公式代码：

```KaTeX
# 原理公式
Lm * Bpix = \frac{Rm * Apix}{tan(a)}
```

详情请参见 KaTeX 文档 [[9]](https://katex.org/docs/supported.html)。

## 参考资料

[1] OpenMV 官网（英文）：https://openmv.io

[2] OpenMV 文档：https://singtown.com/openmv/

[3] OpenMV 教程：https://book.openmv.cc

[4] “色块识别”例程：https://book.openmv.cc/image/blob.html

[5] “ QR Code”和“ Bar Code”例程：https://book.openmv.cc/image/code.html

[6] openmv 物块颜色及色环及二维码识别（from LJTYLJ）：https://www.bilibili.com/video/BV1b7411m7Pc

[7] Hough transform：https://en.wikipedia.org/wiki/Hough_transform

[8] 如何利用 OpenMV 进行测距：https://book.openmv.cc/image/ranging.html

[9] KaTeX文档：https://katex.org/docs/supported.html

### 其他很有帮助的资料

果子小酱的 2019 年电赛OpenMV源码：https://github.com/cwxyr/nuedc-2019-openmv

颜色展示工具：https://www.colorhexa.com

颜色转换工具：https://www.colortell.com/colortool

草料二维码生成：http://cli.im/





