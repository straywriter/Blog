---
title: CMake常用变量
top: false
mathjax: true
date: 2021-03-22 21:59:11
categories:
- CMake
---

-----





# a

项目信息

系统信息

开关选项

常用命令

### 项目信息

| PROJECT_SOURCE_DIR                | 工程的根目录                                                 |
| --------------------------------- | ------------------------------------------------------------ |
| PROJECT_BINARY_DIR                | 运行cmake命令的目录,通常是${PROJECT_SOURCE_DIR}/build        |
| CMAKE_INCLUDE_PATH                | 环境变量,非cmake变量                                         |
| CMAKE_LIBRARY_PATH                | 环境变量                                                     |
| CMAKE_CURRENT_SOURCE_DIR          | 当前处理的CMakeLists.txt所在的路径                           |
| CMAKE_CURRENT_BINARY_DIR          | target编译目录 使用ADD_SURDIRECTORY(src bin)可以更改此变量的值 SET(EXECUTABLE_OUTPUT_PATH <新路径>)并不会对此变量有影响,只是改变了最终目标文件的存储路径 |
| CMAKE_CURRENT_LIST_FILE           | 输出调用这个变量的CMakeLists.txt的完整路径                   |
| CMAKE_CURRENT_LIST_LINE           | 输出这个变量所在的行                                         |
| CMAKE_MODULE_PATH                 | 定义自己的cmake模块所在的路径 SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake),然后可以用INCLUDE命令来调用自己的模块 |
| EXECUTABLE_OUTPUT_PATH            | 重新定义目标二进制可执行文件的存放位置                       |
| LIBRARY_OUTPUT_PATH               | 重新定义目标链接库文件的存放位置                             |
| PROJECT_NAME                      | 返回通过PROJECT指令定义的项目名称                            |
| CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS | 用来控制IF ELSE语句的书写方式                                |



### 系统变量

| CMAKE_MAJOR_VERSION    | cmake主版本号,如3.8.5中的3                |
| ---------------------- | ----------------------------------------- |
| CMAKE_MINOR_VERSION    | cmake次版本号,如3.8.5中的8                |
| CMAKE_PATCH_VERSION    | cmake补丁等级，如3.8.5中的5               |
| CMAKE_SYSTEM           | 系统名称                                  |
| CAMKE_SYSTEM_NAME      | 不包含版本的系统名                        |
| CMAKE_SYSTEM_VERSION   | 系统版本                                  |
| CMAKE_SYSTEM_PROCESSOR | 处理器名称                                |
| UNIX                   | 在所有的类UNIX平台为TRUE,包括OS X和cygwin |
| WIN32                  | 在所有的win32平台为TRUE,包括cygwin        |



### 开关选项

| 编译控制开关名    | 描述                                                    |
| :---------------- | :------------------------------------------------------ |
| BUILD_SHARED_LIBS | 使用 `ADD_LIBRARY` 时生成动态库                         |
| BUILD_STATIC_LIBS | 使用 `ADD_LIBRARY` 时生成静态库                         |
| CMAKE_C_FLAGS     | 设置 C 编译选项,也可以通过指令 ADD_DEFINITIONS()添加。  |
| CMAKE_CXX_FLAGS   | 设置 C++编译选项,也可以通过指令 ADD_DEFINITIONS()添加。 |

### 常用命令

| PROJECT                | PROJECT(projectname [CXX] [C] [Java])                        | 指定工程名称,并可指定工程支持的语言。支持语言列表可忽略,默认支持所有语言 |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| SET                    | SET(VAR [VALUE] [CACHE TYPE DOCSTRING [FORCE]])              | 定义变量(可以定义多个VALUE,如SET(SRC_LIST main.c util.c reactor.c)) |
| MESSAGE                | MESSAGE([SEND_ERROR \| STATUS \| FATAL_ERROR] “message to display” …) | 向终端输出用户定义的信息或变量的值 SEND_ERROR, 产生错误,生成过程被跳过 STATUS, 输出前缀为—的信息 FATAL_ERROR, 立即终止所有cmake过程 |
| ADD_EXECUTABLE         | ADD_EXECUTABLE(bin_file_name ${SRC_LIST})                    | 生成可执行文件                                               |
| ADD_LIBRARY            | ADD_LIBRARY(libname [SHARED \| STATIC \| MODULE] [EXCLUDE_FROM_ALL] SRC_LIST) | 生成动态库或静态库 SHARED 动态库 STATIC 静态库 MODULE 在使用dyld的系统有效,若不支持dyld,等同于SHARED EXCLUDE_FROM_ALL 表示该库不会被默认构建 |
| SET_TARGET_PROPERTIES  |                                                              | 设置输出的名称,设置动态库的版本和API版本                     |
| CMAKE_MINIMUM_REQUIRED | CMAKE_MINIMUM_REQUIRED(VERSION version_number [FATAL_ERROR]) | 声明CMake的版本要求                                          |
| ADD_SUBDIRECTORY       | ADD_SUBDIRECTORY(src_dir [binary_dir] [EXCLUDE_FROM_ALL])    | 向当前工程添加存放源文件的子目录,并可以指定中间二进制和目标二进制的存放位置 EXCLUDE_FROM_ALL含义：将这个目录从编译过程中排除 |
| INCLUDE_DIRECTORIES    | INCLUDE_DIRECTORIES([AFTER \| BEFORE] [SYSTEM] dir1 dir2 … ) | 向工程添加多个特定的头文件搜索路径,路径之间用空格分隔,如果路径包含空格,可以使用双引号将它括起来,默认的行为为追加到当前头文件搜索路径的后面。有如下两种方式可以控制搜索路径添加的位置：CMAKE_INCLUDE_DIRECTORIES_BEFORE,通过SET这个cmake变量为on,可以将添加的头文件搜索路径放在已有路径的前面通过AFTER或BEFORE参数,也可以控制是追加还是置前 |
| LINK_DIRECTORIES       | LINK_DIRECTORIES(dir1 dir2 …)                                | 添加非标准的共享库搜索路径                                   |
| TARGET_LINK_LIBRARIES  | TARGET_LINK_LIBRARIES(target lib1 lib2 …)                    | 为target添加需要链接的共享库                                 |
| ADD_DEFINITIONS        | ADD_DEFINITIONS(-DENABLE_DEBUG -DABC)                        | 向C/C++编译器添加-D定义，参数之间用空格分隔                  |
| ADD_DEPENDENCIES       | ADD_DEPENDENCIES(target-name depend-target1 depend-target2 …) | 定义target依赖的其他target,确保target在构建之前,其依赖的target已经构建完毕 |
| AUX_SOURCE_DIRECTORY   | AUX_SOURCE_DIRECTORY(dir VAR)                                | 发现一个目录下所有的源代码文件并将列表存储在一个变量中 把当前目录下的所有源码文件名赋给变量DIR_HELLO_SRCS |
| EXEC_PROGRAM           | EXEC_PROGRAM(Executable [dir where to run] [ARGS <args>][OUTPUT_VARIABLE <var>] [RETURN_VALUE <value>]) | 用于在指定目录运行某个程序（默认为当前CMakeLists.txt所在目录）,通过ARGS添加参数,通过OUTPUT_VARIABLE和RETURN_VALUE获取输出和返回值 |
| INCLUDE                | INCLUDE(file [OPTIONAL]) 用来载入CMakeLists.txt文件 INCLUDE(module [OPTIONAL])用来载入预定义的cmake模块 | OPTIONAL参数的左右是文件不存在也不会产生错误 可以载入一个文件,也可以载入预定义模块（模块会在CMAKE_MODULE_PATH指定的路径进行搜索） 载入的内容将在处理到INCLUDE语句时直接执行 |
| FIND_                  |                                                              | FIND_FILE(<VAR> name path1 path2 …) VAR变量代表找到的文件全路径,包含文件名FIND_LIBRARY(<VAR> name path1 path2 …) VAR变量代表找到的库全路径,包含库文件FIND_PATH(<VAR> name path1 path2 …) VAR变量代表包含这个文件的路径FIND_PROGRAM(<VAR> name path1 path2 …) VAR变量代表包含这个程序的全路径FIND_PACKAGE(<name> [major.minor] [QUIET] [NO_MODULE] [[REQUIRED \| COMPONENTS] [componets …]])  *用来调用预定义在CMAKE_MODULE_PATH下的Find<name>.cmake模块,你也可以自己定义Find<name>模块,通过SET(CMAKE_MODULE_PATH \*dir)将其放入工程的某个目录供工程使用** |
| IF                     | 数字比较表达式 IF (variable LESS number) IF (string LESS number) IF (variable GREATER number) IF (string GREATER number) IF (variable EQUAL number) IF (string EQUAL number)按照字母表顺序进行比较 IF (variable STRLESS string) IF (string STRLESS string) IF (variable STRGREATER string) IF (string STRGREATER string) IF (variable STREQUAL string) IF (string STREQUAL string) | IF (expression), expression不为：空,0,N,NO,OFF,FALSE,NOTFOUND或<var>_NOTFOUND,为真 IF (not exp), 与上面相反 IF (var1 AND var2) IF (var1 OR var2) IF (COMMAND cmd) 如果cmd确实是命令并可调用,为真 IF (EXISTS dir) IF (EXISTS file) 如果目录或文件存在,为真 IF (file1 IS_NEWER_THAN file2),当file1比file2新,或file1/file2中有一个不存在时为真,文件名需使用全路径 IF (IS_DIRECTORY dir) 当dir是目录时,为真 IF (DEFINED var) 如果变量被定义,为真 IF (var MATCHES regex) 此处var可以用var名,也可以用${var} IF (string MATCHES regex) |
| WHILE                  | `WHILE(condition)    COMMAND1(ARGS ...)    COMMAND2(ARGS ...)    ... ENDWHILE(condition)` | 其真假判断条件可以参考IF指令                                 |
| FOREACH                |                                                              | FOREACH指令的使用方法有三种形式：列表：`FOREACH(loop_var arg1 arg2 ...)     COMMAND1(ARGS ...)     COMMAND2(ARGS ...) ... ENDFOREACH(loop_var)`范围：`FOREACH(loop_var RANGE total)    COMMAND1(ARGS ...)    COMMAND2(ARGS ...)    ... ENDFOREACH(loop_var)`范围和步进：`FOREACH(loop_var RANGE start stop [step])    COMMAND1(ARGS ...)    COMMAND2(ARGS ...)    ... ENDFOREACH(loop_var)`从start开始到stop结束,以step为步进, 注意：直到遇到ENDFOREACH指令,整个语句块才会得到真正的执行。 |



### CMake常用变量

| 变量名                            |                         变量说明                          |
| --------------------------------- | :-------------------------------------------------------: |
| PROJECT_NAME                      |             返回通过PROJECT指令定义的项目名称             |
| PROJECT_SOURCE_DIR                |          CMake源码地址，即cmake命令后指定的地址           |
| PROJECT_BINARY_DIR                | 运行cmake命令的目录,通常是PROJECT_SOURCE_DIR下的build目录 |
| CMAKE_MODULE_PATH                 |               定义自己的cmake模块所在的路径               |
| CMAKE_CURRENT_SOURCE_DIR          |            当前处理的CMakeLists.txt所在的路径             |
| CMAKE_CURRENT_LIST_DIR            |                      当前文件夹路径                       |
| CMAKE_CURRENT_LIST_FILE           |        输出调用这个变量的CMakeLists.txt的完整路径         |
| CMAKE_CURRENT_LIST_LINE           |                   输出这个变量所在的行                    |
| CMAKE_RUNTIME_OUTPUT_DIRECTORY    |                    生成可执行文件路径                     |
| CMAKE_LIBRARY_OUTPUT_DIRECTORY    |                    生成库的文件夹路径                     |
| CMAKE_BUILD_TYPE                  |     指定基于make的产生器的构建类型（Release，Debug）      |
| CMAKE_C_FLAGS                     |     *.C文件编译选项，如 *-std=c99 -O3 -march=native*      |
| CMAKE_CXX_FLAGS                   |   *.CPP文件编译选项，如 *-std=c++11 -O3 -march=native*    |
| CMAKE_CURRENT_BINARY_DIR          |                      target编译目录                       |
| CMAKE_INCLUDE_PATH                |                   环境变量,非cmake变量                    |
| CMAKE_LIBRARY_PATH                |                         环境变量                          |
| CMAKE_STATIC_LIBRARY_PREFIX       |               静态库前缀, Linux下默认为lib                |
| CMAKE_STATIC_LIBRARY_SUFFIX       |                静态库后缀，Linux下默认为.a                |
| CMAKE_SHARED_LIBRARY_PREFIX       |               动态库前缀，Linux下默认为lib                |
| CMAKE_SHARED_LIBRARY_SUFFIX       |               动态库后缀，Linux下默认为.so                |
| BUILD_SHARED_LIBS                 |           如果为ON，则add_library默认创建共享库           |
| CMAKE_INSTALL_PREFIX              |              配置安装路径，默认为/usr/local               |
| CMAKE_ABSOLUTE_DESTINATION_FILES  |        安装文件列表时使用ABSOLUTE DESTINATION 路径        |
| CMAKE_AUTOMOC_RELAXED_MODE        |              在严格和宽松的automoc模式间切换              |
| CMAKE_BACKWARDS_COMPATIBILITY     |                 构建工程所需要的CMake版本                 |
| CMAKE_COLOR_MAKEFILE              |         开启时，使用Makefile产生器会产生彩色输出          |
| CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS |               用来控制IF ELSE语句的书写方式               |