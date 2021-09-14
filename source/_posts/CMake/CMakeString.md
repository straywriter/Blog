---
title: CMake string 使用
top: false
mathjax: true
date: 2021-06-21 21:59:11
categories:
- CMake
---

-----

## string

```cmake
Search and Replace
  string(FIND <string> <substring> <out-var> [...])
  string(REPLACE <match-string> <replace-string> <out-var> <input>...)
  string(REGEX MATCH <match-regex> <out-var> <input>...)
  string(REGEX MATCHALL <match-regex> <out-var> <input>...)
  string(REGEX REPLACE <match-regex> <replace-expr> <out-var> <input>...)

Manipulation
  string(APPEND <string-var> [<input>...])
  string(PREPEND <string-var> [<input>...])
  string(CONCAT <out-var> [<input>...])
  string(JOIN <glue> <out-var> [<input>...])
  string(TOLOWER <string> <out-var>)
  string(TOUPPER <string> <out-var>)
  string(LENGTH <string> <out-var>)
  string(SUBSTRING <string> <begin> <length> <out-var>)
  string(STRIP <string> <out-var>)
  string(GENEX_STRIP <string> <out-var>)
  string(REPEAT <string> <count> <out-var>)

Comparison
  string(COMPARE <op> <string1> <string2> <out-var>)

Hashing
  string(<HASH> <out-var> <input>)

Generation
  string(ASCII <number>... <out-var>)
  string(HEX <string> <out-var>)
  string(CONFIGURE <string> <out-var> [...])
  string(MAKE_C_IDENTIFIER <string> <out-var>)
  string(RANDOM [<option>...] <out-var>)
  string(TIMESTAMP <out-var> [<format string>] [UTC])
  string(UUID <out-var> ...)

JSON
  string(JSON <out-var> [ERROR_VARIABLE <error-var>]
         {GET | TYPE | LENGTH | REMOVE}
         <json-string> <member|index> [<member|index> ...])
  string(JSON <out-var> [ERROR_VARIABLE <error-var>]
         MEMBER <json-string>
         [<member|index> ...] <index>)
  string(JSON <out-var> [ERROR_VARIABLE <error-var>]
         SET <json-string>
         <member|index> [<member|index> ...] <value>)
  string(JSON <out-var> [ERROR_VARIABLE <error-var>]
         EQUAL <json-string1> <json-string2>)
```







## 相关参考

[CMake/CMakeLists.txt at master · Kitware/CMake (github.com)](https://github.com/Kitware/CMake/blob/master/Tests/StringFileTest/CMakeLists.txt)

[CMake - string - 字符串操作。 Synopsis 搜索和替换 用普通字符串搜索和替换 返回在提供的  中找到给定  的位置。如果使用了 RE - 中文 (runebook.dev)](https://runebook.dev/zh-CN/docs/cmake/command/string)

[CMake 手册详解 - ITNewBee](https://itnewbee.org/cmake-手册详解/)

[tianyalu/NeCMakeListsFile: CMakeLists.txt文件详解 (github.com)](https://github.com/tianyalu/NeCMakeListsFile)

[tianyalu/NeCMake: CMake语法详解 (github.com)](https://github.com/tianyalu/NeCMake)