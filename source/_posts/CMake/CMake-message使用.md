---
title: CMake message 用法
top: false
mathjax: true
date: 2020-04-08 21:59:11
categories:
- CMake
---

-----



## 语法

```cmake
General messages
  message([<mode>] "message text" ...)

Reporting checks
  message(<checkState> "message text" ...)
```



## 一般消息

```cmake
message([<mode>] "message text" ...)
```

mode

```
 (无) = 重要消息；
 STATUS = 非重要消息；
 WARNING = CMake 警告, 会继续执行；
 AUTHOR_WARNING = CMake 警告 (dev), 会继续执行；
 SEND_ERROR = CMake 错误, 继续执行，但是会跳过生成的步骤；
 FATAL_ERROR = CMake 错误, 终止所有处理过程；
```



**示例：**

1.输出错误 

```cmake
FATAL_ERROR
message(FATAL_ERROR "
FATAL: In-source builds are not allowed.
       You should create a separate directory for build files.
")

```

2.输出警告 WARNING

```cmake
message(WARNING "OpenCV requires Android SDK tools revision 14 or newer.")
```


3.输出正常 STATUS

```cmake
SET(USER_KEY, "Hello World")
MESSAGE( STATUS "this var key = ${USER_KEY}.")
```

4.输出变量的值

```cmake
SET(USER_KEY, "Hello World")
MESSAGE( STATUS "this var key = ${USER_KEY}.")
```

## 报告检查

CMake 输出中的一个常见模式是指示某种检查开始的消息，然后是报告该检查结果的另一条消息。











## 相关参考

[cmake doc](https://cmake.org/cmake/help/latest/command/message.html)