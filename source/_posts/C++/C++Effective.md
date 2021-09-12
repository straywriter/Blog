---
title: Effective C++ 读书总结
top: false
mathjax: true
categories:
- C++
---

-----



# 1 让自己习惯 C++

## 条款 01：视 C++ 为一个语言联邦

C++ 可以视为一个由四个次语言（sublanguage）组成的联邦而非单一语言：

- C。说到底 C++ 仍是以 C 为基础。
- Object-Oriented C++。这部分也就是 C with Classes 所诉求的：classes、封装（encapsulation）、继承（inheritance）、多态（polymorphism）、virtual 函数（动态绑定）。
- Template C++。这是 C++ 的泛型编程（generic programming）部分。Templates 带来了崭新的编程泛型（programming paradigm），即 template metaprogramming（TMP，模板元编程）。这部分带来的是template metaprogramming，也就是所谓的模板元编程。
- STL。STL 是个 template 程序库，它对容器（containers）、迭代器（iterators）、算法（algorithms）以及函数对象（function objects）的规约有极佳的紧密配合与协调。STL是个template程序库。它对容器、迭代器、算法及函数对象的规约，并且是以templates及程序库的方式构建出来。

**c++ 高效编程守则视状况而变化，取决于你使用 C++ 的哪一部分。** 

> 对内置(也就是C-like)类型而言pass-by-value通常比pass-by-reference高效，但当你从C part of C++移往Object-Oriented C++，由于用户自定义(user-defined)构造函数和析构函数的存在，pass-by-reference-to-const往往更好。运用Template C++时尤其如此，因此此时你甚至不知道所处理的对象的类型。

## 条款 02：尽量以 const，enum，inline 替换 #define

本条款可改为“以编译器替换预处理器”，因为 #define 不被视为语言的一部分。

当有如下宏定义：

```cpp
#define ASPECT_RATIO 1.653
```

> 记号名称 ASPECT_RATIO 并未被编译器看见，于是 ASPECT_RATIO 没有进入记号表（symbol table）内。当出现编译错误信息时，也许只出现 1.653 而非 ASPECT_RATIO。
>

可用常量替换宏：

```
const double AspectRatio = 1.653;
```

> 使用常量还可能可以产生更少的码，因为预处理器盲目的将宏进行替换可能会导致目标码（object code）出现多份。
>

**两种情况值得注意：**

1. 指针需定义为常量指针（constant pointers）。如：`const char* const authorName = "Scott Meyers"`，需有两个 const。
2. 对于 class 专属常量，需将其定义为静态成员常量，确保常量最多只有一份。

-----

“the enum hack”：一个属于枚举类型的数值可当作整型值使用。

enum hack 的某些行为比 const 更为像 #define 一些，如取一个 const 的地址是合法的，而取 enum 的地址是不合法的。当编译器不允许静态整型作为数组大小的声明时，可以用 the enum hack：

```cpp
class GamePlayer {
 private:
  // static const int NumTurns = 5;
  enum { NumTurns = 5 };
  int scores[NumTurns];
}
```

另一个常见 #define 误用的情况是用它来实现宏（macros）。宏看起来像函数，但却不有函数调用的开销。这种宏有太多缺点，非常容易出错，可用模板代替之：

```cpp
/* 以 a 和 b 的较大值调用 f */
#define CALL_WITH_MAX(a, b) f((a) > (b) ? (a) : (b))

template<typename T>
inline void callWithMax(const T& a, const T& b) {
  f(a > b ? a : b);
}
```

## 条款 03：尽可能使用 const

- const 最具威力的用法是在函数声明中的应用。
- 在函数声明中，const 可以与返回值、各参数、函数自身产生关联。
- 令函数返回一个常量值，可以降低因客户错误而造成的意外，而又不至于放弃安全性和高效性。例如：


```cpp
class Rational { ... };
const Rational operator*(const Rational& lhs, const Rational& rhs);
```

将返回值设为 const 可以防止这样的行为：

```cpp
Rational a, b, c;
...
(a * b) = c;	// 因为是自定义类型，所以等号相当于调用成员函数，而非对右值进行赋值，所以不会报错
```

以上错误行为容易在 if 语句中出现：`if (a * b = c)`。

-----

**const 成员函数**

将 const 用于成员函数上可以：

1. 使 class 接口容易被理解，得知哪个函数可以改动对象内容而哪个函数不行。
2. 使“操作 const 对象”成为可能。

------

**在 const 和 non-const 成员函数中避免重复**

对于 const 和 non-const 成员函数，其内部的代码可能是完全重复的，可以用 const 版本的去调用 non-const 版本的成员函数来避免重复：

```cpp
class TextBlock {
 private:
  std::string text;

 public:
  const char& operator[](std::size_t position) const {
    ... // 边界检查
    ... // 日志记录
    ... // 数据完整性检查
    return text[position];
  }

  char& operator[](std::size_t position) {
    return const_cast<char&>(static_cast<const TextBlock&>(*this)[position]);
  }
};
```

如上，non-const 版本的成员函数进行了两次转型操作来调用 const 版本的成员函数，避免了代码重复。

## 条款 04：确定对象被使用前已被初始化

- 对于 C++ 初始化的规则，记忆起来比较繁琐，所以最佳处理办法是：永远在使用对象之前将它初始化。

- 对于无任何成员的内置类型，必须手工完成此事：

```cpp
int x = 0;
const char* text = "A C-style string";

double d;
std::cin >> d; // 以读取 input stream 的方式完成初始化
```

- 至于内置类型以外的东西，需要在构造函数内初始化，确保每一个构造函数都将对象的每一个成员初始化。

- 需要区分赋值和初始化，通常，对象的成员变量的初始化动作发生在进入构造函数本体之前，所以在构造函数内部的动作都是赋值而非初始化。因此，应该使用成员初始化列表的方法。对于内置类型如 int，其初始化和赋值的成本相同，但为了一致性，最好也通过初始化列表进行初始化。

- 内置类型如果没有在初始化列表里进行初始化，它就没有初值（因为如果没有在初始化列表中显式指定的话，编译器默认为成员变量调用默认构造函数，而对于如 int 这样的内置类型，是没有默认构造函数的）。且 const 或 references 的成员变量必须要给定初值，不能进行赋值。所以为了避免需要记住哪些变量需要在成员列表中初始化，统一将所有成员都在初始化列表中进行初始化是有必要的。

- 有一种初始化次序需要注意：不同编译单元内定义之 non-local static 对象的初始化次序。

- 所谓 static 对象，其寿命从被构造出来直到程序结束为止。函数内的 static 对象为 local static 对象，其他 static 对象为 non-local static 对象。而编译单元指的是产生单一目标文件的那些源码。

# 2 构造/析构/赋值运算

## 条款 05：了解 C++ 默默编写并调用哪些函数

- 如果写一个空类，那么编译器会自动为其添加 default 构造函数、copy 构造函数、copy assignment 操作符和一个析构函数，这些函数都是 public 且 inline 的。

- 编译器产生的析构函数是 non-virtual 的，除非这个类的基类自身声明了 virtual 析构函数。（关于移动：只有一个类没有定义任何自己版本的拷贝控制成员，且它的所有数据成员都能够移动构造或移动赋值时，编译器才会为其合成移动构造或移动赋值）

- 有些情况，如：类中含有 reference 成员或 const 成员，则编译器不会 copy 和 copy assignment 函数（reference 和 const 都无法被更改，所以自然不会重新赋值），若基类的 copy assignment 为 private，则子类也不会生成 copy assignment。

## 条款 06：若不想使用编译器自动生成的函数，就该明确拒绝

- 有些类，不想让它可以拷贝，可以将其 copy 构造或 copy assignment 变为 private 函数并不实现它，这样如果还有东西（友元）可以调用 copy 或 copy assignment 的话，就会在链接阶段出现 linkage error。


可以将错误移至编译阶段：

```cpp
class Uncopyable {
 protected:  // 允许 derived 对象构造和析构
  Uncopyable() {}
  ~Uncopyable() {}

 private:  // 阻止 copy
  Uncopyable(const Uncopyable&);
  Uncopyable& operator=(const Uncopyable&);
};

class HomeForSale : private Uncopyable {};
```

这时，任何想要拷贝 HomeForSale 对象的操作就会使编译器尝试生成 copy 和 copy assignment，从而会尝试调用基类的 copy 和 copy assignment，从而编译失败。

## 条款 07：为多态基类声明 virtual 析构函数

- 若派生类对象经由基类指针被删除，而基类带着 non-virtual 析构函数，则其行为未定义——实际上就是派生类成分没被销毁，从而造成内存泄漏。


- 不能无端的将所有类的析构函数声明为 virtual，因为这回导致类大小的膨胀（多了一个 vptr），也不能再和其他语言（C）交互了。一般来说，只有当类中至少有一个 virtual 函数时，才声明 virtual 析构函数。


- 如果类中有 pure virtual 函数，则该类为抽象类，无法被实例化。如果想要一个抽象类，但手上又没有任何 pure virtual 函数，则可将析构函数声明为 pure virtual。但必须为这个析构函数提供一份定义，因为派生类中的析构函数会有一个对基类析构函数的调用，若没有对其定义，则链接器会报错。


- 若设计基类的目的不是为了多态（比如 STL 中的 string、input_iterator_tag 等），则无需 virtual 析构函数。


## 条款 08：别让异常逃离析构函数

- 析构函数中不应该抛出异常，比如在一个 vector 中存在十个 widget，在第一个 widget 析构时有个异常被抛出，而其它九个依然应该被销毁，但第二个 widget 又抛出异常，那么就会存在两个异常，此时将导致不明确行为。


- 如果某个操作需要抛出异常，并需要客户来应付异常，应该将这个操作放在一个普通函数中，供客户使用，而不应该放在析构函数中。并且如果析构函数调用的函数会抛出异常，析构函数因该捕捉任何异常，并吞下他们或结束程序（不让它再传播或不让其产生不明确的行为）。


考虑一个连接数据库的类：

```cpp
class DBConn {
 public:
  // ...
  void close() {  // 共客户使用
    db.close();
    closed = true;
  }
  ~DBConn() {
    if (!closed) {
      try {  // 如果客户没有关闭连接，则析构函数代为关闭
        db.close();
      } catch (...) {
        // 如果关闭失败，则记录下来并结束程序，或吞下异常
      }
    }
  }

 private:
  DBConnection db;
  bool closed;
}
```

我们重新封装了 close 函数（让异常在这个函数中抛出，而非析构函数），可由客户调用，来关闭连接，此时若抛出异常，可以让客户自己去处理。如果客户没有主动关闭连接，则析构函数可代为其关闭，此时的析构函数因该吞下异常或记录异常并结束程序。

## 条款 09：绝不在构造和析构过程中调用 virtual 函数

假设需要一个 class 继承体系，来塑模股市交易，并需要记录日志：

```cpp
class Transaction {  // 所有交易类型的基类
 public:
  Transaction();
  virtual void logTransaction() const = 0;  // 一个因类型不同而不同的日志记录
};

Transaction::Transaction() {
  // ...
  logTransaction();  // 记录这笔交易
}

class BuyTransaction : public Transaction {
 public:
  virtual void logTransaction() const;  // 记录此类型交易
                                        // ...
};

class SellTransaction : public Transaction {
 public:
  virtual void logTransaction() const;  // 记录此类型交易
                                        // ...
};
```

此时，若执行：`BuyTransaction b;` ，则 BuyTransaction 构造函数被调用，但首先还是会调用 Transaction 的构造函数，这时，构造函数内的 logTransaction 调用的是基类的版本，而非子类，基类构造期间虚函数绝不会下降到派生类阶层。此时派生类还未构造，派生类中的变量还未定义，对象的类型依然为基类，即使使用运行时类型信息，也会把对象视为基类。同样的道理适用于析构函数，一旦派生类的析构函数执行，则派生类中的成员变量便呈现出未定义值，C++ 将视派生类不存在，在基类析构函数中，C++ 将完全视其为基类。

可以将虚函数转为非虚函数来解决这一问题，然后让派生类的构造函数传递必要的信息给基类构造函数：

```cpp
class Transaction {  // 所有交易类型的基类
 public:
  explicit Transaction(const string& logInfo);
  void logTransaction(const string& logInfo) const;  // 非虚函数
};

Transaction::Transaction(const string& logInfo) {
  // ...
  logTransaction(logInfo);  // 记录这笔交易
}

class BuyTransaction : public Transaction {
 public:
  void logTransaction(parameters)
      : Transaction(createLogString(parameters)) const;  // 记录此类型交易
  // ...
 private:
  static string createLogString(parameters);
};
```

借由派生类将必要的构造信息向上传递至基类构造函数，派生类中的 createLogString 用来构造一个值传给基类，此函数为 static，这样可以防止意外使用 BuyTransaction 对象内还未初始化的变量。

## 条款 10：令 operator= 返回一个 reference to *this

对所有的赋值相关运算（=、+=、-=），有如下形式：

```cpp
Widget& operator=(const Widget& rhs) {
  // ...
  return *this
}
```

这样即可实现连续赋值操作：`x = y = z = 15;`。

## 条款 11：在 operator= 中处理“自我赋值”

经常会有潜在的自我赋值，如：`a[i] = a[j];` 。考虑如下的类：

```cpp
class Widget {
  // ...
 private:
  Bitmap* pb;
};

Widget& Widget::operator=(const Widget& rhs) {
  delete pb;
  pb = new Bitmap(*rhs.pb);
  return *this;
}
```

此时若自我赋值，则 rhs 中的 pb 将会被销毁，赋值将会失败。

可用“证同测试”进行检测：

```cpp
Widget& Widget::operator=(const Widget& rhs) {
  if (this == &rhs) return *this;
  
  delete pb;
  pb = new Bitmap(*rhs.pb);
  return *this;
}
```

也可安排一下语句顺序，这样在保证异常安全的同时也自动获得了自我赋值安全：

```cpp
Widget& Widget::operator=(const Widget& rhs) {
  Bitmap* pOrig = pb;
  pb = new Bitmap(*rhs.pb);
  delete pOrig;
  return *this;
}
```

若 new Bitmap 抛出异常，则 pb 任然保持原状，且能够处理自我赋值。

也可使用 copy and swap 技术：

```cpp
Widget& Widget::operator=(const Widget& rhs) {
  Widget temp(rhs);
  swap(temp); // 需要在 Widget 中定义这个 swap 方法
  return *this;
}
```

也可以利用 by value 方式接收实参：

```cpp
Widget& Widget::operator=(Widget rhs) { // rhs 是被传对象的一个副本
  swap(temp); // 需要在 Widget 中定义这个 swap 方法
  return *this;
}
```

但这种方法牺牲了清晰性，不过有时可以令编译器产生更高效的代码。

## 条款 12：赋值对象时勿忘其每一个成分

- 当编写 copying 函数（copy 构造和 copy assignment 操作符）时，要确保：

  (1) 复制所有的 local 成员变量，

  (2) 调用所有基类中适当的 copying 函数。

- 若派生类 copy 构造没有指定实参给基类的构造函数（没有主动调用基类构造函数），则基类的默认构造函数会被调用。而 copy assignment 则不会尝试调用默认 copy assignment，所以基类的成员变量保持不变。

- 当两个 copying 函数的实现很相似时，你可能会尝试用一个去调用另一个，避免代码重复，但这无法达到目标。用 copy assignment 调用 copy 构造函数是不合理的，这相当于试图构造一个已经存在的对象。

  令 copy 构造函数调用 copy assignment 操作符同样无意义，构造函数用来初始化新对象，而 assignment 操作符只施行于已初始化的对象上，对一个尚未构造好的对象赋值，就像在一个尚未初始化的对象身上做“只对已初始化对象才有意义”的事一样。这两种行为都别尝试！

- 若发现 copying 函数代码重复，正确的做法是建立一个新成员函数给两者调用，这样的函数往往是 private 并命名为 init。


# 3 资源管理

## 条款 13：以对象管理资源

为确保申请的资源被释放，因该将资源放进对象当中，依赖 C++ 的“析构函数自动调用机制”确保资源被释放。

- **获得资源后立刻放进管理对象内，如放进智能指针中。** 这种观念被称为“Resource Acquisition Is Initialization; RAII”。
- **管理对象运用析构函数确保资源被释放。** 无论控制流如何离开区块，其析构函数都会被自动调用，从而释放资源，若析构函数会抛出异常，参考条款 8。

## 条款 14：在资源管理类中小心 copying 行为

当一个 RAII 对象被复制，可选择一下几种方式：

- **禁止复制。** 有时 RAII 对象被复制并不合理，此时应该禁用之。
- **对底层资源使用“引用计数法”（reference-count）。** 如 shared_ptr，保有资源，直到最后一个使用者被销毁。
- **复制底部资源。** 在复制资源管理对象的同时也应该复制其包含的资源，也就是“深度拷贝”，比如标准库的 string。
- **转移底部资源的拥有权。** 某些情况下，希望确保永远只有一个 RAII 对象指向一个 raw resource，即使 RAII 对象被复制依然如此，这时可将资源的拥有权从被复制物转移到目标物，如 auto_ptr。

> 对于 copying 函数，除非编译器产生的版本做了你想做的事，否则需要自己去编写。
>

## 条款 15：在资源管理类中提供对原始资源的访问

有时候一个 API 可能需要 RAII class 所管理的原始对象作为参数，所以 RAII class 应该提供一个“取得其所管理之资源”的办法，可用显示转换或隐式转换达成目标。

智能指针都会提供一个 get 成员函数来获取其内部的原始指针，对于自己建立的 RAII 对象，也可使用这种方式，设置一个 get 成员函数来提供一个显示转换。这种方式比较安全，但可能会让人觉得有些麻烦，所以另一个方法是提供隐式转换函数：

```cpp
class Font {
 public:
  // ...
  operator FontHandle() const { // 隐式转换函数
  	return f;
	}
  // ...
 private:
   FontHandle f;
};
```

然而，隐式转换会大大增加错误发生的机会，大部分情况下还是应该坚持“让接口容易被正确使用，不易被误用”的条款。

## 条款 16：成对使用 new 和 delete 时要采取相同形式

- 单一对象的内存布局一般而言不同于数组的内存布局，数组所用的内存还包括“数组大小”的记录，以便 delete 知道需要调用多少次析构函数。


- 如果在 new 表达式中使用 []，必须在相应的 delete 表达式中也使用 []，否则，行为是未定义的，可能导致很多析构函数未被调用，若是内置类型如 int，虽然没有析构函数，但任然会出现未定义的行为。如果 new 中不使用 []，也一定不能在 delete 表达式中使用 []，否则，可能会导致 delete 读取若干内存并将其解释为“数组大小”，然后开始多次调用析构函数。


容易出错的地方在于用 typedef 定义数组：

```cpp
typedef std::string AddressLine[4]; // 每个人的地址有 4 行
std::string* pal = new AddressLines; // 相当于使用 new string[4]
delete pal; // 行为未定义！
delete [] pal; // 正确
```

因尽量避免对数组形式做 typedef 动作。

## 条款 17：以独立语句将 newed 对象置入智能指针

考虑如下两个函数：

```cpp
int priority();
void processWidget(std::shared_ptr<Widget> pw, int priority);
```

priority 用来揭示处理程序的优先权，processWidget 用来在动态分配所得的 Widget 上进行带有优先权的处理。此时若进行如下调用：

```cpp
// processWidget(new Widget, priority());  错误，shared_ptr 的构造函数为 explicit，不能缉将内置指针隐式转换为 shared_ptr
processWidget(std::shared_ptr<Widget>(new Widget), priority());
```

则可能出现资源泄露，即使我们使用了“对象管理式资源”。

在 processWidget 调用之前，编译器会做下面三件事：

- 调用 priority
- 执行“new Widget”
- 调用 std::sharedp_ptr 构造函数

因为这三个操作在同一个语句中进行，所以这三件事的顺序有很大的弹性，可能会有如下顺序：

1. 执行“new Widget”
2. 调用 priority
3. 调用 std::shared_ptr 构造函数

此时，若 priority 的调用导致了异常，那么 new Widget 返回的指针将会遗失，因为它还未被装入 shared_ptr 当中。所以在“资源被创建”和“资源被转换为资源管理对象”两个时间点之间可能发生异常干扰而导致内存泄漏。

**可以用独立的语句将 new 出的对象存储于智能指针内来避免这一问题：**

```cpp
std::shared_ptr<Widget> pw(new Widget);
processWidget(pw, priority());
```

因为编译器对于“跨越语句的各项操作”没有重新排列的自由。

# 4 设计与声明

## 条款 18：让接口容易被正确使用，不易被误用

- 许多客户端的错误可以 **通过导入新类型而获得预防** 比如一个描述日期的类：


```cpp
class Date {
 public:
  Date(int month, int day, int year);
  // ...
}
```

- 用户很容易将 month、day 或者 year 输错（比如月和日弄反了，或者输入了一个不存在的日期）。可以通过导入简单的外覆类型（wrapper types）来区别日、月、年：


```cpp
struct Day { /* ... */ };
struct Month { /* ... */ };
struct Year { /* ... */ };
/* 上面几个 struct 的构造函数都是 explict 的，避免隐式类型转换 */

class Date {
 public:
  Date(Month month, Day day, Year year);
  // ...
}

/* 必须这样使用 */
Date d(Month(7), Day(26), Year(2020))
```

- 这样就不可能出现诸如日和月的参数弄反的情况了，还可以对类型取值做出一定的限制。


- 另一个预防客户端错误的办法是，**限制类型内什么事可以做，什么事不能做。** 比如加上一个 const，参考条款 3 中的 “以 const 修饰 operator* 的返回类型”来阻止因“用户自定义类型”而犯错：


```cpp
if (a * b = c) // a，b 为自定类型，原意为比较，结果变成调用用户自定的 operator= 操作符了
```

- 另一个一般性准则是**除非有好的理由，否则应该令你的 types 行为与内置 types 一致。** 就比如上面的 if 语句中错误的使用了赋值符号，若是内置类型如 int，则会直接报错，你自己定义的 type 行为应该也是如此。这样做的另一个理由是**提供行为一致的接口。** 比如 STL 容器的接口都非常一致，都有一个名为 size 的成员函数告诉调用者容器内有多少元素，而 Java 和 .NET 的接口则非常混乱。


- **如果接口要求客户必须接的做某些事，就是有着“不正确的使用”的倾向。** 因为客户可能会忘记做这件事，比如获取资源后必须要求客户记得释放资源。可用 shared_ptr 来定制删除器，从而消除客户的资源管理责任。

## 条款 19：设计 class 犹如设计 type

定义一个新 class 就是定义一个新 type，这意味着你不仅是 class 的设计者，还是 type 设计者，因该带着和“语言设计者当初设计语言内置类型时”一样的谨慎来研讨 class 的设计。每个 class 都要求你面对一下提问：

- 新 type 的对象应该如何被创建和销毁？（构造、析构、new、delete）
- 对象的初始化和对象的赋值该有什么差别？（构造、赋值）
- 新 type 的对象如果被 passed by value，意味着什么？（拷贝构造）
- 什么是新 type 的“合法值”？（约束条件、错误检查、抛出异常）
- 你的新 type 需要配合某个继承图系（inheritance graph）吗？（如：virtual 还是 non-virtual）
- 你的新 type 需要什么样的转换？（explicit 或 non-explicit 构造、类型转换操作符、专门的类型转换函数）
- 什么样的操作符和函数对此新 type 而言是合理的？（成员函数）
- 什么样的标准函数应该驳回？（这些函数应声明为 private 或 delete）
- 谁该取用新 type 的成员？（public、protected、private、friends？）
- 什么是新 type 的“未声明接口”？（做出何种异常安全性或线程安全的保证？）
- 你的新 type 有多么一般化？（是否应该定义为 class template？）
- 你真的需要一个新 type 吗？（若只是简单的继承来添加新功能，可能还不如单纯的定义一些 non-member 函数或 templates）

## 条款 20：宁以 pass-by-reference-to-const 替换 pass-by-value

传递参数时，若传递值（pass-by-value），则这个类的 copy 构造函数会被调用，并且这个类中的成员也会被一个个的进行拷贝，在函数执行完毕后，该参数又要被销毁，其析构函数会被调用，并且类中的成员的析构函数也会一个个调用，代价非常高昂。因此，正确的操作应该是 pass by reference-to-const，首先，传递引用不会引起构造和析构，其次，const 限制了其行为，让函数不会对原变量进行修改。

以引用方式传参还可以避免 slicing（对象切割）问题。若以值传递参数，当一个函数的参数是一个基类，而传递给其的对象是个子类对象，则其子类的部分会被切割掉，仅留下基类的部分，虚函数的调用都将调用基类的版本。而引用传参的方式即可避免这类问题。

C++ 的底层中，reference 往往以指针实现，因此 pass by reference 通常意味着传递的是指针。因此对于内置类型，如 int，pass by value 往往效率更高一些。对 STL 的迭代器和函数对象也是如此。

## 条款 21：必须返回对象时，别妄想返回其 reference

- 当你必须在“返回一个 reference 和返回一个 object”之间抉择时，你的工作是挑出行为正确的那个，其余的优化交给编译器厂商做吧。

- 绝不要返回 pointer 或 reference 指向一个 local stack 对象，或返回 reference 指向一个 heap-allocated 对象，或返回 pointer 或 reference 指向一个 local static 对象。

## 条款 22：将成员变量声明为 private

**语法一致性：** 如果成员变量不是 public，客户唯一能够访问对象的办法就是通过成员函数。如果 public 接口内的每样东西都是函数，客户就无需在访问 class 成员时记住是否该使用小括号了。

**对成员变量有更精确的控制：** 实现出“不准访问”、“只读访问”、“读写访问”，甚至是“只写访问”：

```cpp
class AccessLevels {
 public:
  int getReadOnly() const { return readOnly; }
  void setReadWrite(int value) { readWrite = value; }
  int getReadWrite() { return readWrite; }
  void setWriteOnly(int value) { writeOnly = value; }

 private:
  int noAccess;
  int readOnly;
  int readWrite;
  int writeOnly;
};
```

**封装：** 将成员变量隐藏在函数接口的背后，可以为实现提供弹性，如使得成员变量修改时通知其他对象、验证约束条件、提供多线程的同步控制等等。若对客户隐藏成员变量（封装它们），可以确保 class 的约束条件总是会获得维护，还可以保留日后更改实现的权力。

> 以上三点对 protected 数据同样适用。假设有一个 public 成员变量，而我们最终取消了它，那么所有使用它的客户代码都会被破坏；而如果有一个 protected 成员变量，我们最终取消了它，那么所有使用它的派生类都将被破坏。
>

## 条款 23：宁以 non-member、non-friend 替换 member 函数

有如下表示网页浏览器的类：

```cpp
class WebBrowser {
 public:
  void clearCache();
  void clearHistory();
  void removeCookies();

  // 第一种方式，在类中提供一个函数
  void clearEverything();
};

// 第二种方式，以 non-member 函数的方式提供这样的功能
void clearBrowser(WebBrowser& wb) {
  wb.clearCache();
  wb.clearHistory();
  wb.removeCookies();
}
```

- 可以看到上面有两种方式实现清理浏览器的功能，面向对象的守则要求，数据以及操作数据的函数应该被绑在一块，这意味着以 member 函数方式提供这个功能比较好，但这是基于对面向对象真实意义的一个误解。面向对象的守则要求数据应该尽可能的被封装，然而与直观相反地，member 函数 clearEverything() 带来的封装性比 Non-member 函数低。


- 如果某些东西被封装，它就不在可见，越多东西被封装，越少人可以看到它，越少人看到它，我们就有越大的弹性去改变它。对于成员函数 clearEverything 和非成员函数 clearBrowser 来说，它们提供相同的功能，非成员函数的封装性较大，因为它并不增加“能够访问类中 private 成分”的函数数量，也因此它导致 WebBrowser 类具有更大的封装性。


- 当然，也可以令 clearBrowser 成为某个工具类的静态成员函数，这样也不影响 WebBrowser 的封装性。但 C++ 中，比较自然的做法是让其 clearBrowser 成为一个普通函数，并且将其放在和 WebBrowser 相同的命名空间内。因为 namespace 可以跨越多个源码文件，而 class 则不能，一个像 WebBrowser 这样的类，可能拥有大量的便利函数（比如与书签相关的、与打印相关的、与 cookies 相关的），而客户可能只用到一小部分，于是，我们可以将不同类型的便利函数放在不同的头文件中，只要它们都在相同的 namespace 中即可。这也正是 C++ 标准程序库的组织方式（有 vector、algorithm 等数十个头文件，都在 std 的命名空间中）。


- 将所有便利函数放在多个头文件中但隶属于一个命名空间，还意味着客户可以轻松扩展这一组便利函数。比如现在客户想为 WebBrowser 开发另一组便利函数（比如与下载相关的），只需要在相同的命名空间中再添加一个头文件即可。这也是 class 无法做到的，class 的定义客户是无法扩展的（当然也可以对类进行派生，但如此扩展拥有的只是次级身份）。


## 条款 24：若所有参数皆需类型转换，请为此采用 non-member 函数

考虑如下的有理数类：

```cpp
class Rational {
 public:
  Rational(int numerator = 0, int denominator = 1);
  int numerator() const;
  int denominator() const;

 private:
  // ...
};
```

若想支持算数运算，可供选择的有：member 函数、non-member 函数或 non-member friend 函数来实现。

若选择 member 函数：

```cpp
class Rational {
 public:
  // ...
  const Rational operator*(const Rational& rhs) const;
};
```

则，可以在两个有理数间相乘，但若尝试混合运算：

```cpp
Rational oneHalf(1, 2);
Rational result = oneHalf * 2; 	// OK!
result = 2 * oneHalf;			// Wrong!
```

转换一下上面的式子即可发现问题所在：

```cpp
result = oneHalf.operator*(2);	// OK!
result = 2.operator*(oneHalf);	// Wrong!
```

很显然，2 没有相应的 class 自然也就没有 operator* 成员函数。此时编译器会尝试寻找 non-member operator*：

```cpp
result = operator*(2, oneHalf);	// Wrong!
```

可惜并不存在。

实际上，上述成功的调用发生了隐式类型转换，编译器将 2 转换成了 Rational 并传入了成员函数 operator* 中。若将 Rational 的构造函数声明为 explicit，则调用也会失败。而且只有参数列中的参数才能参与隐式类型转换，成员函数 operator* 中的另一个隐式的成员 this，无法参与隐式类型转换。 所以第二种调用方式会失败。

想要支持混合运算，最好的方法是将 operator* 变为 non-member 函数：

```cpp
const Rational operator*(const Rational& lhs, const Rational& rhs) {
  return Rational(lhs.numerator() * rhs.numerator(), 
                  lhs.denominator() * rhs.denominator());
}
```

这样就支持混合运算了，那么该函数是否应该成为 Rational 的 friend 呢？就本例而言答案是否定的，因为它完全可以利用 Rational 的 public 接口完成工作，所以不应该降低其封装性。

## 条款 25：考虑写出一个不抛异常的 swap 函数

如果 swap 的缺省实现码对你的 class 或 class template 提供可接受的效率，则不需要做任何事，若 swap 缺省实现版的效率不足（意味着你使用了某种 pimpl 手法）：

1. 提供一个 public swap 成员函数，让其高效的置换对象，并且该成员函数绝不该抛出异常。
2. 在你的 class 或 template 所在的命名空间内提供一个 non-member swap，并令它调用上述 swap 成员函数。
3. 如果编写的是个 class 而非 class template，则为你的 class 特化 std::swap，并令其调用你的 swap 成员函数。
4. 如果调用 swap，使用 using 声明式（在函数内使用 `using std::swap;`），以便让 std::swap 在你的函数内曝光可见，然后赤裸裸的调用 swap。

解释一下最后一点：

```cpp
template template<typename T>
void doSomething(T& obj1, T& obj2) {
  using std::swap;  // 令 std::swap 在函数内可用
  swap(obj1, obj2); // 为 T 型对象调用最佳 swap 版本
}
```

上面的代码中，一旦编译器看到 swap 的调用，会查找最适当的函数。C++ 的规则首先确保找到 global 作用域或 T 所在命名空间内的任何 T 专属的 swap，若没有 T 专属之 swap，则会使用 std 内的 swap（因为我们用了 using 声明式让其可见了），若对你对 std::swap 进行了对 T 的特化，则会优先调用特化版本的 swap。

注意，调用 swap 时，不应该使用任何 namespace 修饰符进行调用（如使用 `std::swap();`），否则会强迫编译器只认 std 内的 swap（编译器看不到和 T 在同一命名空间内的，更适合的版本）。

最后，成员版本的 swap 绝不可抛出异常。因为高效的 swap 几乎总是对于内置类型的操作（如 pimpl 是对指针操作），**而内置类型上的操作绝不会抛出异常。** 这一约束只针对成员版本，不必施行于非成员版本，因为 swap 缺省版本时允许抛出异常的（以 copy 构造函数和 copy assignment 操作符为基础）。





# 5 实现

## 条款 26：尽可能延后变量定义式的出现时间

有时，创建对象后，若使用该对象前，程序提前退出了（异常、提前返回等等），则会多余的承担了该对象的一次构造和析构的成本。并且，不止应该延后变量的定义，直到非得使用该变量前一刻为止，甚至应该改尝试延后这份定义知道能够给他初值实参为止，这样可以避免无意义的 default 构造行为（构造加赋值，合并为一次构造即可）。

遇到循环：

```cpp
// 第一种
Widget w;
for (int i = 0; i < n; ++i) {
  w = ...;
}

// 第二种
for (int i = 0; i < n; ++i) {
  Widget w(...);
}
```

两种写法的成本：

- 第一种：1 个构造函数 + 1 个析构函数 + n 个赋值操作
- 第二种：n 个构造函数 + n 个析构函数

除非你知道赋值成本比“析构 + 构造”的成本低，并且对效率高度敏感，否则应该采用第二种做法，因为第一种做法导致 w 的作用域更大，对可理解性和易维护性造成了冲击。

## 条款 27：尽量少做转型动作

应使用 C++ 风格的转型，不要使用旧式的转型，前者容易辨认且各司其职，C++ 的四种转型：

- const_cast 用来将对象的常量性移除。
- dynamic_cast 执行“安全向下转型”，是唯一无法由旧式语法执行的动作，且可能耗费重大运行成本。
- reinterpret_cast 执行低级转型，实际动作和结果取决于编译器，这表示了其不可移植性。
- static_cast 强迫**隐式转换** ，无法将 const 转为 non-const（反之可以）。

转型并非只是告诉编译器把某种类型视为另一种类型，什么都没做，如：

```cpp
class Base { ... };
class Derived : public Base { ... };
Derived d;
Base* pb = &d;
```

这里只是建立一个 base class 指针指向 derived class 对象。这种情况下会有个偏移量在运行期间施行于 Derived* 指针上，用以取得正确的 Base* 指针值。而对象的布局方式随编译器的不同而不同，如果刻意针对某种对象布局而设计转型，那么会导致移到另一平台就行不通了。

假设有一个 Window 基类和一个 SpecialWindow 派生类，两者都定义了 virtual 函数 onResize，并且 SpecialWindow 的 onResize 函数要求首先调用 Window 的 onResize：

```cpp
class Window {
 public:
  virtual void onResize() { ... }
};

class SpecialWindow : public Window {
 public:
  virtual void onResize() {
    static_cast<Window>(*this).onResize();
    ...
  }
};
```

这段代码确实将 *this 转变为了 Window，且调用了 Window::onResize，然而，调用并非当前对象上的函数，而是进行转型动作时所建立的一个“this 对象之 base class 成分”的暂时副本（成员函数只有一份，关键在于隐含的 this 指针不同，因此影响成员函数操作的数据）。所以实际上若 Window::onResize 修改了对象内容，则当前对象其实并没有被改变。

所以若想调用 base class 版本的虚函数，正确做法是：

```cpp
class SpecialWindow : public Window {
 public:
  virtual void onResize() {
    Window::onResize();
    ...
  }
};
```

在注重效率的代码中应避免 dynamic_casts，应试着发展无需转型的代替设计（使用类型安全容器或将 virtual 函数往继承体系上方移动）。

若转型是必要的，应该将其隐藏于某个函数背后，客户可调用该函数而无需将转型放进自己的代码中。

## 条款 28：避免返回 handles 指向对象内部成分

成员变量的封装性最多只等于“返回其 reference”的函数的访问级别，也就是说，虽然成员变量是 private 的，但你提供了一个 public 接口，返回这个成员变量的 reference，那么这个成员变量的实际访问级别就会提高到 public（你可以通过这个 public 接口去修改这个 private 成员变量）。当这个 public 成员函数是 const 成员函数时，问题更大，因为你的本意是不让用户修改这个对象，但返回的 reference 却指向 private 数据，调用者可通过这个 reference 修改内部数据。

将成员函数的返回改为 const reference 可以解决部分问题（不可以通过返回的 reference 修改对象数据了，只能读取），但依然可能造成 dangling handles（空悬号码牌）问题。比如有时会调用一个零时对象的成员函数（该成员函数返回一个 const reference 指向内部成员），取其返回的成员变量的地址并将其赋值给一个指针，这时临时变量将被销毁，其内部的成员也将被销毁，这时这个指针就变为了空悬指针了。

所以，应该避免返回 handles（包括 reference、指针、迭代器）指向对象内部。

## 条款 29：为“异常安全”而努力是值得的

“异常安全”有两个条件：

- **不泄露任何资源。**
- **不允许数据败坏。**

资源泄漏可以通过导入“资源管理类”来解决（参考条款 13 和条款 14）。

异常安全函数提供一下三个保证之一：

- 基本承诺：如果异常被抛出，程序内的任何事物任然保持在有效状态下（调用失败保证对象或数据结构不被破坏，如：所有的 class 约束条件都还满足），但程序的现实状态不可预料（可能与调用前不一样）。
- 强烈保证：如果异常被抛出，程序状态不改变（调用失败则恢复到调用之前的状态）。
- 不抛掷（nothrow）保证：承诺绝不抛出异常，总是能够完成它们承诺的功能。

异常安全码（Exception-safe code）必须提供上述三种保证之一，否则不具备异常安全性。

copy and swap 策略可以实现强烈保证：为你打算修改的对象做出一份副本，然后在该副本上做出一切必要修改，再将修改后的副本与原对象在一个不抛出异常的操作中置换（swap）。实现上通常是用 pimpl idiom（条款 31）。

但 copy and swap 一般不能保证整个函数有强烈的异常安全性：

```cpp
void someFunc() {
  ... 	// copy 副本
  f1();
  f2();
  ... 	// 置换
}
```

如果 f1 和 f2 的异常安全性比“强烈保证”低，就很难让 someFunc 成为强烈异常安全。如，假设 f1 提供基本保证，那么若想 someFunc 成为“强烈保证”，则必须保存 f1 调用之前整个程序的状态，然后捕获 f1 的异常，并恢复状态。

若 f1 和 f2 都是“强烈异常安全”，情况也不能好转。比如若 f1 圆满结束，程序状态则可能改变，若 f2 抛出异常，程序状态就与 someFunc 被调用前不同了。

若函数只操作局部状态（如只影响调用者对象的状态），便相对容易提供强烈保证，但若函数对非局部性数据有连带影响（如改动数据库），则很难让函数提供强烈安全性。

## 条款 30：透彻了解 inlining 的里里外外

如果 inline 函数本体很小，编译器针对其所产生的代码可能比针对函数调用所产生的代码更小。否则，这样做会增加目标码的大小。

inline 只是对编译器的申请，而非强制命令。这项申请可以隐喻提出（将函数定义于 class 定义式内），也可明确提出（在函数定义式前加上 inline）。

inline 函数和 templates 两者通常都定义于头文件中，但两者之间并没有关系。inline 函数之所以被放在头文件中，是因为它是在编译过程中进行 inlining 的，为了将函数调用替换为函数本体，编译器必须知道函数长什么样（如果定义于其他地方则需要链接器的介入了）。而 template 也是同样的道理，它一旦被使用，编译器必须将其具现化，也必须知道它长什么样。

大部分编译器会拒绝太过复杂（如带有循环或递归）的函数 inlining，所有对 virtual 函数的调用都会被拒绝 inlining（virtual 函数在运行期才能确定）。有时候编译器虽然会 inlining 某个函数，但也会为函数生成一个本体，比如程序若需要某个 inline 函数的指针，则编译器必须为此函数生成一个函数本体。而通过函数指针调用的 inline 函数，则可能被 inline，也可能不会。

构造函数通常不应该被设为 inline，有时一个空的构造函数看起来很适合变为 inline，实际上编译器可能会为这个空的构造函数生成很多代码（调用基类构造函数、成员变量构造函数，若这些函数依然为 inline，则代码会急剧膨胀）。

最后，若函数声明为 inline，后期若对函数进行修改，则所有调用该函数的代码都需要重新编译。所以，不要盲目的将函数声明为 inline，首先应该将 inline 施行范围限定在“一定成为 inline”或“十分平淡无奇”的函数上。

## 条款 31：将文件间的编译依存关系降至最低

当对 C++ 程序的某个 class 实现文件做了轻微修改，并没有修改接口，然后重新构建程序，会发现有很多文件都被重新编译和链接了。因为 C++ 没有把“将接口从实现中分离”做的很好，class 的定义式不仅只是详述 class 接口，还包括实现细节：

```cpp
class Person {
 public:
  Person(const std::string& name, const Date& birthday, const Address& addr);
  std::string name() const;
  std::string birthDate() const;
  std::string address() const;
 
 private:
  std::string theName;	// 实现细节
  Date theBirthDate;		// 实现细节
  Address theAddress;		// 实现细节
};
```

这里的 class Person 无法通过编译，因为没有 string、Date、和 Address 的定义式，这些定义通常需要用 #include 包含进来。于是这便形成了一种编译依存关系（compilation dependency）。如果这些头文件或头文件所依赖的其他头文件有任何改变，则每一个含入 Person class 的文件都需要重新编译。

即使使用前置声明也不行，因为编译器需要在编译期间知道对象的大小，所以必须要知道 class 的定义式。

解决方是将 Person 分割为两个 class，一个只提供接口，另一个负责实现该接口：

```cpp
#include <memory>
#include <string>

class PersonImpl;  // Person 实现类的前置声明
class Date;
class Address;

class Person {
 public:
  Person(const std::string& name, const Date& birthday, const Address& addr);
  std::string name() const;
  std::string birthDate() const;
  std::string address() const;

 private:
  // 指针，指向实现物，且标准头文件 shared_ptr 一般是不会改变的
  std::shared_ptr<PersonImpl> pImpl;  
};
```

这里 Person 里面只有一个指针成员，指向其实现类（PersonImpl），这种设计称为 pimpl idiom（pointer to implementation）。这样的设计之下，Person 的客户就完全与 Date，Address 以及 Person 的实现分离了（Person 类中只有一个指针，编译时可以直接知道其大小）。这些 class 的修改都不需要 Person 客户端重新编译了，并且客户无法看到 Person 真实的实现细节，也就不可能写出“取决于那些细节”的代码，这真正是“接口与实现分离”。

这个分离的关键在于以以“声明的依存性”替换“定义的依存性”：让头文件尽可能自我满足，万一做不到，则让它与其他文件内的声明式（而非定义式）相依：

- 如果使用 object reference 或 object pointers 可以完成任务，就不要用 object。可以只靠一个类型声明式就定义出指向该类型的引用或指针，但如果用 object，就需要该类型的定义式。

- 如果能够，尽量以 class 声明式替换 class 定义式。当声明一个函数并用到某个 class 时，并不需要该 class 的定义，即使以 by value 方式传递：

  ```
  class Date;			// class 声明式
  Date today();		// 无需 Date 定义式
  void clearAppointments(Date d);		// 无需 Date 定义式
  ```

  不过一旦有人调用这些函数，则调用之前，Date 定义式要曝光才行。

- 为声明式和定义式提供不同的头文件。如上例，Date 不应该由客户手动前置声明，而是应该由程序库的作者提供两个头文件，一个用于声明式，一个用于定义式，程序库的客户总是 #include 一个声明文件而非前置声明 Date：

  ```
  #include "datefwd.h"		// 内涵声明（未定义）class Date
  Date today();
  void clearAppointments(Date d);
  ```

  正如 C++ 标准程序库头文件的 <iosfwd>，该头文件内涵 iostream 各组件的声明式，其对应的定义则分布在若干不同的头文件中，如 <sstream>、<streambuf>，<iostream> 等，这也就表明对于 template，这种方法也适用。

类似于 Person 这样使用 pimpl idiom 的 class，往往被称为 Handle class，基于此，有两种实现类功能的手段：

第一种是将所有函数转交给相应的实现类（implementation class）并由它们完成实际工作：

```
#include "Person.h" 			// 正在实现 Person
#include "PersonImpl.h"		// 必须包含定义式，否则无法进行函数调用

Person::Person(const std::string& name, const Date& birthday,
               const Address& addr)
    : pImpl(new PersonImpl(name, birthday, addr)) {}

std::string Person::name() const { return pImpl->name(); }
```

另一种方法是令 Person 成为一种特殊的抽象基类，称为 Interface class。这种 class 的目的是详细一一描述派生类的接口，因此通常不带成员变量，也没有构造函数，只有一个 virtual 析构函数：

```
class Person {
 public:
  virtual ~Person();
  virtual std::string name() const = 0;
  virtual std::string birthDate() const = 0;
  virtual std::string address() const = 0;
};
```

这个 class 的客户必须以 Person 的指针或引用来写程序，就像 Handle class 的客户一样，除非 Interface class 的接口被修改，否则其客户无需重新编译。

Interface class 的客户必须有办法为这种 class 创建对象，通常用一个工厂函数或 virtual 构造函数，它们返回指针，指向动态分配所得对象，而该对象支持 Interface class 的接口：

```
class Person {
 public:
  ...
  static std::shared_ptr<Person> create(const std::string& name,
                                        const Date& birthday,
                                        const Address& addr);
  ...
};
```

当然，支持 Interface class 接口的具象类（concrete class）必须被定义出来，并且真正的构造函数必须被调用：

```
class RealPerson : public Person {
 public:
  RealPerson(const std::string& name, const Date& birthday, const Address& addr)
      : theName(name), theBirthDate(birthday), theAddress(addr) {}
  virtual ~RealPerson() {}
  std::string name() const;
  std::string birthDate() const;
  std::string address() const;

 private:
  std::string theName;
  Date theBirthDate;
  Address theAddress;
};

std::shared_ptr<Person> Person::create(const std::string& name,
                                       const Date& birthday,
                                       const Address& addr) {
  return std::shared_ptr<Person>(new RealPerson(name, birthday, addr));
}
```



# 6 继承与面向对象设计

## 条款 32：确定你的 public 继承塑模出 is-a 关系

“public 继承” 意味 is-a，适用于基类身上的每一件事一定也适用于派生类身上，因为每一个派生类对象也都是一个基类对象。

听起来颇为简单，但有时候直觉会误导你，比如企鹅是一种鸟，这是事实，鸟可以飞，这也是事实。若用 C++ 描述这层关系：

```
class Bird {
 public:
  virtual void fly();
};

class Penguin: public Bird {
  ...
};
```

则会出错，因为显然企鹅不会飞。再比如矩形和正方形，可能你会觉得正方形可以继承矩形，因为正方形也是个矩形。但若矩形中有两个方法可以独立的改变长和宽，正方形也继承了这两个方法，则又会出错，因为正方形的长宽必须保持相等，无法独立的去改变。

## 条款 33：避免遮掩继承而来的名称

当内层作用域有个函数或变量名和外层作用域相同时，编译器的名称遮掩规则会直接找到内层作用域的变量，无论类型是否相同。

```
class Base {
 private:
  int x;

 public:
  virtual void mf1() = 0;
  virtual void mf1(int);
  virtual void mf2();
  void mf3();
  void mf3(double);
};

class Derived : public Base {
 public:
  virtual void mf1();
  void mf3();
  void mf4();
};
```

上面的代码中，以 base class 内所有名为 mf1 和 mf3 的函数都被 derived class 内的 mf1 和 mf3 函数遮掩了，无论函数类型（是否为 virtual、参数列表是否相同、返回值是否相同，都不重要）：

```
Derived d;
int x;

d.mf1();	// OK
d.mf1(x);	// WRONG! Base::mf1(int) 被遮掩了
d.mf2();	// OK
d.mf3();	// OK
d.mf3(x);	// WRONG! Base::mf3(int) 被遮掩了
```

若想继承那些重载函数（若不继承则违反了 public 继承的 is-a 关系），需要使用 using 声明式：

```
class Derived : public Base {
 public:
  using Base::mf1;		// 让 Base class 内的 mf1 和 mf3 的所有东西可见
  using Base::mf3;		// 放在 public 区域是因为基类的 public 成员在 public 继承下的子类中也应该是 public
  virtual void mf1();
  void mf3();
  void mf4();
};
```

在 private 继承下，Derived 唯一想继承的 mf1 是那个无参数版本的，可使用转交函数实现：

```
class Derived : private Base {
 public:
  // 转交函数（forwarding funciton）
	virtual void mf1() {
    Base::mf1();
  }
};
```

## 条款 34：区分接口继承和实现继承

- 成员函数的接口总是会被继承。

- 声明一个纯虚函数的目的是为了让派生类只继承函数接口。我们也可以为纯虚函数提供定义，调用时需明确指出 class 名称，如：`Base::draw();` 。

- 声明非纯虚函数的目的，是让 derived class 继承该函数的接口和缺省实现：

  ```
  class Airport { ... }; 		// 机场类
  class Airplane {
   public:
    virtual void fly(const Airport& destination);
  };
  
  void Airplane::fly(const Airport& destination) { /* 缺省实现 */ };
  class ModelA : public Airplane {};
  class ModelB : public Airplane {};
  ```

  可能现在 ModelA 和 ModelB 都可以使用 Airplane 缺省的 fly 实现，但若现在加入了新的 ModelC，且缺省实现无法适用于 ModelC，而程序员忘记为 ModelC 重新定义其 fly 函数，则会出大问题。问题不在于 Airplane::fly 有缺省行为，而在于 ModelC 在没有明确说出“我要”的情况下就继承了该缺省行为。

  解决方法在于切断“虚函数接口”和其“缺省实现”之间的连接：

  ```
  class Airplane {
   public:
    virtual void fly(const Airport& destination) = 0;
   
   protected:
    void defaultFly(const Airport& destination);
  };
  
  void Airplane::defaultFly(const Airport& destination) { /* 缺省实现 */ };
  ```

  现在，Airplane::fly 成为纯虚函数，所有继承 Airplane 的类都要重新声明或实现该接口。若想实现缺省调用，可在 fly 函数中调用 defaultFly：

  ```
  class ModelA : public Airplane {
   public:
    virtual void fly(const Airport& destination) { 
      defaultFly(destination); 
    }
  };
  ```

  注意 defaultFly 为 protected 成员，因为用户不需要知道 fly 的具体实现，只需调用 fly 即可飞行。并且它是一个非虚函数，因为没有任何派生类应该重新定义它。

  也可以利用“纯虚函数也可以拥有自己的实现”这一性质来将接口和缺省实现分开：

  ```
  class Airplane {
   public:
    virtual void fly(const Airport& destination) = 0;
  };
  
  void Airplane::fly(const Airport& destination) { /* 缺省实现 */ };
  
  class ModelA : public Airplane {
   public:
    virtual void fly(const Airport& destination) { 
      Airplane::fly(destination); // 明确调用
    }
  };
  ```

- 声明 non-virtual 函数的目的是为了令派生类继承函数的接口以及一份强制实现。non-virtual 函数代表的意义是不变性凌驾于特异性，所以它绝不应该在派生类中重新定义。

纯虚函数、普通虚函数、non-virtual 函数之间的差异，使得你可以精确的指定你想要派生类继承的东西：只继承接口、继承接口和一个缺省实现，或是继承接口和一份强制实现。

## 条款 35：考虑 virtual 函数以外的其他选择

假设为游戏人物设计继承体系，提供一个成员函数 healthValue，返回其健康程度，不同的任务计算健康程度的方法不同。正常应该是将其变为 virtual，但也有其他一些解法

### 借由 Non-Virtual Interface 手法实现 Template Method 模式

保留 healthValue 为 public 成员函数，但让它成为 non-virtual，并调用一个 private virtual 函数：

```
class GameCharacter {
 public:
  int healthValue() const {
    // ... 一些准备工作
    int retVal = doHealthValue();  // 真正的工作
    // ... 一些收尾工作
    return retVal;
  }

 private:
  virtual int doHealthValue() const {  // 派生类重新定义这个函数
    // ...
   }
};
Copy
```

这种“让客户通过 public non-virtual 成员函数间接调用 private virtual 函数”的手法称为 non-virtual interface（NVI）。这是 Template Method 设计模式的一个独特表现形式。这个 non-virtual 函数称为 virtual 函数的外覆器（wrapper）。

### 借由 Function Pointers 实现 Strategy 模式

NVI 依然需要虚函数，可以让每个人物的构造函数接受一个指针，指向一个健康计算函数，我们可以调用该函数进行实际计算：

```
class GameCharacter;

int defaultHealthCalc(const GameCharacter& gc);

class GameCharacter {
 public:
  typedef int (*HealthCalcFunc)(const GameCharacter&);

  explicit GameCharacter(HealthCalcFunc hcf = defaultHealthCalc)
      : healthFunc(hcf) {}

  int healthValue() const { return healthFunc(*this); }

 private:
  HealthCalcFunc healthFunc;
};
Copy
```

这是 Strategy 设计模式的简单应用，它提供了一些有趣的弹性：

- 同一人物类型之不同实体可以有不同的健康计算函数
- 某已知人物之健康指数计算函数可在运行期变更。比如可设计 setHealthCalculator 成员函数来变更计算函数。

### 借由 std::function 完成 Strategy 模式

没有什么大的区别，只是将 HealthCalcFunc 变成了 std::function 的类型，增加了弹性，可以接受任何 callable 的东西。

### 古典的 Strategy 模式

也没有什么大的区别，就是将继承体系内的 virtual 函数替换成了另一个继承体系内的 virtual 函数：

```
class GameCharacter;

class HealthCalcFunc {
 public:
  virtual int calc(const GameCharacter& gc) const { ... }
};

HealthCalcFunc defaultHealthCalc;

class GameCharacter {
 public:
  explicit GameCharacter(HealthCalcFunc* phcf = &defaultHealthCalc)
      : phealthFunc(phcf) {}

  int healthValue() const { return phealthFunc->calc(*this); }

 private:
  HealthCalcFunc* phealthFunc;
};
Copy
```

## 条款 36：绝不重新定义继承而来的 non-virtual 函数

若你正在编写 class D 并重新定义继承自 class B 的 non-virtual 函数 mf。那么当 mf 被调用时，任何一个 D 对象都可能表现出 B 或 D 的行为：决定因素不在于其本身，而在于指向该对象的指针或引用的类型（因为它们都是静态绑定的，编译期即可决定调用哪个函数）。

并且，public 继承意味着 is-a 的关系，若在 B 中定义了 non-virtual 函数 mf，则表明其不变性凌驾于特异性。若在 D 中重新定义 mf，便与以上的设计相矛盾。

## 条款 37：绝不重新定义继承而来的缺省参数值

由于重新定义继承而来的 non-virtual 函数是错误的，所以这里将讨论范围缩小到继承而来的 virtual 函数。这样本条款的理由就很直接明确了： virtual 函数是动态绑定的，而缺省参数却是静态绑定的。

也就是说，你在调用一个定义于派生类中的虚函数时，却使用的是基类为其指定的缺省参数。这种行为是为了效率考虑，如果缺省参数是动态绑定的，编译器就必须有某种方法在运行期为 virtual 函数决定适当的参数缺省值，这复杂且耗时。

若将基类和派生类的缺省参数都设为相同值，那么又带来了代码重复以及相依性，当基类缺省参数改变，还得将派生类的缺省参数也改变。比较好的做法是采用 NVI（non-virtual interface）手法，在基类中定义一个 public non-virtual 函数，让该函数去调用 private virtual 函数，派生类可以重新定义这个 private virtual 函数。我们可以让 non-virtual 函数指定缺省参数，而 private virtual 函数负责真正的工作。

## 条款 38：通过复合塑模出 has-a 或“根据某物实现出”

复合（composition）有很多同义词：layering（分层），containment（内含），aggregation（聚合）和embedding（内嵌）。

复合意味 has-a 或 is-implemented-in-terms-of（根据某物实现出）。当复合发生于应用域内（现实中的事物，如人、汽车等）的对象之间，表现出 has-a 的关系，当发生于实现域内（实现细节上的人工制品，如缓冲区、互斥器等）则是 is-implemented-in-terms-of 的关系。

is-a 和 has-a 的关系比较清晰，is-a 与 is-implemented-in-terms-of 则比较易混淆。例如你要实现一个 set，你想底层可以用 list 去实现，于是你想到用你的 set 去 public 继承 STL 中的 list，可惜这样做是错误的。如条款 32 所说，对基类为真的每一件事对派生类也应为真。list 中的元素允许重复，而 set 却不允许。

所以 set 和 list 并不能用 is-a 的关系描述。正确的做法是采用 is-implemented-in-terms-of 的关系，也就是在 set 对象中放一个 list，set 对象可根据一个 list 对象实现出来（比如 STL 中的 set 里面有一个红黑树）。

## 条款 39：明智而审慎地使用 private 继承

如果 class 之间的继承关系为 private，则编译器不会自动将一个派生类转换为一个基类（不会有隐式的类型转换）。且由 private 基类继承而来的所有成员，在派生类中都会变成 private 属性。

private 继承意味 implemented-in-terms-of 的关系。若 class D 以 private 继承 class B，你的目的是采用 class B 内已经备妥的某些特性，而不是因为 B 对象和 D 对象存在有任何观念上的关系。根据条款 34，private 继承意味着只继承了实现，而接口部分被略去。

虽然 private 继承和复合（composition）都意味着 is-impleamented-in-terms-of，但 private 继承的级别更低。当派生类需要访问基类的 protected 成员，或需要重新定义继承而来的 virtual 函数时，才可能需要 private 继承。

private 继承还可以造成 empty base 最优化：

```
class Empty {};

class HoldsAnInt {
 private:
  int x;
  Empty e;
};
Copy
```

虽然 Empty 是一个空类，但 sizeof(HoldsAnInt) 一定是大于 sizeof(int) 的，一位内大小为 0 的对象，编译器往往会默默安插一个 char 到空对象中，加上对齐，可能真实大小远比你想象的大。但若采用继承：

```
class HoldsAnInt : private Empty {
 private:
  int x;
};
Copy
```

则 sizeof(HoldsAnInt) == sizeof(int)，这就是所谓的 EBO（empty base optimization）。EBO 一般只在单一继承下才可行。

## 条款 40：明智而审慎地使用多重继承

首先，多重继承可能会从一个以上的基类中继承相同名称（如函数、typedef 等），这会导致歧义。即使这个同名函数只有一个可以被你调用（一个是 public，一个是 private），因为 C++ 解析重载函数的规则是先找到此调用的最佳匹配，再检查是否可以调用。为解决起义，必须明确进行调用，如：`Base1::mf();` 或 `Base2::mf();`。

对于虚拟继承：第一，非必要，不使用。第二如果必须使用虚拟继承，尽可能避免在 virtual base class 中放置数据，这样就无需担心这些 class 身上的初始化所带来的诡异事情了。

多重继承的一个用途之一是：在 public 继承某个 Interface 类的情况下，同时又需要 private 继承某个协助实现的类（条款 39说明了什么时候需要 private 继承）。

# 7 模板与泛型编程

## 条款 41：了解隐式接口和编译器多态

- class 和 template 都支持接口和多态
- 对 class 而言接口是显示的，以函数签名为中心。多态则是通过 virtual 函数发生于运行期
- 对 template 参数而言，接口是隐式的，基于有效表达式（模板函数中的一些方法调用中）。多态则是通过 template 具现化和函数重载解析发生于编译期。

## 条款 42：了解 typename 的双重意义

声明 template 类型参数时，class 和 typename 的意义相同，但其他时候可能不一样：

```
template<typename C>
void print2nd(const C& container) {
  if (container.size() >= 2) {
    C::const_iterator iter(container.begin());
    ++iter;
    int value = *iter;
    std::cout << value;
  }
}
```

template 内出现的名称如果相依于某个 template 参数，称之为 **从属名称** （dependent names）。如果从属名称在 class 内呈嵌套状，则称其为 **嵌套从属名称** （nested dependent name）。如上面的 C::const_iterator，并且这还是个 **嵌套从属类型名称** （nested dependent type name）。像 int 这样不依赖任何 template 参数的名称叫做非从属名称（non-dependent names）。

**嵌套从属名称** 会造成解析困难。例如：`C::const_iterator* x;` ，看起来是声明了一个指针，然而 C++ 的解析器却不知道 const_iterator 是 C 中定义的类型还是 C 中定义的变量，如果是变量，则 * 则代表相乘。C++ 解析器的做法是：如果你不告诉它，那么就默认嵌套从属名称不是类型。所以上面的代码是不会通过编译的。

我们必须在前面加上 typename 关键词，告诉 C++ 说 C::const_iterator 是个类型：`typename C::const_iterator iter(container.begin());` 。

有个例外是：typename 不可以出现在 base class list 内的嵌套从属类型名称前，也不可在 member initialization list 中作为 base class 修饰符。

## 条款 43：学习处理模板化基类内的名称

```
class CompanyA {
 public:
  void sendCleartext(const std::string& msg);
  void sendEncrypted(const std::string& msg);
};

class CompanyB {
 public:
  void sendCleartext(const std::string& msg);
  void sendEncrypted(const std::string& msg);
};

class MsgInfo { ... };

template <typename Company>
class MsgSender {
 public:
  void sendClear(const MsgInfo& info) {
    std::string msg;
    Company c;
    c.sendCleartext(msg);
  }

  void sendSecret(const MsgInfo& info) { ... }
};

template <typename Company>
class LoggingMsgSender : public MsgSender<Company> {
 public:
  /* 与基类的 sendClear 的名字不同，很好，避免了名称遮掩 */
  void sendClearMsg(const MsgInfo& info) { 
    // 记录日志 ...
    sendClear(info); // 这里编译不通过
    // 记录日志 ...
  }
};
```

假设有如上程序，MsgSender 模板类的模板参数是不同的公司，如 CompanyA、CompanyB，用来给不同的公司发送消息。而 LoggingMsgSender public 继承了 MsgSender，用于在发送消息的同时记录日志。在 LoggingMsgSender 中的 sendClearMsg 中，调用了基类的 sendClear。看起来合情合理，但不会编译通过。

因为在 LoggingMsgSender 具现化之前，编译器无法知道继承来的 MsgSender 是什么（Company 参数没给）。所以也就无法知道基类是否有一个 sendClear 函数。

三个解决方法：

- 在 base class 函数调用动作之前加上“this->”：`this->sendClear(info);` 。

- 使用 using 声明式：

  ```
  template <typename Company>
  class LoggingMsgSender : public MsgSender<Company> {
   public:
    using MsgSender<Company>::sendClear; // 告诉编译器 sendClear 在基类中
    /* 与基类的 sendClear 的名字不同，很好，避免了名称遮掩 */
    void sendClearMsg(const MsgInfo& info) { 
      // 记录日志 ...
      sendClear(info); // OK
      // 记录日志 ...
    }
  };
  ```

- 明确指出函数位置，若调用的函数是虚函数，则这种方法会关闭“virtual 绑定行为”：`MsgSender<Company>::sendClear(info);` 。

三种方法都是对编译器承诺：基类模板的任何特化版本，都支持其一般（泛化）版本所提供的接口。

## 条款 44：将与参数无关的代码抽离 templates

使用 templates 可能会导致代码膨胀，解决工具是：共性与变性分析。

```
template <typename T, std::size_t n>
class SquareMatrix {
 public:
  ... 
  void invert();
};
```

这个模板接受一个类型参数 T 和一个非类型参数 size_t，用来计算矩阵的逆。那么对于不同的模板参数 n，如：SquareMatrix(double, 5)、SquareMatrix(double, 10)，会具现出两份 invert。对于这种因为非类型模板参数造成的代码膨胀，可以通过函数参数或 class 成员变量替换 template 参数：

```
template <typename T>
class SquareMatrixBase {
 protected:
  SquareMatrixBase(std::size_t n, T* pMem) : size(n), pData(pMem) {}
  void setDataPtr(T* ptr) { pData = ptr; }

  void invert();

 private:
  std::size_t size;
  T* pData;
};

template <typename T, std::size_t n>
class SquareMatrix : private SquareMatrixBase<T> {  
 // private 继承，表明 base class 只是为了帮助 derived class 实现，而非 is-a 的关系
 private:
  using SquareMatrixBase<T>::invert;  // 避免遮掩 base 中的 invert
  T data[n * n];
  
 public:
  SquareMatrix() : SquareMatrixBase<T>(n, data) {} // 给出矩阵大小和数据指针给 base class
  void invert() { this->invert(); }
};
```

因为类型参数而造成的代码膨胀，如 vector<int> 和 vector<long> 两者的实现码是完全相同的，可以将完全相同的二进制表述的具现类型共享实现码，这个靠连接器（linkers）去实现。

Template 生成多个 class 和多个函数，所以，任何 template 代码都不该与某个造成膨胀的 template 参数产生相依关系。

## 条款 45：运用成员函数模板接受所有兼容类型

考虑实现一个智能指针：

```
template <typename T>
class SmartPtr {
 public:
  explicit SmartPtr(T* realPtr);
};
```

普通的指针是支持隐式转换的，也就是从 derived 向 base 的转换。但上面这个智能指针却不支持。对于一个基类 Base 和一个派生类 Derived，SmartPtr<Base> 和 SamrtPtr<Derived> 是两个完全不同的类型，为了支持两者的转换，我们必须明确的将其编写出来（写不同的构造函数）。

然而派生类的数量是不断增加的，我们不可能每增加一个派生类，都去修改 SmartPtr 来满足需求。因此我们需要的不是为 SmartPtr 写一个或多个构造函数，而是写一个构造函数模板：

```
template <typename T>
class SmartPtr {
 public:
  template <typename U>
  SmartPtr(const SmartPtr<U>& other);  // 为了生成 copy 构造函数
};
```

以上代码代表对任何类型 T 和任何类型 U，可以根据 SmartPtr<U> 生成 SmartPtr<T>。这个 copy 构造函数还存在问题，因为我们需要的只是从 derived 到 base 转换，而不是任意两个类型。

可以和 std 中的智能指针一样，提供一个 get 方法，返回原始指针，然后就可以在构造模板中约束转换行为了：

```
template <typename T>
class SmartPtr {
 public:
  template <typename U>
  SmartPtr(const SmartPtr<U>& other) : heldPtr(other.get()) { ... }

  T* get() const { return heldPtr; }

 private:
  T* heldPtr;
};
```

这里使用了成员初值列来初始化 SmartPtr<T> 中类型为 T* 的成员变量，并以 U* 为初值。这样编译器就会为我们检查 U* 和 T* 的转换是否合法了。

还有一点值得注意，虽然声明了“泛化 copy 构造”或“泛化 assignment 操作”，但语言基本规则并没有改变，如条款 5 所说，编译器可能会为我们生成一些函数，比如，如果程序需要一个 copy 构造函数，而你没有声明，编译器还是会为你自动生成一个。泛化的 copy 构造并不会阻止编译器生成它们自己的 copy 构造。

## 条款 46：需要类型转换时请为模板定义非成员函数

考虑条款 24 的例子，但在这里把改为模板：

```
template<typename T>
class Rational {
 public:
  Rational(const T& numerator = 0, const T& denominator = 1);
  const T numerator() const;
  const T denominator() const;
};

template<typename T>
const Rational<T> operator*(const Rational<T>& lhs, const Rational<T>& rhs) {
  return Rational(lhs.numerator() * rhs.numerator(), 
                  lhs.denominator() * rhs.denominator());
}
```

如条款 24，我们希望它支持混合式算术运算，但这样的语句是无法编译通过的：`Rational<int> result = oneHalf * 2` ，oneHalf 是 Rational<int> 类型。因为编译器无法完成具现化。我们定义的 operator* 的两个参数类型是 Rational<T>，编译器可以根据 oneHalf 推导出 T 是 int，然而却无法根据第二个 int 类型的参数推导出 T 是什么。**在模板推导的时候，是不会把隐式类型转换考虑进来的。**

我们可以将 operator* 声明为 template class 内的一个 friend，而 template class 并不需要进行实参推导（只有函数模板才需要），所以编译器可以在具现化 Rational<T> 的同时声明适当的 operator* 函数：

```
template <typename T>
class Rational;

template <typename T>
const Rational<T> doMultiply(const Rational<T>& lhs, const Rational<T>& rh) {
  return Rational(lhs.numerator() * rhs.numerator(),
                  lhs.denominator() * rhs.denominator());
}

template <typename T>
class Rational {
 public:
  Rational(const T& numerator = 0, const T& denominator = 1);
  const T numerator() const;
  const T denominator() const;

  friend const Rational operator*(const Rational& lhs, const Rational& rhs) {
    return doMultiply(lhs, rhs);
  }
};
Copy
```

我们在 Rational 内部 **声明** 并 **定义** 了一个友元函数。这里使用 friend 仅仅是为了将一个 non-member 函数放在 class 内部，而不是为了让其能够访问类中的 non-public 成分。并且，我们 inline 调用了doMultiply 函数（doMultiply 不支持混合运算，也不需要支持，它仅仅是让 operator* 去调用），doMultiply 函数可以在其他的地方进行定义。于是， 当我们声明一个 Rational<int> 时，class Rational<int> 会被具现化，同时，作为具现化的一部分， friend operator* （接受 Rational<int> 参数）也被自动声明并定义了出来，于是混合运算得以支持。

## 条款 47：请使用 traits classes 表现类型信息

如何设计并实现一个 traits class：

- 确认若干你希望将来可取得的类型相关信息。例如对迭代器而言，我们希望将来可取得其分类（category）。
- 为该信息选择一个名称（如 iterator_category)。
- 提供一个 template 和一组特化版本，内含你希望支持的类型相关信息。

如何使用一个 traits class：

- 建立一组重载函数（身份像劳工）或函数模板，彼此间的差异只在于各自的 traits 参数。令每个函数实现码与其接受之 traits 信息相应和。
- 建立一个控制函数（身份像工头）或函数模板，它调用上述那些“劳工函数”并传递 traits class 所提供的信息。

总结：

- Traits class 使得“类型相关信息”在编译期可用。它们以 templates 和“templates 特化”完成实现。
- 整合重载技术后，traits classes 有可能在编译器对类型执行 if…else 测试。

## 条款 48：认识 template 元编程

Template metaprogramming（TMP，模板元编程）是编写 template-based C++ 程序并执行于编译期的过程。TMP 是“图灵完全”机器，意思是它可以计算任何事物。例如条款 47 中借由 template 和其特化来代替 if…else 测试。再来看一个用“递归模板具现化”代替 for 循环的实现，计算阶乘：

```
template <unsigned n>
struct Factorial {
  enum { value = n * Factorial<n - 1>::value };
};

/* 特化，递归出口 */
template <>
struct Factorial<0> {
  enum { value = 1 };
};
Copy
```

只要指涉 `Factorial<n>::value` 就可以得到 n 阶乘值。

总结：

- Template metaprogramming 可将工作由运行期移往编译期，因而得以实现早期错误侦测和更高的执行效率
- TMP 可被用来生成“基于政策选择组合”（based on combinations of policy choices）的客户定制代码，也可用来避免生成对某些特殊类型并不合适的代码。



# 8 定制 new 和 delete

## 条款 49：了解 new-handler 的行为

客户可以调用 set_new_handler 来指定 operator new 无法分配足够内存时该调用的函数（new-handler 函数）：

```
namespace std {

typedef void (*new_handler)();
new_handler set_new_handler(new_handler p) throw();

}  // namespace std
Copy
```

set_new_handler 的参数和返回都是一个 new_handler 类型的指针。其中，参数是该被调用的 new-handler 函数，返回是被替换的 new-handler 函数。

当 operator new 无法申请足够内存时，会 **不断调用 new-handler 函数** 。所以一个设计良好的 new-handler 函数必须做以下事情：

- 让更多内存可被使用。
- 安装另一个 new-handler。如果目前的 new-handler 无法取得更多内存，可以安装另一个有能力获取更多内存的 new-handler。
- 卸除 new-handler，也就是将 NULL 指针传递给 set_new_handler。这是为了让 operator new 抛出异常。
- 抛出 bad_alloc 的异常。这个异常不会被 operator new 捕获，会直接传到内存索求处。
- 不返回，通常调用 exit 或 abort。

令每个 class 提供自己的 set_new_handler 和 operator new 就可以让每个 class 有自己专属的 new-handler。我们可以设计一个基类，这个基类可以设定类的专属 new-handler，然后将基类转化为 template，让每个派生类都获得这个基类的所拥有的能力：

```
/* 通过 RAII 技术管理 new-handler */
class NewHandlerHolder {
 public:
  /* 取得 new-handler */
  explicit NewHandlerHolder(std::new_handler nh) : handler(nh) {}
  /* 释放 new-handler */
  ~NewHandlerHolder() { std::set_new_handler(handler); }

 private:
  std::new_handler handler;

  NewHandlerHolder(const NewHandlerHolder&);
  NewHandlerHolder& operator=(const NewHandlerHolder&);
};

/* "mixin" 风格的 base class，用来支持 class 专属的 set_new_handler */
template <typename T>
class NewHandlerSupport {
 public:
  static std::new_handler set_new_handler(std::new_handler p) throw();
  static void* operator new(std::size_t size) throw(std::bad_alloc);

 private:
  static std::new_handler currentHandler;
};

template <typename T>
std::new_handler NewHandlerSupport<T>::set_new_handler(
    std::new_handler p) throw() {
  std::new_handler oldHandler = currentHandler;
  currentHandler = p;
  return oldHandler;
}

template <typename T>
void* NewHandlerSupport<T>::operator new(std::size_t size) {
  /* 安装专属 new-handler，分配内存或抛出异常，恢复 global new-handler */
  NewHandlerHolder h(std::set_new_handler(currentHandler));
  return ::operator new(size);
}

/* 将每个 currentHandler 初始化为 NULL */
template <typename T>
std::new_handler NewHandlerSupport<T>::currentHandler = 0;
Copy
```

然后这样使用：

```
class Widget : public NewHandlerSupport<Widget> {
  // ...
};

int main() {
  void outOfMem();
  Widget::set_new_handler(outOfMem);  // 设定 Widget 的专属 new-handler
  Widget* pw1 = new Widget;   // new 的时候会安装专属 new-handler，
                              // new 完之后会恢复 global new-handler（通过 RAII 来实现）

  return 0;
}
Copy
```

这里用 Widget 继承 NewHandlerSupport<Widget> 的手法叫做“怪异的循环模板模式”（curiously recurring template pattern; CRTP）。并且在 NewHandlerSupport 中并未使用其模板参数 T，因为 T 确实无需使用。我们只是希望继承自 NewHandlerSupport 的每个 class，拥有一份实体互异的 NewHandlerSupport 复件（就是不同的 static 成员变量 currentHandler）。

最后，operator new 有一个 “nothrow” 形式，它在分配失败时不抛异常，而是返回 NULL：

```
Widget* pw = new (std::nothrow) Widget;
Copy
```

不过它对异常的强制保证性不高，因为即使 new 不抛出异常，但 new 完之后，调用的构造函数却可能抛出异常，这时异常还是会进行传播。因此没必要用 nothrow new。

## 条款 50：了解 new 和 delete 的合理替换时机

替换编译器提供的 operator new 或 operator delete 的几个理由：

- 用来检测运用上的错误。
- 为了收集使用上的统计数据。
- 为了增加分配和归还的速度。
- 为了降低缺省内存管理器带来的空间额外开销。
- 为了弥补缺省分配器中的非最佳对齐位。
- 为了将相关对象成簇集中。
- 为了获得非传统的行为。

## 条款 51：编写 new 和 delete 时需固守常规

- operator new 应该内含一个无穷循环，并在其中尝试分配内存，如果它无法满足内存需求，就应该调用 new-handler。它也应该有能力处理 0 bytes 申请。Class 专属版本则还应该处理“比正确大小更大的（错误）申请”（就是说基类的 operator new 对派生类可能不适用，因为派生类更大一些）。下面是一份伪代码：

  ```
  void* operator new(std::size_t size) throw(std::bad_alloc) {
    using namespace std;
    if (size == 0) {
      // 处理 0 byte，将其视为 1 byte，不完美，但简单、可行
      size = 1;
    }
    while (true) {
      尝试分配 size bytes;
      if (分配成功) return 一个指针，指向分配来的内存;
  
      // 分配失败
      new_handler globalHandler = set_new_handler(0);
      set_new_handler(globalHandler); // 将 new-handler 设为 null 后又立刻恢复原样
                                      // 只有这样才能获得 new-handler 函数指针
  
      if (globalHandler) (*globalHandler)();
      else throw std::bad_alloc();
    }
  }
  Copy
  ```

  对于 Class 专属 operator new：

  ```
  void* Base::operator new(std::size_t size) throw(std::bad_alloc) {
    /* 如果大小错误，则令标准 operator new 处理
     * 这里没有处理 size 为 0 的情况，因为非附属对象必须有非零大小 
     * 也就是说 sizeof(Base) 不可能为 0（见条款 39）*/
    if (size != sizeof(Base))  
      return ::operator new(size);
    ...
  }
  Copy
  ```

- operator delete 应该在收到 null 指针时不做任何事。Class 专属版本则还应该处理“比正确大小更大的（错误）申请”：

  ```
  void operator delete(void* rawMemory) throw() {
    if (rawMemory == 0) return;
    ...
  }
  
  void Base::operator delete(void* rawMemory, std::size_t size) throw() {
    if (rawMemory == 0) return;
    if (size != sizeof(Base)) {
      ::operator delete(rawMemory);
      return;
  	}
    ...
  }
  Copy
  ```

## 条款 52：写了 placement new 也要写 placement delete

如果一个带额外参数的 operator new 没有“带相同额外参数”的对应版本 operator delete，那么当 new 的内存分配动作需要取消并恢复旧观时就没有任何 operator delete 会被调用，从而造成内存泄漏。

若 `Widget* pw = new (std::cerr) Widget;` 没有抛出异常，而客户代码中有个对应的 delete 被调用：`delete pw;` 。则调用的是正常形式的 operator delete，placement delete 只有在“伴随 placement new 调用而出发的构造函数”出现异常时才会被调用。

这意味着，如果你写了一个 placement new，就必须同时提供一个正常的 operator delete（用于构造期间无异常抛出）和一个 placement delete（用于构造期间有异常抛出）。

缺省情况下，C++ 在 global 作用域有以下形式的 operator new：

```
void* operator new(std::size_t) throw(std::bad_alloc);	// normal new
void* operator new(std::size_t, void*) throw();					// placement new
void* operator new(std::size_t, const std::nothrow_t&) throw(); // nothrow new
Copy
```

如果你在 class 内声明了任何 operator new，它会遮掩上面的这些标准形式。可以通过建立一个 base class，内含所有正常形式的 new 和 delete 来避免名称遮掩：

```
class StandardNewDeleteForms {
 public:
  // normal new&delete
  static void* operator new(std::size_t size) throw(std::bad_alloc) {
    return ::operator new(size);
  }
  static void operator delete(void* pMemory) throw() {
    return ::operator delete(pMemory);
  }
  // placement new&delete
  static void* operator new(std::size_t size, void* ptr) throw() {
    return ::operator new(size, ptr);
  }
  static void operator delete(void* pMemory, void* ptr) throw() {
    return ::operator delete(pMemory, ptr);
  }
  // nothrow new&delete

  static void* operator new(std::size_t size, const std::nothrow_t& nt) throw() {
    return ::operator new(size, nt);
  }
  static void operator delete(void* pMemory, const std::nothrow_t& nt) throw() {
    return ::operator delete(pMemory);
  }
};
Copy
```

然后凡是想以自定义形式扩充标准形式的客户，可利用继承机制以及 using 声明式（见条款 33）取得标准形式。

# 9 杂项讨论

## 条款 53：不要轻忽编译器的警告

- 严肃对待编译器发出的警告信息。努力在你的编译器的最高警告级别下争取“无任何警告”的荣誉
- 不要过度依赖编译器的报警能力，因为不同的编译器对待事情的态度并不相同。一旦移植到另一个编译器上，你原本依赖的警告信息有可能消失。

## 条款 54：让自己熟悉包括 TR1 在内的标准程序库

（这部分内容已经过时了，C++11 已经将 tr1 全部纳入到 std 中了）

- C++ 标准程序库的主要机能由 STL、iostreams、locales 组成。并包含 C99 标准程序库。
- TR1 添加了智能指针、一般化函数指针、hash-based 容器、正则表达式以及另外 10 个组件的支持。
- TR1 自身只是一份规范。为获得 TR1 提供的好处，你需要一份实物。一个好的实物来源是 Boost。

## 条款 55：让自己熟悉 Boost

- Boost 是一个社群，也是一个网站。致力于免费、源码开放、同僚复审的 C++ 程序库开发。Boost 在 C++ 标准化过程中扮演深具影响力的角色。
- Boost 提供许多 TR1 组件实现品，以及其他许多程序库。