---
title: git 子模块删除
top: false
mathjax: true
date: 2021-03-22 21:59:11
categories:
- git
---

-----





## git 删除子模块

[Git] 如何优雅的删除子模块(submodule)或修改Submodule URL

### 1. 优雅的删除子模块



```bash
# 逆初始化模块，其中{MOD_NAME}为模块目录，执行后可发现模块目录被清空
git submodule deinit {MOD_NAME} 
# 删除.gitmodules中记录的模块信息（--cached选项清除.git/modules中的缓存）
git rm --cached {MOD_NAME} 
# 提交更改到代码库，可观察到'.gitmodules'内容发生变更
git commit -am "Remove a submodule." 
```

Done! Nice & clean!

### 2. 修改某模块URL

1. 修改'.gitmodules'文件中对应模块的”url“属性;
2. 使用`git submodule sync`命令，将新的URL更新到文件`.git/config`；



```bash
thinker-g@localhost: ~/app$ git submodule sync 
Synchronizing submodule url for 'gitmods/thinker_g/Helpers'
thinker-g@localhost: ~/app$ # 运行后可观察到'.git/config'中对应模块的url属性被更新
thinker-g@localhost: ~/app$ git commit -am "Update submodule url." # 提交变更
```

*PS: 本实验使用git 2.7.4 完成，较低版本git可能不能自动更新`.git/config`文件，需要修修改完".gitmodule"文件后手动修改`.git/config`.*

以上。





Git子模块是一项重要功能，多用于项目之间的相互引用。Git的子模块初始化和更新都比较简单，然而删除一个子模块目前并没有很好的快速方法。本文也是在Gist上看到一个流程，因此记录一下。



### Git删除子模块

传统在在Git中，为了能够删除一个子模块，需要如下繁琐的流程：

- 在`.gitmodules`文件中删除相关记录
- 提交`.gitmodules`文件
  - `git add .gitmodules`
- 在`.git/config`中删除相关配置
- 删除暂存区数据
  - `git rm --cached path_to_submodule（末尾不加路径符）`
- 删除子模块
  - `rm --rf .git/modules/path_to_submodule（末尾不加路径符）`
- 提交修改
  - `git commit -m "Removed submodule "`
- 删除当前工作区数据（此时为未追踪数据）
  - `rm -rf path_to_submodule`

**现在Git更新了，有了`deinit`命令，流程简化如下：**

- Remove the submodule entry from .git/config
  - `git submodule deinit -f path/to/submodule`
- Remove the submodule directory from the superproject’s .git/modules directory
  - `rm -rf .git/modules/path/to/submodule`
- Remove the entry in .gitmodules and remove the submodule directory located at path/to/submodule
  - `git rm -f path/to/submodule`

### Git删除远程分支

```
git push --delete <remote_name> <branch_name>
```