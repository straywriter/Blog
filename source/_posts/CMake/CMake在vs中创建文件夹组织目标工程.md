---
title: CMake在vs中创建文件夹组织目标工程
top: false
mathjax: true
date: 2021-02-22 21:59:11
categories:
- CMake
---

-----



**打开允许创建文件夹的开关**

```cmake
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
```

**给cmake自动创建的工程重新命名（此步骤可以省略）**

```cmake
set_property(GLOBAL PROPERTY PREDEFINED_TARGETS_FOLDER "CMakeTargets")
```

![](CMake在vs中创建文件夹组织目标工程/image-20210408110212455.png)

**把工程加到文件夹中**

```cmake
set_target_properties(prj PROPERTIES FOLDER "folder")
```

![](CMake在vs中创建文件夹组织目标工程/image-20210408110240078.png)