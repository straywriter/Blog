---
title: CMake target_compile_options使用
top: false
mathjax: true
date: 2021-02-22 21:59:11
categories:
- CMake
---

-----

## 示例

```cmake
target_compile_options(mylib PRIVATE   -O2) # only internal
target_compile_options(mylib INTERFACE -gl) # only external
target_compile_options(mylib PUBLIC    -g)  # same as PRIVATE + INTERFACE

# multiple targets and flags
target_compile_options(mylib1 mylib2 PRIVATE -Wall -Wextra)

target_compile_options(    mylib PUBLIC -DUSEXX)  # Bad
target_compile_definitions(mylib PUBLIC -DUSEXX)  # OK

add_compile_options(-Wall -Wextra) # for all targets in current directory
add_compile_options(-DUSEXX)       # Bad
add_definitions(-DUSEXX)           # OK
```



### 使用cmake生成表达式

In you case you can use:

```cmake
target_compile_options(${TARGET} PRIVATE
          $<$<COMPILE_LANGUAGE:CXX>:${BUILD_FLAGS_FOR_CXX}>
          $<$<COMPILE_LANGUAGE:C>:${BUILD_FLAGS_FOR_C}>)
```

or about compilers:

```cmake
target_compile_options(${TARGET} PRIVATE
          $<$<CXX_COMPILER_ID:Clang>:${BUILD_FLAGS_FOR_CLANG}>
          $<$<CXX_COMPILER_ID:GNU>:${BUILD_FLAGS_FOR_GCC}>
          $<$<CXX_COMPILER_ID:MSVC>:${BUILD_FLAGS_FOR_VISUAL}>)
```

## 相关参考

[Does set_target_properties in CMake override CMAKE_CXX_FLAGS?](https://stackoverflow.com/questions/5096881/does-set-target-properties-in-cmake-override-cmake-cxx-flags)

[It is impossible to reliably use target_compile_options() on MSVC.](https://gitlab.kitware.com/cmake/cmake/-/issues/19084)

[Change default value of CMAKE_CXX_FLAGS_DEBUG and friends in CMake](https://stackoverflow.com/questions/28732209/change-default-value-of-cmake-cxx-flags-debug-and-friends-in-cmake)

[cmake 编译器id](https://cmake.org/cmake/help/v3.13/command/target_link_options.html)

[Does set_target_properties in CMake override CMAKE_CXX_FLAGS?](https://stackoverflow.com/questions/5096881/does-set-target-properties-in-cmake-override-cmake-cxx-flags)

[Compile with /MT instead of /MD using CMake](https://stackoverflow.com/questions/14172856/compile-with-mt-instead-of-md-using-cmake)

[Modern way to set compiler flags in cross-platform cmake project](https://stackoverflow.com/questions/45955272/modern-way-to-set-compiler-flags-in-cross-platform-cmake-project)

[What is the modern method for setting general compile flags in CMake?](https://stackoverflow.com/questions/23995019/what-is-the-modern-method-for-setting-general-compile-flags-in-cmake)

[Difference between add_compile_options and SET(CMAKE_CXX_FLAGS…)](https://stackoverflow.com/questions/39501481/difference-between-add-compile-options-and-setcmake-cxx-flags)



Blog

[CMake – Properties and Options](https://arne-mertz.de/2018/07/cmake-properties-options/)

[CMake 基本用法介绍](https://zhjwpku.com/2019/11/15/cmake-basic-commands-intro.html)

[Build system internals](https://www.dealii.org/9.1.1/developers/cmake-internals.html)

cmake 论坛

[target_compile_options de-duplicates flags but some repeats matter](https://gitlab.kitware.com/cmake/cmake/-/issues/15826)



cmake运行时库的方法

[Changing CMAKE_CXX_FLAGS in project](https://stackoverflow.com/questions/15100351/changing-cmake-cxx-flags-in-project)

[CMake - remove a compile flag for a single translation unit](https://stackoverflow.com/questions/28344564/cmake-remove-a-compile-flag-for-a-single-translation-unit)

[Does set_target_properties in CMake override CMAKE_CXX_FLAGS?](https://stackoverflow.com/questions/5096881/does-set-target-properties-in-cmake-override-cmake-cxx-flags)

[Not possible to set "/MT" or "/MTd" for Visual Studio 15 2017 generator](https://gitlab.kitware.com/cmake/cmake/-/issues/18390)

[Compile with /MT instead of /MD using CMake](https://stackoverflow.com/questions/14172856/compile-with-mt-instead-of-md-using-cmake)

csdn

[cmake:msvc分别对不同的target使用不同的运行库选项(/MT或/MD)](https://blog.csdn.net/10km/article/details/79973750)

[cmake:设置编译选项的讲究(add_compile_options和CMAKE_CXX_FLAGS的区别)](https://blog.csdn.net/10km/article/details/51731959)

