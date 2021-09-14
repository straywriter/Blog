---
title: CMake变量
top: false
mathjax: true
date: 2021-01-11 21:59:11
categories:
- CMake
---

-----

## 基础变量

### 局部变量

普通变量（normal variable）相当于编程中脚本内部变量，类似于脚本文件的局部变量，这种变量不能跨越CMakeLists.txt文档。普通变量定义方式如下：

```
set(var "value")
```

设置一个普通变量var，值为value。

和编程语言中局部变量的用法类似，这个变量会屏蔽CMake缓存中的同名变量，（类似局部变量屏蔽全局变量）。但是这条语句不会改变缓存中的var变量。

当使用include()直接进行.cmake文件的引入时，可以实现变量的通用引用。

-----

**在CMake中定义和使用变量时，可以使用引号也可以不使用引号，并且它们会产生不同的结果。**

**定义变量时使用引号**

```cmake
set(TITLE learn cmake quotes!)
message(${TITLE})
输出：
learncmakequotes!
```

当我们不用引号定义变量的时候，相当于我们定义了一个包含多个成员的字符串数组，当使用message输出的时候，其实是挨着输出了这5个元素，结果就是learncmakequotes!了。我们也可以用foreach验证下这个结果：

```cmake
foreach(e ${TITLE})
    message(${e})
endforeach()
```

**使用变量时使用引号**

对于例1中`${TITLE}`变量，如果使用引号，也会有不同的结果

```cmake
message("${TITLE}")
输出：
learn;cmake;quotes!
```

- 此时{TITLE}还是一个数组，我们用`"${TITLE}"`这种形式的时候，表示要让CMake把这个数组的所有值当成一个整体，而不是分散的个体。
- 于是，为了保持数组的含义，又提供一个整体的表达方式，CMake就会用;把这数组的多个值连接起来。
- 无论是在CMake还是Shell里，用分号分割的字符串，形式上是一个字符串，但把它当成命令执行，就会被解析成多个用分号分割的部分。
- 对于单一的字符串变量(不包含特殊字符)，用不用引号，结果都是一样的。

**定义变量时使用引号，使用的时候不用**

当使用引号时，这个值就是普通的字符层，不再是数组了。

```cmake
set(TITLE "learn cmake quotes!")
message(${TITLE})
message("${TITLE}")
输出：
learn cmake quotes!
learn cmake quotes!
```

**总结**

- 引号对于CMake中变量的定义，其功能主要是当有空格的时候，区别变量时一个数组还是纯粹的字符串；
- 在使用的时候，对于普通字符串，加不加引号没什么区别，而对于数组，加引号会将数组以分号间隔输出，而不加引号则是直接拼接数组。



### 缓存变量

cache variable用于缓存变量，定义如下：

```cmake
set(var "value" CACHE STRING "" FORCE)
```

这条语句设置了一个CACHE语句，类型是STRING，说明信息为空字符串，上述都不能省略。

CACHE作用如下：

- 如果缓存中存在同名的变量，根据FORCE来决定是否写入缓存：如果没有FORCE，这条语句不起作用，使用缓存中的变量；如果有FORCE，使用当前设置的值。
  - 注意，如果是FORCE，也能修改-D选项设置的CACHE变量，所以有可能传入的生成命令选项是无效的。
- 如果缓存中不存在同名的变量，则将这个变量写入缓存并使用。

缓存变量也可以设置只在本文件内生效，将STRING类型改为INTERNAL即可。



### 环境变量

- 读取环境变量：`$ENV{variable_name}`
- 设置环境变量：`set(ENV{variable_name} value)`

### option变量

- 主要是缓存的字符串，只能是ON或OFF，他们允许一些特殊的处理，如依赖，这个变量可以跨文本。
- 不要将其option与set命令搞错。给定的值option实际上只是“初始值”（在第一个配置步骤中一次传送到缓存），之后将由用户通过CMake的GUI或者命令行进行更改



## 相关参考

[cmaek list](https://cmake.org/cmake/help/latest/command/list.html)

[Cmake命令之list介绍](https://www.jianshu.com/p/89fb01752d6f)

[cmake string](https://cmake.org/cmake/help/latest/command/string.html)

[cmake string 测试](https://github.com/Kitware/CMake/blob/master/Tests/StringFileTest/CMakeLists.txt)

[CMake中变量总结 | 拾荒志 (murphypei.github.io)](https://murphypei.github.io/blog/2018/10/cmake-variable)

[CMake中引号用法总结 | 拾荒志 (murphypei.github.io)](https://murphypei.github.io/blog/2018/10/cmake-quotes)