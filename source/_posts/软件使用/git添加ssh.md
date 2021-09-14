---
title: git添加ssh
top: false
mathjax: true
date: 2020-07-03 18:37:41
categories:
- 软件使用
---

-----

## 配置ssh key

1、在任意文件夹下，右键git bash,到.ssh文件夹下看看是否为空，无直接创建，有备份删除创建

```
$ cd ~/.ssh
$ git config --global user.name "yourname"
$ git config --global user.email "youremail"
$ ssh-keygen -t rsa -C "youremail"（后三个空格即可，也可以根据提示输入）
```

2、这时候.ssh下将出现两个文件id_rsa和id_rsa.pub，id_rsa.pub是公钥，打开复制里面的内容

3、在github中的yourname.github.io仓库下的setting下的deploy ssh下添加key,将上述内容复制即可

4、测试

```
ssh -T git@github.com
```

出现如下信息则正常

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

## Hexo 添加ssh

`hexo` 的配置文件 `_config.yml` 中的 `deploy` 属性，将地址改为ssh的GitHub地址。



## 参考

- [Changing a remote’s URL](https://help.github.com/articles/changing-a-remote-s-url/)

- [Generating SSH keys](https://help.github.com/articles/generating-ssh-keys/)

- [利用SSH省去在hexo deploy输入密码 | sly's blog (eesly.github.io)](https://eesly.github.io/2014/10/26/利用ssh省去在hexo-deploy输入密码/)
- [设置 SSH 使用 hexo deploy 时免输用户名密码 - SegmentFault 思否](https://segmentfault.com/a/1190000005125610)