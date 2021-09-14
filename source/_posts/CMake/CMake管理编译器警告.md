---
title: CMake管理编译器警告
top: false
mathjax: true
date: 2021-03-22 21:59:11
categories:
- CMake
---

-----







-----





示例

```cmake
add_library(my_library …) 
target_include_directories(my_library PUBLIC include/)
target_link_libraries(my_library PUBLIC other_library)
target_compile_options(my_library PRIVATE -Werror -Wall -Wextra)
```

 使用生成器表达式

```cmake
target_compile_options(my_library PRIVATE
     $<$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:AppleClang>,$<CXX_COMPILER_ID:GNU>>:
          -Wall>
     $<$<CXX_COMPILER_ID:MSVC>:
          /W4>)
```

## 防止头文件中的警告





## 只包含头文件的库

```cmake
add_library(my_library INTERFACE)
target_sources(my_library INTERFACE …)
target_include_directories(my_library SYSTEM INTERFACE include/)
```



## 相关参考

[Tutorial: Managing Compiler Warnings with CMake](https://foonathan.net/2018/10/cmake-warnings/)

[cmake 构建c项目将警告当作错误 - 简书 (jianshu.com)](https://www.jianshu.com/p/7400fe0a0299)