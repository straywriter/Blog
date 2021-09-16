---
title: archlinux软件安装
top: false
mathjax: true
date: 2020-04-018 18:37:41
categories:
- 其他
---

-----

# 软件包



1. 添加archlinuxcn源 在 /etc/pacman.conf 文件末尾添加以下两行：

```
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```


然后安装 GPG key

```
sudo pacman -Syu
sudo pacman -S archlinuxcn-keyring
```



## 添加archlinuxcn源

archlinuxcn源里头有许多中文用户常用的软件包，加上这个源我们才能安装许多常用的软件。使用vim打开`/etc/pacman.conf`，在结尾上加上：

```text
[archlinuxcn]
Server =  https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

刷新数据库`sudo pacman -Syy`，并且`sudo pacman -S archlinuxcn-keyring`。



安装yay以后我们就可以在AUR里面下载软件了，意味着我们能够下载更多的软件了。添加完archlinuxcn的源以后，运行：

```text
sudo pacman -S yay
```

默认的yay源不知道在地球的哪个角落，快整一个国内的源：

```bash
yay --aururl "https://aur.tuna.tsinghua.edu.cn" --save
```



程序启动器

```text
yay -S dmenu-git
```

## yay

`2、 yay`

[yay](https://github.com/Jguer/yay) 是下一个最好的 AUR 助手。它使用 Go 语言写成，宗旨是提供最少化用户输入的 `pacman` 界面、yaourt 式的搜索，而几乎没有任何依赖软件。

yay 的特性：

- `yay` 提供 AUR 表格补全，并且从 ABS 或 AUR 下载 PKGBUILD
- 支持收窄搜索，并且不需要引用 PKGBUILD 源
- `yay` 的二进制文件除了 `pacman` 以外别无依赖
- 提供先进的包依赖解决方案，以及在编译安装之后移除编译时的依赖
- 当在 `/etc/pacman.conf` 文件配置中启用了色彩时支持色彩输出
- `yay` 可被配置成只支持 AUR 或者 repo 里的软件包

安装 yay：

你可以从 `git` 克隆并编译安装。

```text
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

使用 yay：

搜索：

```text
yay -Ss <package-name>
```

安装：

```text
yay -S <package-name>
```

## **2 配置aur**

安装yay

```text
sudo pacman -S yay
```

修改`aururl`

```text
yay --aururl "https://aur.tuna.tsinghua.edu.cn" --save
```



```text
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```









## **3. 更换`shell`为`zsh`**

```text
chsh -s /bin/zsh
```



## 其他驱动

声卡驱动

```
sudo pacman -S alsa-utils pulseaudio-alsa
```

触摸板驱动

```
sudo pacman -S xf86-input-libinput
```



## 字体安装

```bash
sudo pacman -S noto-fonts-cjk wqy-microhei wqy-microhei-lite wqy-bitmapfont
sudo pacman -S wqy-zenhei ttf-arphic-ukai ttf-arphic-uming adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts
```



# 安装中文输入法

archlinux中文输入法框架有`fcitx`/`ibus`/`fcitx5`，其中`fcitx5`貌似是刚刚出现的，之前没见过，所以我就试了试，安装配置起来还挺简单的：

1. 安装`fcitx5`软件包
2. 创建`~/.pam_environment`并配置环境变量：

```text
GTK_IM_MODULE DEFAULT=fcitx
QT_IM_MODULE DEFAULT=fcitx
XMODIFIERS DEFAULT=@im=fcitx
```

1. 安装中文输入法引擎`fcitx5-rime`
2. 安装输入法模块来保证输入法在各种程序中使用：`pacman -S fcitx5-gtk fcitx5-qt5`
3. 启动fcitx5，右键fcitx5的系统托盘图标，点击`configure`添加rime输入法
4. `ctrl`+`space`键测试是否可以正常使用



安装fcitx

```text
sudo pacman -S fcitx fcitx-im fcitx-googlepinyin fcitx-configtool
```

```text
sudo pacman -S fcitx-im
sudo pacman -S fcitx-cofigtool
```

在家目录下创建`.xprofile`文件并写入以下内容

```text
export XIM=fcitx
export XIM_PROGRAM=fcitx
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

然后安装喜欢的输入法，这里推荐实用讯飞输入法（搜狗输入法有bug可能用不了）

讯飞输入法需要去aur里安装

```text
yay -S iflyime
```

重启查看`fcitx 配置`是否已经添加了讯飞输入法



或者

使用vim打开`~/.xprofile`，如果没有就新建一个，在最后添上：

```bash
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

然后需要在`~/.xinitrc`当中的exec的前面加上一句：

```text
[[ -f ~/.xprofile ]] && source ~/.xprofile 
```

加上了这句话，才会在启动窗口的时候载入`~/.xprofile`。

安装完重启见效果，如果开机的时候没有自动启动输入法，可以在上面的文件的最后再添一行`fcitx &`，表示在后台启动`fcitx`。



# zsh





# oh-my-zsh

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

```
wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O - | sh
```



```text
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```



安装历史记录插件和语法检查插件

```text
cd ~/.oh-my-zsh/plugins
git clone git://github.com/zsh-users/zsh-autosuggestions.git
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
```



先确保git，wget, curl已经安装

```text
sudo pacman -S git wget curl
```

安装ohmyzsh

```text
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

安装历史记录插件和语法检查插件

```text
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



## 插件

官方介绍的方法是直接`clone`仓库到`oh-my-zsh`自定义的插件目录，让其能够使用此插件，但这种方式有个问题，就是插件要想更新的话，需要重新`clone`或者`pull`。而我发现arch仓库中是有这两个插件的，那我们使用仓库中的插件就可以跟着仓库一起更新了。



```undefined
yay -S zsh-syntax-highlighting zsh-autosuggestions
```

这两个是`zsh`插件，用上面的方式配置是不行的，因为`oh-my-zsh`找不到这两个插件（会报plugin not found）。为此我们要进行一下特殊处理，创建这两个插件的符号链接到`oh-my-zsh`的自定义插件目录。



```undefined
sudo ln -s /usr/share/zsh/plugins/zsh-syntax-highlighting /usr/share/oh-my-zsh/custom/plugins/
sudo ln -s /usr/share/zsh/plugins/zsh-autosuggestions /usr/share/oh-my-zsh/custom/plugins/
```

打开一个新终端，接下来就可以使用功能强大的`zsh`了。



## 其他

**如何检测是安装zsh：**

```
zsh --version;
```

**on-my-zsh 安装**

**一、自动安装**

```
wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O - | sh
```

 

**二、手动安装**

**1. 克隆仓库**

```
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
```

**2. 如果你已存在~/.zshrc文件，则备份现有的~/.zshrc文件**

```
cp ~/.zshrc ~/.zshrc.orig
```

 

**3. 创建一个新的zsh配置文件**

```
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

 

**4. 改变默认的Shell**

```
chsh -s /bin/zsh
```





其他

https://zhuanlan.zhihu.com/p/58073103

# dwm

```
# git clone git://git.suckless.org/dwm
# git clone git://git.suckless.org/st
# git clone git://git.suckless.org/dmenu
```

官方

> [https://wiki.archlinux.org/index.php/Dwm_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)](https://wiki.archlinux.org/index.php/Dwm_(简体中文))
>
> https://www.cnblogs.com/tanglizi/p/8457821.html
>
> https://blog.csdn.net/u010563350/article/details/106325331	
>
> https://www.shuzhiduo.com/A/kmzLAAwBJG/
>
> [http://lanhin.xyz/2014/09/24/dwm-linux-%E4%B8%8A%E7%9A%84%E6%A1%8C%E9%9D%A2%E7%AE%A1%E7%90%86%E5%99%A8/](http://lanhin.xyz/2014/09/24/dwm-linux-上的桌面管理器/)
>
> https://parrotsec-cn.org/t/suckless/3740
>
> https://my.oschina.net/u/4323130/blog/4238813
>
> http://www.hk-jk.com/a/bbintgpdappxz/20200602-415.html
>
> https://blog.csdn.net/u010563350/article/details/106325331



### 实现透明

您需要额外安装窗口渲染器，并在后台默认启动。如:xcompmgr,picom。

# st

https://wiki.archlinux.org/index.php/St



pannar



# grub 美化





# neovim





# 临时记录

安装 yaourt

\```sudo pacman -S yaourt`

在 “/etc/yaourtrc” 修改，去掉 #AURURL 的注释，修改为

\```AURURL=”https://aur.tuna.tsinghua.edu.cn”`

再次更新软件列表

```
sudo pacman -Syu
```

安装 net-tools ,typora,搜狗输入法,chrome,wps,网易云音乐

```
sudo pacman -S net-tools
sudo pacman -S typora
sudo pacman -S fcitx-im fcitx-configtool fcitx-sogoupinyin
sudo pacman -S google-chrome
sudo pacman -S wps-office ttf-wps-fonts
sudo pacman -S netease-cloud-music
```

安装 Tim 和 微信

```
yaourt qq
```





.bashrc: 每次**终端登录时**读取并运用里面的设置。

.xinitrc: 每次**startx启动X界面时**读取并运用里面的设置

.xprofile: 每次**使用gdm等图形登录时**读取并运用里面的设置

单独在图形界面启用中文locale



添加如下内容到文件~/.xprofile文件中

export LC_ALL="zh_CN.UTF-8"



***

## **安装常用软件**

### **1. 聊天类**

- qq(wine)
  yay -S [http://deepin.com.qq.im](http://deepin.com.qq.im/)
  或者qq(linux)，这个不推荐，太难用了(但是是官方的)
  sudo pacman -S qq-linux
  或者tim
  yay -S deepin.com.qq.office
  或者qq轻聊版
  yay -S deepin.com.qq.im.light
- 微信
  yay -S deepin.com.wechat2
- telegram
  sudo pacman -S telegram-desktop
- deepin qq和微信在`kde`桌面下可能遇到打不开的问题,解决方法如下
  安装如下程序
  sudo pacman -S gnome-settings-daemon
  执行以下操作
  sudo cp /etc/xdg/autostart/org.gnome.SettingsDaemon.XSettings.desktop ~/.config/autostart
  后打开设置，找到`开机和关机`中的`自动启动`，将`GNOME Settings Daemon's xsettings plugin`设置为已启用，注意要先点击右下角的`高级`按钮，在弹出框中选中`只在Plasma中自动启用`，确定即可

### **2. 办公类**

- WPS
  sudo pacman -S wps-office ttf-wps-fonts
- typora
  sudo pacman -S typora
- mindmaster（亿图思维导图）
  yay -S yay mindmaster-cn



### **3. 开发类**

- vscode
  sudo pacman -S code
- postman
  sudo pacman -S postman-bin
- eclipse（java）
  sudo pacman -S eclipse-java
- pycharm
  专业版
  sudo pacman -S pycharm-professional
  社区版
  sudo pacman -S pycharm-community-edition
- IDEA
  专业版
  sudo pacman -S intellij-idea-ultimate-edition
  社区版
  sudo pacman -S intellij-idea-community-edition

### **4. 娱乐类**

- 网易云音乐
  官方版
  sudo pacman -S netease-cloud-music
  非dde桌面下可能遇到无法输入中文的问题，需要做以下修改

1. 安装`qcef`

$ yay -S qcef

1. 1. 修改`/opt/netease/netease-cloud-music/netease-cloud-music.bash`文件为以下内容

\#!/bin/sh HERE="$(dirname "$(readlink -f "${0}")")" export XDG_CURRENT_DESKTOP=DDE exec "${HERE}"/netease-cloud-music $@
民间大神版
sudo pacman -S electron-netease-cloud-music

- qq音乐（wine）
  yay -S deepin.com.qq.qqmusic

### **5. 实用工具类**

- 谷歌浏览器
  sudo pacman -S google-chrome
- 火狐浏览器
  sudo pacman -S firefox
- virtual box
  sudo pacman -S virtualbox
  选择`virtualbox-host-modules-arch`模块
  sudo pacman -S linux-headers
  将当前用户加入`vboxusers`组
  sudo gpasswd -a $USER vboxusers
  其他可选相关项
  注意如果遇到让你选择类型，记得选和第一步一样的类型
  sudo pacman -S virtualbox-guest-dkms sudo pacman -S virtualbox-guest-iso sudo pacman -S virtualbox-guest-utils yay -S virtualbox-ext-oracle
  重启
- 百度网盘
  sudo pacman -S baidunetdisk-bin

### **6. 不可描述类**

- qv2ray
  sudo pacman -S qv2ray



## **美化grub启动界面**

1. 去商店下载主题包
   [gnome-look](https://www.gnome-look.org/)
   [kde-look](https://store.kde.org/)
   访问有点慢。。。
2. 解压下载好的主题
   sudo tar -xf 主题包名
3. 复制到grub主题目录
   sudo cp -r 主题包名 /usr/share/grub/themes/
4. 修改文件添加主题
   sudo vim /etc/default/grub
   找到`#GRUB_THEME=`去掉注释，该为对应的主题名称，就像这样
   GRUB_THEME="/usr/share/grub/themes/主题包名/theme.tx



1. 在/etc/pacman.conf中添加archlinuxcn的源. (google-chrome并不在archlinux软件库中,所以需要添加archlinuxcn的软件源)

```text
[archlinuxcn]
SigLevel = Optional TrustAll
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

添加清华源, 或者可以添加华为, 中科大或者163源都可以.

\2. 安装密钥包.

```text
pacman -S archlinuxcn-keyring
```

\3. 更新软件库.

```text
pacman -Sy
```

\4. 可以安装谷歌浏览器或者其他的archlinuxcn软件库里的软件了.

```text
pacman -S google-chrome
```

安装完成后需要注意的是google-chrome的程序名为google-chrome-stable, 而不是chrome或者google-chrome





## 相关参考

https://zhuanlan.zhihu.com/p/138951848















