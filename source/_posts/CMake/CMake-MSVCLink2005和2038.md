---
title: CMake 解决MSVC RuntimeLibrary问题
top: false
mathjax: true
date: 2021-02-22 21:59:11
categories:
- CMake
---

-----





**问题描述**

使用googletest时产生的错误

> error LNK2038: 检测到“RuntimeLibrary”的不匹配项: 值“MTd_StaticDebug”不匹配值“MDd_DynamicDebug”
>
> error LNK2005: "public: __cdecl std::_Lockit::~_Lockit(void)" (??1_Lockit@std@@QEAA@XZ) 已经在 msvcprtd.lib(MSVCP140D.dll) 中定义

## 方案一 全局方法

```cmake
if(MSVC)
  set(variables CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
                CMAKE_CXX_FLAGS_RELWITHDEBINFO CMAKE_CXX_FLAGS_MINSIZEREL)
  foreach(variable ${variables})
    if(${variable} MATCHES "/MD")
      string(REGEX REPLACE "/MD" "/MT" ${variable} "${${variable}}")
    endif()
  endforeach()
endif()
```



```cmake
if(MSVC)
    add_compile_options(
        $<$<CONFIG:>:/MT> #---------|
        $<$<CONFIG:Debug>:/MTd> #---|-- Statically link the runtime libraries
        $<$<CONFIG:Release>:/MT> #--|
    )
endif()
```



## 方案二 target 控制

```cmake
target_compile_options(
		Foo PUBLIC
        $<$<CONFIG:>:/MT> #---------|
        $<$<CONFIG:Debug>:/MTd> #---|-- Statically link the runtime libraries
        $<$<CONFIG:Release>:/MT> #--|
)
```



## 字符串替换

```cmake
set(CompilerFlags
        CMAKE_CXX_FLAGS
        CMAKE_CXX_FLAGS_DEBUG
        CMAKE_CXX_FLAGS_RELEASE
        CMAKE_CXX_FLAGS_MINSIZEREL
        CMAKE_CXX_FLAGS_RELWITHDEBINFO
        CMAKE_C_FLAGS
        CMAKE_C_FLAGS_DEBUG
        CMAKE_C_FLAGS_RELEASE
        CMAKE_C_FLAGS_MINSIZEREL
        CMAKE_C_FLAGS_RELWITHDEBINFO
        )
foreach(CompilerFlag ${CompilerFlags})
    string(REPLACE "/MD" "/MT" ${CompilerFlag} "${${CompilerFlag}}")
    set(${CompilerFlag} "${${CompilerFlag}}" CACHE STRING "msvc compiler flags" FORCE)
    message("MSVC flags: ${CompilerFlag}:${${CompilerFlag}}")
endforeach()
```



## 目标属性(Properties on targets) LINKER LANGUAGE

`LINKER LANGUAGE`
该目标属性用于指定编译器的语言。即当调用可执行程序、共享库和模块时，用于指定编译器链接语言（C or CXX），若是没有设置，则默认具有最高链接器首选项值的语言。

```cmake
set_target_properties(test PROPERTIES LINKER_LANGUAGE CXX) // 指定C++
set_target_properties(test PROPERTIES LINKER_LANGUAGE C) // 指定C
```



## 修改MSVC运行时库的属性

```cmake
cmake_minimum_required(VERSION 3.15)
cmake_policy(SET CMP0091 NEW)
project(my_project)

add_executable(foo foo.c)
set_property(TARGET foo PROPERTY
             MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
```

[Compile with /MT instead of /MD using CMake](https://stackoverflow.com/questions/14172856/compile-with-mt-instead-of-md-using-cmake)

[CMAKE_MSVC_RUNTIME_LIBRARY](https://cmake.org/cmake/help/latest/variable/CMAKE_MSVC_RUNTIME_LIBRARY.html#variable:CMAKE_MSVC_RUNTIME_LIBRARY)

## 错误原理

**值“MT_StaticRelease”不匹配值“MD_DynamicRelease”** 该错误出现的通常原因：工程同时配置有动态库和静态库

## 相关参考

[Compile with /MT instead of /MD using CMake](https://stackoverflow.com/questions/14172856/compile-with-mt-instead-of-md-using-cmake)

[Not possible to set "/MT" or "/MTd" for Visual Studio 15 2017 generator](https://gitlab.kitware.com/cmake/cmake/-/issues/18390)

[MSVC_RUNTIME_LIBRARY](https://cmake.org/cmake/help/latest/prop_tgt/MSVC_RUNTIME_LIBRARY.html#msvc-runtime-library)

[MSVC 按类别列出的编译器选项](https://docs.microsoft.com/zh-cn/cpp/build/reference/compiler-options-listed-by-category?view=msvc-160)

[visual  studio 解决方法](https://pianshen.com/article/6231589124/)

中文

[使用CMake用/ MT而不是/ MD编译](https://stackoom.com/question/xT0S/使用CMake用-MT而不是-MD编译)