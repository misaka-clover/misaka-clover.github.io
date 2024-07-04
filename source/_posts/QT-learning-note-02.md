---
title: 【QT 学习笔记02】第一个项目！
date: 2022-05-12 13:27:34
#updated: 2022-05-01 15:59:52
description: 继续挖坑
tags: 
    - 博文
    - QT
    - 笔记
    - 知识
categories: 叶子的小知识
comment: true
keywords: QT,学习,笔记
---

<img src="/images/QT-learning-note-02/cover.jpg" width="450" alt="cover" />

<!-- more -->

今天本来是想要参考[上一节](https://mskclover.com/2022/04/26/QT-learning-note-01/)提到的教程，继续学习 Qt 的。但参考的那两个教程都太陈旧了，可能是因为新的环境变化太大，他们给出的例程我没办法在电脑上复现，所以只得作罢。而且我发现，中文网络上的教程文章几乎都是 18 年之前的，之后很少再有新的文章了。像我在一个 Qt 的中文论坛又找到的一份教程：[《Qt 快速入门系列教程》](https://www.qter.org/forum.php?mod=viewthread&tid=193)，作者之前还出版了这本书，已经到第三版了，并且在 2019 年 2 月的时候还对教程进行一定程度的更新，有重修教程、重振论坛的意思，但现在看起来，他最终还是没能成功：他的 Qt5 新教程在写了十篇以后，停止在了同年的 3 月 20 日，非常可惜。

所以，我只好另寻他路。我在搜索引擎中找到了[这个博客](https://subingwen.cn/)中的 Qt 教程，看起来他以前好像是一个机构等职业讲师，在 [B站](https://space.bilibili.com/147020887) 也有更新相关的视频。我粗略地看了一下，应该是能满足我们目前的学习需要了，所以这次就开始学习[这个教程](https://mskclover.com/2022/05/12/QT-learning-note-02/#%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)了。

## 第一个 Qt 项目

略去已经学习过的部分后，我们这次终于开始使用 IDE 来构建项目了：Qt 官方提供的 Qt Creater，其基本用法就不再多说了。**这次以 `HelloQtGui` 这个项目作为第一个例程，目的是使初学者知晓项目中各个文件的关系**，下面贴出项目的结构与相关代码吧：

![helloqtgui-program](/images/QT-learning-note-02/helloqtgui-program.jpg)_项目结构_

### 项目文件（.pro）

最上面的 `HelloQtGui.pro` 是项目文件，其中记录着项目的一些属性信息，在编译时系统会自动修改其中的内容，也可以手动改动其中的一些参数，以实现额外的功能，在[后面](https://mskclover.com/2022/04/26/QT-learning-note-03/)会遇见相关的例子，这里就先贴出代码与注释：

```properties
# HelloQtGui.pro

# 在项目文件中, 注释需要使用 井号(#)
# 项目编译的时候需要加载哪些底层模块
QT       += core gui

# 如果当前Qt版本大于4, 会添加一个额外的模块: widgets
# Qt5 中对 gui 模块进行了拆分, 将 widgets 独立出来了
greaterThan(QT_MAJOR_VERSION, 4): QT += widgets

# 使用 c++11 新特性
CONFIG += c++11

# The following define makes your compiler emit warnings if you use
# any Qt feature that has been marked deprecated (the exact warnings
# depend on your compiler). Please consult the documentation of the
# deprecated API in order to know how to port your code away from it.
# 如果在项目中调用了废弃的函数, 项目编译的时候会有警告的提示
DEFINES += QT_DEPRECATED_WARNINGS

# You can also make your code fail to compile if it uses deprecated APIs.
# In order to do so, uncomment the following line.
# You can also select to disable deprecated APIs only up to a certain version of Qt.
#DEFINES += QT_DISABLE_DEPRECATED_BEFORE=0x060000    # disables all the APIs deprecated before Qt 6.0.0

# 项目中的源文件
SOURCES += \
    main.cpp \
    mainwindow.cpp \
    testdialog.cpp \
    testwidget.cpp

# 项目中的头文件
HEADERS += \
    mainwindow.h \
    testdialog.h \
    testwidget.h

# 项目中的窗口界面文件
FORMS += \
    mainwindow.ui \
    testdialog.ui \
    testwidget.ui

# Default rules for deployment.
qnx: target.path = /tmp/$${TARGET}/bin
else: unix:!android: target.path = /opt/$${TARGET}/bin
!isEmpty(target.path): INSTALLS += target
```

### main.cpp

这里是程序的主函数，多是自动生成的代码：

```c
// main.cpp

#include "mainwindow.h"     // 生成的窗口类头文件
#include <QApplication>     // 应用程序类头文件

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);         // 创建应用程序对象，在一个 Qt 项目中实例对象有且仅有一个
                                        // 类的作用：检测触发的事件，进行事件的循环并处理
                                        //定义一个 QT 应用程序对象，它是 QT 图像界面的入口
                                        //它的构造函数接收与 main 函数一样的参数
    MainWindow w;       // 创建窗口类对象
    w.show();           // 显示窗口
    return a.exec();    // exec() 阻塞函数进入事件循环函数等待用户操作
    					// 使在程序执行完以后，窗口不会自动消失
}
```

### mainwindow.h

这是窗口界面对应的头文件，被上方的 `main.cpp` 所包含，生成代码如下：

```c
// mainwindow.h

#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>      // Qt 标准窗口类头文件

QT_BEGIN_NAMESPACE
// mainwindow.ui 文件中也有一个类叫 MainWindow, 将这个类放到命名空间 Ui 中
namespace Ui { class MainWindow; }      // 对应的是 ui 文件中的类
QT_END_NAMESPACE

class MainWindow : public QMainWindow   // 继承了基础类
{
    Q_OBJECT    // 这个宏是为了能够使用Qt中的信号槽机制

public:
    MainWindow(QWidget *parent = nullptr);
    ~MainWindow();

private:
    Ui::MainWindow *ui;     // 定义指针指向窗口的 UI 对象
};
#endif // MAINWINDOW_H
```

看不懂是吧？其实我们就算看得懂代码，也不知道它具体是干什么的，反正能跑就行，后面应该慢慢就知道了……

### mainwindow.cpp

这里是上面对应头文件中类的源文件：

```c
// mainwindow.cpp

#include "mainwindow.h"
#include "ui_mainwindow.h"

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)    // 基于 mainwindow.ui 创建一个实例对象
                                // 相当于将 ui 对象实例化
{
    // 将 mainwindow.ui 的实例对象和 this 指向的当前类的对象进行关联
    // 这样同名的两个类对象就产生了关联, 合二为一了
    ui->setupUi(this);          // 相当于在后面操作 ui 时相当于直接操作指向的类

    // 一般在 Qt 的构造函数中进行初始化操作（窗口，数据，……）
}

MainWindow::~MainWindow()
{
    delete ui;
}
```

### mainwindow.ui

这是一个 xml 格式的文件，在 Qt 中的每一个窗口对对应着一个可编辑的可视化界面，不过一般不需要在这里编辑，IDE 给我们提供了更简单的 Design 界面，在那里只需要拖动组件就可以了，这里还是贴出初始代码作参考：

```xml
// mainwindow.ui

<!-- 双击这个文件看到的是一个窗口界面, 如果使用文本编辑器打开看到的是一个XML格式的文件 -->
<!-- 看不懂这种格式没关系, 我们不需要在这种模式下操作这个文件。 -->
<!-- 这里只是给大家介绍这个文件的本质 -->
<?xml version="1.0" encoding="UTF-8"?>
<ui version="4.0">
 <class>MainWindow</class>
 <widget class="QMainWindow" name="MainWindow">
  <property name="geometry">
   <rect>
    <x>0</x>
    <y>0</y>
    <width>800</width>
    <height>600</height>
   </rect>
  </property>
  <property name="windowTitle">
   <string>MainWindow</string>
  </property>
  <widget class="QWidget" name="centralwidget"/>
  <widget class="QMenuBar" name="menubar"/>
  <widget class="QStatusBar" name="statusbar"/>
 </widget>
 <resources/>
 <connections/>
</ui>
```

## Qt 的窗口类

想要为 Qt 项目添加新的窗口，首先得新建一个子类。

### 三个基础窗口类

在 IDE 中先单机项目的主文件并选中，然后在右键菜单中点击 “Add new ...”，在新弹出的窗口中，选择 “Qt” 与 “设计师界面类”，并点击右下角的 “choose...” 进行下一步：

![qwidget_1](/images/QT-learning-note-02/qwidget_1.jpg)

在这里可以看到有三种类，分别是：`QWidget`、`QDialog` 和 `QMainWindow`：

![qwidget_2](/images/QT-learning-note-02/qwidget_2.jpg)

这三种类的关系如下所示：

![qwidget_3](/images/QT-learning-note-02/qwidget_3.jpg)_图片来自于苏丙榅的[入门教程](https://mskclover.com/2022/05/12/QT-learning-note-02/#%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)_

在创建新窗口时，需要让自己的窗口类继承上述三个窗口类中的其中一个。它们分别有如下的性质：

* `QWidget`
  * 是所有窗口的基类
  * Qt 中的控件（如按钮、输入框、单选框等）都属于窗口，其基类均为 `QWidget`
  * 可以内嵌到其他的窗口中 —— 没有边框
  * 可以不内嵌而单独显示 —— 独立窗口，具有边框
* `QDialog`
  * 对话框类
  * 不能内嵌到其他窗口中
* `QMainWindow`
  * 具有工具栏、状态栏和菜单栏
  * 不能内嵌到其他窗口中

### 窗口的显示

在 Qt 中，显示窗口的方式有两种：内嵌入其他的窗口一并显示，或作为独立窗口单独显示：

* 内嵌窗口
  * 依附于某一个大的窗口，作为了大窗口的一部分
  * 大窗口就算这个内嵌窗口的父窗口
  * **在父窗口显示的时候，内嵌窗口也会一并显示**
* 独立窗口（非内嵌）
  * 有边框，有标题栏
  * 需要调用函数才可以显示

基于上面的第一个项目，现分别创建两个测试窗口以说明其区别。`TestWidgget` 和 `TestDialog` 的头文件与源代码均与上面最初的 `mainwindow` 没有大的不同，修改窗口控件也只需要在 Design 中编辑即可，此处的重点在于子窗口的两种不同的显示。以后若无更改，新窗口的自动生成代码将不会再重复贴出了。

因此，下面贴出控制窗口显示的 `mainwindow.cpp` 文件：

```c
// 新的 mainwindow.cpp

#include "mainwindow.h"
#include "ui_mainwindow.h"
#include "testwidget.h"			// 新包含了两个子类的头文件
#include "testdialog.h"

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)    // 基于 mainwindow.ui 创建一个实例对象
                                // 相当于将 ui 对象实例化
{
    // 将 mainwindow.ui 的实例对象和 this 指向的当前类的对象进行关联
    // 这样同名的两个类对象就产生了关联, 合二为一了
    ui->setupUi(this);          // 相当于在后面操作 ui 时相当于直接操作指向的类

    // 一般在 Qt 的构造函数中进行初始化操作（窗口，数据，……）
    
    // 显示当前窗口的时候，显示另外一个窗口 TestWidget
#if 1
    // 创建窗口对象，没有给 w 对象指定父对象
    // 所以这个窗口是一个独立窗口，有四周的边框
    // 要显示独立窗口需要进行 show（） 操作
    TestWidget* w = new TestWidget;
    // 显示当前窗口
    w -> show();
#else
    // 如果在创建窗口对象的时候给其指定了父对象，这个窗口就不是一个独立窗口
    // 所以在当前父窗口显示的时候，子窗口被嵌入，与父窗口一起显示出来
    // 此时子窗口是没有独立边框的
    TestWidget* w = new TestWidget(this);
#endif

#if 1
    // 创建对话框窗口
    TestDialog* dlg = new TestDialog(this);     // 不管是否指定窗口的父对象，它都不会内嵌到主窗口中
    // 非模态
    dlg -> show();          // 需要手动调用父类的 show()
#else
    // 创建对话框窗口
    TestDialog* dlg = new TestDialog(this);
    // 模态，exec()
    // 阻塞程序的执行
    dlg -> exec();
    // 此时无法显示主窗口，也无法切换焦点，需要关闭 dlg 窗口才会出现主窗口
#endif
}

MainWindow::~MainWindow()
{
    delete ui;
}
```

此时代码中是有两个分支的，在两个 if 均为 1 的时候，它们均为独立窗口 —— 其中 `TestWidgget` 窗口继承了 `QWidget` 类，在没有指定父类的时候，它是独立窗口；而 `TestDialog` 窗口基础的是 `QDialog` 类，所以无论它是否指定父对象（是否在 new 后指向 `this`），它都只会是独立窗口。

![qwidgt](/images/QT-learning-note-02/qwidget_4.jpg)

_其中 Form 窗口是 TestWidget 产生的_

若给 `TestWidgget` 指定父对象（将第一个 if 置 0），则其会嵌入到主窗口中：

![qwidget_5](/images/QT-learning-note-02/qwidget_5.jpg)

现在的 `TestDialog` 是非模态显示（调用的 `show()` 函数)，若想让对话窗口改为模态显示，则需调用 `exec()` 函数（将第二个 if 置 0）：

![qwidget_6](/images/QT-learning-note-02/qwidget_6.jpg)

此时只有对话框窗口，且焦点无法切换到其他地方，只有在关闭此对话窗口以后，才会显示主窗口。

## 坐标体系

Qt 的坐标体系与大多数显示一样，均以左上角为坐标原点。不过有一点特殊的是，在一个 Qt 窗口中，其中嵌入的子窗口是以父窗口的左上角为左上角为坐标原点，也就是相对坐标，而非绝对坐标：

![coordinate_1](/images/QT-learning-note-02/coordinate_1.jpg)_图片来自于苏丙榅的[入门教程](https://mskclover.com/2022/05/12/QT-learning-note-02/#%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)_

下面用 `coordinate` 项目作为例程示例：

```c
// corrdinate mainwindow.cpp

#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <QPushButton>          // 按钮控件需要的库

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)
{
    ui->setupUi(this);

    // 让窗口出现在屏幕的这个位置，没有指定则是默认居中
    // 坐标原点是左上角
    //this -> move(100, 100);

    // 创建一个按钮，让这个按钮作为当前创建的子控件
    QPushButton* btnA = new QPushButton(this);      // 一定要指定父对象，否则按钮就成了一个独立的窗口
        											// 此时父对象依旧是主窗口
    // 移动按钮的位置
    // 基类 QWidget 中的 move() 函数
    btnA -> move(10, 10);
    // 给按钮设置固定的大小
    btnA -> setFixedSize(200, 200);

    // 创建第二个按钮
    QPushButton* btnB = new QPushButton(this);
    btnB -> move(10, 10);
    btnB -> setFixedSize(100, 100);

    // 创建第三个按钮
    QPushButton* btnC = new QPushButton(this);
    btnC -> move(10, 10);
    btnC -> setFixedSize(50, 50);
}

MainWindow::~MainWindow()
{
    delete ui;
}
```

![coordinate_2](/images/QT-learning-note-02/coordinate_2.jpg)

若在代码块上方解除注释掉的代码 `this -> move(100, 100);`，则窗口会以左上角为坐标原点进行显示：

![coordinate_3](/images/QT-learning-note-02/coordinate_3.jpg)

所以我们可以把整个屏幕认为是一个最大的窗口。

## 总结

这次我们脱离了普通的文本编辑器，使用 Qt 官方提供的 Qt Creater 这个 IDE 进行编程，构建了第一个项目，并重点区分了三种重要的基础类与窗口的显示方式，最后以窗口的坐标体系作结。而在下一节，我们应该会学习 Qt 的数据类型与信号槽 —— 后者是 Qt 中非常重要的一部分，到那时，或许我们的 Qt 才算是真正的入门了吧。

## 参考资料

[1] [Qt 入门](https://subingwen.cn/qt/qt-primer/)，苏丙榅，爱编程的大丙，2021

