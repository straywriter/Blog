---
title: C++ GoogleTest基本使用
top: false
mathjax: true
categories:
- C++
---

-----



## 断言(Assertions)

- GTEST的断言是类似函数调用的宏定义，对类或者函数使用断言来判断它的行为。当一个断言失败了，GTEST会打印这个测试断言的源文件、行数以及失败信息。也可以提供定制的错误信息接在GTEST的错误信息后面。

- `ASSERT_*`和`EXPECT_*`这两种断言成对出现，用来测试相同的东西，但是对当前函数有不同的影响。
- `ASSERT_*`版本在断言失败时产生致命错误，并且终止当前函数。
- `EXPECT_*`版本则产生非致命错误，且不会终止当前函数。
- 通常更倾向于使用`EXPECT_*`，因为这样能够允许在一个测试用例中报告多个错误。如果断言继续下去没有意义的话，就应该使用`ASSERT_*`进行判断。
- `ASSERT_*`立刻从当前函数返回，可能会跳过之后的清理代码，这将会导致空间泄漏。根据泄漏的性质，它可能值得修复，也可能不值得修复。因此，如果除了断言错误外还出现堆检查器错误，请记住检查这一点。

使用`<<`符号来将自定义的错误信息添加进宏里面，例如：

```cpp
ASSERT_EQ(x.size(), y.size()) << "Vectors x and y are of unequal length";

for (int i = 0; i < x.size(); ++i) {
  EXPECT_EQ(x[i], y[i]) << "Vectors x and y differ at index " << i;
}
```



### 基本的断言

判断真假的断言

| Fatal assertion            | Nonfatal assertion         | Verifies             |
| -------------------------- | -------------------------- | -------------------- |
| `ASSERT_TRUE(condition);`  | `EXPECT_TRUE(condition);`  | `condition` is true  |
| `ASSERT_FALSE(condition);` | `EXPECT_FALSE(condition);` | `condition` is false |

### 比较断言

这类断言用来比较两个值

| Fatal assertion          | Nonfatal assertion       | Verifies       |
| ------------------------ | ------------------------ | -------------- |
| `ASSERT_EQ(val1, val2);` | `EXPECT_EQ(val1, val2);` | `val1 == val2` |
| `ASSERT_NE(val1, val2);` | `EXPECT_NE(val1, val2);` | `val1 != val2` |
| `ASSERT_LT(val1, val2);` | `EXPECT_LT(val1, val2);` | `val1 < val2`  |
| `ASSERT_LE(val1, val2);` | `EXPECT_LE(val1, val2);` | `val1 <= val2` |
| `ASSERT_GT(val1, val2);` | `EXPECT_GT(val1, val2);` | `val1 > val2`  |
| `ASSERT_GE(val1, val2);` | `EXPECT_GE(val1, val2);` | `val1 >= val2` |

- 断言参数的值必须是可比较的，否则会产生一个编译错误。
- 当断言失败时，如果自定义的错误支持`<<`运算符，那么GTEST将会打印他们，否则将会尝试用其他的方式打印出他们。
- 用户自定义类型仅仅当定义了比较操作时，断言才能够比较的对象的大小，但是这不被Google的C++类型规范所提倡，这种情况下应当使用`ASSERT_TRUE()`或者`EXPECT_TRUE()`来进行判断。
- 不过还是应当尽可能的使用`ASSERT_EQ(actual, expected)`，因为他能够在测试失败时告知`actual`和`expected`的值。

- `ASSERT_EQ()`在比较指针时比较的是指针的值，当比较两个C风格的字符串时，将会比较他们是否有相同的内存地址，而不是有相同的值。因此在比较C风格字符串的时候应当使用`ASSERT_STREQ()`，但是在比较两个string对象的时候，应当使用`ASSERT_EQ`。
- 在进行指针的比较时应当使用`*_EQ(ptr, nullptr)`和`*_NE(ptr, nullptr)`代替`*_EQ(ptr, NULL)`和`*_NE(ptr, NULL)`，因为`nullptr`被定义了类型而`NULL`却没有。
- 当比较浮点数时应该使用浮点数断言来避免近似值导致的问题。
- 本节的宏定义对`string`和`wstring`都适用。

### 字符串比较

这节的断言用来比较C语言风格的字符串，在比较两个string对象时，应该使用`EXPECT_EQ`,`EXPECT_NE`。

| Fatal assertion                | Nonfatal assertion             | Verifies                                                 |
| ------------------------------ | ------------------------------ | -------------------------------------------------------- |
| `ASSERT_STREQ(str1,str2);`     | `EXPECT_STREQ(str1,str2);`     | the two C strings have the same content                  |
| `ASSERT_STRNE(str1,str2);`     | `EXPECT_STRNE(str1,str2);`     | the two C strings have different contents                |
| `ASSERT_STRCASEEQ(str1,str2);` | `EXPECT_STRCASEEQ(str1,str2);` | the two C strings have the same content, ignoring case   |
| `ASSERT_STRCASENE(str1,str2);` | `EXPECT_STRCASENE(str1,str2);` | the two C strings have different contents, ignoring case |

**注意：“CASE”表明忽略大小写，一个`NULL`指针和空字符串不一样**

### 简单的测试例子

创建一个测试：

1. 使用`TEST()`宏定义来定义和命名一个测试函数，这些宏就是没有返回值的普通C++函数。
2. 在这个函数中，可以包含任何有效的c++语句中，使用各种GTEST断言来检查值。
3. 测试结果由断言决定；如果测试中的任何断言失败(致命或非致命)，或者测试崩溃，则整个测试失败。

```cpp
TEST(TestSuiteName, TestName) {
  ... test body ...
}
```

- `TEST()`的第一个参数是测试套件（Test Suite）的名称，第二个参数是这个测试套件中该测试（Test）的名称。
- 两种名称都必须是合法的C++标识符，并且不能包含任何下划线`_`。不同测试套件中的测试可以有相同的名字。

举个例子，被测函数是一个简单的斐波那契函数：

```c
int Factorial(int n);  // Returns the factorial of n
```

一个测试可以写成：

```c++
// Tests factorial of 0.
TEST(FactorialTest, HandlesZeroInput) {
  EXPECT_EQ(Factorial(0), 1);
}

// Tests factorial of positive numbers.
TEST(FactorialTest, HandlesPositiveInput) {
  EXPECT_EQ(Factorial(1), 1);
  EXPECT_EQ(Factorial(2), 2);
  EXPECT_EQ(Factorial(3), 6);
  EXPECT_EQ(Factorial(8), 40320);
}
```

逻辑上来说，相关的测试应该在同一个测试套件（Test Suite）中。在上述的例子中，有两个测试`HandlesZeroInput`和`HandlesPositiveInput`，他们属于同一个测试套件`FactorialTest`。

## Test Fixtures(为多个测试使用相同的配置)

当两个或更多的测试需要使用相似的数据时，可以使用*Test Fixture*。这可以对不同的测试重用相同的数据对象配置。

创建一个fixture：

1. 从`::testing::Test`派生出一个类。用`protected:`开始它的类主体，因为需要从子类访问fixture成员。
2. 在类中声明所有准备使用的对象
3. 如果需要，可以编写一个默认构造函数或`SetUp()`函数来为每个测试准备对象。常见的错误是将`SetUp()`拼写为`Setup()`，在c++ 11中可以使用`override`来确保拼写正确。
4. 如有必要，编写一个析构函数或`TearDown()`函数以释放您在`SetUp()`中分配的所有资源。 若要了解何时应使用构造函数/析构函数以及何时应使用`SetUp()/ TearDown()`。
5. 如果需要，定义要共享的测试的子程序。

当使用fixture时，使用`TEST_F()`代替`TEST()`，因为`TEST_F()`允许你在Test Fixture中获取对象和子程序：

```cpp
TEST_F(TestFixtureName, TestName) {
  ... test body ...
}
```

和`TEST()`类似，第一个参数是测试套件的名字，但是`TEST_F()`的这个参数必须和`Test Fixture`类的名字相同。还需要在使用`Test Fixture`对象之前定义这个`Test Fixture`类，否则会导致编译错误`virtual outside class declaration`。

对于每个`TEST_F()`来说，GTEST在运行时都会创建一个新的test fixture对象，并且通过`SetUp()`立刻初始化这个对象，再运行测试，结束后通过调用`TearDown()`来进行清理工作，最后将删除这个test fixture对象。注意，在同一个测试套件中的不同测试拥有不同的test fixture对象，GTEST在新建下一个test fixture对象时总是会先删除上一个test fixture对象，并且不会在多个不同的测试中重用一个test fixture对象。所以如果任何测试改变了它的test fixture对象，并不会影响其他测试的test fixture对象。

下面用对一个FIFO队列类`Queue`编写测试，他有以下接口：

```cpp
template <typename E>  // E is the element type.
class Queue {
 public:
  Queue();
  void Enqueue(const E& element);
  E* Dequeue();  // Returns NULL if the queue is empty.
  size_t size() const;
  ...
};
```

定义一个fixture类。按照惯例，应该给它起一个`FooTest`的名字，其中`Foo`是被测试的类。

```cpp
class QueueTest : public ::testing::Test {
 protected:
  void SetUp() override {
     q1_.Enqueue(1);
     q2_.Enqueue(2);
     q2_.Enqueue(3);
  }

  // void TearDown() override {}

  Queue<int> q0_;
  Queue<int> q1_;
  Queue<int> q2_;
};
```

在这个例子中，不需要`TearDown()`函数，因为析构器已经完成了析构工作，不需要再进行清理。

```cpp
TEST_F(QueueTest, IsEmptyInitially) {
  EXPECT_EQ(q0_.size(), 0);
}

TEST_F(QueueTest, DequeueWorks) {
  int* n = q0_.Dequeue();
  EXPECT_EQ(n, nullptr);

  n = q1_.Dequeue();
  ASSERT_NE(n, nullptr);
  EXPECT_EQ(*n, 1);
  EXPECT_EQ(q1_.size(), 0);
  delete n;

  n = q2_.Dequeue();
  ASSERT_NE(n, nullptr);
  EXPECT_EQ(*n, 2);
  EXPECT_EQ(q2_.size(), 1);
  delete n;
}
```

- 上面使用了`ASSERT_*`和`EXPECT_*`断言。当希望测试在断言失败后继续显示更多错误时使用`EXPECT_*`，而在失败后继续运行测试没有意义则使用`ASSERT_*`。
- 例如，Dequeue测试中的第二个断言是`ASSERT_NE(nullptr, n)`，因为我们稍后需要对指针`n`进行解引用，这将在`n`的值为`NULL`时导致段错误。

当测试运行时，以下步骤将会发生：

1. GTEST构建一个`QueueTest`对象`t1`
2. `t1.SetUp()`初始化`t1`
3. 第一个测试在`t1`上运行
4. `t1.TearDown()`在第一个测试结束时进行清理
5. 析构`t1`
6. 在进行另外一个`QueueTest`对象测试`DequeueWorks`测试时，重复上述步骤

## 调用测试

- `TEST()`和`TEST_F()`向googletest隐式注册其测试。与许多其他C ++测试框架不同，不必重新列出所有已定义的测试即可运行它们。
- 定义测试后，可以使用`RUN_ALL_TESTS()`运行它们，如果所有测试成功，将返回0，否则返回1。`RUN_ALL_TESTS()`在链接单元中运行所有测试，它们可以来自不同的测试套件，甚至来自不同的源文件。

当调用`RUN_ALL_TESTS()`宏时：

- 保存所有GTEST标志的状态
- 为第一个测试创建一个test fixture对象
- 通过`SetUp()`初始化这个对象
- 在fixture对象上运行测试
- 通过`TearDown()`函数进行清理
- 删除fixture对象
- 恢复所有GTEST标志的状态
- 重复上述步骤直到测试结束

**当一个致命性的错误发生时，后续的步骤将会被跳过。**

> 重要说明：一定不能忽略`RUN_ALL_TESTS()`的返回值，否则会出现编译器错误。 这种设计的基本原理是，自动化测试服务将根据其退出代码（而不是根据其stdout / stderr输出）来确定测试是否通过。 因此`main()`函数必须返回`RUN_ALL_TESTS()`的值。
>
> 另外，您应该只调用一次`RUN_ALL_TESTS()`。 多次调用它会与某些高级googletest功能（例如线程安全的死亡测试）发生冲突，因此不被支持。

## 编写main()函数

`gtest_main`库提供了一个合适的程序入口点，通过链接`gtest_main`动态库而不是`gtest`库，**大多用户无需编写他们自己的main函数**(Google Test提供了`main()`函数的基本实现。

如果适合你的需求，则只需将测试与gtest_main库链接就可以了。本节的其余部分仅适用于需要在测试运行前做一些自定义的事情，而这些事情不能在test fixture和测试套件的框架内表达。

如果您编写自己的`main()`函数，则该函数应返回`RUN_ALL_TESTS()`的值。

下面是一个模板：

```cpp
#include "this/package/foo.h"

#include "gtest/gtest.h"

namespace my {
namespace project {
namespace {

// The fixture for testing class Foo.
class FooTest : public ::testing::Test {
 protected:
  // You can remove any or all of the following functions if their bodies would
  // be empty.

  FooTest() {
     // You can do set-up work for each test here.
  }

  ~FooTest() override {
     // You can do clean-up work that doesn't throw exceptions here.
  }

  // If the constructor and destructor are not enough for setting up
  // and cleaning up each test, you can define the following methods:

  void SetUp() override {
     // Code here will be called immediately after the constructor (right
     // before each test).
  }

  void TearDown() override {
     // Code here will be called immediately after each test (right
     // before the destructor).
  }

  // Class members declared here can be used by all tests in the test suite
  // for Foo.
};

// Tests that the Foo::Bar() method does Abc.
TEST_F(FooTest, MethodBarDoesAbc) {
  const std::string input_filepath = "this/package/testdata/myinputfile.dat";
  const std::string output_filepath = "this/package/testdata/myoutputfile.dat";
  Foo f;
  EXPECT_EQ(f.Bar(input_filepath, output_filepath), 0);
}

// Tests that Foo does Xyz.
TEST_F(FooTest, DoesXyz) {
  // Exercises the Xyz feature of Foo.
}

}  // namespace
}  // namespace project
}  // namespace my

int main(int argc, char **argv) {
  ::testing::InitGoogleTest(&argc, argv);
  return RUN_ALL_TESTS();
}
```

- `:: testing :: InitGoogleTest()`函数解析命令行中的googletest标志，并删除所有可识别的标志。
- 这允许用户通过各种标志控制测试程序的行为，将在AdvancedGuide中介绍这些标志。
- 注意，必须在调用`RUN_ALL_TESTS()`之前调用该函数，否则标志将无法正确初始化。

## 已知的限制

GTEST被设计成线程安全的。在使用`pthread`的系统上，GTEST的实现是线程安全的，而在其他系统(如Windows)上多线程并发使用`Google Test`的断言并不安全。

一般情况下断言都是在主线程中进行的，因此在绝大多数测试中这并不会产生问题。



## 更多的断言

这章覆盖了一些使用频率较少但是仍然很重要的断言

### 明确的成功和失败

下面三个断言没有确切的测试一个值或者表达式，而是直接生成一个成功或者失败。就像实际执行测试的宏一样，你可以将定制的失败消息传递进去。

```cpp
SUCCEED();
```

产生一个成功。这并不意味着整个测试都是成功，只有当测试的断言在执行过程中没有一个失败，测试才被认为是成功的。

NOTE：`SUCCEED()`纯粹是纪实的并且现阶段不产生任何用户可见的输出。

```c
FAIL();
ADD_FAILURE();
ADD_FAILURE_AT("file_path", line_number);
```

`FAIL()`产生一个致命的失败，然而`ADD_FAILURE()`和`ADD_FAILURE_AT()`产生一个非致命的错误。他们可以在控制流时决定测试的成功或者失败。例如：

```c
switch(expression) {
  case 1:
     ... some checks ...
  case 2:
     ... some other checks ...
  default:
     FAIL() << "We shouldn't get here.";
}
```

NOTE:只能在返回值是void的函数里才能使用`FAIL()`

### 异常的断言

这些用于验证一段代码是否抛出（或不抛出）给定类型的异常。

| Fatal assertion                            | Nonfatal assertion                         | Verifies                                          |
| ------------------------------------------ | ------------------------------------------ | ------------------------------------------------- |
| `ASSERT_THROW(statement, exception_type);` | `EXPECT_THROW(statement, exception_type);` | `statement` throws an exception of the given type |
| `ASSERT_ANY_THROW(statement);`             | `EXPECT_ANY_THROW(statement);`             | `statement` throws an exception of any type       |
| `ASSERT_NO_THROW(statement);`              | `EXPECT_NO_THROW(statement);`              | `statement` doesn't throw any exception           |

例如：

```c
ASSERT_THROW(Foo(5), bar_exception);
EXPECT_NO_THROW({
  int n = 5;
  Bar(&n);
});
```

### 为了更好地错误信息而使用谓词断言

- 尽管GTEST有一套丰富的断言，但是却永远不够，因为不可能预测用户可能遇到的所有场景。
- 有些时候缺乏更好地宏，用户只能使用`EXPECT_TRUE()`来检查复杂的表达式。但是不能向用户展示表达式各个部分的值，会让哪里出错变得难以理解。
- 作为一种变通方法，一些用户选择自己构建失败消息，将其流式传输到`EXPECT_TRUE()`中，当一个表达式有副作用或者评估起来很昂贵时，这将是十分困难的。

GTEST为了解决这个问题，提出了三个选项：

#### 使用一个存在的布尔函数

如果已经存在一个返回bool型的函数（或者是可以隐式转换成bool），可以在谓词断言中使用它来免费打印函数的参数：

| Fatal assertion                   | Nonfatal assertion                | Verifies                    |
| --------------------------------- | --------------------------------- | --------------------------- |
| `ASSERT_PRED1(pred1, val1)`       | `EXPECT_PRED1(pred1, val1)`       | `pred1(val1)` is true       |
| `ASSERT_PRED2(pred2, val1, val2)` | `EXPECT_PRED2(pred2, val1, val2)` | `pred2(val1, val2)` is true |
| `...`                             | `...`                             | `...`                       |

- 上述中，`predn`是一个n个参数的谓词函数，`val1`,`val2`,...,和`valn`是函数的参数。
- 当谓词应用于给定的参数时返回值为`true`，那么断言为成功，否则为失败。当断言失败时，他会打印每个参数的值。在这两种情况下，参数只被计算一次。

例子如下：

```cpp
// Returns true if m and n have no common divisors except 1.
bool MutuallyPrime(int m, int n) { ... }
const int a = 3;
const int b = 4;
const int c = 10;
```

断言如下时，将会成功：

```cpp
EXPECT_PRED2(MutuallyPrime, a, b);
```

断言如下时，将会失败

```cpp
EXPECT_PRED2(MutuallyPrime, b, c);
```

失败信息如下

```swift
MutuallyPrime(b, c) is false, where
b is 4
c is 10
```

#### 使用一个返回断言结果的函数

尽管`EXPECT_PRED*`和类似的宏对于快速工作非常方便，但是语法并不令人满意。对不不同的参数数量需要使用不同的宏，这更像Lisp而不是C++，`::testing::AssertionResult`类解决了这个问题。

一个`AssertionResult`对象代表了一个断言的结果（无论是成功还是失败，或是相关的信息）

```c
namespace testing {
// Returns an AssertionResult object to indicate that an assertion has
// succeeded.
AssertionResult AssertionSuccess();
// Returns an AssertionResult object to indicate that an assertion has
// failed.
AssertionResult AssertionFailure();
```

可以使用`<<`操作符将信息传输到`AssertionResult`对象。

返回值用`AssertionResult`对象代替`bool`值能够在断言中提供更多可读的信息。例如将`IsEven()`定义如下：

```c
::testing::AssertionResult IsEven(int n) {
  if ((n % 2) == 0)
     return ::testing::AssertionSuccess();
  else
     return ::testing::AssertionFailure() << n << " is odd";
}
```

替代

```cpp
bool IsEven(int n) {
  return (n % 2) == 0;
}
```

那么，错误的断言`EXPECT_TRUE(IsEven(Fib(4)))`将会打印

```c
Value of: IsEven(Fib(4))
  Actual: false (3 is odd)
Expected: true
```

替代原来模糊的结果

```c
Value of: IsEven(Fib(4))
  Actual: false
Expected: true
```

#### 使用谓词格式器

当由`(ASSERT | EXPECT)_PRED_*`和`(ASSERT | EXPECT)_(TRUE | FALSE)`生成的默认消息不令人满意，或者谓词的某些参数不支持将数据流传输到ostream，就可以使用下面的谓词格式器来定制信息的格式。

| Fatal assertion                                  | Nonfatal assertion                               | Verifies                                 |
| ------------------------------------------------ | ------------------------------------------------ | ---------------------------------------- |
| `ASSERT_PRED_FORMAT1(pred_format1, val1);`       | `EXPECT_PRED_FORMAT1(pred_format1, val1);`       | `pred_format1(val1)` is successful       |
| `ASSERT_PRED_FORMAT2(pred_format2, val1, val2);` | `EXPECT_PRED_FORMAT2(pred_format2, val1, val2);` | `pred_format2(val1, val2)` is successful |
| `...`                                            | `...`                                            | ...                                      |

与上一组宏之间的区别在于，`(ASSERT | EXPECT)_PRED_FORMAT *`使用谓词格式化`(pred_formatn)`来代替谓词，前者是具有签名的函数：

```c
::testing::AssertionResult PredicateFormattern(const char* expr1,
                                               const char* expr2,
                                               ...
                                               const char* exprn,
                                               T1 val1,
                                               T2 val2,
                                               ...
                                               Tn valn);
```

- 其中`val1`，`val2`，...和`valn`是谓词参数的值，而`expr1`，`expr2`，...和`exprn`是它们在源代码中出现的相应变量名。 
- 类型`T1`，`T2`，...和`Tn`可以是值类型或引用类型。 例如，如果参数的类型为Foo，则可以将其声明为`Foo`或`const Foo＆`。

例如，改进使用`EXPECT_PRED2()`来测试`MutuallyPrime()`中的失败消息：

```c
// Returns the smallest prime common divisor of m and n,
// or 1 when m and n are mutually prime.
int SmallestPrimeCommonDivisor(int m, int n) { ... }
// A predicate-formatter for asserting that two integers are mutually prime.
::testing::AssertionResult AssertMutuallyPrime(const char* m_expr,
                                               const char* n_expr,
                                               int m,
                                               int n) {
  if (MutuallyPrime(m, n)) return ::testing::AssertionSuccess();
    
  return ::testing::AssertionFailure() << m_expr << " and " << n_expr
      << " (" << m << " and " << n << ") are not mutually prime, "
      << "as they have a common divisor " << SmallestPrimeCommonDivisor(m, n);
}
```

使用`EXPECT_PRED_FORMAT2`:

```swift
EXPECT_PRED_FORMAT2(AssertMutuallyPrime, b, c);
```

将会获得以下信息：

```cpp
b and c (4 and 10) are not mutually prime, as they have a common divisor 2.
```

前面介绍的许多内置断言是`(EXPECT | ASSERT)_PRED_FORMAT *`的特殊情况。 实际上，大多数确实是使用`(EXPECT | ASSERT)_PRED_FORMAT *`定义的。

### 浮点数比较

- 比较浮点数很棘手。由于舍入误差，两个浮点将很难完全匹配。因此`ASSERT_EQ`的比较通常不起作用。
- 由于浮点具有十分广泛的取值范围，没有单个固定的错误界限能够一直有效。
- 最好以固定的相对误差范围进行比较，除了那些接近0的值，因为浮点数在0附近会丢失精度。
- 通常，为了使浮点的比较有意义，用户需要仔细选择误差范围。如果他们不希望或不在乎，则比较“最后一位”（Units in Last Place）是一个很好的默认值，并且googletest提供了断言来做到这一点。

#### 浮点宏

| Fatal assertion                 | Nonfatal assertion              | Verifies                                 |
| ------------------------------- | ------------------------------- | ---------------------------------------- |
| `ASSERT_FLOAT_EQ(val1, val2);`  | `EXPECT_FLOAT_EQ(val1, val2);`  | the two `float` values are almost equal  |
| `ASSERT_DOUBLE_EQ(val1, val2);` | `EXPECT_DOUBLE_EQ(val1, val2);` | the two `double` values are almost equal |

> “几乎相等”是指这些值彼此之间在4个ULP之内。

以下断言允许选择可接受的错误范围：

| Fatal assertion                       | Nonfatal assertion                    | Verifies                                                     |
| ------------------------------------- | ------------------------------------- | ------------------------------------------------------------ |
| `ASSERT_NEAR(val1, val2, abs_error);` | `EXPECT_NEAR(val1, val2, abs_error);` | the difference between `val1` and `val2` doesn't exceed the given absolute error |

#### 浮点类型谓词格式函数

一些浮点运算很有用，但并不常用。为了避免出现新的宏，我们将它们作为谓语格式函数提供，可在谓词断言宏中使用

```cpp
EXPECT_PRED_FORMAT2(::testing::FloatLE, val1, val2);
EXPECT_PRED_FORMAT2(::testing::DoubleLE, val1, val2);
```

验证`val1`是否小于等于`val2`。也可以将上表中的`EXPECT_PRED_FORMAT2`替换为`ASSERT_PRED_FORMAT2`。

### 使用gMock匹配器进行断言

gMock带有一个匹配器库，用于验证传递给模拟对象的参数。gMock匹配器是知道如何描述自己的基本谓词。可以在这些断言宏中使用gMock匹配器。

| Fatal assertion                | Nonfatal assertion             | Verifies              |
| ------------------------------ | ------------------------------ | --------------------- |
| `ASSERT_THAT(value, matcher);` | `EXPECT_THAT(value, matcher);` | value matches matcher |

例如，`StartsWith(prefix)`是一个匹配器，它匹配以`prefix`开头的字符串，例如：

```cpp
using ::testing::StartsWith;
...
    // Verifies that Foo() returns a string starting with "Hello".
    EXPECT_THAT(Foo(), StartsWith("Hello"));
```

gMock具有丰富的匹配器集。 您可以做很多googletest无法独自完成的事情。 

gMock与googletest捆绑在一起，因此无需添加任何构建依赖项即可利用此优势。只需添加`“ testing / base / public / gmock.h”`，就可以开始使用。

### 更多的字符串断言

您可以使用带有`EXPECT THAT()`或`ASSERT THAT()`的gMock字符串匹配器来执行更多的字符串比较技巧(子字符串、前缀、后缀、正则表达式等)。例如:

```cpp
using ::testing::HasSubstr;
using ::testing::MatchesRegex;
...
  ASSERT_THAT(foo_string, HasSubstr("needle"));
  EXPECT_THAT(bar_string, MatchesRegex("\\w*\\d+"));
```

如果该字符串包含格式正确的HTML或XML文档，则可以检查其DOM树是否与XPath表达式匹配：

```cpp
// Currently still in //template/prototemplate/testing:xpath_matcher
#include "template/prototemplate/testing/xpath_matcher.h"
using prototemplate::testing::MatchesXPath;
EXPECT_THAT(html_string, MatchesXPath("//a[text()='click here']"));
```

### Windows HRESULT断言

这些断言测试`HRESULT`成功或者失败

| Fatal assertion                        | Nonfatal assertion                     | Verifies                            |
| -------------------------------------- | -------------------------------------- | ----------------------------------- |
| `ASSERT_HRESULT_SUCCEEDED(expression)` | `EXPECT_HRESULT_SUCCEEDED(expression)` | `expression` is a success `HRESULT` |
| `ASSERT_HRESULT_FAILED(expression)`    | `EXPECT_HRESULT_FAILED(expression)`    | `expression` is a failure `HRESULT` |

生成的输出包含与表达式返回的HRESULT代码相关的可读错误消息。

例如可以这样使用：

```php
CComPtr<IShellDispatch2> shell;
ASSERT_HRESULT_SUCCEEDED(shell.CoCreateInstance(L"Shell.Application"));
CComVariant empty;
ASSERT_HRESULT_SUCCEEDED(shell->ShellExecute(CComBSTR(url), empty, empty, empty, empty));
```

### 类型断言

可以调用以下函数：

```ruby
::testing::StaticAssertTypeEq<T1, T2>();
```

来判断`T1`和`T2`的值是否相同。如果断言得到满足，该函数将不执行任何操作。如果类型不同，则函数调用将编译失败，编译器错误消息将指出T1和T2不是同一类型，并且很可能（取决于编译器）显示`T1`和`T2`的实际值。 这主要使用在模板代码中。

**警告**：在类模板或函数模板的成员函数中使用时，`StaticAssertTypeEq <T1，T2>（）`仅在实例化函数时才有效。 例如，给定：

```cpp
template <typename T> class Foo {
 public:
  void Bar() { ::testing::StaticAssertTypeEq<int, T>(); }
};
```

代码

```cpp
void Test1() { Foo<bool> foo; }
```

不会导致编译错误，因为`Foo<bool>::Bar()`没有被实例化。相反，以下代码会导致编译错误：

```cpp
void Test2() { 
    Foo<bool> foo; 
    foo.Bar();
    }
```

### 断言的位置

- 可以在任何C ++函数中使用断言，它不一定是测试类中的的方法。但是有一个约束，生成致命故障的断言`(FAIL*和ASSERT_ *)`只能在返回void的函数中使用。
- 如果将其放在返回值费控的函数中，将得到一个令人困惑的编译错误，例如：`"error: void value not ignored as it ought to be"`或者`"cannot initialize return object of type 'bool' with an rvalue of type 'void'"`或者`"error: no viable conversion from 'void' to 'string'"`。
- 如果需要在一个返回值不是void的函数中使用致命断言，可以让函数在参数中返回需要返回的值。例如，将函数`T2 Foo(T1 x)`重写成`void Foo(T1 x, T2* result)`。
- 这需要确保`* result`包含一些合理的值，即使函数过早的返回也需要确保。由于函数现在返回void，因此可以在其中使用任何断言。
- 如果不能选择更改函数的类型，则应仅使用会产生非致命故障的断言，例如`ADD_FAILURE *`和`EXPECT_ *`。

**注意**:根据c++语言规范，构造函数和析构函数不被认为是返回值为void的函数，因此不能在它们中使用致命的断言;如果尝试，将得到编译错误。相反，要么调用`abort`并崩溃整个测试可执行文件，要么将致命的断言放在`SetUp/TearDown`函数中;

**警告**:当构造函数或析构函数调用一个包含致命断言的helper函数(私有的void-returning方法)时，不会终止当前的测试。它仅仅会过早地从构造函数或析构函数中返回，并且可能让对象处在在一个部分构造或部分析构的状态。所以应该使用`abort`或者`SetUp/TearDown`函数代替它。

## 教GTEST如何打印你的值

- 当测试断言(例如`EXPECT_EQ`)失败时，googletest会输出参数值以帮助进行调试。它使用用户可扩展值打印器执行此操作。
- 该打印器知道如何打印内置的C++类型、原生数组、STL容器以及任何支持`<<`操作符的类型。对于其他类型，它将在值中打印原始字节，并希望用户可以理解它。

如前所述，打印器是可扩展的。这意味着可以教它在打印特定类型方面做的更好，而不是仅打印转储字节。为此，需要为你定义的类型定义`<<`操作符：

```cpp
#include <ostream>

namespace foo {
class Bar {  // We want googletest to be able to print instances of this.
...
  // Create a free inline friend function.
  friend std::ostream& operator<<(std::ostream& os, const Bar& bar) {
    return os << bar.DebugString();  // whatever needed to print bar to os
  }
};

// If you can't declare the function in the class it's important that the
// << operator is defined in the SAME namespace that defines Bar.  C++'s look-up
// rules rely on that.
std::ostream& operator<<(std::ostream& os, const Bar& bar) {
  return os << bar.DebugString();  // whatever needed to print bar to os
}

}  // namespace foo
```

有时团队可能会认为为`Bar`设置`<<`操作符可能是不好的风格，或者`Bar`可能已经具有`<<`操作符但没有做想要的事情（并且无法更改它）。如果是这样，可以改为定义一个`PrintTo()`函数：

```cpp
#include <ostream>
namespace foo {
class Bar {
  ...
  friend void PrintTo(const Bar& bar, std::ostream* os) {
    *os << bar.DebugString();  // whatever needed to print bar to os
  }
};

// If you can't declare the function in the class it's important that PrintTo()
// is defined in the SAME namespace that defines Bar.  C++'s look-up rules rely
// on that.
void PrintTo(const Bar& bar, std::ostream* os) {
  *os << bar.DebugString();  // whatever needed to print bar to os
}
}  // namespace foo
```

> 如果同时定义了`<<`和`PrintTo()`，则在使用googletest时将使用后者。这样就可以自定义待打印值在googletest输出中的显示方式，而不会影响依赖于`<<`操作符的行为的代码。

如果您想使用googletest的值打印器自己打印值`x`，只需调用`::testing::PrintToString(x)`，它会返回一个`std::string`：

```c
vector<pair<Bar, int> > bar_ints = GetBarIntVector();
EXPECT_TRUE(IsCorrectBarIntVector(bar_ints))
    << "bar_ints = " << ::testing::PrintToString(bar_ints);
```

## 死亡测试

- 在许多应用程序中，有一些断言会在不满足条件的情况下导致应用程序失败。
- 这些健全性检查可确保程序处于已知的良好状态，并在某些程序状态损坏后的最早可能的时间点失败。
- 如果断言检查了错误的条件，则程序可能会以错误的状态运行，这可能导致内存损坏，安全漏洞或更糟。因此，测试此类断言语句是否按预期工作至关重要。
- 由于这些前提条件检查会导致进程终止，因此我们将此类测试称为死亡测试。 更一般而言，任何以预期方式检查程序是否终止（除非引发异常）的测试也都是死亡测试。
- 请注意，如果一段代码抛出异常，则出于死亡测试的目的，我们不会将其视为“死亡”，因为代码的调用者可以捕获该异常并避免崩溃。 如果要验证代码引发的异常，请参见异常断言。

### 如何编写死亡测试

GTEST提供以下的宏来进行死亡测试

| Fatal assertion                                  | Nonfatal assertion                               | Verifies                                                     |
| ------------------------------------------------ | ------------------------------------------------ | ------------------------------------------------------------ |
| `ASSERT_DEATH(statement, matcher);`              | `EXPECT_DEATH(statement, matcher);`              | `statement` crashes with the given error                     |
| `ASSERT_DEATH_IF_SUPPORTED(statement, matcher);` | `EXPECT_DEATH_IF_SUPPORTED(statement, matcher);` | if death tests are supported, verifies that `statement` crashes with the given error; otherwise verifies nothing |
| `ASSERT_DEBUG_DEATH(statement, matcher);`        | `EXPECT_DEBUG_DEATH(statement, matcher);`        | `statement` crashes with the given error **in debug mode**. When not in debug (i.e. `NDEBUG` is defined), this just executes `statement` |
| `ASSERT_EXIT(statement, predicate, matcher);`    | `EXPECT_EXIT(statement, predicate, matcher);`    | `statement` exits with the given error, and its exit code matches `predicate` |

其中，`statement`是预期会导致进程终止的语句，`predicate`是评估整数退出状态的函数或函数对象，`matcher`是与`const std :: string＆`匹配的`gMock marcher`或（Perl）正则表达式 ，两者都与`statement`的`stderr`输出匹配。由于遗留原因，裸字符串（即没有匹配器）被解释为`ContainsRegex(str)`，而不是`Eq(str)`。 请注意，`statement`可以是任何有效的语句（包括复合语句），而不必是表达式。

> 注意：在宏的描述中使用崩溃(crash)代表进程是以一个非零的状态码终结的。这有两种可能：一是进程调用了非零值得`exit()`或者`_exit()`，二是被一个信号量杀死了。
>  这表明了如果`statement`终结了一个进程并返回0，`EXPECT_DEATH()`不认为这是一个崩溃。在这种情况下应该使用`EXPECT_EXIT`代替，或者更精确地限制退出代码。

这里的谓词(predicate)必须接受一个`int`返回一个`bool`。仅仅当谓词返回`true`时，代表死亡测试成功。GTEST定义了一些谓词能够处理大部分的情况：

```css
::testing::ExitedWithCode(exit_code)
```

如果程序使用给定的退出码正常退出，那么将返回`true`

```php
::testing::KilledBySignal(signal_number)  // Not available on Windows.
```

如果程序被给定的信号量杀死，就会返回`true`

`* _DEATH`宏是`* _EXIT`的便捷包装器，它们使用谓词来验证进程的退出代码是否为非零。

注意一个死亡测试只关注三件事：

1. `statement`有没有终止或者退出进程
2. 对于`ASSERT_EXIT`和`EXPECT_EXIT`退出状态是否满足谓词？ 或者在`ASSERT_DEATH`和`EXPECT_DEATH`的情况下退出状态是否为非零
3. `stderr`的输出是否符合`marcher`

特别是，如果语句生成`ASSERT_ *`或`EXPECT_ *`错误，则不会导致死亡测试失败，因为googletest断言不会终止该进程。

死亡测试的例子：

```php
TEST(MyDeathTest, Foo) {
  // This death test uses a compound statement.
  ASSERT_DEATH({
    int n = 5;
    Foo(&n);
  }, "Error on line .* of Foo()");
}

TEST(MyDeathTest, NormalExit) {
  EXPECT_EXIT(NormalExit(), ::testing::ExitedWithCode(0), "Success");
}

TEST(MyDeathTest, KillMyself) {
  EXPECT_EXIT(KillMyself(), ::testing::KilledBySignal(SIGKILL),
              "Sending myself unblockable signal");
}
```

这些测试验证了：

- 调用`Foo(5)`导致进程使用给定的错误信息结束
- 调用`NormalExit()`导致进程在`stderr`上打印`Success`并且退出码为0
- 调用`KillMyself()`会用信号量`SIGKILL`杀死进程

### 死亡测试命名

- 重要说明：当测试包含死亡测试时，如上面的示例所示。强烈建议遵循命名**test suite**（而非测试）加`* DeathTest`的约定， 下面的“死亡测试和线程”部分解释了原因。
- 如果一个test fixture类被普通测试和死亡测试所共享，可以使用using或typedef为Fixture类引入别名，并避免重复其代码

```cpp
class FooTest : public ::testing::Test { ... };

using FooDeathTest = FooTest;

TEST_F(FooTest, DoesThis) {
  // normal test
}

TEST_F(FooDeathTest, DoesThat) {
  // death test
}
```

### 死亡测试如何工作的

在后台，`ASSERT_EXIT()`产生一个新进程并在该进程中执行死亡测试语句。具体发生情况的详细信息取决于平台和变量`::testing::GTEST_FLAG(death_test_style)`。

- 在POSIX系统上，使用

  ```
  fork()
  ```

  (或Linux上的

  ```
  clone()
  ```

  ）来生成子子进程，然后

  - 如果变量的值为`“ fast”`，则将立即执行死亡测试语句。
  - 如果变量的值是`“threadsafe”`，则子进程将像第一次调用一样重新执行单元测试二进制文件，但是会带有一些额外的标志导致只运行这一个死亡测试

- 在Windows上，使用`CreateProcess（）`API生成子对象，然后重新执行二进制文件并且仅运行该单个死亡测试-就像POSIX上的线程安全模式一样。

变量的其他值是非法的，将导致死亡测试失败。 当前，该标志的默认值为`“fast”`

1. 子进程的退出码符合谓词
2. 子进程的stderr输出符合正则表达式

如果死亡测试语句运行到完成而没有死亡，则子进程将终止，并且断言失败。

### 死亡测试和线程

有两种死亡测试类型的原因与线程安全性有关。由于fork线程时存在的众所周知的问题，死亡测试应该在单线程上下文中运行。但是，有时布置这样的环境是不可行的。例如，静态初始化的模块可能在到达main之前启动线程。线程一旦创建后，可能很难或无法清理它们。

googletest具有三个功能，旨在提高人们对线程问题的认识：

1. 运行死亡测试时，如果有多个线程正在运行，则会发出警告。
2. 名称以`“ DeathTest”`结尾的Test suites将在所有其他测试之前运行。
3. 使用`clone()`而不是`fork()`来在Linux上生成子进程（在Cygwin和Mac上不支持`clone()`），因为在父进程具有多个线程时，`fork()`更有可能导致子进程挂起。

在死亡测试语句中创建线程是完全可行的。它们在单独的进程中执行，不会影响父进程。

### 死亡测试类型

引入了“线程安全”死亡测试类型，以帮助减轻可能在多线程环境中进行测试的风险。它以增加测试执行时间（可能会如此）为代价，以提高线程安全性。

自动测试框架未设置样式标志。可以通过设置标志来选择一种特定的死亡测试类型：

```cpp
testing::FLAGS_gtest_death_test_style="threadsafe"
```

可以在`main()`中执行此操作，以设置二进制文件或单个测试中所有死亡测试的样式。 在每个测试前会保存标志并在运行完成后恢复它，例如：

```cpp
int main(int argc, char** argv) {
  InitGoogle(argv[0], &argc, &argv, true);
  ::testing::FLAGS_gtest_death_test_style = "fast";
  return RUN_ALL_TESTS();
}

TEST(MyDeathTest, TestOne) {
  ::testing::FLAGS_gtest_death_test_style = "threadsafe";
  // This test is run in the "threadsafe" style:
  ASSERT_DEATH(ThisShouldDie(), "");
}

TEST(MyDeathTest, TestTwo) {
  // This test is run in the "fast" style:
  ASSERT_DEATH(ThisShouldDie(), "");
}
```

### 警告

`ASSERT_EXIT()`的`statement`参数可以是任何有效的C++语句。如果它通过return语句或引发异常离开当前函数，则将死亡测试视为失败。某些googletest宏可能会从当前函数中返回（例如`ASSERT_TRUE()`），因此请确保在`statement`中避免使用它们。

由于`statement`在子进程中运行，因此它引起的任何内存中副作用（例如，修改变量，释放内存等）在父进程中都是无法观察到的。特别是，如果在死亡测试中释放内存，则程序将无法通过堆检查，因为父进程将永远不会看到回收的内存。 为了解决这个问题，可以:

1. 在死亡测试中不释放内存
2. 在父进程中再释放一遍内存
3. 在程序中不实用堆检查

由于实现细节，不能在同一行上放置多个死亡测试断言，否则编译将失败，并显示明显的错误消息。

尽管“线程安全”类型的死亡测试提供了提高的线程安全性，但是在向`pthread_atfork(3)`注册的处理程序存在的情况下，诸如死锁之类的线程问题仍然可能出现。

## 在子例程中使用断言

### 向断言添加跟踪

如果从多个地方调用了一个测试子例程，则当其中的一个断言失败时，很难确定该失败来自哪个子例程调用。可以使用额外的日志记录或自定义失败消息来缓解此问题，但这通常会使测试混乱。更好的解决方案是使用`SCOPED_TRACE`宏或`ScopedTrace`工具：

```cpp
SCOPED_TRACE(message);
ScopedTrace trace("file_path", line_number, message);
```

`message`可以是可流式传输到`std::ostream`的任何内容。`SCOPED_TRACE`宏将使当前文件名，行号和给定的消息被添加到每个失败消息中。`ScopedTrace`在参数中接受显式的文件名和行号，这对于编写测试非常有用。当控件离开当前词法范围时，效果将被撤消。
 例如：

```cpp
void Sub1(int n) {
  EXPECT_EQ(Bar(n), 1);
  EXPECT_EQ(Bar(n + 1), 2);
}
TEST(FooTest, Bar) {
  {
     SCOPED_TRACE("A");  // This trace point will be included in
                                      // every failure in this scope.
     Sub1(1);
   }
   // Now it won't.
   Sub1(9);
  }
```

结果信息如下：

```undefined
path/to/foo_test.cc:11: Failure
Value of: Bar(n)
Expected: 1
  Actual: 2
Google Test trace:
path/to/foo_test.cc:17: A

path/to/foo_test.cc:12: Failure
Value of: Bar(n + 1)
Expected: 2
  Actual: 3
```

如果没有跟踪，将很难知道这两个失败分别来自`Sub1()`的调用。

使用`SCOPED_TRACE`的一些技巧：

1. 使用适当的`message`，并且在子例程的开头而不是在每个调用的地方使用`SCOPED_TRACE`。
2. 在循环内调用子例程时，请在`SCOPED_TRACE`中将循环迭代器作为消息的一部分，以便您可以知道失败来自哪个迭代。
3. 有时跟踪点的行号足以标识子例程的特定调用。在这种情况下，不必为`SCOPED_TRACE`选择唯一的消息，可以简单地使用“”。
4. 如果外部作用域中已经有了一个`SCOPED_TRACE`，则可以在内部作用域中继续使用`SCOPED_TRACE`。 在这种情况下，所有活动的跟踪点都将以遇到宏的相反顺序包含在故障消息中。
5. 跟踪转储可以在Emacs中选中行号并`return`，就会跳转到源文件中的该行

### 传递致命错误

使用`ASSERT_ *`和`FAIL *`有一个难以理解的常见陷阱，即当它们失败时只会中止当前功能，而不是整个测试。 例如，以下测试将发生段错误：

```cpp
void Subroutine() {
  // Generates a fatal failure and aborts the current function.
  ASSERT_EQ(1, 2);

  // The following won't be executed.
  ...
}

TEST(FooTest, Bar) {
  Subroutine();  // The intended behavior is for the fatal failure
                 // in Subroutine() to abort the entire test.
  // The actual behavior: the function goes on after Subroutine() returns.
  int* p = NULL;
  *p = 3;  // Segfault!
}
```

为了缓解这种情况，googletest提供了三种不同的解决方案。即抛出异常、`(ASSERT | EXPECT)_NO_FATAL_FAILURE`断言和`HasFatalFailure()`函数。 在以下两个小节中将对它们进行了描述。

**使用一个带有异常处理的断言**

以下代码可以将ASSERT-failure变成异常：

```cpp
class ThrowListener : public testing::EmptyTestEventListener {
  void OnTestPartResult(const testing::TestPartResult& result) override {
    if (result.type() == testing::TestPartResult::kFatalFailure) {
      throw testing::AssertionException(result);
    }
  }
};
int main(int argc, char** argv) {
  ...
  testing::UnitTest::GetInstance()->listeners().Append(new ThrowListener);
  return RUN_ALL_TESTS();
}
```

如果有其他侦听器，则应在其他侦听器之后添加此侦听器，否则它们将看不到失败的`OnTestPartResult`。

**在子例程中断言**

如上所示，如果您的测试调用了一个其中有`ASSERT_ *`失败的子例程，则该子例程返回后，测试将继续。人们通常希望致命的失败像异常一样传播。为此，googletest提供了以下宏

| Fatal assertion                       | Nonfatal assertion                    | Verifies                                                     |
| ------------------------------------- | ------------------------------------- | ------------------------------------------------------------ |
| `ASSERT_NO_FATAL_FAILURE(statement);` | `EXPECT_NO_FATAL_FAILURE(statement);` | `statement` doesn't generate any new fatal failures in the current thread. |

该宏仅仅检查执行断言的线程中的故障，来确定此类断言的结果。如果`statement`创建了新线程，那么这些线程中的故障将被忽略。

```cpp
ASSERT_NO_FATAL_FAILURE(Foo());

int i;
EXPECT_NO_FATAL_FAILURE({
  i = Bar();
});
```

Windows当前不支持来自多个线程的断言

**在当前测试中检测失败**

如果当前测试中的断言遭受致命故障,`::testing::Test`类中的`HasFatalFailure()`将返回true。这使函数可以在子例程中捕获致命故障并尽早返回。

```css
class Test {
 public:
  ...
  static bool HasFatalFailure();

};
```

典型的用法是模拟一个抛出异常的操作：



```kotlin
TEST(FooTest, Bar) {
  Subroutine();
  // Aborts if Subroutine() had a fatal failure.
  if (HasFatalFailure()) return;
  // The following won't be executed.
  ...
}
```

如果在`TEST()`，`TEST_F()`或`test fixture`之外使用`HasFatalFailure()`，则必须添加`::testing::Test::`前缀，如下所示：

```php
if (::testing::Test::HasFatalFailure()) return;
```

类似的，如果当前测试至少有一个非致命故障，则`HasNonfatalFailure()`返回`true`，如果当前测试有两种失败中的任意一个，则`HasFailure()`返回`true`。

## 记录额外的信息

在测试代码中，您可以调用`RecordProperty（"key"，value）`记录额外的信息，其中`value`可以是字符串或`int`。 `key`记录的最后一个值将被发送到指定的XML输出。 例如:

```bash
TEST_F(WidgetUsageTest, MinAndMaxWidgets) {
  RecordProperty("MaximumWidgets", ComputeMaxUsage());
  RecordProperty("MinimumWidgets", ComputeMinUsage());
}
```

将会输出XML：

```xml
  ...
    <testcase name="MinAndMaxWidgets" status="run" time="0.006" classname="WidgetUsageTest" MaximumWidgets="12" MinimumWidgets="9" />
  ...
```

> 注意：

> - `RecordProperty()`是`Test`类的静态成员。 因此，如果在`TEST`主体和测试夹具类的外部使用，则必须以`::testing::Test::`作为前缀。

> - `key`必须是合法的XML属性名称，并且不能和GTEST已有的属性相冲突(`name`, `status`, `time`, `classname`, `type_param`, and `value_param`)。

> - 允许在测试的生命周期之外调用`RecordProperty（）`。 如果在测试外部调用它，但在测试套件的`SetUpTestSuite（）`和`TearDownTestSuite（）`方法之间调用，它将被归因于该测试套件的XML元素。 如果在所有测试套件之外（例如在测试环境中）调用它，则它将归因于顶级XML元素。

## 在相同测试套件中的不同测试之间共享资源

googletest为每个测试创建一个新的test fixture对象，使测试独立且易于调试。然而，有时测试使用的资源昂贵，因此“每个测试一份拷贝”模型的成本过高。

如果测试不更改资源，那么共享单个资源副本不会有任何危害。 因此，除了按测试设置`Setup/TearDown`外，googletest还支持按testsuit设置`SetUp/TearDown`。 要使用它：

1. 在`test fixture`类（例如FooTest）中，将一些成员变量声明为**静态变量**以保存共享资源。
2. 在`test fixture`类之外（通常在它的下面），定义那些成员变量，可以选择性的初始化它们。
3. 在同一`test fixture`类中，定义一个静态的`void SetUpTestSuite（）`函数（请记住不要将它拼写为带有小u的`SetupTestCase`）来配置共享资源，并定义一个静态的`TearDownTestCase（）`函数来清理共享资源。

GTEST在运行`FooTest`中第一个测试之前调用`SetUpCase`(在创建第一个`FooTest`对象前)，并且在最后一个测试之后（删除最后一个`FooTest`对象之后）调用`TearDownCase()`。在中间，所有的测试使用共享的资源。

请记住，测试顺序是不确定的，因此代码不能依赖于另一个测试。 同样，测试必须不能修改任何共享资源的状态。如果确实要修改状态，则必须在将控制权传递给下一个测试之前将状态恢复为原始值。

例如：

```cpp
class FooTest : public ::testing::Test {
 protected:
  // Per-test-suite set-up.
  // Called before the first test in this test suite.
  // Can be omitted if not needed.
  static void SetUpTestCase() {
    shared_resource_ = new ...;
  }
  // Per-test-suite tear-down.
  // Called after the last test in this test suite.
  // Can be omitted if not needed.
  static void TearDownTestCase() {
    delete shared_resource_;
    shared_resource_ = NULL;
  }
  // You can define per-test set-up logic as usual.
  virtual void SetUp() { ... }
  // You can define per-test tear-down logic as usual.
  virtual void TearDown() { ... }
  // Some expensive resource shared by all tests.
  static T* shared_resource_;
};
T* FooTest::shared_resource_ = NULL;
TEST_F(FooTest, Test1) {
  ... you can refer to shared_resource_ here ...
}
TEST_F(FooTest, Test2) {
  ... you can refer to shared_resource_ here ...
}
```

注意：尽管上面的代码声明`SetUpTestSuite（）`为`protect`，但有时可能需要将其声明为`public`，例如与`TEST_P`一起使用时。

## 全局SetUp和TearDown

`setup`和`teardown`可以在测试层面，测试套件级别上进行，同样也可以在程序级别上进行。

首先，定义一个知道如何`setup`和`teardown`的`::test::Environment`的子类：

```csharp
class Environment : public ::testing::Environment {
 public:
  ~Environment() override {}
  // Override this to define how to set up the environment.
  void SetUp() override {}
  // Override this to define how to tear down the environment.
  void TearDown() override {}
};
```

接下来调用`::testing::AddGlobalTestEnvironment()`函数在GTEST中注册一个Environment的实例。

```cpp
Environment* AddGlobalTestEnvironment(Environment* env);
```

当调用`RUN_ALL_TESTS()`时，首先调用每个environment的`SetUp()`函数，接下来如果环境报告没有报错且未调用`GTEST_SKIP()`，则继续运行测试。`RUN_ALL_TEST()`总是会调用每个environment对象的`TearDown()`函数，无论测试有没有运行。

注册多个environment对象是可行的，在此套件中，按他们注册的顺序调用它们的`SetUp()`，并按相反的顺序调用他们的`TearDown()`。

注意GTEST拥有已注册环境对象的所有权。 因此**请勿自行删除它们**。

一般在`main()`函数中调用`RUN_ALL_TEST()`之前需要先调用`AddGlobalTestEnvironment()`。但是如果使用了`gtest_main`，则需要在`main()`函数之前调用`AddGlobalTestEnvironment()`。实现这个的一种方法是定义一个全局变量：

```php
::testing::Environment* const foo_env =

    ::testing::AddGlobalTestEnvironment(new FooEnvironment);
```

但是，强烈建议编写自己的`main()`并在其中调用`AddGlobalTestEnvironment()`，因为依赖全局变量的初始化会使代码更加难以阅读，当从不同的翻译单元注册多个environment时，可能会导致问题，并且在environment之间也会存在依赖管理依赖关系（编译器不保证初始化来自不同翻译单元的全局变量的顺序）。

## 值参数化测试

值参数化测试允许使用不同的参数测试代码，而无需编写同一测试的多个副本。 这在许多情况下很有用，例如：

- 有一段代码，其行为受到一个或多个命令行标志的影响，想要确保代码在这些标志取各种值的情况下都能正确执行。
- 要测试面向对象的接口的不同实现。
- 想要用不同的输入测试代码（数据驱动测试）, 这一特性很容易被滥用，请谨慎使用。

### 如何编写值参数化测试

为了编写值参数化测试，首先要定义一个`testing::Test`和`testing::WithParamInterface<T>`(后者是纯接口类)的派生类-`fixture`类，其中`T`是参数类型。为了便于书写，可以直接从`testing::TestWithParam<T>`派生，它是`testing::Test`和`testing::WithParamInterface<T>`共同派生出来的一个类。`T`可以是任意可拷贝类型，如果它是一个裸指针，还需要负责指针的生命周期。

注意：如果测试定义了`SetUpTestCase()`或`TearDownTestCase()`，则必须将它们声明为**public**而不是**protected**，才能使用`TEST_P`。

```cpp
class FooTest :
    public testing::TestWithParam<const char*> {
  // You can implement all the usual fixture class members here.
  // To access the test parameter, call GetParam() from class
  // TestWithParam<T>.
};

// Or, when you want to add parameters to a pre-existing fixture class:
class BaseTest : public testing::Test {
  ...
};
class BarTest : public BaseTest,
                public testing::WithParamInterface<const char*> {
  ...
};
```

然后，使用`TEST_P`宏根据需要使用此fixture定义尽可能多的测试模式。`_P`后缀的意思是“参数化”或“模式”。



```cpp
TEST_P(FooTest, DoesBlah) {
  // Inside a test, access the test parameter with the GetParam() method
  // of the TestWithParam<T> class:
  EXPECT_TRUE(foo.Blah(GetParam()));
  ...
}

TEST_P(FooTest, HasBlahBlah) {
  ...
}
```

可以通过`INSTANTIATE_TEST_CASE_P`，使用任何一组想要的参数来实例化test suite。googletest定义了许多函数来生成测试参数。它们返回我们所称的参数生成器。下面是它们的摘要，这些函数都在`testing`命名空间中：

| Parameter Generator                              | Behavior                                                     |
| ------------------------------------------------ | ------------------------------------------------------------ |
| `Range(begin, end [, step])`                     | Yields values `{begin, begin+step, begin+step+step, ...}`. The values do not include `end`. `step` defaults to 1. |
| `Values(v1, v2, ..., vN)`                        | Yields values `{v1, v2, ..., vN}`.                           |
| `ValuesIn(container)` and  `ValuesIn(begin,end)` | Yields values from a C-style array, an  STL-style container, or an iterator range `[begin, end)` |
| `Bool()`                                         | Yields sequence `{false, true}`.                             |
| `Combine(g1, g2, ..., gN)`                       | Yields all combinations (Cartesian product) as std::tuples of the values generated by the `N` generators. |

以下语句将实例化`FooTest` 测试套件(test suite)中的测试，试参数参数值分别为`“ meeny”`，`“ miny”`和`“ moe”`。

```php
INSTANTIATE_TEST_CASE_P(InstantiationName,
                         FooTest,
                         testing::Values("meeny", "miny", "moe"));            
```

**注意**：上面的代码必须放在全局或命名空间范围，而不是函数中。

**注意**：不要忘记这一步！ 如果忘记了，那么测试会默默地通过，但是其实没有任何套件运行！

目前正在进行一些工作，以使`GoogleTestVerification`测试套件能够发现省略了`INSTANTIATE_TEST_SUITE_P`，并将该行为标记为一个错误。 如果有一个测试套件遗漏了`INSTANTIATE_TEST_SUITE_P`但不用视作一个错误，例如，它位于可能由于其他原因而链接到的库中，或者测试用例列表是动态的并且可能为空，那么可以通过以下方式来标记测试套件以取消此检查：

```undefined
GTEST_ALLOW_UNINSTANTIATED_PARAMETERIZED_TEST(FooTest);
```

为了区分该模式的不同实例，`INSTANTIATE_TEST_SUITE_P`的第一个参数将作为一个前缀添加到实际的测试套件名称中。 请记住为不同的实例选择唯一的前缀。 上面实例化的测试将具有以下名称：

- `InstantiationName/FooTest.DoesBlah/0` for `"meeny"`
- `InstantiationName/FooTest.DoesBlah/1` for `"miny"`
- `InstantiationName/FooTest.DoesBlah/2` for `"moe"`
- `InstantiationName/FooTest.HasBlahBlah/0` for `"meeny"`
- `InstantiationName/FooTest.HasBlahBlah/1` for `"miny"`
- `InstantiationName/FooTest.HasBlahBlah/2` for `"moe"`

这些名称可以在`--gtest_filter`中使用

下面的语句将再次实例化`FooTest`的所有测试，每个测试的参数值分别为`“ cat”`和`“ dog”`：

```rust
const char* pets[] = {"cat", "dog"};
INSTANTIATE_TEST_SUITE_P(AnotherInstantiationName, FooTest,
                         testing::ValuesIn(pets));
                         
```

上面的命名实例化的测试名称如下

- `AnotherInstantiationName/FooTest.DoesBlah/0` for `"cat"`
- `AnotherInstantiationName/FooTest.DoesBlah/1` for `"dog"`
- `AnotherInstantiationName/FooTest.HasBlahBlah/0` for `"cat"`
- `AnotherInstantiationName/FooTest.HasBlahBlah/1` for `"dog"`

请注意，`INSTANTIATE_TEST_CASE_P`将实例化给定测试套件中的所有测试，无论它们的定义在`INSTANTIATE_TEST_CASE_P`语句之前还是之后。

### 创建值参数化抽象测试

之前，我们在同一源文件中定义并实例化`FooTest`。 有时可能需要在库中定义值参数化的测试，然后让别人实例化它们， 这种模式称为抽象测试。 作为其使用的一个示例，在设计接口时，可以编写标准的抽象测试套件（也许使用工厂函数作为测试参数），期望该接口的所有实现都能通过。 当其他人实现这个接口时，他们可以实例化你的套件以获得所有接口一致性测试。

应该按如下形式组织代码来定义抽象测试：

1. 将定义参数化测试fixture的类定义在一个头文件里，如`foo_param_test.h`，把这个看做是对抽象测试的声明。
2. 将`TEST_P`定义放在`foo_param_test.cc`中，其中引用头文件`foo_param_test.h`， 将此视为实现抽象测试。

定义它们之后，可以通过引用`foo_param_test.h`头文件，调用`INSTANTIATE_TEST_SUITE_P（）`函数，并且依赖包含`foo_param_test.cc`的目标库来实例化它们。 这样就可以在不同的源文件中多次实例化同一抽象测试套件。

### 为值参数化的测试参数指定名称

`INSTANTIATE_TEST_SUITE_P()`的最后一个可选参数允许使用者定制一个能够基于测试参数生成的测试名后缀的函数。该函数应接受一个类型为`testing::TestParamInfo <class ParamType>`的参数，并返回`std :: string`。

`testing::PrintToStringParamName`是一个内置的测试名后缀生成器，它返回`testing::PrintToString(GetParam())`的值。它不适用于`std::string`或C字符串。

注意：测试名称必须是非空的，唯一的，并且只能包含ASCII字母数字字符。特别是，它们不应包含下划线

```c
class MyTestSuite : public testing::TestWithParam<int> {};

TEST_P(MyTestSuite, MyTest)
{
  std::cout << "Example Test Param: " << GetParam() << std::endl;
}

INSTANTIATE_TEST_SUITE_P(MyGroup, MyTestSuite, testing::Range(0, 10),
                         testing::PrintToStringParamName());
```

提供自定义函数可以更好地控制测试参数名称的生成，尤其是对于自动转换不会生成有用的参数名称的类型(例如，如上所述的字符串)。下面的示例说明了多个参数，枚举类型和字符串的情况，并演示了如何组合生成器。为了简洁起见，它使用lambda表达式：



```c
enum class MyType { MY_FOO = 0, MY_BAR = 1 };

class MyTestSuite : public testing::TestWithParam<std::tuple<MyType, string>> {
};

INSTANTIATE_TEST_SUITE_P(
    MyGroup, MyTestSuite,
    testing::Combine(
        testing::Values(MyType::VALUE_0, MyType::VALUE_1),
        testing::ValuesIn("", "")),
    [](const testing::TestParamInfo<MyTestSuite::ParamType>& info) {
      string name = absl::StrCat(
          std::get<0>(info.param) == MY_FOO ? "Foo" : "Bar", "_",
          std::get<1>(info.param));
      absl::c_replace_if(name, [](char c) { return !std::isalnum(c); }, '_');
      return name;
    });
```

## 类型测试

假设现在具有同一个接口的多个实现，并且想要确保它们都满足一些共同的要求。或者，可能已经定义了几种应该符合相同“概念”的类型，并且想要对其进行验证。在两种情况下，都希望针对不同类型重复相同的测试逻辑。

虽然可以为每种要测试的类型编写一个`TEST`或`TEST_F`（甚至可以将测试逻辑分解为从TEST调用的功能模板中），但这种操作很繁琐且无法扩展，如果要对n种类型进行m个测试，最终将编写m * n个`TEST`。

类型化测试允许在一系列的类型上重复相同的测试逻辑。尽管编写类型测试时必须知道类型列表，但只需要编写一次测试逻辑。 下面是操作方式：

首先，定义一个从`::testing::Test`派生的fixture类模板，应当通过某种类型对其进行参数化：

```cpp
template <typename T>
class FooTest : public ::testing::Test {
 public:
  ...
  typedef std::list<T> List;
  static T shared_;
  T value_;
};
```

接下来，将类型列表与测试套件相关联，将对该列表中的每种类型重复此操作:

```cpp
using MyTypes = ::testing::Types<char, int, unsigned int>;
TYPED_TEST_SUITE(FooTest, MyTypes);
```

类型别名（`using`或`typedef`）是`TYPED_TEST_SUITE`宏正确解析所必需的。否则，编译器会认为类型列表中的每个逗号都会引入一个新的宏参数。

然后，使用`TYPED_TEST（）`代替`TEST_F（）`为该测试套件定义类型测试。 可以根据需要重复多次：



```php
TYPED_TEST(FooTest, DoesBlah) {
  // Inside a test, refer to the special name TypeParam to get the type
  // parameter.  Since we are inside a derived class template, C++ requires
  // us to visit the members of FooTest via 'this'.
  TypeParam n = this->value_;
  // To visit static members of the fixture, add the 'TestFixture::'
  // prefix.
  n += TestFixture::shared_;
  // To refer to typedefs in the fixture, add the 'typename TestFixture::'
  // prefix.  The 'typename' is required to satisfy the compiler.
  typename TestFixture::List values;
  values.push_back(n);
  ...
}
TYPED_TEST(FooTest, HasPropertyA) { ... }
```

## 类型参数化测试

类型参数化测试与类型测试类似，不同之处在于它们不需要提前知道类型列表。相反，可以先定义测试逻辑，然后再使用不同的类型列表实例化它，甚至可以在同一程序中多次实例化它。

如果要设计接口或概念，则可以定义一组类型参数化的测试，以验证接口/概念的任何有效实现应具有的属性。 然后，每个实现的作者都可以使用其类型实例化测试套件以验证其是否符合要求，而不必重复编写类似的测试。 例如：

首先，定义一个像类型测试那样的fixture类模板。

```cpp
template <typename T>
class FooTest : public ::testing::Test {
  ...
};
```

然后声明将定义一个类型参数化的测试套件

```undefined
TYPED_TEST_SUITE_P(FooTest);
```

然后，使用`TYPED_TEST_P（）`定义类型参数化的测试。 可以根据需要重复多次：

```cpp
TYPED_TEST_P(FooTest, DoesBlah) {

  // Inside a test, refer to TypeParam to get the type parameter.

  TypeParam n = 0;

  ...

}


TYPED_TEST_P(FooTest, HasPropertyA) { ... }
```

现在，棘手的部分是，需要使用`REGISTER_TYPED_TEST_SUITE_P`宏注册所有测试模式，然后才能实例化它们。 宏的第一个参数是测试套件名称, 其余的是此测试套件中测试的名称：

```undefined
REGISTER_TYPED_TEST_SUITE_P(FooTest,

                            DoesBlah, HasPropertyA);
```

最后，您可以随意用所需的类型实例化模式。 如果将上面的代码放在头文件中，则可以`#include`将其包含在多个C ++源文件中，并将其实例化多次。

```cpp
typedef ::testing::Types<char, int, unsigned int> MyTypes;
INSTANTIATE_TYPED_TEST_SUITE_P(My, FooTest, MyTypes);
```

为了区分模式的不同实例,`INSTANTIATE_TYPED_TEST_SUITE_P`宏的第一个参数是一个前缀，它将添加到实际的测试套件名称中。 请记住为不同的实例选择唯一的前缀。

在类型列表仅包含一种类型的特殊情况下，您可以直接编写该类型，而无需`::testing::Types <...>`，如下所示：

```cpp
INSTANTIATE_TYPED_TEST_SUITE_P(My, FooTest, int);
```

## 测试private代码

如果更改软件的内部实现，只要用户看不到更改，测试就不会中断。因此，根据黑盒测试原则，大多数时候应该通过其公共接口来测试代码。

如果仍然需要测试内部实现代码，请考虑是否有更好的设计。需要测试内部实现通常表明该类做了太多事情。考虑提取一个实现类，并对其进行测试，然后在原始类中使用该实现类。

如果一定要测试非公共接口代码的话，例如以下两种情况：

- 静态函数（和静态成员函数不同）或者没有命名的命名空间
- private或者protected的类成员

可以使用独特的方法来测试他们：

- 未命名名称空间中的静态函数和定义/声明仅在同一编译单元中可见。要对其进行测试，可以将需要测试的`.cc`文件`#include`到`* _test.cc`文件中。（#include `.cc`文件不是重用代码的好方法，不应该在生产代码中这样做！）
- 更好的方法是将私有代码移到`foo::internal`命名空间中，其中`foo`是项目通常使用的命名空间，并将私有声明放入`* -internal.h`文件中。编写的`.cc`文件和测试允许包含此内部标头，但客户端则不允许。这样，就可以完全测试内部实现，而不会泄漏给客户。
- 私有类成员只能在类内部或由友元访问。 要访问类的私有成员，可以将test fixture声明为类的友元，并在fixture中定义访问器。然后，使用test fixture进行的测试就可以通过fixture中的访问器访问生产类的私有成员。请注意，即使fixture是生产类的友元，测试也不会自动成为生产类的友元，因为它们是在fixture的子类中定义的。
- 测试私有成员的另一种方法是将它们重构为实现类，然后在`* -internal.h`文件中对其进行声明。客户端不允许包含此标头，但是测试代码可以。这就是所谓的Pimpl（私有实现）习惯用法。

或者可以生产类中添加以下代码，声明一个独立的测试作为被测试类的友元：

```undefined
FRIEND_TEST(TestSuiteName, TestName);
```

例如：

```cpp
// foo.h
class Foo {
  ...
 private:
  FRIEND_TEST(FooTest, BarReturnsZeroOnNull);

  int Bar(void* x);
};

// foo_test.cc
...
TEST(FooTest, BarReturnsZeroOnNull) {
  Foo foo;
  EXPECT_EQ(foo.Bar(NULL), 0);  // Uses Foo's private member Bar().
}
```

当测试类在命名空间中定义时要特别注意：如果希望它们成为生产类的友元，则应该在生产类的同一名称空间中定义test fixture和测试。 例如，如果要测试的代码如下所示：

```cpp
namespace my_namespace {

class Foo {
  friend class FooTest;
  FRIEND_TEST(FooTest, Bar);
  FRIEND_TEST(FooTest, Baz);
  ... definition of the class Foo ...
};

}  // namespace my_namespace
```

那么测试代码应该如下：

```cpp
namespace my_namespace {

class FooTest : public ::testing::Test {
 protected:
  ...
};

TEST_F(FooTest, Bar) { ... }
TEST_F(FooTest, Baz) { ... }

}  // namespace my_namespace
```

## 捕获失败

如果要在googletest之上构建测试工具集，那么应该使用什么框架测试这个测试工具集？当然是 googletest。

所需要面临的挑战是验证改测试工具集是否能够正确报告故障。在通过引发异常报告失败的框架中，可以捕获异常并对其进行断言。 但是googletest不使用异常，那么应该如何测试一段代码是否会产生预期的失败？

`gunit-spi.h`包含一些用于执行此操作的结构。在`#include`该头文件之后，就可以使用：

```undefined
EXPECT_FATAL_FAILURE(statement, substring);
```

来断言该包含给定`substring`的消息的`statement`在当前线程中是否产生了致命错误（例如`ASSERT_ *`），或使用：

```undefined
EXPECT_NONFATAL_FAILURE(statement, substring);
```

来断言非致命错误。

上述宏仅检查当前线程中的失败，以确定这类期望的结果。 如果`statement`创建新线程，这些线程中的故障也将被忽略。如果您还想捕获其他线程中的故障，请改用以下宏之一：

```undefined
  EXPECT_FATAL_FAILURE_ON_ALL_THREADS(statement, substring);
  EXPECT_NONFATAL_FAILURE_ON_ALL_THREADS(statement, substring);
```

注意：Windows暂时不支持来自多个线程的断言

从技术上来说，还有以下警告：

1. 这些宏不能使用流导入失败信息
2. `EXPECT_FATAL_FAILURE{_ON_ALL_THREADS}()`中的`statement`不能引用非静态变量或者`this`对象的非静态成员
3. `EXPECT_FATAL_FAILURE{_ON_ALL_THREADS}()`中的`statement`不能返回值。

## 程序化的注册测试

`TEST`宏可处理绝大多数的测试用例，但是也会有极少数情况下需要运行时注册逻辑。 对于这些情况，框架提供`::testing::RegisterTest`来允许调用者动态注册任意测试。

这是仅在`TEST`宏不足时才使用的高级API。 应该尽可能首选宏，因为它们避免了调用此函数的大部分复杂性。

例如：

```cpp
template <typename Factory>
TestInfo* RegisterTest(const char* test_suite_name, const char* test_name,
                       const char* type_param, const char* value_param,
                       const char* file, int line, Factory factory);
```

`factory`参数是可调用的（可移动构造的）`factory`对象或函数指针，它创建Test对象的新实例。它处理呼叫者的所有权。 可调用对象的签名为`Fixture *（）`，其中`Fixture`是测试的test fixture类。所有的测试使用相同`test_suite_name`注册，并且必须返回相同的fixture类型。 这会在运行时进行检查。

该框架将从factory推断出fixture类，并将为此调用`SetUpTestSuite`和`TearDownTestSuite`。

必须在调用`RUN_ALL_TESTS（）`之前调用`::testing::RegisterTest`，否则行为是不确定的。

例如：

```dart
class MyFixture : public ::testing::Test {
 public:
  // All of these optional, just like in regular macro usage.
  static void SetUpTestSuite() { ... }
  static void TearDownTestSuite() { ... }
  void SetUp() override { ... }
  void TearDown() override { ... }
};

class MyTest : public MyFixture {
 public:
  explicit MyTest(int data) : data_(data) {}
  void TestBody() override { ... }

 private:
  int data_;
};

void RegisterMyTests(const std::vector<int>& values) {
  for (int v : values) {
    ::testing::RegisterTest(
        "MyFixture", ("Test" + std::to_string(v)).c_str(), nullptr,
        std::to_string(v).c_str(),
        __FILE__, __LINE__,
        // Important to use the fixture type as the return type here.
        [=]() -> MyFixture* { return new MyTest(v); });
  }
}
...
int main(int argc, char** argv) {
  std::vector<int> values_to_test = LoadValuesFromConfig();
  RegisterMyTests(values_to_test);
  ...
  return RUN_ALL_TESTS();
}
```

## 获取当前的测试名

有时某个功能可能需要知道当前正在运行的测试的名称。 例如，可能使用test fixture的`SetUp（）`方法来根据正在运行的测试来设置最佳文件名。`::testing::TestInfo`类具有以下信息：

```cpp
namespace testing {
class TestInfo {
 public:
  // Returns the test suite name and the test name, respectively.
  //
  // Do NOT delete or free the return value - it's managed by the
  // TestInfo class.
  const char* test_suite_name() const;
  const char* name() const;
};
}
```

要获取当前正在运行的测试的`TestInfo`对象，请对`UnitTest`单例对象调用`current_test_info()`：

```php
// Gets information about the currently running test.
  // Do NOT delete the returned object - it's managed by the UnitTest class.
  const ::testing::TestInfo* const test_info =
    ::testing::UnitTest::GetInstance()->current_test_info();

  printf("We are in test %s of test suite %s.\n",
         test_info->name(),
         test_info->test_suite_name());      
```

如果没有测试在运行，`current_test_info（）`返回一个空指针。 特别是，不能从`TestSuiteSetUp（）`，`TestSuiteTearDown（）`（可以隐式知道测试套件名称）或他们调用的函数中获取测试套件名称。

## 通过处理测试事件来扩展googletest

googletest提供了事件监听器API，可让接收有关测试程序进度和测试失败的通知。可以监听的事件包括测试程序，测试套件或测试方法的开始和结束。 可以使用此API来扩充或替换标准控制台输出，替换XML输出或提供完全不同形式的输出，例如GUI或数据库。还可以将测试事件用作检查点以实现资源泄漏检查器。

### 定义事件监听器

要定义事件监听器，可以将`testing :: TestEventListener`或`testing :: EmptyTestEventListener`子类化。前者是一个（抽象的）接口，可以覆`override`每个纯虚拟方法来处理测试事件（例如，测试开始时， `OnTestStart（）`方法将被调用。） 后者提供了接口中所有方法的空实现，因此子类只需要`override`其关心的方法。

触发事件时，其上下文作为参数传递给处理程序函数。 将使用以下参数类型：

- UnitTest反映整个测试程序的状态
- TestSuite具有有关测试套件的信息，其中可以包含一个或多个测试
- TestInfo包含一个测试的状态
- TestPartResult表示测试断言的结果

事件处理程序函数可以检查其接收到的参数，以找到有关事件和测试程序状态的令人感兴趣的信息。

例如：

```cpp
class MinimalistPrinter : public ::testing::EmptyTestEventListener {
    // Called before a test starts.
    virtual void OnTestStart(const ::testing::TestInfo& test_info) {
      printf("*** Test %s.%s starting.\n",
             test_info.test_suite_name(), test_info.name());
    }

    // Called after a failed assertion or a SUCCESS().
    virtual void OnTestPartResult(const ::testing::TestPartResult& test_part_result) {
      printf("%s in %s:%d\n%s\n",
             test_part_result.failed() ? "*** Failure" : "Success",
             test_part_result.file_name(),
             test_part_result.line_number(),
             test_part_result.summary());
    }

    // Called after a test ends.
    virtual void OnTestEnd(const ::testing::TestInfo& test_info) {
      printf("*** Test %s.%s ending.\n",
             test_info.test_suite_name(), test_info.name());
    }
  };
```

### 使用事件监听器

在`main()`函数调用`RUN_ALL_TESTS()`前，在GTEST事件监听器列表中添加定义的监听器的实例化来使用它。

```php
int main(int argc, char** argv) {
  ::testing::InitGoogleTest(&argc, argv);
  // Gets hold of the event listener list.
  ::testing::TestEventListeners& listeners =
        ::testing::UnitTest::GetInstance()->listeners();
  // Adds a listener to the end.  googletest takes the ownership.
  listeners.Append(new MinimalistPrinter);
  return RUN_ALL_TESTS();
}
```

仅有一个问题：默认的测试结果打印器仍然有效，因此定义的输出将于原始的输出混合在一起。 要取消显示默认打印器，只需将其从事件侦听器列表中释放并删除即可。可以通过添加一行代码来做到这一点：

```cpp
  delete listeners.Release(listeners.default_result_printer());
  listeners.Append(new MinimalistPrinter);
  return RUN_ALL_TESTS();
```

现在，坐下来享受与默认的测试完全不同的输出。

可以在列表中追加一个以上的侦听器。当触发`On * Start（）`或`OnTestPartResult（）`事件时，侦听器将按照它们在列表中出现的顺序来接收事件（因为新的侦听器被添加到列表的末尾，默认的文本打印器和默认的XML生成器将首先收到事件）。侦听器将以相反的顺序接收`On * End（）`事件。

### 生成失败监听器

处理事件时，可以使用引发故障的宏（`EXPECT _ *（）`，`ASSERT _ *（）`，`FAIL（）`等）。但他们有一些限制：

1. 无法在`OnTestPartResult（）`中产生任何故障（否则将导致递归调用`OnTestPartResult（）`）
2. 不允许处理`OnTestPartResult（）`的侦听器生成任何故障。

将侦听器添加到侦听器列表时，应该将处理`OnTestPartResult（）`的侦听器放在可能产生故障的侦听器之前。这样可以确保后者产生的故障归因于前者的正确测试。

## 运行测试程序：更多选项

googletest测试程序是普通的可执行文件。构建完成后，可以直接运行它们，并通过以下环境变量和/或命令行标志影响它们的行为。为了使这些标志起作用，您的程序必须在调用`RUN_ALL_TESTS()`之前调用`::testing::InitGoogleTest()`。

要查看受支持的标志及其用法的列表，请使用--help标志运行测试程序。 也可以使用-h，-？或/？作为缩写。

如果同时由环境变量和标志指定选项，则后者优先。

### 选择测试

**列出测试名字**

有时，有必要在运行它们之前在程序中列出可用的测试，以便在需要时可以应用过滤器。包含标志`--gtest_list_tests`会覆盖所有其他标志，并以以下格式列出测试：

```undefined
TestSuite1.
  TestName1
  TestName2
TestSuite2.
  TestName
```

如果提供了该标志，则列出的测试均不会实际运行， 此标志没有相应的环境变量。

**运行测试的子集**

默认情况下，googletest程序会运行用户定义的所有测试。有些情况下，只想运行一部分测试（例如用于调试或快速验证更改）。如果将`GTEST_FILTER`环境变量或`--gtest_filter`标志设置为过滤器字符串，则googletest将仅运行其名称（以`TestSuiteName.TestName`的形式）与过滤器匹配的测试。

过滤器的格式是由`':'`分隔的通配符模式列表（称为正模式），可以选择后跟`“-”`和另一个由`“：”`分隔的模式列表（称为负模式）。当且仅当测试匹配任何正模式但不匹配任何负模式时，测试才与过滤器匹配。

模式可以包含`“ *”`（与任何字符串匹配）或`“？”`（匹配任何单个字符）。为方便起见，过滤器`“ * -NegativePatterns”`也可以写为`“ -NegativePatterns”`。

例如：

- `./foo_test` 没有标志，所以运行所有的测试
- `./foo_test --gtest_filter=*` 也运行所有测试，因为他匹配所有东西
- `./foo_test --gtest_filter=FooTest.*`运行`FooTest`测试套件里的所有测试
- `./foo_test --gtest_filter=*Null*:*Constructor*`运行名字里有`NULL`或者`Constructor`的测试
- `./foo_test --gtest_filter=-*DeathTest.*`运行所有非死亡测试
- `./foo_test --gtest_filter=FooTest.*-FooTest.Bar`运行测试套件`FooTest`中除了`FooTest.Bar`的其他测试
- `./foo_test --gtest_filter=FooTest.*:BarTest.*-FooTest.Bar:BarTest.Foo`运行测试套件`FooTest`中除了`FooTest.Bar`的测试以及测试套件`BarTest`中除了`BarTest.foo`的其他测试

**短暂禁用测试**

如果有无法立即修复的损坏测试，则可以在其名称中添加`DISABLED_`前缀,这会将其从执行中排除。这比注释掉代码或使用`#if 0`更好，因为被禁用的测试仍然会被编译。

如果需要禁用测试套件中的所有测试，则可以将`DISABLED_`添加到每个测试名称的前面，或者将其添加到测试套件名称的前面。

例如，下面的测试不会运行但是仍然会被编译:

```cpp
// Tests that Foo does Abc.
TEST(FooTest, DISABLED_DoesAbc) { ... }

class DISABLED_BarTest : public ::testing::Test { ... };

// Tests that Bar does Xyz.
TEST_F(DISABLED_BarTest, DoesXyz) { ... }
```

注意：此功能仅应用于暂时性的禁止测试。之后仍然需要修复已禁用的测试。如果测试程序包含任何禁用的测试，则googletest会打印一条横幅来提醒你。

提示：您可以轻松使用`gsearch`和/或`grep`来获取禁用的测试的数量，此数字可用作提高测试质量的指标。

**暂时运行不可运行的测试**

要将禁用的测试包括在测试执行中，只需使用`--gtest_also_run_disabled_tests`标志调用测试程序或将`GTEST_ALSO_RUN_DISABLED_TESTS`环境变量设置为非`0`的值即可。您可以将其与`--gtest_filter`标志结合使用，以进一步选择要运行的禁用测试 。

### 重复测试

偶尔，会遇到测试结果是不确定值的测试。也许它只会在1％的时间内失败，这使得在调试器中重现该错误变得相当困难。这可能是造成挫败感的主要原因。

`--gtest_repeat`标志允许多次重复程序中的所有（或选定的）测试方法。希望不稳定的测试最终会失败，并提供调试的机会。 使用方法如下：

```cpp
$ foo_test --gtest_repeat=1000
Repeat foo_test 1000 times and don't stop at failures.

$ foo_test --gtest_repeat=-1
A negative count means repeating forever.

$ foo_test --gtest_repeat=1000 --gtest_break_on_failure
Repeat foo_test 1000 times, stopping at the first failure.  This
is especially useful when running under a debugger: when the test
fails, it will drop into the debugger and you can then inspect
variables and stacks.

$ foo_test --gtest_repeat=1000 --gtest_filter=FooBar.*
Repeat the tests whose name matches the filter 1000 times.
```

如果测试程序包含`global set-up/tear-down`代码，则在每次迭代中也会重复执行，因为其中可能会出现脆弱性。还可以通过设置`GTEST_REPEAT`环境变量来指定重复计数。

### 乱序测试

可以指定`--gtest_shuffle`标志（或将`GTEST_SHUFFLE`环境变量设置为`1`）以按随机顺序在程序中运行测试。这有助于揭示测试之间的不良依存关系

默认情况下，googletest使用当前时间当做计算随机数的种子，因此每次都会得到不同的序列。控制台输出包括随机种子值，以便以后可以重现与测试序列相关的失败。如果要明确指定随机种子，请使用`--gtest_random_seed = SEED`标志（或设置`GTEST_RANDOM_SEED`环境变量），其中`SEED`是[0，99999]范围内的整数。种子值0很特殊：它告诉googletest执行从当前时间获得种子的默认行为。

如果将其与`--gtest_repeat = N`结合使用，则googletest在每次迭代中选择其他随机种子，并重新随机排列测试。

### 控制测试的输出

**给终端输出标记颜色**

GTEST可以使用颜色标记终端输出，让用户更容易发现重要信息。

您可以将`GTEST_COLOR`环境变量或`--gtest_color`命令行标志设置为`yes`，`no`或`auto`（默认设置）来启用颜色，禁用颜色或让googletest决定。 当值为`auto`时，当且仅当输出到终端并且TERM环境变量设置为`xterm`或`xterm-color`时，googletest才会使用颜色。

**不显示运行时间**

默认情况下，googletest会显示运行每个测试所需的时间。 要禁用它，使用`--gtest_print_time = 0`命令行标志运行测试程序，或者将`GTEST_PRINT_TIME`环境变量设置为`0`。

**不输出UTF-8文本**

如果断言失败，则googletest会以十六进制编码的字符串以及可读的UTF-8文本（如果其中包含有效的非ASCII UTF-8字符）的形式输出`string`类型的预期和实际值。如果由于没有兼容UTF-8的输出媒体而不想显示UTF-8文本，请使用`--gtest_print_utf8 = 0`来运行测试程序，或者将环境变量`GTEST_PRINT_UTF8`设置为`0`。

**生成XML报告**

googletest除了可以正常输出文本外，还可以输出详细的XML报告。该报告包含每个测试的持续时间，可以帮助确定慢速测试。仪表板还使用该报告显示每个测试方法的错误消息。

要生成XML报告，请将`GTEST_OUTPUT`环境变量或`--gtest_output`标志，设置为字符串`“ xml：path_to_output_file”`，这将在给定位置创建文件。 也可以只使用字符串`“ xml”`，在这种情况下，将输出到当前目录的`test_detail.xml`。

如果指定一个目录（例如，在Linux上为`“ xml：output / directory /”`或在Windows上为`“ xml：output \ directory \”`），则googletest将在该目录中创建XML文件，该文件以测试可执行文件命名（例如测试程序`foo_test`或`foo_test.exe`的结果为`foo_test.xml`）。 如果文件已经存在（可能是上次运行遗留下来的文件），则googletest将选择其他名称（例如`foo_test_1.xml`）以避免覆盖它。

该报告是基于`junitreport Ant`任务。由于该格式最初是用于Java的，因此需要进行一些解释才能使其适用于googletest测试，如下所示：

```xml
<testsuites name="AllTests" ...>
  <testsuite name="test_case_name" ...>
    <testcase    name="test_name" ...>
      <failure message="..."/>
      <failure message="..."/>
      <failure message="..."/>
    </testcase>
  </testsuite>
</testsuites>
```

- 根节点`<testsuites>`代表整个测试程序
- `<testsuite>`元素代表googletest的测试套件
- `<testcase>`元素代表googletest的测试函数

下面的程序：

```undefined
TEST(MathTest, Addition) { ... }
TEST(MathTest, Subtraction) { ... }
TEST(LogicTest, NonContradiction) { ... }
```

将生成如下报告：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<testsuites tests="3" failures="1" errors="0" time="0.035" timestamp="2011-10-31T18:52:42" name="AllTests">
  <testsuite name="MathTest" tests="2" failures="1" errors="0" time="0.015">
    <testcase name="Addition" status="run" time="0.007" classname="">
      <failure message="Value of: add(1, 1)&#x0A;  Actual: 3&#x0A;Expected: 2" type="">...</failure>
      <failure message="Value of: add(1, -1)&#x0A;  Actual: 1&#x0A;Expected: 0" type="">...</failure>
    </testcase>
    <testcase name="Subtraction" status="run" time="0.005" classname="">
    </testcase>
  </testsuite>
  <testsuite name="LogicTest" tests="1" failures="0" errors="0" time="0.005">
    <testcase name="NonContradiction" status="run" time="0.005" classname="">
    </testcase>
  </testsuite>
</testsuites>
```

需要注意的事项：

- `<testsuites>`或`<testsuite>`元素的`test`属性告诉googletest程序或测试套件包含多少个测试函数，而`failures`属性告诉他们其中有多少失败。
- `time`属性以秒为单位表示测试、测试套件或整个测试程序的持续时间。
- `timestamp`属性记录测试执行的本地日期和时间。
- 每个`<failure>`元素对应一个失败的googletest断言。

**生成JSON报告**

googletest还可以生成JSON报告作为XML的另一种格式。要生成JSON报告，请将`GTEST_OUTPUT`环境变量或`--gtest_output`标志，设置为字符串`“ json：path_to_output_file”`，GTEST将在给定位置创建文件。 也可以只使用字符串`“ json”`，在这种情况下，可以在当前目录的`test_detail.json`文件中找到输出。

JSON报告形式如下：

```json
{
  "$schema": "http://json-schema.org/schema#",
  "type": "object",
  "definitions": {
    "TestCase": {
      "type": "object",
      "properties": {
        "name": { "type": "string" },
        "tests": { "type": "integer" },
        "failures": { "type": "integer" },
        "disabled": { "type": "integer" },
        "time": { "type": "string" },
        "testsuite": {
          "type": "array",
          "items": {
            "$ref": "#/definitions/TestInfo"
          }
        }
      }
    },
    "TestInfo": {
      "type": "object",
      "properties": {
        "name": { "type": "string" },
        "status": {
          "type": "string",
          "enum": ["RUN", "NOTRUN"]
        },
        "time": { "type": "string" },
        "classname": { "type": "string" },
        "failures": {
          "type": "array",
          "items": {
            "$ref": "#/definitions/Failure"
          }
        }
      }
    },
    "Failure": {
      "type": "object",
      "properties": {
        "failures": { "type": "string" },
        "type": { "type": "string" }
      }
    }
  },
  "properties": {
    "tests": { "type": "integer" },
    "failures": { "type": "integer" },
    "disabled": { "type": "integer" },
    "errors": { "type": "integer" },
    "timestamp": {
      "type": "string",
      "format": "date-time"
    },
    "time": { "type": "string" },
    "name": { "type": "string" },
    "testsuites": {
      "type": "array",
      "items": {
        "$ref": "#/definitions/TestCase"
      }
    }
  }
}
```

该报告使用符合以下JSON编码的Proto3的格式

```go
syntax = "proto3";

package googletest;

import "google/protobuf/timestamp.proto";
import "google/protobuf/duration.proto";

message UnitTest {
  int32 tests = 1;
  int32 failures = 2;
  int32 disabled = 3;
  int32 errors = 4;
  google.protobuf.Timestamp timestamp = 5;
  google.protobuf.Duration time = 6;
  string name = 7;
  repeated TestCase testsuites = 8;
}

message TestCase {
  string name = 1;
  int32 tests = 2;
  int32 failures = 3;
  int32 disabled = 4;
  int32 errors = 5;
  google.protobuf.Duration time = 6;
  repeated TestInfo testsuite = 7;
}

message TestInfo {
  string name = 1;
  enum Status {
    RUN = 0;
    NOTRUN = 1;
  }
  Status status = 2;
  google.protobuf.Duration time = 3;
  string classname = 4;
  message Failure {
    string failures = 1;
    string type = 2;
  }
  repeated Failure failures = 5;
}
```

例如，以下程序：

```undefined
TEST(MathTest, Addition) { ... }
TEST(MathTest, Subtraction) { ... }
TEST(LogicTest, NonContradiction) { ... }
```

将有以下输出：

```json
{
  "tests": 3,
  "failures": 1,
  "errors": 0,
  "time": "0.035s",
  "timestamp": "2011-10-31T18:52:42Z",
  "name": "AllTests",
  "testsuites": [
    {
      "name": "MathTest",
      "tests": 2,
      "failures": 1,
      "errors": 0,
      "time": "0.015s",
      "testsuite": [
        {
          "name": "Addition",
          "status": "RUN",
          "time": "0.007s",
          "classname": "",
          "failures": [
            {
              "message": "Value of: add(1, 1)\n  Actual: 3\nExpected: 2",
              "type": ""
            },
            {
              "message": "Value of: add(1, -1)\n  Actual: 1\nExpected: 0",
              "type": ""
            }
          ]
        },
        {
          "name": "Subtraction",
          "status": "RUN",
          "time": "0.005s",
          "classname": ""
        }
      ]
    },
    {
      "name": "LogicTest",
      "tests": 1,
      "failures": 0,
      "errors": 0,
      "time": "0.005s",
      "testsuite": [
        {
          "name": "NonContradiction",
          "status": "RUN",
          "time": "0.005s",
          "classname": ""
        }
      ]
    }
  ]
}
```

注意：JSON文档的确切格式可能会更改。

### 控制如何报告失败

**将断言失败转变为断点**

在debugger下运行测试程序时，如果调试器可以非常方便的捕获断言失败并自动进入交互模式。googletest的`break-on-failure`模式支持此行为

要启用它，需要将`GTEST_BREAK_ON_FAILURE`环境变量设置为非`0`的值。或者可以使用`--gtest_break_on_failure`命令行标志。

**禁用捕获抛出的测试异常**

无论是否启用例外，都可以使用googletest。如果测试抛出C ++异常或（在Windows上）结构化异常（SEH），默认情况下googletest会捕获该异常，将其报告为测试失败，然后继续执行下一个测试方法。这使得可以尽可能多的运行测试。另外，在Windows上，未捕获的异常将导致弹出窗口，因此捕获异常可以自动运行测试。

在调试测试失败时，可能希望调试器处理异常，以便可以在引发异常时检查调用堆栈。 为此，请将`GTEST_CATCH_EXCEPTIONS`环境变量设置为`0`，或在运行测试时使用`--gtest_catch_exceptions = 0`标志。

## 相关参考



[GoogleTest User’s Guide | GoogleTest](https://google.github.io/googletest/)

[A quick introduction to the Google C++ Testing Framework – IBM Developer](https://developer.ibm.com/technologies/systems/articles/au-googletestingframework/)

[GoogleTest: C++ unit test framework (yolinux.com)](http://www.yolinux.com/TUTORIALS/Cpp-GoogleTest.html)





[GTEST做C++单元测试初级教程（GTEST Prime译文） - 简书 (jianshu.com)](https://www.jianshu.com/p/c7c702c0abb9)

[使用GTEST编写C++测试用例进阶教程（GTEST advanced中文译文） - 简书 (jianshu.com)](https://www.jianshu.com/p/215edbfc2e0a)

[玩转Google开源C++单元测试框架Google Test系列(gtest)(总) - CoderZh - 博客园 (cnblogs.com)](https://www.cnblogs.com/coderzh/archive/2009/04/06/1426755.html)

[Google Test(gtest)使用 | blueyi's notes (maxwi.com)](http://notes.maxwi.com/2017/11/29/gtest/)

[google/googletest: GoogleTest - Google Testing and Mocking Framework (github.com)](https://github.com/google/googletest)