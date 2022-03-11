---
layout: post
title: Termux： 属于代码人的生产力工具
categories: [Termux]
description: 本文将书写本人最近这段时间接触Termux的经历以及搭建教程
keywords: Linux, Termux
---

## 前言

前些日子我第一次入手了一台安卓平板，还顺带购买了一个键盘套。看着这个胜似笔记本电脑的配置，我在想作为计算机类专业大学生如果能够用它来敲代码，那以后的日子就不需要背着沉重的电脑跑来跑去。
随后当我接触了Termux这款软件后，我深受感慨，就是这样一款软件，彻头彻尾改变了这块板子的价值。

## 运行在安卓系统上的Linux系统——Termux基础界面

Termux软件首次打开后，看到的便是黑底白字的标配代码界面。

![](/img/termux/1646976019262_012141.jpg)

这是基于安卓系统的阉割版Linux，但已经包含所有常用的linux命令了，还可以编辑启动时代码，设置快捷命令等linux基础操作。

*第一次进入时记得输入`pkg update`或`pkg upgrade`来更新软件包。*

另外，我们还可以通过pkg或者apt命令来安装一些基础软件包，不论是文本编辑器vim、nano，编译器clang、python3，下载工具wget、curl、git，都包含在Termux自带的源里。

不仅如此，还可以使用命令`termux-setup-storage`来实现对本地安卓文件的访问。

*但这样会涉及到数据安全，三思而后行！*

## 利用Proot体验完整的linux系统并搭建Code Server实现可视化编程环境

然而光这些还无法满足我的需求，后来了解到一个神奇的软件包proot-distro，通过`pkg install proot-distro`便可安装这个软件包。

凭借这个软件包，便可安装Arch linux、Debian、Ubuntu等主流linux系统了。

![](/img/termux/screenshot.jpg)

在Github上有个项目Code Server，该项目旨于实现基于微软Visual Studio Code的远程调试功能。在Release栏里可以找到已经编译好的deb和rpm安装包，通过本地下载或者linux上的wget下载下来安装后，便可用命令`code-server`启动服务器。

服务器默认端口号为8080且仅支持本地IP(127.0.0.1)，密码也是随机的，因此不得不用nano或者vim在后端编辑`nano ~/.config/code-server/config.yaml`来修改服务器配置。

如果想弄本地协同共享，需要把第一行ip改为
*0.0.0.0:<你想要的端口号>*

最后后端输入`ifconfig`找到自己的ipv4地址（192.168.开头）然后连接同一个网络的设备就可以在设备上的任意一个浏览器内通过 
*这个地址:端口号*
 进入Code Server界面了。

![](/img/termux/1646975979473_012121.jpg)

另外本机可以通过
*127.0.0.1:端口号*
来进入，这样即使没有连接网络也可以使用Code Server。

随后在插件栏搜索安装Code Runner插件，就可以顺利编译运行代码文件了。

![](img/termux/1646975979460_012055.jpg)

但是Code Server还是有局限的，那就是没有cppdbg插件支持，因而无法对C语言进行调试（也可能是我的技术不够）

如果能为这个系统安装桌面环境，那么这些功能便不在话下了。

## 安装图形桌面环境 Udroid

*个人不推荐该方法的图形界面，如果真的是在实际操作建议跳转到下一节*

早期版本中Proot是有支持图形化桌面的Ubuntu系统的，但因部分原因在今年二月份被删掉了，因此这时不得不凭借Github上的[Udroid项目](https://github.com/RandomCoderOrg/ubuntu-on-android)来进行图形化界面Ubuntu的安装了。

通过项目README.md的内容步骤便可完成Udroid的安装。

出于安卓设备的性能原因，桌面环境当然不可能是PC端Ubuntu默认的GNOME桌面，而是比较简单复古的Xfce桌面。由于Termux并不自带图形显示器，便需要利用VNC来进行远程控制。

终端输入`vncserver-start`命令便可启动VNC服务，记下屏幕输出的端口号，随后安装第三方app VNC Viewer连接VNC服务器，IP输入
*localhost:端口号*
，便可实现远程连接。

![](/img/termux/1646975979432_011946.jpg)

这样就能进入图形化桌面环境了。

## 硬解的桌面环境

上节的Udroid的Xfce桌面环境是通过软件解算的，并没有调用设备GPU，因此体验又卡又慢，连用VLC看个1080p 60fps的视频都卡。

原本Termux是有硬件解算桌面环境的解决方案的，那就是利用xwayland软件包搭建基于Wayland的Xorg显示服务器，这个显示服务器传输利用的是硬解，安装Termux-X11便可接收显示信号。

然而不幸的是，xwayland软件包在今年二月份也被删除了，Wayland官方给的自编译xwayland方案费时费力，我试了两三天也没能编译成功。

这时只能另辟蹊径，在Google Play上找到了一个名为AndroNix的第三方app，这个app给予了多达8种linux系统，每种系统都可以自由选择Xfce、Lxde、LxQt三种桌面环境，随后直接复制脚本自动安装。

![](/img/termux/1646975979451_012034.jpg)

还好这些脚本是自带wayland显示服务器解决方案的，但Termux-X11是无法接收显示信号的，还得用VNC Viewer接收。

## 结尾

就这样经过一番折腾，Termux终于被我调教成了对我来说比较完美的状态，但我相信这并不是Termux的极限。相信之后我的技术水平提升到一定水准后，能够让这强大的app发挥更大的潜能。

![](/img/termux/1646975979442_012007.jpg)

