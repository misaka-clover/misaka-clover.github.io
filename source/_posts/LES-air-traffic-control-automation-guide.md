---
title: 莱斯空管自动化系统管制席界面指南
date: 2024-04-19 17:17:34
updated: 2024-05-06 15:59:52
description: 第一篇工作文档
tags: 
    - 博文
    - 笔记
    - 莱斯
    - 空管
    - 自动化系统
    - 管制席
categories: 叶子的工作文档
comment: true
keywords: 莱斯, 空管, 自动化系统, Les, Air Traffic Control, ATC, Automation
---

![SDD](/images/LES-air-traffic-control-automation-guide/SDD.jpg)_莱斯的自动化界面，与欧洲猫十分相似_

## 写在前面

我在上学的时候一直有整理笔记的习惯，到了工作中也依然如此，所以就想把整理的一些笔记做成文档发上来。民航领域算是一个非常封闭的地方，互联网上总是难以查到相关的资料，特别是在中国大陆地区…… 我相信我们每个人的微薄之举都可以让互联网变得更好！所以，也希望这些资料能对需要的人有帮助吧。

<!-- more -->

在正文开始之前，想先向不熟悉的朋友介绍一下：

[空中交通管制](https://en.wikipedia.org/wiki/Air_traffic_control/)，是为飞机提供引导的地面管制人员/服务。相比于上个世纪初使用望远镜进行目视监视与程序管制，现代空管使用雷达管制，这就需要一套自动化系统，能同时处理多个监视源的数据，并将目标与所有的相关信息显示在频幕上 —— 这就是空管自动化系统（Air Traffic Control Automation）。

一般而言，目前国际上比较流行的自动化系统是 [THALES](https://en.wikipedia.org/wiki/Thales_Group/) 公司的 欧洲猫 Eurocat (现名 [TopSky](https://en.wikipedia.org/wiki/TopSky/))，非常的经典，也相当稳定。

![FIRS](/images/LES-air-traffic-control-automation-guide/FIRS.jpg)_FIRs controlled by [TopSky - ATC](https://www.thalesgroup.com/en/markets/aerospace/air-traffic-management/topsky-atc/)_

不过，由于目前国产化的趋势愈演愈烈，早已蔓延到了民航领域，所以中国 🇨🇳 不仅开始自己制造 C919 支线客机，同时也在力推国产化的空管自动化系统，[中国电科 CETC](https://zh.wikipedia.org/zh-tw/中国电子科技集团/) 开发的 莱斯 LES 空管自动化系统也开始逐渐替代曾经的进口系统。某种意义上，莱斯在界面上是像素级抄袭了欧洲猫自动化系统，或许可以解释为管制习惯了欧洲猫的界面。另一方面，莱斯系统的稳定性也远不如欧洲猫，相比于后者在二十年间几乎没有出现过严重故障，我们现在甚至都不敢将莱斯长期使用（48 小时以上）。至于厂家简陋的算法、效率低下的技术支持与极不负责的测试，那就是后话了。

闲谈到此为止，接下来让我们来看看莱斯空管自动化系统的管制界面吧。

## 管制席部份 SDD

整个空管自动化系统的管制席位界面分为三个部份：

- **通用信息窗口 GIW**：工作模式、显示信息、目标属性 和 过滤设置 等
- **态势界面窗口 SDW**：显示雷达目标 和 背景地图 等
- **命令行窗口 CLW**：显示内容设置、飞行计划 和 辅助功能

其中，通用信息窗口和命令行窗口可以打开或者关闭。

## 通用信息窗口 GIW

![GIW](/images/LES-air-traffic-control-automation-guide/GIW.jpg)

![GIW_detail](/images/LES-air-traffic-control-automation-guide/GIW_detail.jpg)

### 放像控制 RepCtrl

应该是放像席位的选择（Replay Control），在《1.2 新终端莱斯自动化 V3.5 系统（塔台）维护规程》中，有如下描述：

> 点击雷达管制界面的左上角的“RepCtrl”项，单击选择控制放像的席位（例POS88），如下图1所示。

### 系统逻辑组 System Name

如图中的 “APG000” 指示系统当前的逻辑组。

<img src="/images/LES-air-traffic-control-automation-guide/GIW1.jpg" width="300" alt="GIW1" />

### 席位等级

根据《1.1新终端欧洲猫自动化V10系统(塔台)应知应会》，有：

![mode](/images/LES-air-traffic-control-automation-guide/mode.jpg)

系统模式：NORM —> DEGD —> NORM（自动）

席位模式：NORM —> DEGD —> LOW —>NORM（手动）

### 席位物理组与逻辑组 Logical Position

根据《1.1 新终端欧洲猫自动化 V10 系统（塔台）应知应会》，有：

> **系统物理组：**
>
> 每个物理组都配置了若干台物理席位，每个席位都有一个物理名称，席位的 物理名称和工作站的物理位置是相对应的，且席位物理名称的更改是下线的。

> **系统逻辑组：**
>
> 系统为每个物理组分配了若干个逻辑席位名称，一个物理组可用的逻辑席位名称的个数大于物理席位的个数。逻辑席位主要用于主任管制席的扇区分配，主任管制席把一个或几个功能扇区划分到某个逻辑席位，然后再把这个逻辑席位配置到某个具体的物理席位上。
>
> 在实际工作中这种扇区分配模式发挥着重要作用：当某个席位的硬件故障时，主任管制席可以将该席位的逻辑席位配置到另外一个空置的席位上，它所包含的所有功能扇区就相应的被配置到当前空置的席位上。

### 逻辑席位 TWSMPLGP

本席位配置的逻辑席位（Tower System Monitor Position - Logical Position），点击后会显示该逻辑席位所包含的扇区：

![TWSMPLGP](/images/LES-air-traffic-control-automation-guide/TWSMPLGP.jpg)

### 计量单位 UNIT

席位计量单位设置 PUS 共有四种：

- **公制式 MET**

- **英制式 IMP**

- **公制混合式 MET M**

- **英制混合式 IMP M**

> 单位混合表示席位单位以主单位为主，航迹标牌可以单独切换单位。

### 指北设置 North

地图显示的校准方向有两种：

- **真北 TRUE**

- **磁北 MAGN**

### 速度矢量线 VEL 与 短航路探针 SRP

VEL 仅指示飞机的航向，而 SRP 与飞机的飞行计划相关，比如它会将飞机与航路点相连，另一端指向另一个方向：

![SRP](/images/LES-air-traffic-control-automation-guide/SRP.jpg)

### 尾迹点设置 HIST

历史轨迹（Historical Trail）设置，根据《NUMEN3000 自动化系统 V3.5 版本 - 管制员席操作手册》有：

> 尾迹点设置：鼠标左键点击 HIST 按钮，控制在 ILS 窗口中是否显示航迹尾迹点，并可设置尾迹点显示个数。
> （计划航迹不显示尾迹点）

### 航空识别码 ACID

航空识别码（Aircraft Identification）用于区别标牌上的识别码类型，上栏有四个选项：

- **ACID**：显示航班号
- **SSR**：显示二次代码
- **24 BIT**：显示 24 位地址码
- **TRGT ID**：同样显示航班号（Target ID）

> 雷达管制席位，航迹与计划相关或被挂上雷达 radtag 后，航迹标牌上就不再显示二次代码。
> 鼠标右键点击“ACID”按钮，所有与飞行计划相关或者雷达简标牌航空器的航班号显示处快速切换为二次代码显示。

![ACID](/images/LES-air-traffic-control-automation-guide/ACID.jpg)

下栏的 **标牌快看 QLK ALL** 是标牌强制显示，如：跑道上未升空但已打开应答机的目标、空中有二次代码但没有飞行计划的不明航迹等。

> 对于已经被高度过滤、二次代码过滤及区域过滤（SHOEBOX）的航空器，可强制显示其数据块标牌。

### 上升下降率显示设置 DIR

打开后可在标牌的右边显示各个航班的上升下降率（Descent or Increase Rate）。

### 标牌格式切换 Normal/Extend 和 Fixed/Shared

飞行相关航迹的标牌有两种显示格式：

- 正常数据库标牌格式 Normal
- 扩展型数据块标牌格式 Extend

> 正常数据库标牌格式显示 4 行飞行数据，扩展型数据块标牌显示 5 行飞行数据。

而扩展型数据块标牌格式 Extend 还有两种显示方式：

- 固定型 Fixed
- 分时扩展型 Shared

> 固定型显示 5 行飞行数据，其中第四行显示目的机场和机型。分时扩展型也显示 5 行数据，但是第四行在目的**机场、机型**为一组数据，**下一报告点，下下报告点**为第二组数据两组数据之间进行切换显示。

### 飞行计划航迹显示过滤 FPASD

可以显示全部计划航迹的功能，根据资料，有：

> 当功能关闭时航迹的所有显示属性，包括标识符、标牌、引线都被过滤。

### 第二通用信息窗口 GIW2

点击后打开一排选项：

![GIW2](/images/LES-air-traffic-control-automation-guide/GIW2.jpg)

- **Main Window**：调整态势界面主窗口的大小

- **Second Window**：调整第二态势窗口的大小

- **Display Level**：仅显示当前高度范围内的目标（高度过滤）

- **SSR Code Filter**：二次代码过滤

    > 被过滤的航空器不显示数据块标牌。告警的、管制的及处于移交状态的航空器不进行二次代码过滤。
    >
    > 二次代码集过滤指航空器的二次应答机代码的前两位与输入的过滤条件相同即可过滤，或者激活两个二次代码段后，两个二次代码中间的全部过滤。
    >
    > 一共可输入两个二次代码集和两个指定二次代码，输入完毕后需回车确认；若想取消过滤设置需清空相关的文本框。

- **Target Address**：地址码过滤

    > 一共可输入两个指定地址码，输入完毕后需回车确认；若想取消过滤设置需清空相关的文本框。

- **All Flights**：管制航迹显示过滤

    - 所有航迹 All Flights
    - 本席位管制 Jurisdiction
    - 本系统管制 Controlled

    > 可过滤除飞行计划航迹、告警航迹、测试点、模拟目标之外的航空器数据块标牌，标识符不过滤。

![all_flights](/images/LES-air-traffic-control-automation-guide/all_flights.jpg)

- **T&SM**：测试点及模拟目标显示过滤设置

    > 可过滤系统接收到的监视目标中为测试点和模拟目标的动目标航迹，过滤航迹标识符及标牌。
    >
    > T&SM 按钮蓝底灰字表示显示相关目标。

    比如从 SMP 上找到的这个目标，航迹符 I 表明这是一个测试点：

![tsm](/images/LES-air-traffic-control-automation-guide/tsm.jpg)

## 态势界面窗口 SDW

雷达航迹符类别：略

目标标牌格式：略

## 命令行窗口 CLW

![CLW](/images/LES-air-traffic-control-automation-guide/CLW.jpg)

相比上面的示意图，我们的则是：

![ourCLW](/images/LES-air-traffic-control-automation-guide/ourCLW.jpg)

下面按照我们系统的实际顺序介绍。

### 显示设置 Set up

<img src="/images/LES-air-traffic-control-automation-guide/setup.jpg" width="700" alt="setup" />

#### 罗盘 Graphic Options

选项 Compass Bearing 罗盘方位打开后，会在 SDW 四周以正北方向顺时针每 10 度显示方位，默认关闭。

#### 动态文本 Txa Font

~~网上查到这是一种字体，但不知道在哪里能看到变化。~~

是动态字体显示，请参见后面的「动态文本」一栏。

#### 标牌字体 Label Font

选择航迹标牌的文字大小。

#### 标牌位置 Label Position

- **Auto Label Offset**：自动标牌偏移，系统会自动调整标牌的位置，以避免标牌产生重合；
- **Global Orientation**：全局方向，前面的选项用于调整所有标牌的方向；
- **Default**：默认，标牌会默认指向同一个方向（目测右上 45 度左右）；

#### 边缘标牌旋转 Anti Edge Rotation

选择 Main 时，超出屏幕边缘的标牌将旋转其位置，直到强制显示完整标牌为止。

选项 2nd 和 other 未能看到变化。

#### 标牌引线长度 Leader Size

选择航迹标识符到标牌的连接线长度。

#### 重置自定义方向 Reset Manual Orientation

点击后，SDW 中所有的标牌方向将被重置。

#### 距离环设置 Range Ring Options

打开后一组半径不同的同心圆，右侧可调整各环之间的距离。数值单位与当前席位单位显示制式一致，公制式为公里，英制式为海里。

#### 亮度调节 Brightness

- 文本/动态文本 Text
- 管制航迹 Jurisdiction
- 预管制航迹 Announced
- 预管制航迹标牌 Announced Label
- 非管制航迹的标识符 NJUR Symbol
- 非管制航迹的数据块标牌 NJUR Label
- 电子进程单 Strip
- 背景地图 Map
- 填充图形 Infill：激活的危险区及扇区
- 工具 Tool

#### 音量调节 Volume

左侧按钮可测试音量。

#### 监视源选择 Surveillance Selection

默认显示综合航迹，系统也提供单雷达、旁路和其他监视源的目标显示，可判断是否为假目标。

<img src="/images/LES-air-traffic-control-automation-guide/Surveillance.jpg" width="400" alt="Surveillance" />

- Class 下有：NORM 综合航迹、BYPS 旁路航迹 和 TSDP 塔台航迹；
- Type 下有：SYS 融合航迹、RADAR、ADS-B 和 WAN；
- Source 下：接入的各路雷达，在旧版本的莱斯系统中可以选择某一路的雷达数据，而新版本仅能显示 ALL 所有雷达数据；

### 地图 Map

点击按钮后打开 MAP WINDOW，有三个栏目是比较重要的：系统地图、全局地图和本地地图：

![map](/images/LES-air-traffic-control-automation-guide/map.jpg)

#### 系统地图 System Maps

> 系统地图是由离线设置的各类基础数据图形，包括空域、扇区、航线、导航台、报告点、机场等位置图形，管制员可以根据工作需要选择所需图形显示在席位 SDW 背景地图中，以此来直观地掌握各元素的地理位置。

一般我们使用：

#### 云层地图 Generated Maps

> 提供根据云图的浓度进行气象云图的显示控制，气象云图按照浓度划分可分为浓、淡两级，不同浓度的云图显示不同的颜色。

#### 私有地图 User and Local Online Maps

> 在一般的管制席位可进行**本地私有地图**的编辑，而在主任管制席位可进行**全局私有地图**和**临时危险区**的编辑，主任席位编辑的全局私有地图只在本逻辑组范围内进行数据发布和强制显示；当具备主任权限时也可将本地私有地图转换为全局私有地图进行发布。

按照我的理解，本地私有地图 User Maps 仅在本管制席的地图中显示，而全局私有地图 Local Online Maps 可以在整个逻辑组的范围内显示。不过按照这样的话，全局私有地图和全局地图有什么区别呢？

#### 全局地图 Global Maps

> 在具有主任管制权限的席位，可实现全局地图的创建、删除、保存、私有地图转换成全局地图功能，该功能在本逻辑组内有效。

选择私有地图后，通过右侧的「PrivToGlb」按钮，即可将其**全局化**（具体步骤请参见手册）。

### 主任参数设置 Info

> 在一般的管制席位可查看当前逻辑组范围内所有席位及相关参数的各种主任参数设置，只有在登录具备主任管制权限的管制席位才可对相关的参数进行修改。

<img src="/images/LES-air-traffic-control-automation-guide/info.jpg" width="500" alt="info" />

在进行任何设置之前，需要先在最右侧的 LogIn 按钮登录。

我们的系统中，没有 WTH MAPS 和 HOST，而 RMAP、MIL、SSR INHIB 和 SPARE 按钮是灰色的，它们是灰色的原因为认为是没有登录账户的缘故。

#### 二次代码 SSR

点击按钮后打开 SSR CODES 窗口：

![SSR](/images/LES-air-traffic-control-automation-guide/SSR.jpg)

> 根据数据库中设置的二次代码组和二次代码组所使用在的范围，可以设置所有的代码组在它所在的范围是否应用。其中：
>
> - TRANSIT 国际飞行
> - SUPERTRANSIT 国际飞跃飞行
> - DOMISTIC 情报区内飞行
> - SUBDOMISTIC 国内飞行
> - SUPERDOMISTIC 地区飞行
> - MILITARY 军方飞行
> - VFR 目视飞行

不过在我们的系统上，范围顺序为：DOMISTIC、MILITARY、SUBDOMISTIC、SUPERDOMISTIC、SUPERTRANSIT、TRANSIT、VFR。

> 在主用状态下，飞行计划处理模块将根据二次代码组的启用状态自动分配二次代码，系统为备份系统时不能自动分配该代码段。

#### 扇区分配设置 SECT

点击后打开 CONFIGURATION 窗口：

<img src="/images/LES-air-traffic-control-automation-guide/sect.jpg" width="600" alt="sect" />

> 管制席可以分配一个或多个扇区，一个扇区只能有一个管制席位管制。
> 助理管制席可以和一个或多个管制席配对，一个管制席只能有一个助理管制席位与之配对。
> 席位名称为灰色时表示该席位 SDD 软件不正常或不在 FREE 模式和 OPS 模式。

上面的按钮第一行是物理席位 POS；而第二行是逻辑席位，在实际系统中为 BYA 和 BYC；下面的区域显示了分配给各个席位的管制扇区。下面的「配对席位显示区」就没有显示了，可能是因为没有登录账户的缘故。

#### 修正海压 QNH

> 系统提供对 QNH 修正值的管理。设置的 QNH 值在雷达处理航迹时修正航迹的高度。系统可自动接收 AFTN 电报及 QNH 传感器输出的 QNH 数据，在雷达管制席位上自动更新。

<img src="/images/LES-air-traffic-control-automation-guide/QNH.jpg" width="400" alt="QNH" />

#### 自动移交 AHO

窗口全称应该为：AUTO HEADOVER STATIS，我们的系统上都是 OFF。

<img src="/images/LES-air-traffic-control-automation-guide/AHO.jpg" width="600" alt="AHO" />

#### 限制区域 RMAP

我们的系统中的按钮是灰色的，所以看不了，不过知道就好。

<img src="/images/LES-air-traffic-control-automation-guide/RMAP.jpg" width="600" alt="RMAP" />

#### 单位 UNIT

点击按钮打开 LOGICAL POSITION UNIT STATUS 窗口，在这里可以为每个逻辑席位设置四种不同的单位形式，公制单位包括公制和公制混合，英制单位包括英制和英制混合。单位混合表示席位单位以主单位为主，航迹标牌可以单独切换单位。

<img src="/images/LES-air-traffic-control-automation-guide/UNIT.jpg" width="600" alt="UNIT" />

#### 容量 CAP

点击按钮后弹出 FLOW CAP 窗口，可在此设置机场的容量，我们系统设置的值是 66：

<img src="/images/LES-air-traffic-control-automation-guide/CAP.jpg" width="300" alt="CAP" />

当机场的容量超限时，会弹出提示窗口 CAP WARN MSG：

<img src="/images/LES-air-traffic-control-automation-guide/CAP_warning.jpg" width="600" alt="UNCAP_warningIT" />

#### 炮射点 SHOOT

我们的系统中并未设置。

> 在主任席可进行炮射点的添加、修改、删除和激活操作，并可导出炮射点数据，炮射点数据包括：ID、中心点经纬度、作用半径、使用设备、地理信息以及备注。抛射点激活时，在 SDW 中以红色星星显示，如下图所示：

<img src="/images/LES-air-traffic-control-automation-guide/SHOOT.jpg" width="600" alt="SHOOT" />

#### 进离场程序 SIDSTAR

根据参考资料 [4] 和 [5]，这是这是 标准立场程序 SID 和标准进场程序 STAR 的设置：

> 进离场航线设置窗口，可设置进、离场单独显示，且只显示当前激活跑道对应的进离场设置，可设置启用或关闭进离场航线上对应点，也可按照条件进行过滤显示，过滤条件包含：进场/离场、机场、点。

我们的系统都 OFF 掉了。

<img src="/images/LES-air-traffic-control-automation-guide/SIDSTAR.jpg" width="600" alt="SIDSTAR" />

#### 军事活动区 MIL

我们系统中的按钮是灰色的。

> 在主任席可进行军事活动区激活设置，当前时刻处于某一激活时间段内时军事活动区将在背景地图上以填充图形显示，否则军事活动区不显示。

<img src="/images/LES-air-traffic-control-automation-guide/MLT.jpg" width="600" alt="MLT" />

#### 跑道 RWY

点击按钮后打开 RUNWAY IN USE 窗口：

> 机场跑道设置窗口是用来设置机场缺省的起飞降落跑道。可以发布使用跑道当前的状态信息。其中，跑道状态有起飞，降落，起降，关闭四种，Local 为本机跑道状态信息，Global 为全局跑道状态信息。

我们系统未使用。

![RWY](/images/LES-air-traffic-control-automation-guide/RWY.jpg)

#### 二次代码告警抑制 SSR INHIB

我们系统的这个按钮是灰色的。

> 在主任管制权限下，可对指定的二次代码进行告警屏蔽，可屏蔽的告警包括：短期冲突告警、低高度告警、（临时）危险区侵入告警、偏离飞行指令高度告警、进近下滑道监视告警、五边侵入告警。

<img src="/images/LES-air-traffic-control-automation-guide/SSR_INHIB.jpg" width="300" alt="SSR_INHIB" />

#### 席位告警状态 PSS

这里可以查看莱斯系统逻辑组各个席位的告警状态，在系统备用的情况下，除了 RA 告警被 OFF 掉，其余告警均设为 VIS。

- ON：声音＋标牌闪烁
- VIS：标牌闪烁 VISUAL
- OFF：不提示

这里写错了，中期冲突告警就是 MTCD，正如我们系统中显示的那样，而不是图中 PSS 窗口的 MTCA（注意：短期冲突告警是 STCA，不要把缩写搞混了）。

<img src="/images/LES-air-traffic-control-automation-guide/PSS.jpg" width="600" alt="PSS" />

| 告警缩写 | 告警名称                  |
| -------- | ------------------------- |
| STCA     | 短期冲突告警              |
| MSAW     | 最低高度告警              |
| DAIW     | 危险区域告警              |
| RAM      | 偏航告警                  |
| CLAM     | 高度偏离告警              |
| NTZ      | 非侵入区告警/五边侵入告警 |
| MTCD     | 中期冲突告警              |
| COS      | 目标丢失告警              |
| APM      | 下滑道监视告警            |
| DCW      | 起飞冲突告警              |
| ADSB     | ADS-B 告警提示            |
| RVSM     | RVSM 告警提示             |
| WFSCA    | 尾流间隔告警              |
| RA       | 目标碰撞告警              |
| DUPE     | 重码告警                  |
| IC       | 相关不一致告警            |
| RIW      | 错误跑道告警              |

#### 签到情况 SIGN

> 用户签到只在管制席可用，主任席无用户签到框。用户签到信息窗口，每台席位的用户签到和退出信息显示在签到信息列表中。

<img src="/images/LES-air-traffic-control-automation-guide/SIGN.jpg" width="400" alt="SIGN" />

#### 切换 SWITCH

> 系统提供 AIDC 移交开关功能、进程单自动打印功能、协调状态提示显示开关、主飞行数据处理机开关以及主监视数据处理机开关。

其中：DCL 是 数字放行，ADS-B/S 可能是适用 S mode 的 ADS-B。而在我们的系统中，Print Strip 按钮变成了 SSR ALLOC。

![SWICTH](/images/LES-air-traffic-control-automation-guide/SWICTH.jpg)

#### 主备切换 SPARE

目前莱斯是备用系统，不过可能是因为没有登录用户的缘故，所以按钮是灰色的。

> 在主任席位可进行主备系统切换的开关设置。
>
> 当作为备份系统时，默认所有扇区的自动移交出和自动移交入开关均为开，系统只进行收报不发报，所有类型告警在席位的显示状态均为「VISUAL」。而当系统切换为主用系统时默认所有扇区的自动移交开关为关，系统可自动发报，所有告警类型的席位提示方式为「ON」。而当系统切换为主备中间状态「STAND」后，系统不向外发送飞行计划同步信息，同时也不接收外系统送过来的飞行计划同步信息；

<img src="/images/LES-air-traffic-control-automation-guide/SPARE.jpg" width="400" alt="SPARE" />

### 第二/局部 窗口 Second

点击后可打开一个可调节其大小的小窗口，其内同样可对航迹进行管制操作，包括移交、弹出飞行计划窗口等。

<img src="/images/LES-air-traffic-control-automation-guide/second.jpg" width="600" alt="second" />

### 飞行计划 FPL

这是非常重要的一部份，在此可了解航班的状态。

> 飞行计划窗口分为两大类显示区域：**功能按钮区**和**数据显示区**。用户可以通过功能区创建、修改和删除飞行计划等，在数据显示区可以显示飞行计划的详细内容：

![FPL1](/images/LES-air-traffic-control-automation-guide/FPL1.jpg)

![FPL2](/images/LES-air-traffic-control-automation-guide/FPL2.jpg)

#### 飞行计划数据栏目

| 栏目            | 描述                                                         | 格式/个字符 |
| --------------- | ------------------------------------------------------------ | ----------- |
| 计划状态 STATUS | INHB：              抑制<br/>NACT：              静止<br/>PREA：               预激活<br/>COOR：              协调<br/>CONT：               激活<br/>HND：                移交<br/>FIN：                   结束<br/>FUR：                  未来<br/>INHI：                 抑制<br/>SUPS：                悬挂<br/>UCTL：                未管制 | 4           |
| CSEC            | 责任扇区或下一个扇区                                         | 5           |
| PSSR            | 以前用的二次代码（模式和 SSR）                               | 5           |
| ASSR            | 分配的二次代码（模式和 SSR）                                 | 5           |
| OPDATA          | 标牌上的自由文本（管制自填）                                 | 10          |
| ATD             | 实际起飞时间                                                 | 4           |
| ACID            | 航班号                                                       | 8           |
| FRUL            | 飞行规则                                                     | 1           |
| FTYP            | 飞行类型                                                     | 1           |
| TYP -N          | 机型架数                                                     | 2           |
| TYP -AC-TYP     | 机型                                                         | 4           |
| TYP -WTUR       | 尾流                                                         | 1           |
| ATOW            | Actual Take-off Weight                                       |             |
| EQUIP           | 设备                                                         | 8           |
| ADEP            | 起飞机场                                                     | 4           |
| EOBT            | Estimated Off-Block Time                                     |             |
| ETD             | 预计起飞时间                                                 | 4           |
| TAS             | 真空速（巡航速度）                                           | 5           |
| RFL             | 请求飞行高度（巡航高度）                                     | 5           |
| RTE             | 航线（ICAO 15），点击后可以显示全程航路报告点的名称、时间和高度 | 1023        |
| ADES            | 目的机场                                                     | 4           |
| EET             | 飞行总时间                                                   | 4           |
| ALTN            | 备降机场                                                     | 9           |
| REGN            |                                                              |             |
| DC              | DC SFRA                                                      |             |
| OTHER           | ICAO 18 去除 REG                                             | 250         |
| Update Time     | 计划更新时间  形如：HH:MM:SS                                 | 8           |
| Obsolete Status | 如果显示的计划不是最新的，显示 Not Current                   |             |

#### 飞行计划功能按钮

| NEXT 后的第一排 | 具体内容                       |
| --------------- | ------------------------------ |
| NEXT            | 显示相同航班号的下一条飞行计划 |
| CRE             | 创建飞行计划                   |
| RPL             | 重复性飞行计划显示             |
| EST             | 人工协调                       |
| DEP             | 人工起飞                       |
| ACC             | 移交接收或强制接收             |
| JTRF            | 对航班进行强制授权扇区功能     |
| MCOU            | 人工航迹相关                   |
| INHI            | 抑制状态功能                   |
| TRS             | ATC 电报发送                   |
| P/CFL           | 预计剖面飞行高度/指令飞行高度  |
| FMF             | 飞行信息功能                   |

| PREV 后的第二排 | 具体内容                       |
| --------------- | ------------------------------ |
| PREV            | 显示相同航班号的上一条飞行计划 |
| CRTE            | 航路创建                       |
| STE             | 模板库中计划显示               |
| BACK            | 飞行计划取消协调               |
| CDEP            | 取消起飞                       |
| HND             | 移交                           |
| HNDI            | 修改航班移交模式               |
| DCOU            | 人工取消雷达相关               |
| CNL             | 飞行计划取消                   |
| AUR             | Airspace Usage Requirements    |
| PRL             | 飞行员报告高度                 |
| HSTR            | 飞行计划历史信息               |

| 计划次序 后的第三排 | 具体内容             |
| ------------------- | -------------------- |
| MOD                 | 飞行计划修改         |
| PAST                | 过去计划库中计划显示 |
| INAT                | 计划变为静止功能     |
| TMA                 | 终端区信息           |
| EXIT                | 退出条件             |
| HOLD                | 等待功能             |
| ATA                 | 实际落地时间         |
| SUP                 | 添加补充信息         |
| /                   | /                    |
| TAS                 | 真空速               |
| /                   | /                    |

点击功能按钮后会出现的**特殊内容**如下：

| 栏目 | 描述                                                         | 格式/个字符 |
| ---- | ------------------------------------------------------------ | ----------- |
| GATE | 停机位                                                       | 4           |
| ARWY | 到达跑道                                                     | 3           |
| DRWY | 起飞跑道                                                     | 3           |
| SID  | 标准起飞航线                                                 | 5           |
| STAR | 标准到达航线                                                 | 5           |
| CFL  | 指令飞行高度                                                 | 5           |
| PCFL | 预计剖面计算高度                                             | 5           |
| BPN  | 入界协调点，对于本管制区起飞的飞行计划为空白                 | 11          |
| ETN  | 预计起飞时间，预计或实际飞越 BPN 的时间，对于本管制区起飞的飞行计划等于 ETD | 4           |
| BPX  | 出界协调点，对于进港飞行计划，BPX 为空白                     | 11          |
| ETX  | 预计或实际飞越 BPX 的时间                                    | 4           |
| XFL  | 出界协调点飞行高度                                           | 4           |
| PRL  | 飞行员报告高度                                               | 5           |
| FMF  | 飞行情报                                                     | 50          |
| SUP  | 补充信息                                                     | 100         |

#### 菜单栏选项

| 菜单栏      | 具体内容           |
| ----------- | ------------------ |
| CLEAR       | 清除功能           |
| FPCP        |                    |
| Trial       |                    |
| Add/Del     |                    |
| PRINT STRIP | 打印进程单         |
| PRINT FDR   |                    |
| Enter       | 确认执行当前的操作 |
| Escape      | 取消当前功能       |

### 电子进程单 SSTP A

> 电子进程单设置窗口允许管制员选择某种类型的电子进程单列表进行显示。

详细信息请参考资料《NUMEN3000 自动化系统 V3.5 版本 - 管制员席操作手册》。

![SSTPA](/images/LES-air-traffic-control-automation-guide/SSTPA.jpg)

![SSTPA_format](/images/LES-air-traffic-control-automation-guide/SSTPA_format.jpg)

### 丢失航迹列表 Lost

> 当本席位管制的与飞行计划相关的航空器出现丢失监视目标从而导致与飞行计划自动去相关时，相关的航班信息会出现在目标丢失列表内，每个列表项显示丢失航班的航班号和对于的应答机编码，若目标再次出现并且与飞行计划相关上，相关的丢失信息将自动从列表中消失。出现在列表内的丢失目标信息在系统下线设置 VSP 时间后自动从列表内删除。

![Lost](/images/LES-air-traffic-control-automation-guide/Lost.jpg)

### 雷达简标牌列表 Tag List

> 在具备管制权限的席位创建了雷达简标牌后相关的雷达简标牌信息会显示 在简标牌列表内，如下图所示：
>
> 在列表内显示雷达简标牌的航班号、二次代码、飞行指令高度和自由文本信息。鼠标左键点击列表信息的航班号字段可打开雷达简标牌编辑窗口；鼠标中键点击航班号可删除雷达简标牌。

![Tag_List](/images/LES-air-traffic-control-automation-guide/Tag_List.jpg)

### 雷达简标牌删除 Del Tag

这里可以删除雷达简标牌。

### 人工挂标牌 Rad Tag

这里可以对未相关航迹人工挂标牌。

### 下行数据参数 DAP

根据这个名字，我猜这个简写的英文应该是 Downlink Aircraft Parameters，航空器数据下行参数。

> DAP 窗口中的数据项内容可通过数据库离线配置，打开下行数据显示窗口后，窗口中的内容会随着雷达所送过来的数据不断地进行更新。

<img src="/images/LES-air-traffic-control-automation-guide/DAP.jpg" width="600" alt="DAP" />

### 航班排序管理 TFC MGT

我猜英文的简写可能是 Traffic Management，交通管理。

<img src="/images/LES-air-traffic-control-automation-guide/TFCMGT.jpg" width="600" alt="TFCMGT" />

> 航班排序管理窗口可以根据选择的定义点显示电子进程单，并且查询出的电子进程单显示在窗口的自动排序区，自动排序区按照过点时间从大到小进行排序，窗口中间有一条隔离条，下方是人工排序区，可以将自动区的一条或多条进程单移到人工区，在人工区可以人工排序。
>
> 对于在该窗口中选择的本场机场起降和相应跑道的条件，在各个席位同条件的窗口中人工区的单子是同步顺序的，也就是说一个席位调整了人工区的顺序， 其他席位同步调整。
>
> 该窗口中对于本场起飞的航班可以修改全局信息。

### 仪表着陆系统 ILS

在这里可以监视航班在进近下滑道道状态：

> 在管制席位提供仪表着落系统功能，在 ILS 窗口中提供航空器在降落阶段相对于指定跑道的剖面图和俯视图。

<img src="/images/LES-air-traffic-control-automation-guide/ILS.jpg" width="600" alt="ILS" />


### 动态文本 TXA

这个缩写应该是 Text Entry，文本输入。通过这个按钮，我们可以在态势界面的任何地方留下提示文字。

> CLW 窗口中设有 TXA 按钮，点击 TXA 按钮，在 SDW 窗口中鼠标左键点击一处，弹出文本输入框可进行输入。在 Setup 窗口可设置两种文本字体。

<img src="/images/LES-air-traffic-control-automation-guide/TXA.jpg" width="400" alt="TXA" />

### 测距 BRL-L/BRL CLEAR

通过这个按钮，可以对两个目标进行测距操作，测量标牌的显示内容分别为：

- 直线距离
- 方位差
- 高度差

> 可实现两航迹间相对位置测量，航迹与固定点间相对位置测量，两固定点位置间相对位置测量，在终点处显示测量标牌，每种测量标牌显示内容不一样，**显示单位与席位当前显示单位制式一致**。鼠标左键点击测量标牌，可再次调节测量的终点；鼠标右键可调整测量标牌的位置；鼠标中键点击测量标牌，可删除 BRL 测量线。

<img src="/images/LES-air-traffic-control-automation-guide/BRL.jpg" width="400" alt="BRL" />

### ETO 告警方式设置 WAYP ON/OFF/VIS

ETO 是 预计结束时间 Estimated Time Over 的缩写，而按钮上的缩写 WAYP 推测是 Waypoint 航路点的缩写，指预计经过航路点的时间告警显示方式设置。

> ETO 在席位的告警方式共有三种设置：ON、VISUAL 和 OFF；当选择“ON”时，有 ETO 告警的航迹将出现告警字段闪烁显示并且发出告警提示音；当选择“VISUAL”时，告警航迹仅显示告警闪烁；当选择“OFF”时不显示 ETO 告警。 
>
> 该设置对席位中所有出现 ETO 告警的航迹有效。

### 数字放行列表 DCL LIST

> 系统对预激活或协调的飞行计划收到 RCD 报文，点击塔台席“DCL LIST”按钮，弹出“DCL LIST”界面。
>
> 窗口中“Sort ACID”和“Sort ETD”按钮为排序按钮，“Sort ACID”按钮点击后可以按照航班号字母顺序从小到大进行排序，“Sort ETD”按钮点击后可以按照 ETD 时间从小到大进行排序。窗口中显示的内容为 DCL 状态、航班号、预计起飞时间、状态、目的机场、停机位、跑道、离场程序、巡航高度。

其中 ETD 代表 预计到达时间 Estimated Time of Departure。

<img src="/images/LES-air-traffic-control-automation-guide/DCL.jpg" width="600" alt="DCL" />

### 跑道预测 RUNWAY QUERY

> 系统可以根据输入的机场名称预测当前时间至 2 小时之后时间段的跑道流量架次 。蓝色柱状体表示起飞架次，棕色柱状体表示落地架次，黄色部分表示超过告警值的架次。

<img src="/images/LES-air-traffic-control-automation-guide/RUNWAYQUERY.jpg" width="600" alt="RUNWAYQUERY" />

### 屏幕配置保存 SAVE LOAD

<img src="/images/LES-air-traffic-control-automation-guide/SAVELOAD.jpg" width="400" alt="SAVELOAD" />

> 屏幕配置是管制席位显示界面在某一时刻的显示布局情况，这些情况包括：地图显示中心、显示范围、系统地图显示调用、距离环、航迹速度矢量线、航迹历史点选择、航迹标牌字体大小以及过滤条件设置。
>
> 在管制席位提供本席位当前屏幕配置的存储功能，在席位重启后，可进行人工加载。

### 登录 LogIn

<img src="/images/LES-air-traffic-control-automation-guide/login.jpg" width="600" alt="login" />

在 CLW 的最右侧按钮是主任席的登录按钮，莱斯手册中所说的在 Info 窗口中打开 LogIn 窗口是**错的**。

输入用户名和密码登录以后，LogIn 按钮会变为 LogOut 按钮。

### 管制-机师数据链 CPDLC

![CPDLC](/images/LES-air-traffic-control-automation-guide/CPDLC.jpg)

## 略缩词列表

| 略缩词                         | 英文对应                                                     |
| ------------------------------ | ------------------------------------------------------------ |
|                                |                                                              |
| 通用信息窗口 GIW               | General Information Window                                   |
| 态势显示窗口 SDW               | Situational Display Window                                   |
| 命令行窗口 CLW                 | Command Line Window                                          |
| 回放控制 RepCtrl               | Replay Control                                               |
| 塔台监控席逻辑席位 TWSMPLGP    | Tower System Monitor Position - Logical Position             |
| 速度矢量线 VEL                 | Velocity Vector                                              |
| 短航路探针 SRP                 | Short Range Probe                                            |
| 尾迹点设置 HIST                | Historical Trail                                             |
| 空管编码 ACID                  | Aircraft Identification                                      |
| 上升下降率显示设置 DIR         | Descent or Increase Rate                                     |
| 飞行计划航迹显示过滤 FPASD     | Flight Plan Air Situation Display                            |
| 非管制航迹的标识符 NJUR Symbol | Non-Jurisdiction Route                                       |
| 二次代码 SSR code              | Secondary Surveillance Radar code                            |
| 修正海平面气压高度 QNH         | A Q(uestion) code, to concisely ask for <br />**atmospheric pressure at mean sea level** (MSL) |
| 限制区域 RMAP                  | Restricted Map                                               |
| 容量 CAP                       | Capacity                                                     |
| 容量超限警告 CAP WARN MSG      | Capacity Warning Message                                     |
| 标准离场程序 SID               | Standard Instrument Departure Routes                         |
| 标准进场程序 STAR              | Standard Arrival Routes                                      |
| 二次代码告警抑制 SSR INHIB     | SSR Inhibit                                                  |
| 缩小垂直最小间隔 RVSM          | Reduced Vertical Separation Minimum                          |
| 短期冲突告警 STCA              | Short Term Conflict Alert                                    |
| 最低高度告警 MSAW              | Minimum Safe Altitude Warning                                |
| 危险区域告警 DAIW              | Danger area infringement warning                             |
| 偏航告警 RAM                   | Route adherence monitoring                                   |
| 高度偏离告警 CLAM              | Cleared level adherence monitoring                           |
| 非侵入区告警/五边侵入告警 NTZ  | No Transgression Zone                                        |
| 中期冲突告警 MTCD              | Medium Term Conflict Detection                               |
| 目标丢失告警 COS               | [Confliction of Separation/Continued Operational Safety]     |
| 下滑道监视告警 APM             | Approach Path Monitor                                        |
| 起飞冲突告警 DCW               | Departure Conflict Warning                                   |
| ADS-B 告警提示 ADSB            | [Automatic Dependent Surveillance-Broadcast]                 |
| RVSM 告警提示 RVSM             | [Reduced Vertical Separation Minimum]                        |
| 尾流间隔告警 WFSCA             | [Wake Final Separation Conflict Alert]                       |
| 目标碰撞告警 RA                | TCAS RA<br />Traffic Collision Avoidance System Resolution Advisory |
| 重码告警 DUPE                  | Duplicate (A duplicated SSR code)                            |
| 相关不一致告警 IC              | Inconsistent Correlation                                     |
| 错误跑道告警 RIW               | [Runway Incursion Warning]                                   |
| 数字放行 DCL                   | Departure Clearance                                          |
| 预计离港时间 ETD               | Estimated Time of Departure                                  |
| 预计到达时间 ETA               | Estimated Time of Arrival                                    |
| 实际离港时间 ATD               | Actual Time of Departure                                     |
| 实际到达时间 ATA               | Actual Time of Arrival                                       |
| 空域使用请求 AUR               | Airspace Usage Requirements                                  |
| 实际起飞重量 ATOW              | Actual Take-off Weight                                       |
| 可变系统参数 VSP               | Variable System Parameter                                    |
| 航空器下行参数 DAP             | Downlink Aircraft Parameters                                 |
| 飞行数据记录 FDR               | light Data Recorder                                          |
| 仪表着陆系统 ILS               | Instrument Landing System                                    |
| 预计结束时间 ETO               | Estimated Time Over                                          |
| 预计着陆时间 ELDT              | Estimated Landing Time                                       |
| 到达管理 AMAN                  | Arrival Manager                                              |
| 离港管理 DMAN                  | Departure Manager                                            |
| 管制员-飞行员数据链通信 CPDLC  | Controller-Pilot Data Link Communications                    |



## 参考资料

1. 《1.1 新终端欧洲猫自动化 V10 系统（塔台）应知应会》

2. 《1.2 新终端莱斯自动化 V3.5 系统（塔台）维护规程》

3. 《NUMEN3000 自动化系统 V3.5 版本 - 管制员席操作手册》

4. *国际民航组织有关 SID/STAR 标准用语的修订*，**中国民用航空局 CAAC**，2012-06-06
    https://www.caac.gov.cn/big5/www.caac.gov.cn/XWZX/GJZX/201312/t20131204_16047.html

5. *SID/STAR Home*，**ICAO**
    https://www.icao.int/airnavigation/sidstar/Pages/default.aspx

6. *SIDs and STARs*，**SKYbrary**
    https://skybrary.aero/articles/sids-and-stars

7. *Traffic Alert and Collision Avoidance System (TCAS)*，**National Business Aviation Association (NBAA)**
    https://nbaa.org/aircraft-operations/communications-navigation-surveillance-cns/tcas/

8. *What are the differences among ETO, ETA, and ELDT?*，**StackExchange**，Aviation
    https://aviation.stackexchange.com/questions/91912/what-are-the-differences-among-eto-eta-and-eldt

9. *Controller–pilot data link communications*，**Wikipedia**
    https://en.wikipedia.org/wiki/Controller%E2%80%93pilot_data_link_communications

10. *聊聊空管自动化系统的各种告警功能*，**VATSIM 中国分部论坛**，1681250 Jiaqing Jiang，2022-11
    https://community.vatprc.net/t/topic/7430
