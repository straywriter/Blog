---
title: C++基础
top: false
mathjax: true
date: 2021-03-22 21:59:11
categories:
- CMake
---

-----

## 基础命令

### 最低版本 cmake_minimum_required

```cmake
cmake_minimum_required(VERSION X.X)
```

判断cmake版本

```cmake
#检查当前版本是否小鱼3.12是，则更新版本号变量,否则设置版本为3.12 
if(${CMAKE_VERSION} VERSION_LESS 3.12)
    cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
else()
    cmake_policy(VERSION 3.12)
endif()
```

现在可以使用`cmake_minimum_required(VERSION 2.8.3)`检查cmake的最小版本



### 设置项目名称 project

cmake中使用 `project(MyProject[C] [C++])` 不过后面的语言参数经常省略，默认支持所有语言。



执行project()函数就是定义了一个项目名称，并且会生成两个变量`_BINARY_DIR和_SOURCE_DIR`和`PROJECT_BINARY_DIR`者两个变量是cmake中预定义的变量。变量是否相同，关键在于是内部构建还是外部构建：

- 内部构建： `cmake ./ && make`
- 外部构建：`mkdir build && cd build && cmake ../ && make` 当cmake在外部构建时，产生的中间文件和可执行文件并不在同一目录，因此`PROJECT_BINARY_DIR`和`_BINARY_DIR`内容相同，内部构建的时候指向CMakeLists.txt文件的目录，外部构建的，指向target编译的目录。 此外：PROJECT_SOURCE_DIR和_SOURCE_DIR无论内部构建还是外部构建，指向的内容都是一样的，都指向工程的根目录



### 生成可执行文件 add_executable

语法：`add_executable(exename srcname)`

- exename:生成的可执行文件的名字
- srcname:以来的源文件

生成exe的名字，并且指出需要的源文件。 获取文件路径中的所有源文件：`aux_sourcr_directory(<dir> <variable>)`例如：`aux_sourcr_directory(. DIR_SRCS)`将当前目录下的源文件名字存放到变量DIR_SRCS里面 ，如果源文件比较多，直接用DIR_SRCS变量即可。`add_executable(Demo ${DIR_SRCS})`生成名为Demo的可执行文件。



### 生成库文件 add_library

cmake中使用`add_library`来实现库文件的生成； 命令：

```cmake
add_library(
    libname 
    [SHARED|STATIC|MODULE] 
    [EXCLUDE_FROM_ALL] 
    source1 
    source2 
    ... 
    sourceN
    )
```

- libname:生成的库文件的名字

- | `[SHARED|STATIC|MODULE]`：生成库文件的类型（动态库 | 静态库 | 模块） |
  | -------------------------------------------------- | ------ | ------ |
  |                                                    |        |        |

- EXCLUDE_FROM_ALL：有这个参数表示该库不会被默认构建

- source2 … sourceN：生成库依赖的源文件，如果源文件比较多，可以使用aux_sourcr_directory命令获取路径下所有源文件，具体章节参见：CMake基础知识简介->生成可执行文件->获取路径中所有源文件

示例：`add_library(ALib SHARE alib.cpp)`

### 添加头文件目录 target_include_directories

```cmake
target_include_directories(
    <target> [SYSTEM] 
    [BEFORE] 
    <INTERFACE|PUBLIC|PRIVATE> [items1...] 
    [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...]
    )

```

`include_directories([AFTER|BEFORE] [SYSTEM] dir1 [dir2 …])` 参数解析：

- `AFTER|BEFORE`：指定了要添加路径是添加到原有列表之前还是之后
- `SYSTEM`：若指定了system参数，则把被包含的路径当做系统包含路径来处理。
- `dir1 [dir2 …]`:把这些路径添加到CMakeLists及其子目录的CMakeLists的头文件包含项目中相当于g++选项中的-l的参数的作用。

注意：两者都是将include目录添加到目标区域中，但是include_directories是对整个项目进行全局添加，target_include_directories是将CMake中针对指定的目标进行添加。



### 添加链接库文件路径 target_link_libraries



```cmake
target_link_libraries(
    <target> 
    [item1 [item2 [...]]] 
    [[debug|optimized|general] <item>] ... )
```



- item: 对应的链接库文件
- `debug|optimized|general`:调试配置/其它配置/所有配置



 **添加需要的库文件的目录**

```cmake
link_directories
```



### 控制目标属性 set_target_properties

以上的几条命令的区分都是：是否带target前缀，在CMake里面，一个target有自己的属性集，如果我们没有显示的设置这些target的属性的话，CMake默认是由相关的全局属性来填充target的属性，我们如果需要单独的设置target的属性，需要使用命令：set_target_properties()

```cmake
set_target_properties(
    target1 
    target2 
    ... 
    PROPERTIES 属性名称1 值 
    属性名称2 值 
    ... 
    )
```

- 控制编译选项的属性是：COMPILE_FLAGS
- 控制链接选项的属性是：LINK_FLAGS
- 控制输出路径的属性：EXECUTABLE_OUTPUT_PATH（exe的输出路径）、LIBRARY_OUTPUT_PATH（库文件的输出路径）

## 变量

### 局部变量

普通变量（normal variable）相当于编程中脚本内部变量，类似于脚本文件的局部变量，这种变量不能跨越CMakeLists.txt文档。普通变量定义方式如下：

```cmake
set(var "value")
```

设置一个普通变量var，值为value，引号的作用可以详见我的另一篇文章。

和编程语言中局部变量的用法类似，这个变量会屏蔽CMake缓存中的同名变量，（类似局部变量屏蔽全局变量）。但是这条语句不会改变缓存中的var变量。



当使用include()直接进行.cmake文件的引入时，可以实现变量的通用引用。

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

### 内置变量





## 语句

### 条件语句 if

基本语法：

```cmake
if(expression)    
    COMMAND1(ARGS ...)    
    COMMAND2(ARGS ...)    
    ...
else(expression)    
    COMMAND1(ARGS ...)    
    COMMAND2(ARGS ...)    
    ...
endif(expression)
```

注意：ENDIF要和IF对应

- if (expression)，expression不为：空,0,N,NO,OFF,FALSE,NOTFOUND或_NOTFOUND,为真
- IF (not exp)，与上面相反
- if (var1 AND var2)，var1且var2都为真，条件成立
- if (var1 OR var2)，var1或var2其中某一个为真，条件成立
- if (COMMAND cmd)， 如果cmd确实是命令并可调用，为真；
- if (EXISTS dir) 如果目录存在，为真
- if (EXISTS file) 如果文件存在，为真
- if (file1 IS_NEWER_THAN file2)，当file1比file2新，或file1/file2中有一个不存在时为真，文件名需使用全路径
- if (IS_DIRECTORY dir) 当dir是目录时，为真
- if (DEFINED var) 如果变量被定义，为真
- if (string MATCHES regex) 当给定变量或字符串能匹配正则表达式regex时，为真

例：IF (“hello” MATCHES “ell”) MESSAGE(“true”) ENDIF (“hello” MATCHES “ell”)

**数字表达式**

- if (var LESS number)，var小于number为真。
- if (var GREATER number)，var大于number为真。
- if (var EQUAL number)，var等于number为真。

**字母顺序比较**

- IF (var1 STRLESS var2)，var1字母顺序小于var2为真。
- IF (var1 STRGREATER var2)，var1字母顺序大于var2为真。
- IF (var1 STREQUAL var2)，var1和var2字母顺序相等为真。





```cmake
if(<condition>)
  <commands>
elseif(<condition>) # optional block, can be repeated
  <commands>
else()              # optional block
  <commands>
endif()

#####

IF(var),如果变量不是:空,0,N, NO, OFF, FALSE, NOTFOUND 或<var>_NOTFOUND 时,表达式为真。
IF(NOT var ),与上述条件相反。
IF(var1 AND var2),当两个变量都为真是为真。
IF(var1 OR var2),当两个变量其中一个为真时为真。
IF(COMMAND cmd),当给定的 cmd 确实是命令并可以调用是为真。
IF(EXISTS dir)或者 IF(EXISTS file),当目录名或者文件名存在时为真。
IF(file1 IS_NEWER_THAN file2),当 file1 比 file2 新,或者 file1/file2 其中有一个不存在时为真,文件名请使用完整路径。
IF(IS_DIRECTORY dirname),当 dirname 是目录时,为真。
IF(variable MATCHES regex)
IF(string MATCHES regex)
```



### 循环语句 while

WHILE(condition) COMMAND1(ARGS …) COMMAND2(ARGS …) …ENDWHILE(condition)

条件判断参考if



### 循环语句 Foreach

FOREACH有三种使用形式的语法，且每个FOREACH都需要一个ENDFOREACH()与之匹配。

**列表循环**

- 语法：

```cmake
FOREACH(loop_var arg1 arg2 ...) 
    COMMAND1(ARGS ...) 
    COMMAND2(ARGS ...) 
    ...
ENDFOREACH(loop_var)
```

- 例子：

  ```cmake
  AUX_SOURCE_DIRECTORY(.SRC_LIST)
  FOREACH(F ${SRC_LIST}) 
    MESSAGE(${F})
  ENDFOREACH(F)
  ```

  例子中，先将当前路径的源文件名放到变量SRC_LIST里面，然后遍历输出文件名

**循环范围**

- 语法：

```cmake
FOREACH(loop_var RANGE total) 
    COMMAND1(ARGS ...) 
    COMMAND2(ARGS ...)
    ... 
ENDFOREACH(loop_var)
```

- 例子：

```cmake
FOREACH(VAR RANGE 100)
    MESSAGE(${VAR})
ENDFOREACH(VAR)
```

例子中默认起点为0，步进为1，作用就是输出：0~100。

**范围步进循环**

- 语法:

```cmake
FOREACH(loop_var RANGE start stop [step]) 
    COMMAND1(ARGS ...) 
    COMMAND2(ARGS ...)
    ... 
ENDFOREACH(loop_var)
```

- 例子：

```cmake
FOREACH(A RANGE 0 100 10)
    MESSAGE(${A})
ENDFOREACH(A)
```

例子中，起点是0，终点是100，步进是10，输出：0,10,20,30,40,50,60,70,80,90,100。



## 构建规范以及构建属性

用于指定构建规则以及程序使用要求的指令：

- target_include_directories()
- target_compile_definitions()
- target_compile_options()

指令格式：

```cmake
target_include_directories(
    <target> 
    [SYSTEM] 
    [BEFORE] 
    <INTERFACE|PUBLIC|PRIVATE> 
    [items1...] 
    [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...]
    )
主要是设置include的头文件的查找目录，也就是GCC的[-lidr...]选项
target_compile_definitions(
    <target> 
    <INTERFACE|PUBLIC|PRIVATE> 
    [items1...]
    [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...]
    )
设置c++文件中预定义的宏变量。 ```
```

target_compile_options( [BEFORE] <INTERFACE|PUBLIC|PRIVATE> [items1...] [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...] )

```cmake
    gcc其它的一些编译选项，比如-fPIC,-fPIC作用于编译阶段，告诉编译器产生与位置无关代码Position-Independent Code)，则产生的代码中，没有绝对地址，全部使用相对地址，故而代码可以被加载器加载到内存的任意位置，都可以正确的执行。这正是共享库所要求的，共享库被加载时，在内存的位置不是固定的。

以上的额三个命令会生成

- INCLUDE_DIRECTORIES, 
- COMPILE_DEFINITIONS, 
- COMPILE_OPTIONS
变量的值。或者 :
- INTERFACE_INCLUDE_DIRECTORIES
- INTERFACE_COMPILE_DEFINITIONS
- INTERFACE_COMPILE_OPTIONS

这三个命令都有三种可选模式: PRIVATE, PUBLIC。INTERFACE.PRIVATE模式仅填充不是接口的目标属性;INTERFACE模式仅填充接口目标的属性.PUBLIC模式填充这两种的目标属性。

## 7 宏和函数
CMake里面可以定义自己的函数（function）和宏（macro）
函数和宏的区别：

函数是有范围的，而宏没有。如果希望函数设置的变量在函数的外部也可以看见，就需要使用PARENT_SCOPE来修饰，但是函数对于变量的控制会比较好，不会有变量泄露。函数很难将计算结果传出来，使用宏就可以将一些值简单的传出来。

使用示例
- 宏(macro)

​```cmake
macro( [arg1 [arg2 [arg3 ...]]])     
    COMMAND1(ARGS ...)     
    COMMAND2(ARGS ...)     
    ...
endmacro()
```





## cmake configure_file

使用configure_file命令可以使得在普通文件中也能使用CMake中的变量

语法：

```cmake
configure_file(
    <input> <output> 
    [COPYONLY] 
    [ESCAPE_QUOTES] 
    [@ONLY] 
    [NEWLINE_STYLE [UNIX|DOS|WIN32|LF|CRLF] ]
    );
```

参数：

- COPYONLY：

  只拷贝文件，不进行任何的变量替换。这个选项在指定了 NEWLINE_STYLE 选项时不能使用（无效）。

- ESCAPE_QUOTES：

  躲过任何的反斜杠(C风格)转义。躲避转义，比如你有个变量在CMake中是这样的 set(FOO_STRING “"foo"”) 那么在没有 ESCAPE_QUOTES 选项的状态下，通过变量替换将变为 `"foo"`，如果指定了 ESCAPE_QUOTES 选项，变量将不变。

- @ONLY：

  限制变量替换:，让其只替换被 @VAR@ 引用的变量（那么 ${VAR}格式的变量将不会被替换）。这在配置 ${VAR} 语法的脚本时是非常有用的。

- NEWLINE_STYLE:

  指定输出文件中的新行格式。UNIX 和 LF 的新行是 \n ，DOS 和 WIN32 和 CRLF 的新行格式是 \r\n 。 这个选项在指定了 COPYONLY 选项时不能使用（无效）。



#### source_group命令

使用该命令可以将文件在VS中进行分组显示；`source_group("Header Files" FILES ${HEADER_FILES})`；以上命令是将变量HEADER_FILES里面的文件，在VS显示的时候都显示在“Header Files”选项下面

## 运行其它程序 execute_process

CMakeLists.txt中可以使用`execute_process` 来运行系统中的程序。

```cmake
execute_process(
    [COMMAND <cmd1> [args1...]] 
    [COMMAND <cmd2> [args2...] [...]] 
    [WORKING_DIRECTORY <directory>] 
    [TIMEOUT <seconds>] 
    [RESULT_VARIABLE <variable>] 
    [OUTPUT_VARIABLE <variable>] 
    [ERROR_VARIABLE <variable>] 
    [INPUT_FILE <file>] 
    [OUTPUT_FILE <file>] 
    [ERROR_FILE <file>] 
    [OUTPUT_QUIET] 
    [ERROR_QUIET] 
    [OUTPUT_STRIP_TRAILING_WHITESPACE] 
    [ERROR_STRIP_TRAILING_WHITESPACE]
    )
```

**参数解析**

- `COMMAND`：子进程的命令行，CMake使用操作系统的API直接执行子进程，所有的参数逐字传输，没有中间脚本参与，像“>”的输出重定向也会被直接的传输到子进程里面，当做普通的参数进行处理。
- `WORKING_DIRECTORY`：指定的工作目录将会设置为子进程的工作目录
- `TIMEOUT`：子进程如果在指定的秒数之内没有结束就会被中断
- `RESULT_VARIABLE`：变量被设置为包含子进程的运算结果，也就是命令执行的最后结果将会保存在这个变量之中，返回码将是来自最后一个子进程的整数或者一个错误描述字符串
- `OUTPUT_VARIABLE`、`ERROR_VARIABLE`：输出变量和错误变量
- `INPUT_FILE`、`OUTPUT_FILE`、`ERROR_FILE`：输入文件、输出文件、错误文件
- `OUTPUT_QUIET`、`ERROR_QUIET`:输出忽略、错误忽略，标准输出和标准错误的结果将被默认忽略.

使用示例:

```cmake
set(MAKE_CMD "/src/bin/make.bat")
MESSAGE("COMMAND: ${MAKE_CMD}")
execute_process(
    COMMAND "${MAKE_CMD}" 
    RESULT_VARIABLE 
    CMD_ERROR OUTPUT_FILE CMD_OUTPUT
    )
MESSAGE( 
    STATUS "CMD_ERROR:" 
    ${CMD_ERROR}
    )
MESSAGE(
    STATUS 
    "CMD_OUTPUT:" 
    ${CMD_OUTPUT}
    )

#输出：COMMAND:/src/bin/make.batCMD_ERROR:No such file or directoryCMD_OUTPUT:（因为这个路径下面没有这个文件）
```



## CMake编译中target_link_libraries中属性PRIVATE、PUBLIC、INTERFACE含义

当创建动态库时，

- 如果源文件(例如CPP)中包含第三方头文件，但是头文件（例如hpp）中不包含该第三方文件头，采用PRIVATE。
- 如果源文件和头文件中都包含该第三方文件头，采用PUBLIC。
- 如果头文件中包含该第三方文件头，但是源文件(例如CPP)中不包含，采用 **INTERFACE**。

> **原文：CMake target_link_libraries Interface Dependencies**
>
> http://stackoverflow.com/questions/26037954/cmake-target-link-libraries-interface-dependencies
>
> 其他属性可以参考http://www.cmake.org/cmake/help/v3.0/manual/cmake-buildsystem.7.html#transitive-usage-requirements
>



### C++11功能的激活

使用`target_compile_features(<target> <PRIVATE|PUBLIC|INTERFACE> <feature> [...])`

添加c++11标准：`target_compile_features(<project_name> PUBLIC cxx_std_11)`。

注意：target必须是由:`add_executable`或者`add_library`生成的目标。

也可以使用下面的方式来进行支持：

```cmake
#设置c++标准级别

set(CMAKE_CXX_STANDARD 11)
#告诉cmake使用它

set(CMAKE_CXX_STANDARD_REQUIRED ON)
#(可选)确保-std=c++11
```

### 中间过程优化

```cmake
#检测编译器是否支持过程间优化

check_ipo_supported(RESULT result)

#如果不支持，判断进不去
if(result)
    #为工程设置过程间优化

    set_target_properties(foo PROPERTIES INTERPROCEDURAL_OPTIMIZATION TRUE)
endif()
```

### cmake中的option

option命令可以设置默认值；例如`option(address "this is path for value" ON)`设置address的默认值为ON，并且添加注释提示。

注意：没有设置默认值时，默认的默认值是OFF，如果值已经改变一定要清除CMakeCache.txt。

可以另外当去创建option.txt文件，然后使用include进行包含。

可以使用`cmake_dependent_option`设置存在依赖的option ,但是一般建议使用if判断来进行配置。

```cmake
cmake_dependent_option(DEPENT_USE_CURL "this is dependent on USE_CURL" ON "USE_CURL;NOT USE_MATH" OFF)
#设置一个option：DEPENT_USE_CURL,第二个参数是他的说明，ON后面的参数是一个表达式，当“USE_CURL”且“USE_MATH”为真的时候，DEPENT_USE_CURL取ON，为假取OFF
```

### 属性调试模块(CMakePrintHelpers)

```cmake
CMAKE_PRINT_PROPERTIES(
    [TARGETS target1 .. targetN]
    [SOURCES source1 .. sourceN]
    [DIRECTORIES dir1 .. dirN]
    [TESTS test1 .. testN]
    [CACHE_ENTRIES entry1 .. entryN]
    PROPERTIES prop1 .. propN
    )
```

如果要检查foo目标的INTERFACE_INCLUDE_DIRS和LOCATION的值，则执行：

```cmake
cmake_print_properties(
    TARGETS foo 
    PROPERTIES 
    INTERFACE_INCLUDE_DIRS 
    LOCATION
    )
```



## 安装和测试

下一步我们将为我们的工程添加安装规则和测试.安装规则相当简单,为了安装MathFunctions库和头文件,我们需要在MathFunctions文件夹的CMakeLists.txt文件中,添加如下内容:

```cmake
install (TARGETS MathFunctions DESTINATION bin)
install (FILES MathFunctions.h DESTINATION include)
```

对于应用程序,需要在最顶层的CMakeLists.txt文件中安装可执行程序和配置头文件.

```cmake
# add the install targets
install (TARGETS Tutorial DESTINATION bin)
install (FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"        
         DESTINATION include)
```

**注意:上面这几行内容,需要添加在如下代码之后,否则编译会报错,提示找不到”Tutorial”**

```cmake
# add the executable
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Tutorial  ${EXTRA_LIBS})
```

这就是所有要做的.到这里你应该可以构建这个教程了,然后输入”make install”(或则使用IDE编译出INSTALL目标),它将安装适当的头文件,库,和可执行程序.CMake变量”CMAKE_INSTALL_PREFIX”用来决定这些文件将被安装的根路径.添加测试也是很简单的.在顶层的CMakeLists.txt文件中添加一些简单的测试,来确认应用程序工作正常.

```cmake
include(CTest)

# does the application run
add_test (TutorialRuns Tutorial 25)

# does it sqrt of 25
add_test (TutorialComp25 Tutorial 25)
set_tests_properties (TutorialComp25 PROPERTIES PASS_REGULAR_EXPRESSION "25 is 5")

# does it handle negative numbers
add_test (TutorialNegative Tutorial -25)
set_tests_properties (TutorialNegative PROPERTIES PASS_REGULAR_EXPRESSION "-25 is 0")

# does it handle small numbers
add_test (TutorialSmall Tutorial 0.0001)
set_tests_properties (TutorialSmall PROPERTIES PASS_REGULAR_EXPRESSION "0.0001 is 0.01")

# does the usage message work?
add_test (TutorialUsage Tutorial)
set_tests_properties (TutorialUsage PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.*number")
```

在构建完成之后需要运行”ctest”命令来运行上面的测试.第一个测试用例用来确保应用程序是否有段错误,crash,返回值非0.这个是一个简单的CTest测试.接下来的几个测试都是利用”PASS_REGULAR_EXPRESSION”测试属性来验证输出是否包含特定字符串.在这个例子当中验证平方根计算是否正确,以及当输入错误信息时候输出使用信息.如果你希望添加更多测试来测试不同的输入值,你可以考虑创建一个宏像如下这样:

```cmake
#define a macro to simplify adding tests, then use it
macro (do_test arg result)
  add_test (TutorialComp${arg} Tutorial ${arg})
  set_tests_properties (TutorialComp${arg}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result})
endmacro (do_test)

# do a bunch of result based tests
do_test (25 "25 is 5")
do_test (-25 "-25 is 0")
```

do_test的每一次调用,就会有一次测试被添加到工程当中,测试的名字、输入、结果,基于传递的参数.



## 添加系统检测

下一步让我们考虑添加一些代码到我们的工程,以支持目标平台没有的特性.在这个例子当中,我们将添加一些代码来验证目标平台是否具有log和exp函数.当然几乎所有的平台都有这些函数,教程假设一下这种少数情况.如果平台有log函数,那么我们在mysqrt函数中用它来计算输出.第一步我们利用CheckFunctionExists.cmake宏测试这些函数是否有效,在顶层CMakeLists.txt文件中添加如下内容:

```cmake
# does this system provide the log and exp functions?
include (CheckFunctionExists)
check_function_exists (log HAVE_LOG)
check_function_exists (exp HAVE_EXP)
```

接着我们修改”TutorialConfig.h.in”来定义那些CMake在平台上查找到的值,修改如下:

```cmake
// does the platform provide exp and log functions?
#cmakedefine HAVE_LOG
#cmakedefine HAVE_EXP
```

在使用log和exp之前,确定他们是否被定义,是非常重要的.在CMake中,配置文件的命令会立刻使用目前的配置.最后在mysqrt函数中,我们可以提供一个基于log和exp函数的实现,代码如下:

```cmake
// if we have both log and exp then use them
#if defined (HAVE_LOG) && defined (HAVE_EXP)
  result = exp(log(x)*0.5);
#else // otherwise use an iterative approach
  . . .
```

**注意:这里需要在”mysqrt.cxx”文件里添加头文件#include “TutorialConfig.h”,否则找不到定义的宏**



