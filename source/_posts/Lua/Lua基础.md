---
title: Lua基础
top: false
mathjax: true
categories:
- Lua
---

-----





## 简介

源码包下载后，我们可以看一下lua-5.3.5/src目录下的代码结构。代码结构基本会分3部分

**虚拟机核心功能部分**

| 文件       | 作用                     |
| ---------- | ------------------------ |
| lua.c      | lua的可执行入口 main函数 |
| lapi.c     | C语言接口                |
| ldebug.c   | Debug 接口               |
| ldo.c      | 函数调用以及栈管理       |
| lfunc.c    | 函数原型及闭包管理       |
| lgc.c      | 垃圾回收机制             |
| lmem.c     | 内存管理接口             |
| lobject.c  | 对象操作函数             |
| lopcodes.c | 虚拟机字节码定义         |
| lstate.c   | 全局状态机 管理全局信息  |
| lstring.c  | 字符串池                 |
| ltable.c   | 表类型的相关操作         |
| ltm.c      | 元方法                   |
| lvm.c      | 虚拟机                   |
| lzio.c     | 输入流接口               |

**源代码解析和预编译**

| 文件      | 作用                     |
| --------- | ------------------------ |
| lcode.c   | 代码生成器               |
| ldump.c   | 序列化预编译的Lua 字节码 |
| llex.c    | 词法分析器               |
| lparser.c | 解析器                   |
| lundump.c | 还原预编译的字节码       |

**内嵌库**

| 文件       | 作用                   |
| ---------- | ---------------------- |
| lauxlib.c  | 库编写用到的辅助函数库 |
| lbaselib.c | 基础库                 |
| ldblib.c   | Debug 库               |
| linit.c    | 内嵌库的初始化         |
| liolib.c   | IO 库                  |
| lmathlib.c | 数学库                 |
| loadlib.c  | 动态扩展库管理         |
| loslib.c   | OS 库                  |
| lstrlib.c  | 字符串库               |
| ltablib.c  | 表处理库               |

每次阅读源码，其实最难的是开始，通过网上各种资料，先把lua的整个目录结构弄明白，幸好lua真的比较小，很容易就能弄明白每个文件是干什么的。接下去就是开始一点一点的啃整个源码的过程了。

啃整个lua语言链路解析过程之前，我会优先把lua周边的库以及虚拟机字节码这块搞明白，然后再开始进行整个解析流程的阅读。

 









## 相关文章

