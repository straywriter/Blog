---
title: vcpkg使用
top: false
mathjax: true
date: 2021-08-14 13:45:33
categories:
- 工具
---

-----

## 简介

vcpkg是命令行包管理工具，在使用第三方库的c或c++开发中可以简化相关的配置操作。vcpkg安装的包支持Visual Studio 2015 update 3及以上版本，包括vs2017工具集，目前在windows平台已有超过900多个包，linux平台超过350个包。在默认情况下，vcpkg会优先使用vs2017进行编译。如果未安装，则使用vs2015编译和安装。

## 下载

```
git clone https://github.com/microsoft/vcpkg
```

## 编译Vcpkg

**注意：**

> Vcpkg大量使用的psl脚本，所以官方强烈推荐使用PowerShell而不时CMD命令行来执行各种操作。尽管在使用的时候兼容CMD，但是在编译这一步，请使用PowerShell。

编译很简单，使用PowerShell执行Vcpkg工程目录下的“bootstrap-vcpkg.bat”命令，即可编译。编译好以后会在同级目录下生成vcpkg.exe文件。编译期间，脚本会自动下载vswhere组件。

### 指定编译某种架构的程序库

```
vcpkg  help triplet
Available architecture triplets:
  arm-uwp
  arm-windows
  arm64-uwp
  arm64-windows
  x64-linux
  x64-osx
  x64-uwp
  x64-windows
  x64-windows-static
  x86-uwp
  x86-windows
  x86-windows-static
```

安装某个框架的某个库:

```
vcpkg install boost:x64-windows
```

## 导入导出库

### 导出库

```
vcpkg export jsoncpp --7zip
```

注意，导出时必须指定导出的包格式。vcpkg支持5种导出包格式，有：

| 参数   | 格式                   |
| :----- | :--------------------- |
| –raw   | 以不打包的目录格式导出 |
| –nuget | 以nuget包形式导出      |
| –ifw   | 我也不知道这是啥格式   |
| –zip   | 以zip压缩包形式导出    |
| –7zip  | 以7z压缩包形式导出     |

如果要指定输出目录和特定文件名，需使用”–output=”参数

### 导入库

```
\vcpkg.exe import xxx.7z
```

## 集成

### 集成到全局

```
vcpkg integrate install
```

移除全局集成

```
vcpkg integrate remove
```

### 集成到工程

### 集成到Cmake

```
-DCMAKE_TOOLCHAIN_FILE=/scripts/buildsystems/vcpkg.cmake”
```

### 集成静态库

Vcpkg默认编译链接的是动态库，如果要链接静态库，目前还没有简便的方法。需要做如下操作

1. 用文本方式打开vcxproj工程文件
2. 在xml的段里面增加如下两句话即可

```
<VcpkgTriplet>x86-windows-static</VcpkgTriplet>
<VcpkgEnabled>true</VcpkgEnabled>12
```

在CMake中集成静态库，需要额外指令

```
cmake .. -DCMAKE_TOOLCHAIN_FILE=.../vcpkg.cmake -DVCPKG_TARGET_TRIPLET=x86-windows-static
```

## vcpkg 文件夹层次结构

所有 vcpkg 功能和数据都自包含在称为“实例”的单独目录层次结构中。 没有注册表设置或环境变量。 可以在一台计算机上设置任意数量的 vcpkg 实例，它们彼此互不干扰。

vcpkg 实例的内容如下：

- buildtrees - 包含从中生成每个库的源的子文件夹
- docs - 文档和示例
- downloads - 任何已下载工具或源的缓存副本。 运行安装命令时，vcpkg 会首先搜索此处。
- installed - 包含每个已安装库的标头和二进制文件。 与 Visual Studio 集成时，实质上是相当于告知它将此文件夹添加到其搜索路径。
- packages - 在不同的安装之间用于暂存的内部文件夹。
- ports - 用于描述每个库的目录、版本和下载位置的文件。 如有需要，可添加自己的端口。
- scripts - 由 vcpkg 使用的脚本（cmake、powershell）。
- toolsrc - vcpkg 和相关组件的 C++ 源代码
- triplets - 包含每个受支持目标平台（如 x86-windows 或 x64-uwp）的设置。

## 遇到的错误

### boost 安装错误

### QT 安装错误

## 参考

[vcpkg 官网](https://github.com/microsoft/vcpkg)

[vcpkkg 文档](https://github.com/microsoft/vcpkg/blob/master/docs/index.md)

https://docs.microsoft.com/zh-cn/cpp/build/vcpkg?view=vs-2019

[Visual Studio开源库集成器Vcpkg全教程--利用Vcpkg轻松集成开源第三方库](https://blog.csdn.net/cjmqas/article/details/79282847)