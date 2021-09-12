---
title: C++ Google Benchmark基本使用
top: false
mathjax: true
categories:
- C++
---

-----







x

google benchmark是一个由Google开发的基于googletest框架的c++ benchmark工具，它易于安装和使用，并提供了全面的性能测试接口。





### 基础示例

```cpp
#include <benchmark/benchmark.h>
static void BM_SomeFunction(benchmark::State& state) {
  // Perform setup here
  for (auto _ : state) {
    // This code gets timed
    SomeFunction();
  }
}
// Register the function as a benchmark
BENCHMARK(BM_SomeFunction);
// Run the benchmark
BENCHMARK_MAIN();

```



## 相关参考

Google Benchmarks 

[google/benchmark: A microbenchmark support library (github.com)](https://github.com/google/benchmark)



在线C++Benchmarks 

[Quick C++ Benchmarks (quick-bench.com)](https://quick-bench.com/)

[Perfbench | Benchmark C++ code online](https://perfbench.com/)

C++ 在线编译汇编语言

[Compiler Explorer (godbolt.org)](https://godbolt.org/)





文章

[Google benchmark：一个简单易用的C++ benchmark库 (airekans.github.io)](https://airekans.github.io/cpp/2015/04/18/google-benchmark)[c++性能测试工具：google benchmark入门（一） - apocelipes - 博客园 (cnblogs.com)](https://www.cnblogs.com/apocelipes/p/10348925.html)

[c++性能测试工具：google benchmark入门（一） - apocelipes - 博客园 (cnblogs.com)](https://www.cnblogs.com/apocelipes/p/10348925.html)

[c++性能测试工具：google benchmark入门（二） - apocelipes - 博客园 (cnblogs.com)](https://www.cnblogs.com/apocelipes/p/11067594.html)

Video

[(184) How to use Google Benchmark for C++ programs - YouTube](https://www.youtube.com/watch?v=9VKR8u9odrA)