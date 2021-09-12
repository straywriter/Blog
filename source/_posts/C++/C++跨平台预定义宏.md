---
title: C++跨平台预定义宏
top: false
mathjax: true
categories:
- C++
---

-----



x





| Macro                                             | Definition                 |
| :------------------------------------------------ | :------------------------- |
| `__linux__`                                       | Linux and Linux-derived    |
| `__ANDROID__`                                     | Android                    |
| `#if defined(__linux__) && !defined(__ANDROID__)` | Linux (non-Android)        |
| `__APPLE__`                                       | Darwin (macOS and iOS)     |
| `_WIN32`                                          | Windows (32- and 64-bit)   |
| `_WIN64`                                          | Windows 64-bit             |
| `_MSC_VER`                                        | Visual Studio              |
| `__GNUC__`                                        | gcc                        |
| `__clang__`                                       | clang                      |
| `__EMSCRIPTEN__`                                  | emscripten                 |
| `__MINGW32__`                                     | MinGW 32, MinGW-w64 32-bit |
| `__MINGW64__`                                     | MinGW-w64 64-bit           |
| `ARM9` (1)                                        | Nintendo DS (ARM9)         |
| `_3DS` (1)                                        | Nintendo 3DS               |
| `__SWITCH__` (1)                                  | Nintendo Switch            |







## 相关参考

[Common Predefined Macros (The C Preprocessor) (gnu.org)](https://gcc.gnu.org/onlinedocs/cpp/Common-Predefined-Macros.html)

[Predefined Macros (The C Preprocessor) (gnu.org)](https://gcc.gnu.org/onlinedocs/cpp/Predefined-Macros.html)

[Guide to predefined macros in C++ compilers (gcc, clang, msvc etc.) (kowalczyk.info)](https://blog.kowalczyk.info/article/j/guide-to-predefined-macros-in-c-compilers-gcc-clang-msvc-etc..html)

[Predefined C/C++ macros for cross-platform development - DEV Community](https://dev.to/tenry/predefined-c-c-macros-43id)



[c++ - How to identify platform/compiler from preprocessor macros? - Stack Overflow](https://stackoverflow.com/questions/4605842/how-to-identify-platform-compiler-from-preprocessor-macros)

[c++ - How to detect reliably Mac OS X, iOS, Linux, Windows in C preprocessor? - Stack Overflow](https://stackoverflow.com/questions/5919996/how-to-detect-reliably-mac-os-x-ios-linux-windows-in-c-preprocessor)

[Predefined macros | Microsoft Docs](https://docs.microsoft.com/en-us/cpp/preprocessor/predefined-macros?view=msvc-160)

[CPP / C++ - Preprocessor and Macros (caiorss.github.io)](https://caiorss.github.io/C-Cpp-Notes/Preprocessor_and_Macros.html)

[Pre-defined Compiler Macros / Wiki / Libraries (sourceforge.net)](https://sourceforge.net/p/predef/wiki/Libraries/)



[Replacing text macros - cppreference.com](https://en.cppreference.com/w/cpp/preprocessor/replace)