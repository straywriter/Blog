---
title: CMake File使用
top: false
mathjax: true
date: 2021-07-03 21:59:11
categories:
- CMake
---

-----

-----

file

```cmake
Reading
  file(READ <filename> <out-var> [...])
  file(STRINGS <filename> <out-var> [...])
  file(<HASH> <filename> <out-var>)
  file(TIMESTAMP <filename> <out-var> [...])
  file(GET_RUNTIME_DEPENDENCIES [...])

Writing
  file({WRITE | APPEND} <filename> <content>...)
  file({TOUCH | TOUCH_NOCREATE} [<file>...])
  file(GENERATE OUTPUT <output-file> [...])
  file(CONFIGURE OUTPUT <output-file> CONTENT <content> [...])

Filesystem
  file({GLOB | GLOB_RECURSE} <out-var> [...] [<globbing-expr>...])
  file(MAKE_DIRECTORY [<dir>...])
  file({REMOVE | REMOVE_RECURSE } [<files>...])
  file(RENAME <oldname> <newname> [...])
  file(COPY_FILE <oldname> <newname> [...])
  file({COPY | INSTALL} <file>... DESTINATION <dir> [...])
  file(SIZE <filename> <out-var>)
  file(READ_SYMLINK <linkname> <out-var>)
  file(CREATE_LINK <original> <linkname> [...])
  file(CHMOD <files>... <directories>... PERMISSIONS <permissions>... [...])
  file(CHMOD_RECURSE <files>... <directories>... PERMISSIONS <permissions>... [...])

Path Conversion
  file(REAL_PATH <path> <out-var> [BASE_DIRECTORY <dir>] [EXPAND_TILDE])
  file(RELATIVE_PATH <out-var> <directory> <file>)
  file({TO_CMAKE_PATH | TO_NATIVE_PATH} <path> <out-var>)

Transfer
  file(DOWNLOAD <url> [<file>] [...])
  file(UPLOAD <file> <url> [...])

Locking
  file(LOCK <path> [...])

Archiving
  file(ARCHIVE_CREATE OUTPUT <archive> PATHS <paths>... [...])
  file(ARCHIVE_EXTRACT INPUT <archive> [...])
```



# Reading

```cmake
file(READ <filename> <variable>
     [OFFSET <offset>] [LIMIT <max-in>] [HEX])
```

读取文件名为 `<filename>` 的文件并将其内容存储到 `<variable>` 变量中。

可选的参数： `<offset>` 指定起始读取位置，`<max-in>` 最多读取字节数，`HEX` 将数据转为十六进制（处理二进制数据十分有用）。

```cmake
file(STRINGS <filename> <variable> [<options>...])
```

从 `<filename>` 文件解析一串 ASCII 字符串并存储到 `<variable>` 中。文件中的二进制文件将被忽略。回车字符（`\r`,CR）将被忽略。选项如下：

- `LENGTH_MAXIMUM <max-len>` ：只考虑比给定长度少的字符串。
- `LENGTH_MINIMUM <min-len>` ：只考虑比给定长度多的字符串。
- `LIMIT_COUNT <max-num>` ：限制提取不同字符的数量。
- `LIMIT_INPUT <max-in>` ：限制从文件读取的字节总数。
- `LIMIT_OUTPUT <max-out>` ： 限制存储到 `<variable>` 变量中的字节数。
- `NEWLINE_CONSUME` ： 将新行字符串（`\n`,LF）作为字符内容的一部分而不是终止。
- `NO_HEX_CONVERSION` ：Intel Hex 和 Motorola S-record 文件在读取时自动转化为二进制，除非这个参数被指定。
- `REGEX <regex>` ：只考虑匹配正则表达式的字符串。
- `ENCODING <encoding-type>` ： 指定字符串的编码，目前支持的编码有：UTF-8, UTF-16LE, UTF-16BE, UTF-32LE, UTF-32BE。如果 ENCODING 选项没有指定且文件有字节顺序标志（Byte Order Mark），ENCODING 选择会根据字节顺序标志选择默认值。

**例如：**

```cmake
file(STRINGS myfile.txt myfile)
```

上面代码存储文件中的列表，其中每一个元素在文件中占一行。

```cmake
file(<HASH> <filename> <variable>)
```

上面代码计算文件内容的哈希值并存储在 `<variable>` 中。支持的 `<HASH>` 算法名字在 [string()](https://cmake.org/cmake/help/latest/command/string.html#supported-hash-algorithms) 命令中列出。

```cmake
file(TIMESTAMP <filename> <variable> [<format>] [UTC])
```

计算一个体现文件修改时间的字符串并将其存储到 `<variable>` 中。如果该命令不能获得时间戳变量则输出为空字符串（""）。

查看 [string(TIMESTAMP)](https://cmake.org/cmake/help/latest/command/string.html#command:string) 命令来获得 `<format>` 和 `UTC` 选项。

# Writing

```cmake
file(WRITE <filename> <content>...)
file(APPEND <filename> <content>...)
```

写入 `<content>` 到 `<filename>` 文件中。

如果文件不存在则创建。

如果文件已存在，`WRITE` 模式将覆盖内容，如果为 `APPEND` 模式将追加内容。

任何在 `<filename>` 文件路径中的不存在文件夹都将被创建。

如果文件是构建输入，使用 [configure_file()](https://cmake.org/cmake/help/latest/command/configure_file.html#command:configure_file) 命令来保证只在内容更改时更新文件。

```cmake
file(TOUCH [<files>...])
file(TOUCH_NOCREATE [<files>...])
```

如果文件不存在创建一个空文件。如果文件已存在，在函数执行时，它的访问和修改时间将被更新到函数调用执行时刻。

使用 `TOUCH_NOCREATE` 访问一个已存在的文件但不会创建它，如果文件不存在，该命令将被静默的忽略。

使用 `TOUCH` 和 `TOUCH_NOCREATE` 都将修改已存在文件的内容。

```cmake
file(GENERATE OUTPUT output-file
     <INPUT input-file|CONTENT content>
     [CONDITION expression])
```

给当前 [CMake Generator](https://cmake.org/cmake/help/latest/manual/cmake-generators.7.html#manual:cmake-generators(7)) 支持的每一个构建配置产生一个输出文件。从输入内容计算 [生成器表达式(generator expressions)](https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html#manual:cmake-generator-expressions(7)) 并产生输出内容。选项如下：

`CONDITION <condition>` ： 对条件为真的特定配置产生输出文件。在计算生成器表达式之后条件必须为0或者1.

`CONTENT <content>` ：使用明确指定的内容作为输入。

`INPUT <input-file>` : 从指定文件获取内容作为输入。如果是相对路径认为是相对于 [CMAKE_CURRENT_SOURCE_DIR](https://cmake.org/cmake/help/latest/variable/CMAKE_CURRENT_SOURCE_DIR.html#variable:CMAKE_CURRENT_SOURCE_DIR) 。详情查看 [CMP0070](https://cmake.org/cmake/help/latest/policy/CMP0070.html#policy:CMP0070) 规则。

`OUTPUT <output-file>` ：指定产生的输出文件名。使用生成器表达式（例如`$<CONFIG>`）产生特定的配置输出文件名。如果生成内容一致，则多个配置可能产生相同输出文件。否则 `<output-file>` 必须给每个配置计算一个单独的文件名。相对路径（在计算生成器表达式之后）被认为是相对于 [CMAKE_CURRENT_BINARY_DIR](https://cmake.org/cmake/help/latest/variable/CMAKE_CURRENT_BINARY_DIR.html#variable:CMAKE_CURRENT_BINARY_DIR) 。详情查看 [CMP0070](https://cmake.org/cmake/help/latest/policy/CMP0070.html#policy:CMP0070) 规则。

必须给定确切的一个 `CONTENT` 或 `INPUT` 。一个特定的 `OUTPUT` 文件最多被一个 `file(GENERATE)` 调用(invocation)命名。在后续 cmake 运行时，只有当其内容修改了，生成文件才会被修改且对应的时间戳被更新。

还要注意，`file（GENERATE）`直到生成阶段才会创建输出文件。当 `file(GENERATE)` 命令返回时输出文件还没有被写入，只有当处理完整个项目的 `CMakeLists.txt` 文件才会被写入。

# Filesystem

```cmake
file(GLOB <variable>
     [LIST_DIRECTORIES true|false] [RELATIVE <path>] [CONFIGURE_DEPENDS]
     [<globbing-expressions>...])
file(GLOB_RECURSE <variable> [FOLLOW_SYMLINKS]
     [LIST_DIRECTORIES true|false] [RELATIVE <path>] [CONFIGURE_DEPENDS]
     [<globbing-expressions>...])
```

产生一个匹配 `<globbing-expressions>` 的文件列表并将它存储到变量 `<variable>` 中。文件名替代表达式和正则表达式相似，但更简单。如果 `RELATIVE` 标志位被设定，将返回指定路径的相对路径。结果将按字典顺序排序。

如果 `CONFIGURE_DEPENDS` 标志位被指定，CMake 将在编译时给主构建系统添加逻辑来检查目标，以重新运行 `GLOB` 标志的命令。如果任何输出被改变，CMake都将重新生成这个构建系统。

> 注意：我们不推荐使用 GLOB 来从源文件树收集源文件列表。如果当源文件添加或删除时没有 CMakeLists.txt 文件被修改，那么在 CMake 重新生成时并不会识别出它们。`CONFIGURE_DEPENDS` 标志位可能不会再所有生成器上可靠的工作，如果一个新的生成器在以后被添加，并不会被支持。如果项目使用它将会被卡主。即使 `CONFIGURE_DEPENDS` 可靠的工作，在每个重新构建的过程中做检查也十分浪费性能。

文件名替代表达式的使用示例如下：

```shell
*.cxx      - 匹配所有后缀名为 cxx 的文件
*.vt?      - 匹配所有后缀名为 vta,...,vtz 的文件
f[3-5].txt - 匹配 f3.txt, f4.txt, f5.txt 文件
```

`GLOB_RECURSE` 将会递归所有匹配文件夹的子文件夹和匹配的文件。子文件夹为符号链接时只有当 `FOLLOW_SYMLINKS` 被指定或规则 [CMP0009](https://cmake.org/cmake/help/latest/policy/CMP0009.html#policy:CMP0009) 没有设置为 `NEW` 时才会被递归。

默认 `GLOB_RECURSE` 省略结果列表中的目录，设置 `LIST_DIRECTORIES` 为 true 来添加目录到结果列表中。如果  `FOLLOW_SYMLINKS` 被指定或规则 [CMP0009](https://cmake.org/cmake/help/latest/policy/CMP0009.html#policy:CMP0009) 没有设置为 `OLD`  。`LIST_DIRECTORIES` 将符号链接作为路径。

递归文件名包括的例子：

```undefined
/dir/*.py  - match all python files in /dir and subdirectories
```



```cmake
file(RENAME <oldname> <newname>)
```

在文件系统中从 `<oldname>` 移动文件或文件夹到 `<newname>` ，自动替换目标路径。

```cmake
file(REMOVE [<files>...])
file(REMOVE_RECURSE [<files>...])
```

移动指定文件，`REMOVE_RECURSE` 模式将移动给定文件和文件夹，和非空文件夹。如果指定文件不存在不会给出错误。

```cmake
file(MAKE_DIRECTORY [<directories>...])
```

创建给定文件夹，并根据需求创建其父文件夹。



```cmake
file(<COPY|INSTALL> <files>... DESTINATION <dir>
     [FILE_PERMISSIONS <permissions>...]
     [DIRECTORY_PERMISSIONS <permissions>...]
     [NO_SOURCE_PERMISSIONS] [USE_SOURCE_PERMISSIONS]
     [FILES_MATCHING]
     [[PATTERN <pattern> | REGEX <regex>]
      [EXCLUDE] [PERMISSIONS <permissions>...]] [...])
```

`COPY` 表示复制文件、路径和符号链接到目标路径。以相对当前源文件路径计算输入路径的相对路径，以相对当前构建路径计算目标路径。复制保留输入文件时间戳，文件如果存在目标路径且时间戳相同，会对其进行优化。复制保留输入文件访问权限，除非明确权限或 指定`NO_SOURCE_PERMISSIONS`(默认指定 `USE_SOURCE_PERMISSIONS`)。

查看 [install(DIRECTORY)](https://cmake.org/cmake/help/latest/command/install.html#command:install) 命令了解文件权限，`FILES_MATCHING`, `PATTERN`, `REGEX` 和 `EXCLUDE` 选项。复制文件夹时会保留它们的文件结构，即使使用选项来选择文件的子集。

`INSTALL` 选项和 `COPY` 略有不同：它打印状态信息（根据 [CMAKE_INSTALL_MESSAGE](https://cmake.org/cmake/help/latest/variable/CMAKE_INSTALL_MESSAGE.html#variable:CMAKE_INSTALL_MESSAGE)） 变量，默认为 `NO_SOURCE_PERMISSIONS` 选项。安装脚本使用 [install()](https://cmake.org/cmake/help/latest/command/install.html#command:install) 命令产生，install() 命令使用了 `INSTALL` 选项并附带一些内部选项供内部使用。

# Path Conversion

```cmake
file(RELATIVE_PATH <variable> <directory> <file>)
```

计算文件 `<file>` 相对`<directory>` 的相对路径并存储到 `<viriable>` 变量中。

```cmake
file(TO_CMAKE_PATH "<path>" <variable>)
file(TO_NATIVE_PATH "<path>" <variable>)
```

`<TO_CMAKE_PATH>` 模式转换一个本地 `<path>` 为带 (/) 的 cmake 风格路径。输入可以是一个路径或系统搜索路径（如：`$ENV{PATH}`）。一个搜索路径可以被转换为以 `:` 分割的 cmake 风格列表。

`<TO_NATIVE_PATH>` 模式转换一个 cmake 风格的 `<path>` 为一个本地路径，路径根据平台 `\`（windows 平台）或使用 `/` （其他平台）。

应总是使用双引号将将 `<path>` 括起来以保证它在此命令中作为单个参数对待。

# Transfer

```cmake
file(DOWNLOAD <url> <file> [<options>...])
file(UPLOAD   <file> <url> [<options>...])
```

`<DOWNLOAD>` 模式将下载指定的 `<url>` 到本地 `<file>`。`UPLOAD` 模式将上传本地 `<file>` 到给定的 `<url>`。

`<DOWNLOAD>` 和 `<UPLOAD>` 都有以下选项：

`INACTIVITY_TIMEOUT <seconds>`: 在活动停止一段时间后终止操作。

`LOG <variable>`: 存储人眼可读的本操作日志到指定变量。

`SHOW_PROGRESS`: 在操作完成前，将过程信息作为状态信息进行打印。

`STATUS <variable>`: 将操作结果的状态存储到指定变量中。状态是一个以 `;` 分割有2个元素的列表。第一个元素是操作的数字返回值，第二个变量是错误信息的字符串。数字`0` 意味着在操作过程中没有错误。

`TIMEOUT<seconds>`: 在指定的总时间过后终止操作。

`UPERPWD <username>:<password>`: 设置操作的用户名和密码。

`HTTPHEADER <HTTP-header>`: 操作的 HTTP 头。子选项可以重复多次。

`NETRC <level>`: 指定操作将使用哪个 .netrc 文件。如果该选项没有给定，将使用 `CMAKE_NETRC` 变量进行替代。可用等级如下：

- `IGNORED`: .netrc 文件被忽略，该选项为默认选项。
- `OPTIONAL`: .netrc 文件是可选的，URL 中的信息优先使用。该文件将被扫描，以查找在 URL 中没有指定的哪些信息。
- `REQUIRED`: .netrc 文件是必须的，URL 中的信息将被忽略。

`NETRC_FILE <file>`: 如果 `NETRC` 等级为 `OPTIONAL` 或`REQUIRED`，该参数给你家目录下的 .netrc 文件指定一个替代文件。如果该参数没有被指定，将会使用 `CMAKE_NETRC 变量替代。

如果两个 `NETRC` 参数都没有指定，CMake 将对应的检测 `CMAKE_NETRC` 和 `CMAKE_NETRC_FILE` 变量。

关于`DOWNLOAD`的额外选项 ：

`EXPECTED_HASH ALGO=<value>`: 验证下载内容的 hash 值是否匹配给定值，其中 `ALGO` 是 `file(<HASH>)`支持的其中一种算法。如果不匹配，操作失败返回错误。

`EXPECTED_MD5 <value>`: Historical short-hand for `EXPECTED_HASH MD5=<value>`.

`TLS_VERIFY <ON|OFF>`: 指定哪里去验证服务器的 `https://` URLS 证书。默认不验证。

`TLS_CAINFO <file>`: 给 `https://` URLs 指定用户证书授权文件。

对于 `https://` URLs CMake 必须在支持 OpenSSL 下进行构建。默认 `TLS/SSL` 验证是不检查的。设置 `TLS_VERIFY` 为 `ON` 来检查验证或者使用 `EXPECTED_HASH` 来验证现在文件的内容。如果两个 `TLS` 都没有指定，CMake 将对应的检测 `CMAKE_TLS_VERIFY` 和 `CMAKE_TLS_CAINFO` 变量。

# Locking

```cmake
file(LOCK <path> [DIRECTORY] [RELEASE]
     [GUARD <FUNCTION|FILE|PROCESS>]
     [RESULT_VARIABLE <variable>]
     [TIMEOUT <seconds>])
```

如果没有指定 `DIRECTORY` 选项和 `<path>/cmake.lock` 文件，将锁定一个 `<path>` 指定的文件。文件将会在 `GUARD` 选项（默认为`PROCESS`）定义的区域内锁定。`RELEASE`选项可以被明确的来解锁文件。如果 `TIMEOUT`选项没有指定，CMake 将会等待锁定完成或出现错误失败。如果 `TIMEOUT` 被设置为0，锁将会执行一次并立刻报告执行结果。如果 `TIMEOUT` 不是0，CMake 将在 `<seconds>` 指定的时间内尝试锁定文件。如果没有指定 `RESULT_VARIABLE` 选项，任何错误将会作为严重错误来打断操作。否则结果将被存储在 `<variable>` 中且在成功时返回0或在失败时返回错误信息。

注意锁只是一个建议——无法保证其他进程响应该锁，也就是说，所锁同步两个或多个共享一些可修改资源的 CMake 实例。相同的逻辑使用 `DIRECTORY` 选项——锁住父文件夹并不能阻止其他 `LOCK` 命令锁任何子文件夹或文件。

不允许尝试锁定文件两次。任何中间文件夹和文件本身不存在的话都将被创建。`RELEASE` 操作期间 `GUARD` 和 `TIMEOUT` 参数将被忽略。



## 相关参考

[file — CMake 3.21.2 Documentation](https://cmake.org/cmake/help/latest/command/file.html)

[cmake file命令详解 - 简书 (jianshu.com)](https://www.jianshu.com/p/ed151fdcf473)

[CMakeFile命令之file - Boblim - 博客园 (cnblogs.com)](https://www.cnblogs.com/fnlingnzb-learner/p/7221648.html)

[cmake使用教程（十）-关于file - 掘金 (juejin.cn)](https://juejin.cn/post/6844903634170298382)