---
title: archlinux安装
top: false
mathjax: true
date: 2020-04-018 18:37:41
categories:
- 其他
---

-----

# Arch Linux 系统安装

官网wiki ：[官方指南 简体中文](https://wiki.archlinux.org/index.php/Installation_guide_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))



# 连接网络

连接前查看设备  `#ip link` `#ip a`

对于无线网络，请确保无线网卡未被 [rfkill](https://wiki.archlinux.org/index.php/Rfkill) 禁用。

硬件阻塞 Hard blocked

rfkill unblock

## 有线连接

要连接到网络:

- 有线以太网 —— 连接网线



相关文章

[使用systemd-networkd管理网络](https://lisongmin.github.io/os-systemd-networkd/)

## 无线连接

WiFi —— 使用 [iwctl](https://wiki.archlinux.org/index.php/Iwctl) 验证无线网络

无线网连接

```
iwctl
```

进入iwd模式，输入

```text
device list
```

查看你的网卡名字，这里假设是wlan0，输入

```text
station wlan0 scan
```

检查扫描网络，输入

```text
station wlan0 get-networks
```

查看网络名字，假设名字叫BUPT-portal，输入

```text
station wlan0 connect BUPT-portal
```

接着输入密码（如果有密码的话），输入

```text
exit
```

退出iwd模式

连接成功之后，检查可以连接到pacman源

```text
pacman -Syyy
```



相关文章

[Archlinux无线联网教程](https://www.cnblogs.com/vachester/p/5637027.html)



## 使用镜像

[Mirrors (简体中文)](https://wiki.archlinux.org/index.php/Mirrors_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E5%BC%BA%E5%88%B6_pacman_%E5%88%B7%E6%96%B0%E8%BD%AF%E4%BB%B6%E5%8C%85%E5%88%97%E8%A1%A8)

***

**按速度排序**

更快的源可以显著的提升pacman的性能,和arch的整体操作体验。可以使用 `rankmirrors` 将镜像列表按速度排列。但是`rankmirrors`不能测试这些源的速度。

`cd`到`/etc/pacman.d/`目录:

```
# cd /etc/pacman.d
```

备份已经存在的`/etc/pacman.d/mirrorlist`:

```
# cp mirrorlist mirrorlist.backup
```

编辑`/etc/pacman.d/mirrorlist.backup`，取消要测速镜像前的注释。

让rankmirrors带上参数`-n`对这个备份文件`mirrorlist.backup`执行操作,然后把输出重定向以方便生成一个新的/etc/pacman.d/mirrorlist源列表:

```
# rankmirrors -n 6 mirrorlist.backup > mirrorlist
```

**注意：** **-n 6**:将生成6个最接近的源，运行`rankmirrors -h`可查看所有可用选项。

**按速度和状态排序**

仅是使用最快的镜像服务器并不是一件好事，因为它们可能是过时的。我们更推荐先[#按速度排序](https://wiki.archlinux.org/index.php/Mirrors_(简体中文)#按速度排序)，然后在选出的镜像中按[#镜像状态](https://wiki.archlinux.org/index.php/Mirrors_(简体中文)#镜像状态)排序。

只要简单地访问它们的[#镜像状态](https://wiki.archlinux.org/index.php/Mirrors_(简体中文)#镜像状态)连接，然后将它们按照尽量新的顺序排序。将越新的镜像排到`/etc/pacman.d/mirrorlist`的越上面。如果镜像真的太过时了，别用它们（把它们注释掉，然后再[#按速度排序](https://wiki.archlinux.org/index.php/Mirrors_(简体中文)#按速度排序)），重复这么做，排除过时的镜像。最后将有6个又快又新的镜像。

当出现镜像问题是，应该重复上面的步骤。或者一段时间就重复一次以保持`/etc/pacman.d/mirrorlist`最新，即使没有镜像问题。

**使用 Reflector**

[Reflector](https://wiki.archlinux.org/index.php/Reflector)工具可以从[镜像状态](https://www.archlinux.org/mirrors/status/)页面自动获取最新的镜像列表，过滤掉未及时同步的镜像，然后按照速度排序覆盖`/etc/pacman.d/mirrorlist`。

***

命令：

```

```



# 更新系统时间

```
# timedatectl set-ntp true
```

使用 `timedatectl status` 检查服务状态。



# 建立硬盘分区

检查和分区

```
lsblk
```

```
cfdisk
```

分区格式化

```
mkfs.ext4
```

挂载分区

```
mount /dev/nvme0n1p5 /mnt
```

```
mkswap /dev/sdX2
swapon /dev/sdX2
```

```reStructuredText
mkdir /mnt/boot
```

```text
mount /dev/nvme0n1p2 /mnt/boot
```

挂载镜像

挂载，就是把设备依附到目录树上，linux下一切皆文件，挂载之后，这个分区就成了一个文件夹了。首先挂载根分区。

```text
mount /dev/sdb5 /mnt
```

由于需要使用UEFI引导，所以需要创建其挂载点并挂载。

```text
mkdir -p /mnt/boot/efi
mount /dev/sdb2 /mnt/boot/efi
```

还创建了home目录，所以也要挂载

```text
mount /dev/sdb7 /mnt/home
```



# 安装基本系统

## 安装相关软件

```
pacstrap /mnt base linux linux-firmware 
```

```
iwd
```



## 生成fstab文件

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

检查生成的fstab文件

```bash
cat /mnt/etc/fstab
```

## 正式配置新系统

切换到装好的系统

```bash
arch-chroot /mnt
```

# 进入系统



设置时区

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

或

```bash
timedatectl set-timezone Asia/Shanghai
```

同步硬件时钟

```bash
hwclock --systohc
```



设置locale

```bash
nano /etc/locale.gen
```

生成locale

```bash
locale-gen
```



创建并写入/etc/locale.conf文件

```bash
nano /etc/locale.conf 
```

填入内容，注意这里只能填这个

```bash
LANG=en_US.UTF-8
```

5.创建并写入hostname

```bash
nano /etc/hostname
```



修改hosts

7.为root用户创建密码

```text
passwd
```

然后输入并确认密码（linux终端的密码没有回显，输完直接回车就好）



## 创建启动器

安装基本的包，这里使用grub为启动器

```bash
pacman -S grub efibootmgr networkmanager network-manager-applet dialog wireless_tools wpa_supplicant os-prober mtools dosfstools ntfs-3g base-devel linux-headers reflector git
```

**如果你不知道这些包的作用，请务必确保输入的指令与上面的一致**



检查完毕回车，需要选择直接回车就好，等待安装结束



输入

```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=Arch
```



**为了让grub接管启动器之后还能正确地进入Windows系统**

首先创建文件夹

```bash
mkdir /mnt/windows10
```

挂载Windows系统所处的区，我这里是nvme0n1p4

```bash
mount /dev/nvme0n1p4 /mnt/windows10
```

生成grub.cfg

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

## 退出新系统并取消挂载

```
exit
```

或者

```
umount -a
```



```
reboot
```

# 进入系统

启动网络服务

```text
systemctl enable --now NetworkManager
```

设置WiFi

```text
nmtui
```



## 新建用户并授权

```text
useradd -m -G wheel mir
```

wheel后面是你的用户名，这里输入的是mir

为用户创建密码

```text
passwd mir
```

输入并确认密码

授权

```text
EDITOR=nano visudo
```

***

使用以下命令添加一个用户

```text
useradd -m -G "附加组" -s "登陆shell" "用户名"
```

其中：

- `-m`/`--create-home`：创建用户主目录/home/[用户名]
- `-G`/`--groups`：用户要加入的附加组列表；使用逗号分隔多个组，不要添加空格；如果不设置，用户仅仅加入初始组。
- `-s`/`--shell`：用户默认登录shell的路径

1. 赋予用户root权限

- 安装sudo软件包: `pacman -S sudo`
- 在`/etc/sudoers`文件中的`root ALL=(ALL) ALL`行下添加`yourname ALL=(ALL) ALL`

## 安装显卡驱动

安装AMD集显驱动

```text
pacman -S xf86-video-amdgpu
```

安装NVIDIA独显驱动

```text
pacman -S nvidia nvidia-utils
```

**其他驱动**

声卡驱动

```
sudo pacman -S alsa-utils pulseaudio-alsa
```

触摸板驱动

```
sudo pacman -S xf86-input-libinput
```



## 安装Display Server

这里用的是开源世界最为流行的xorg

```text
pacman -S xorg
```

## 安装字体

```
pacman -S noto-fonts-cjk
```



# 卸载

删除相关分区

删除UEFI 引导 win使用Easy UEFI



# 安装软件

## 安装国内软件包

添加archlinuxcn源 在 /etc/pacman.conf 文件末尾添加以下两行：

```
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```


然后安装 GPG key

```
sudo pacman -Syyu
sudo pacman -S archlinuxcn-keyring
```





## 中文输入法安装

官网文档： 

字体安装

```
sudo pacman -S noto-fonts-cjk wqy-microhei wqy-microhei-lite wqy-bitmapfont
sudo pacman -S wqy-zenhei ttf-arphic-ukai ttf-arphic-uming adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts
```

安装fcitx

```text
sudo pacman -S fcitx fcitx-im fcitx-googlepinyin fcitx-configtool
```

在家目录下创建`.xprofile`文件并写入以下内容

```
export XIM=fcitx
export XIM_PROGRAM=fcitx
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

开机启动



## 声音

[Arch Linux ALSA声音系统](https://cloud-atlas.readthedocs.io/zh_CN/latest/linux/arch_linux/archlinux_alsa.html)

安装alsa-utitilts
运行alsamixer
保存设置好的音量等：alsactl store
要点：一定要记得，音轨下面的M表示静音。通过按m取消静音。





[Arch Linux 安装完成后配置声音](https://xlui.me/t/archlinux-xfce4-alsa/)

## 安装zsh 

安装zsh

```
sudo pacman -S zsh
```

## 安装 oh-my-zsh

[arch zsh](https://wiki.archlinux.org/index.php/Zsh_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

[wiki](https://github.com/ohmyzsh/ohmyzsh/wiki)

```
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

安装历史记录插件和语法检查插件

```
cd ~/.oh-my-zsh/plugins
git clone git://github.com/zsh-users/zsh-autosuggestions.git
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
```

下载好后在`～/.zshrc`文件中加入上述插件

找到`plugins=(git)`，改为如下（sudo插件无须下载，效果为连按两次`esc`键给命令加上`sudo`）

```text
plugins=(
    git
    sudo
    zsh-syntax-highlighting
    zsh-autosuggestions
)
```

使插件生效

```text
source ~/.zshrc
```

**改变默认的Shell**

```
chsh -s /bin/zsh
```



## suckless 系列软件安装

## dwm

```
# git clone git://git.suckless.org/dwm
# git clone git://git.suckless.org/st
# git clone git://git.suckless.org/dmenu
```

[arch wiki](https://wiki.archlinux.org/index.php/Dwm_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))



### patch 补丁

透明度 窗口 每个标签 临时窗口



### 状态栏配置



**字体配置**



终端配置



## 按键映射

https://qastack.cn/superuser/566871/how-to-map-the-caps-lock-key-to-escape-key-in-arch-linux

caps 和esc



相关文章：

[ArchLinux dwm的安装和配置](https://www.shuzhiduo.com/A/kmzLAAwBJG/)

[DWM——Linux 上的桌面管理器](http://lanhin.xyz/2014/09/24/dwm-linux-%E4%B8%8A%E7%9A%84%E6%A1%8C%E9%9D%A2%E7%AE%A1%E7%90%86%E5%99%A8/)

[linux下的dwm的基本安装配置](https://blog.csdn.net/u010563350/article/details/106325331)

[简单可用Arch Linux安装 第二部分](https://zhuanlan.zhihu.com/p/165020957)

https://gitee.com/xiexie1993/dwm



## latex 安装



# 其他管理软件安装及配置

## compton picom 窗口渲染器



## ranger



# 状态栏 输入法 trayer



## lazygit





## 终端 alacritty



## 键盘映射



# vim 配置



## 相关参考

[如何将 Vim 剪贴板里面的东西粘贴到 Vim 之外的地方？](https://www.zhihu.com/question/19863631)