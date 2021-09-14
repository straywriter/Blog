---
title: CMake
top: false
mathjax: true
date: 2021-07-21 21:59:11
categories:
- CMake
---

-----



### 8.2 在CMake对文件的操作

#### 8.2.1 file命令：

- file(WRITE filename “message to write”… ):

  WRITE选项会写一条消息到名为filename中，如果文件存在，则会覆盖原文件，如果文件不存在，他将创建该文件。

- file(APPEND filename “message to write”… ):

  APPEND选项和WRITE选项一样，只是APPEND会写到文件的末尾。

- file(READ filename variable [LIMIT numBytes] [OFFSET offset] [HEX])：

  READ选项会将读取的文件内容存放到变量variable，读取numBytes个字节，从offset位置开始，如果指定了[HEX]参数，二进制代码就会转换为十六进制的转换方式。

- file(STRINGS filename variable [LIMIT_COUNT num] [LIMIT_INPUT numBytes] [LIMIT_OUTPUT numBytes] [LENGTH_MINIMUM numBytes] [LENGTH_MAXIMUM numBytes] [NEWLINE_CONSUME] [REGEX regex] [NO_HEX_CONVERSION])：

  STRINGS标志，将会从一个文件中将ASCII字符串的list解析出来，然后储存在variable 变量中，文件中的二进制数据将会被忽略，回车换行符会被忽略（可以设置NO_HEX_CONVERSION选项来禁止这个功能）。LIMIT_COUNT：设定了返回字符串的最大数量；LIMIT_INPUT：设置了从输入文件中读取的最大字节数；LIMIT_OUTPUT：设置了在输出变量中允许存储的最大字节数；LENGTH_MINIMUM：设置了返回字符串的最小长度，小于该长度的字符串将会被忽略；LENGTH_MAXIMUM设置了返回字符串的最大长度，大于该长度的字符串将会被忽略；NEWLINE_CONSUME：该标志允许新行被包含到字符串中，而不是终止他们；REGEX：指定了返回的字符串必须满足的正则表达式。例如：`file(STRINGS myfile.txt myfile)`，将myfile.txt中的文本按行存储到myfile这个变量中。

- file(GLOB variable [RELATIVE path] [globbing expressions]…)

  GLOB：该选项将会为所有匹配表达式的文件生成一个文件list，并将该list存放在variable 里面，文件名的查询表达式和正则表达式类似，匹配和正则表达式相似。

- file(GLOB_RECURSE variable [RELATIVE path] [FOLLOW_SYMLINKS] [globbing expressions]…)

  GLOB_RECURSE会生成一个类似于通常GLOB选项的list，不过该选项可以递归查找文件中的匹配项。例如：/dir/*.py -就会匹配所有在/dir文件下面的python文件。

- file(RENAME )

  RENAME选项对同一个文件系统下的一个文件或目录重命名。

- file(REMOVE [file1 …])

  REMOVE选项将会删除指定的文件，包括在子路径下的文件。

- file(REMOVE_RECURSE [file1 …])

  REMOVE_RECURSE选项会删除给定的文件以及目录，包括非空目录

- file(MAKE_DIRECTORY [directory1 directory2 …])

  MAKE_DIRECTORY选项将会创建指定的目录，如果它们的父目录不存在时，同样也会创建。

- file(RELATIVE_PATH variable directory file)

  RELATIVE_PATH选项会确定从direcroty参数到指定文件的相对路径，然后存到变量variable中。

- file(TO_CMAKE_PATH path result)

  TO_CMAKE_PATH选项会把path转换为一个以unix的 / 开头的cmake风格的路径

- file(TO_NATIVE_PATH path result)

  TO_NATIVE_PATH选项与TO_CMAKE_PATH选项很相似，但是它会把cmake风格的路径转换为本地路径风格

- file(DOWNLOAD url file [TIMEOUT timeout] [STATUS status] [LOG log] [EXPECTED_MD5 sum] [SHOW_PROGRESS])

  DOWNLOAD将给定的url下载到指定的文件中，如果指定了LOG log，下载的日志将会被输出到log中，如果指定了STATUS status选项下载操作的状态就会被输出到status里面，该状态的返回值是一个长度为2的list，list第一个元素是操作的返回值，是一个数字 ，第二个返回值是错误的字符串，错误信息如果是0，就表示没有错误；如果指定了TIMEOUT time选项，time秒之后，操作就会推出。如果指定了EXPECTED_MD5 sum选项，下载操作会认证下载的文件的实际MD5和是否与期望值相匹配，如果不匹配，操作将返回一个错误；如果指定了SHOW_PROGRESS，进度信息会被打印出来，直到操作完成。



示例

```
 FILE(WRITE filename "message to write"... )
 FILE(APPEND filename "message to write"... )
 FILE(READ filename variable)
 FILE(GLOB variable [RELATIVE path] [globbing expression_r_rs]...)
 FILE(GLOB_RECURSE variable [RELATIVE path] [globbing expression_r_rs]...)
 FILE(REMOVE [directory]...)
 FILE(REMOVE_RECURSE [directory]...)
 FILE(MAKE_DIRECTORY [directory]...)
 FILE(RELATIVE_PATH variable directory file)
 FILE(TO_CMAKE_PATH path result)
 FILE(TO_NATIVE_PATH path result)
```

