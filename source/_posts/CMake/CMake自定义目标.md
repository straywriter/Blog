---
title: C++ STL容器用法
top: false
mathjax: true
date: 2021-02-20 21:59:11
categories:
- CMake
---

-----

## 自定义内容

### add_custom_command 为某一个工程添加一个自定义的命令

*参考链接：* [各平台编译器中的Pre-build及Post-build操作](https://www.jianshu.com/p/66df9650a9e2)

```cmake
add_custom_command(TARGET target 
    PRE_BUILD | PRE_LINK| POST_BUILD 
    COMMAND command1[ARGS] [args1...] 
    [COMMAND command2[ARGS] [args2...] ...] 
    [WORKING_DIRECTORYdir] 
    [COMMENT comment][VERBATIM]
    )
```

执行命令的时间由第二个参数决定:

1. PRE_BUILD - 命令将会在其他依赖项执行前执行。
2. PRE_LINK - 命令将会在其他依赖项执行完后执行。
3. POST_BUILD - 命令将会在目标构建完后执行。

例子：

```cmake
add_custom_command(
    TARGET ${PROJECT_NAME} 
    POST_BUILD 
    COMMAND ${CMAKE_COMMAND} -E sleep 5
    )
#目标就是TARGET后面跟的工程，当PROJECT_NAME被生成的时候就会执行COMMAND后面的命令。

add_custom_command(
    TARGET test_elf 
    PRE_BUILD 
    COMMAND 
    move E:/cfg/start.o ${CMAKE_BINARY_DIR}/. && 
    )

#在test_el执行依赖之前，将start.o文件复制到编译目录
```

### add_custom_command：添加自定义命令来产生一个输出

参数格式：

```cmake
add_custom_command(
    OUTPUT output1 [output2 ...]
    COMMAND command1[ARGS] [args1...]
    [COMMAND command2 [ARGS] [args2...] ...]
    [MAIN_DEPENDENCYdepend]
    [DEPENDS[depends...]]
    [IMPLICIT_DEPENDS<lang1> depend1 ...]
    [WORKING_DIRECTORYdir]
    [COMMENT comment] [VERBATIM] [APPEND]
    )
```

参数解析：

- 其中ARGS选项 是为了向后兼容，MAIN_DEPENDENCY选项是针对VisualStudio给出一个建议，这两选项可以忽略。
- COMMAND：指定一些在构建阶段执行的命令。如果指定了多于一条的命令，他会按照顺序去执行。如果指定了一个可执行目标的名字（被add_executable()命令创建），他会自动被在构建阶段创建的可执行文件的路径替换。
- DEPENDS:指定目标依赖的文件，如果依赖的文件是和CMakeLists.txt相同目录的文件，则命令就会在CMakeLists.txt文件的，目录执行。如果没有指定DEPENDS，则只要缺少OUTPUT，该命令就会执行。如果指定的位置和CMAkeLists.txt不是同一位置，会先去创建依赖关系，先去将依赖的目标或者命令先去编译。
- WORKING_DIRECTORY：使用给定的当前目录执行命令，如果是相对路径，则相对于当前源目录对应的目录结构进行解析

例子：

```cmake
#首先生成creator的可执行文件 
add_executable(creator creator.cxx) 
#获取EXE_LOC的LOCATION属性存放到creator里面 
get_target_property(creator EXE_LOC LOCATION) 
#生成created.c文件 
add_custom_command(
    OUTPUT ${PROJECT_BINARY_DIR}/created.c 
    DEPENDS creator 
    COMMAND ${EXE_LOC} 
    ARGS ${PROJECT_BINARY_DIR}/created.c 
    ) 
#使用上一步生成的created.c文件来生成Foo可执行文件 
add_executable(Foo ${PROJECT_BINARY_DIR}/created.c)
```

注意：不要在多个相互独立的文件中使用该命令产生相同的文件，放置冲突。

### add_custom_target:增加定制目标。

```cmake
add_custom_target(
    Name [ALL] [command1 [args1...]] 
    [COMMAND command2 [args2...] ...] 
    [DEPENDS depend depend depend ... ] 
    [BYPRODUCTS [files...]] 
    [WORKING_DIRECTORY dir] 
    [COMMENT comment] 
    [VERBATIM] [USES_TERMINAL] 
    [SOURCES src1 [src2...]]
    )
```

add_custom_target 可以增加定制目标，常常用于编译文档、运行测试用例等。

### add_custom_command和add_custom_target的区别

- 命令命名里面的区别就在于：command和target，前者是自定义命令，后者是自定义目标
- 目标：使用add_custom_target定义的叫做自定义目标，因此这些“目标”区别于正常的目标，他们不生成exe或者lib，但是仍然会具有一些正常目标相同的属性，构建他们的时候，只是调用了为他们设置的命令，如果自定义目标对于其他目标有依赖，那么就会优先生成依赖的那些目标。
- 自定义命令：自定义命令不是一个“可构建”的对象，并且没有可以设置的属性，自定义命令是一个在构建依赖目标之前被调用的命令，自定义命令的依赖可以通过add_custom_command(TARGET target …)形式显式设置，也可以通过add_custom_command(OUTPUT output1 …)生成文件的形式隐式设置。显示执行的时候，每次构建目标，首先会执行自定义的命令，隐式执行的时候，如果自定义的命令依赖于其他文件，则在构建目标的时候先去执行生成其他文件。



## 相关参考

[代码构建系统之CMake | Walker's Blog (walkerdu.com)](http://walkerdu.com/2020/10/27/cmake/)

[CMake 3.20 中文 (runebook.dev)](https://runebook.dev/zh-CN/docs/cmake/-index-)