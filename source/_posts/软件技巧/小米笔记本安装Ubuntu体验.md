---
title: 小米笔记本安装Ubuntu体验
toc: true
date: 2019-07-20 23:25:39
tags:
categories:
---

迫于 Windows 的开发体验，我还是服软了。这让我这软粉老脸往哪搁

## 首先关闭小米笔记本的 secure boot

按开机键后狂按 F2 进入 BIOS 界面

在BIOS里面设置，就在安全选项的第一个，设置一个新的密码

具体步骤：

1、找到安全选项，选择security里选择set supervisor password，设置好密码。

2、把下面的secure boot改成disabled。

3、以后进BIOS就需要这个密码，一定要记好密码。

关闭之后才能安装显卡，不然安装完成显卡也木有用

## 安装结束后会出现卡 Logo 或 关机出现 watchdog:bug

### 根本原因

很明显的一个显卡的问题，总是在图形界面出毛病。通常是因为小米本用的是GTX系列的 Nvidia 显卡，但是 Ubuntu 下的 Nvidia 显卡驱动是开源的 nouveau.(截至目前已经有消息出现官方支持的 Nvidia 显卡驱动) 

### 先禁用原先的 nouveau

```bash
sudo chmod 666 /etc/modprobe.d/blacklist.conf
sudo vi /etc/modprobe.d/blacklist.conf
```

在`blacklist.conf`最后添加几行：

```shell
blacklist vga16fb
blacklist nouveau
blacklist rivafb
blacklist nvidiafb
blacklist rivatv
```
最后保存文件之后记得把文件属性复原

```bash
sudo chmod 644 /etc/modprobe.d/blacklist.conf
```

接着更新一下内核
```bash
sudo update-initramfs -u
reboot
```

重启之后应该还可以进入图形界面，因为还有一张 Intel 的集成显卡，重启之后确认一下 nouveau 是否已经被屏蔽掉

`lsmode | greo nouveau`

### 安装 Nvidia 驱动

添加Graphic Drivers PPA

```bash
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
```

再查看推荐的驱动版本

```bash
ubuntu-drivers devices
```

找到`recommend`的 nvidia-driver-XXX 然后准备开始安装

```bash
sudo apt install nvidia-driver-XXX
reboot
```

Tips: 这里公网说要在非图形化界面进行安装，但是我直接执行了以上也没有什么异常。

并且我发现当前的 `Ubuntu 18.04.2` 并没有 TTY 界面

正常 `ctrl+alt+f3,4,5` 就可以启动非图形化界面

可以试试以下解决方案

编辑 `/etc/default/grub`

修改GRUB_CMDLINE_LINUX_DEFAULT的值由“quiet splash”为”nomodest”

```bash
sudo update-grub // 更新grub
reboot
```

## 一些其他优化

### 安装 Chomre 浏览器

```bash
sudo wget http://www.linuxidc.com/files/repo/google-chrome.list -P /etc/apt/sources.list.d/

wget -q -O - https://dl.google.com/linux/linux_signing_key.pub  | sudo apt-key add -

sudo apt update

sudo apt install google-chrome-stable
```

### 卸载自带的软件

```bash
dkpg --list // 查看所有安装的软件
sudo apt purge xxx // 卸载
```

### 初次安装出现的字体错误

安装成功后输入的 `门` 字应该是日本字体，修复方法参考如下：

```bash
sudo vim /etc/fonts/conf.d/64-language-selector-prefer.conf 
```


这个文件配置了当前字体的优先级，JP 的优先级高于 SC 所以会出现字体出错的情况

## 参考资料
> - [Ubuntu 不能进 tty](https://zhuanlan.zhihu.com/p/49607213)
> - [关闭小米笔记本安全启动](http://bbs.xiaomi.cn/t-35635269)
> - [Ubuntu 18.04 安装N卡](https://blog.csdn.net/hustcw98/article/details/79324917)
> - [Ubuntu 安装 Chrome 浏览器](https://jingyan.baidu.com/article/148a1921e1910a4d71c3b1d9.html)
> - [Ubuntu 字体出错](http://www.mamicode.com/info-detail-2553199.html)