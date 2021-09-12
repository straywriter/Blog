---
title: C++ STL容器用法
top: false
mathjax: true
date: 2021-03-22 21:59:11
categories:
- C++
---

-----





# a

- 序列容器
  - vector
  - list
  - deque
  - array
  - forward_list
- 容器适配器
  - stack
  - queue
  - priority_queue
- 关联容器
  - set
  - multset
  - map
  - multimap
- 无序关联容器
  - unordered_set
  - unordered_multiset
  - unordered_map
  - unordered_multimap



any





## 开始

- 





## vector

- C + + 标准库 vector 类是序列容器的类模板。

-  vector以线性方式存储给定类型的元素，并允许快速随机访问任何元素。 当随机访问性能处于高级版时，vector是序列的首选容器。

```cpp
template <class Type, class Allocator = allocator<Type>>
class vector
```

**参数**

*`Type`*\
要存储在矢量中的元素数据类型

*`Allocator`*\
表示所存储分配器对象的类型，该分配器对象封装有关矢量的内存分配和解除分配的详细信息。 此参数为可选参数，默认值为 `allocator<Type>`。

**注解**

向量允许在序列末尾插入和删除常量事件。 若要在矢量中间插入或删除元素，则需要线性时间。  `deque` 类容器在序列的开头和结尾处速度更快。  `list` 类容器在序列内任何位置的插入和删除速度更快。

当成员函数必须将矢量对象中所含序列增加到超过其当前存储容量时，将进行矢量重新分配。 其他的插入和删除均可能改变序列中的各个存储地址。 在所有此类情况下，指向序列更改部分的迭代器或引用将变为无效。 如果未进行重新分配，则只有插入/删除点前的迭代器和引用保持有效。

 `vector<bool>` 类是类型的元素的类模板向量的完全专用化 **`bool`** 。 它具有用于专用化的基础类型的分配器。

 `vector<bool>` 引用类是一个嵌套类，其对象可提供对对象中 (单个位) 的元素的引用 `vector<bool>` 。

**成员**

**Typedef**

| 名称                                                | 说明                                                         |
| --------------------------------------------------- | ------------------------------------------------------------ |
| [allocator_type](#allocator_type)                   | 一个类型，它表示矢量对象的 `allocator` 类。                  |
| [`const_iterator`](#const_iterator)                 | 一种类型，它提供可读取矢量中元素的随机访问迭代器 **`const`** 。 |
| [`const_pointer`](#const_pointer)                   | 一种类型，它提供指向 **`const`** 矢量中元素的指针。          |
| [`const_reference`](#const_reference)               | 一种类型，该类型提供对 **`const`** 存储在向量中的元素的引用。 它用于读取和执行 **`const`** 操作。 |
| [`const_reverse_iterator`](#const_reverse_iterator) | 一种类型，它提供可读取矢量中任何元素的随机访问迭代器 **`const`** 。 |
| [`difference_type`](#difference_type)               | 一个类型，它提供矢量中两个元素的址间的差异。                 |
| [`iterator`](#iterator)                             | 一个类型，它提供可读取或修改向量中任何元素的随机访问迭代器。 |
| [`pointer`](#pointer)                               | 一个类型，提供指向向量中元素的指针。                         |
| [`reference`](#reference)                           | 一个类型，它提供对向量中存储的元素的引用。                   |
| [`reverse_iterator`](#reverse_iterator)             | 一个类型，它提供可读取或修改反向矢量中的任意元素的随机访问迭代器。 |
| [`size_type`](#size_type)                           | 一个类型，它计算矢量中的元素数目。                           |
| [`value_type`](#value_type)                         | 一个类型，它代表向量中存储的数据类型。                       |

**函数**

| 名称                              | 说明                                                     |
| --------------------------------- | -------------------------------------------------------- |
| [`assign`](#assign)               | 清除矢量并将指定的元素复制到该空矢量。                   |
| [`at`](#at)                       | 返回对矢量中指定位置的元素的引用。                       |
| [`back`](#back)                   | 返回对向量中最后一个元素的引用。                         |
| [`begin`](#begin)                 | 对该向量中第一个元素返回随机访问迭代器。                 |
| [`capacity`](#capacity)           | 返回在不分配更多的存储的情况下向量可以包含的元素数。     |
| [`cbegin`](#cbegin)               | 返回指向向量中第一个元素的随机访问常量迭代器。           |
| [`cend`](#cend)                   | 返回一个随机访问常量迭代器，它指向刚超过矢量末尾的位置。 |
| [`crbegin`](#crbegin)             | 返回一个指向反向矢量中第一个元素的常量迭代器。           |
| [`crend`](#crend)                 | 返回一个指向反向矢量末尾的常量迭代器。                   |
| [`clear`](#clear)                 | 清除向量的元素。                                         |
| [`data`](#data)                   | 返回指向向量中第一个元素的指针。                         |
| [`emplace`](#emplace)             | 将就地构造的元素插入到指定位置的向量中。                 |
| [`emplace_back`](#emplace_back)   | 将一个就地构造的元素添加到向量末尾。                     |
| [`empty`](#empty)                 | 测试矢量容器是否为空。                                   |
| [`end`](#end)                     | 返回指向矢量末尾的随机访问迭代器。                       |
| [`erase`](#erase)                 | 从指定位置删除向量中的一个元素或一系列元素。             |
| [`front`](#front)                 | 返回对向量中第一个元素的引用。                           |
| [`get_allocator`](#get_allocator) | 将对象返回到矢量使用的 `allocator` 类。                  |
| [`insert`](#insert)               | 将一个或多个元素插入到指定位置的向量中。                 |
| [`max_size`](#max_size)           | 返回向量的最大长度。                                     |
| [`pop_back`](#pop_back)           | 删除矢量末尾处的元素。                                   |
| [`push_back`](#push_back)         | 在矢量末尾处添加一个元素。                               |
| [`rbegin`](#rbegin)               | 返回指向反向向量中第一个元素的迭代器。                   |
| [`rend`](#rend)                   | 返回一个指向反向矢量末尾的迭代器。                       |
| [`reserve`](#reserve)             | 保留向量对象的最小存储长度。                             |
| [`resize`](#resize)               | 为矢量指定新的大小。                                     |
| [`shrink_to_fit`](#shrink_to_fit) | 放弃额外容量。                                           |
| [`size`](#size)                   | 返回向量中的元素数量。                                   |
| [`swap`](#swap)                   | 交换两个向量的元素。                                     |

**运算符**

| 名称                   | 说明                                   |
| ---------------------- | -------------------------------------- |
| [`operator[]`](#op_at) | 返回对指定位置的矢量元素的引用。       |
| [`operator=`](#op_eq)  | 用另一个向量的副本替换该向量中的元素。 |



###  assign

清除矢vector将指定的元素复制到该空vector。

```cpp
void assign(size_type count, const Type& value);
void assign(initializer_list<Type> init_list);

template <class InputIterator>
void assign(InputIterator first, InputIterator last);
```

**参数**

*`first`*\
要复制的元素范围内的第一个元素的位置。

*`last`*\
要复制的元素范围外的第一个元素的位置。

*`count`*\
要插入到矢量的元素的副本数。

*`value`*\
插入到向量中的元素的值。

*`init_list`*\
包含要插入的元素的 initializer_list。

首先， `assign` 清除向量中的任何现有元素。 然后，将 `assign` 原始向量中指定范围的元素插入到向量中，或将新的指定值元素的副本插入到向量中。

### at

返回对矢量中指定位置的元素的引用。

```cpp
reference at(size_type position);

const_reference at(size_type position) const;
```

**参数**

*`position`*\
要在矢量中引用的元素的下标或位置编号。

**返回值**

对自变量中的下标元素的引用。 如果 *`position`* 大于矢量的大小，将 `at` 引发异常。

**注解**

如果将的返回值 `at` 分配给 `const_reference` ，则无法修改矢量对象。 如果将 `at` 的返回值分配给 `reference`，则可以修改矢量对象。

### back

返回对向量中最后一个元素的引用。

```cpp
reference back();

const_reference back() const;
```

**返回值**

向量的最后一个元素。 如果向量为空，则返回值不确定。

**注解**

如果将的返回值 `back` 分配给 `const_reference` ，则无法修改矢量对象。 如果将 `back` 的返回值分配给 `reference`，则可以修改矢量对象。

当使用 [`_ITERATOR_DEBUG_LEVEL`](../standard-library/iterator-debug-level.md) 定义为1或2的进行编译时，如果尝试访问空向量中的元素，将发生运行时错误。 有关详细信息，请参阅 [检查迭代](../standard-library/checked-iterators.md)器。

### begin

对该向量中第一个元素返回随机访问迭代器。

```cpp
const_iterator begin() const;

iterator begin();
```

**返回值**

发现 `vector` 中第一个元素或空 `vector` 之后的位置的随机访问迭代器。 请始终比较返回的值 [`vector::end`](#end) 以确保它有效。

**注解**

如果将的返回值 `begin` 分配给 [`vector::const_iterator`](#const_iterator) ，则 `vector` 无法修改对象。 如果将的返回值 `begin` 分配给 [`vector::iterator`](#iterator) ，则 `vector` 可以修改该对象。

### capacity

返回在不分配更多的存储的情况下向量可以包含的元素数。

```cpp
size_type capacity() const;
```

**返回值**

分配给该向量的当前存储长度。

**注解**

[`resize`](#resize)如果分配足够的内存来容纳此函数，则成员函数将更有效。 使用成员函数 [`reserve`](#reserve) 指定分配的内存量。

### cbegin

返回一个 **`const`** 迭代器，该迭代器用于寻址范围内的第一个元素。

```cpp
const_iterator cbegin() const;
```

**返回值**

一个 **`const`** 随机访问迭代器，指向范围的第一个元素，或刚超出空范围末尾 (空范围) 的位置 `cbegin() == cend()` 。

**注解**

如果返回值为 `cbegin` ，则不能修改范围中的元素。

可以使用此成员函数替代 `begin()` 成员函数，以保证返回值为 `const_iterator`。 通常，它与 [`auto`](../cpp/auto-cpp.md) 类型推导关键字一起使用，如下面的示例中所示。 在此示例中，将视为 `Container` 支持和的任何类型的可修改 (非 **`const`**) 容器 `begin()` `cbegin()` 。

```cpp
auto i1 = Container.begin();
// i1 is Container<T>::iterator
auto i2 = Container.cbegin();

// i2 is Container<T>::const_iterator
```

### cend

返回 **`const`** 后端迭代器，该迭代器指向矢量最后一个元素之后的元素。

```cpp
const_iterator cend() const;
```

**返回值**

`const`向量的过去的迭代器。 它指向矢量最后一个元素之后的元素。 该元素是占位符，不应取消引用。 仅将它用于比较。 如果向量为空，则为 `vector::cend() == vector::cbegin()` 。

**注解**

`cend` 用于测试迭代器是否超过了其范围的末尾。

可以使用此成员函数替代 `end()` 成员函数，以保证返回值为 `const_iterator`。 通常，它与 [`auto`](../cpp/auto-cpp.md) 类型推导关键字一起使用，如下面的示例中所示。 在此示例中，将视为 `Container` 支持和的任何类型的可修改 (非 **`const`**) 容器 `end()` `cend()` 。

```cpp
auto i1 = Container.end();
// i1 is Container<T>::iterator
auto i2 = Container.cend();

// i2 is Container<T>::const_iterator
```

返回的值 `cend` 不应被取消引用。 仅将它用于比较。

### clear

清除向量的元素。

```cpp
void clear();
```

### const_iterator

一种类型，它提供可读取矢量中元素的随机访问迭代器 **`const`** 。

```cpp
typedef implementation-defined const_iterator;
```

**注解**

类型 `const_iterator` 不能用于修改元素的值。



###  const_pointer

一种类型，它提供指向 **`const`** 矢量中元素的指针。

```cpp
typedef typename Allocator::const_pointer const_pointer;
```

**注解**

类型 `const_pointer` 不能用于修改元素的值。

[iterator](#iterator) 更常用于访问矢量元素。

### const_reference

一种类型，该类型提供对 **`const`** 存储在向量中的元素的引用。 它用于读取和执行 **`const`** 操作。

```cpp
typedef typename Allocator::const_reference const_reference;
```

**注解**

类型 `const_reference` 不能用于修改元素的值。

###  const_reverse_iterator

一种类型，它提供可读取矢量中任何元素的随机访问迭代器 **`const`** 。

```cpp
typedef std::reverse_iterator<const_iterator> const_reverse_iterator;
```

**注解**

类型 `const_reverse_iterator` 无法修改元素的值，它用于反向循环访问矢量。

###  crbegin

返回一个指向反向矢量中第一个元素的常量迭代器。

```cpp
const_reverse_iterator crbegin() const;
```

**返回值**

一种常量反向随机访问迭代器，用于寻址反向 [`vector`](../standard-library/vector-class.md) 或处理非反向中最后一个元素之后的第一个元素 `vector` 。

**注解**

如果返回值为 `crbegin` ，则 `vector` 无法修改对象。

### crend

返回一个 `const` 后端反向迭代器，该迭代器指向反向向量的最后一个元素之后的元素。

```cpp
const_reverse_iterator crend() const;
```

**返回值**

`const`反向向量的反向结束迭代器。 它指向反向向量的最后一个元素之后的元素，该元素与非反转向量的第一个元素之前的元素相同。 该元素是占位符，不应取消引用。 仅将它用于比较。

**注解**

`crend` 用于反向，正 `vector` 如 [`vector::cend`](#cend) 用于 `vector` 。

如果返回值为 `crend` (适当递减的) ，则 `vector` 无法修改对象。

`crend` 可用于测试反向迭代器是否已到达其 `vector` 的末尾。

返回的值 `crend` 不应被取消引用。 仅将它用于比较。

### data

返回指向向量中第一个元素的指针。

```cpp
const_pointer data() const;

pointer data();
```

**返回值**

一个指针，指向中的第一个元素 [`vector`](../standard-library/vector-class.md) 或空后面的位置 `vector` 。

### difference_type

一个类型，它提供引用同一向量中元素的两个迭代器之间的差异。

```cpp
typedef typename Allocator::difference_type difference_type;
```

**注解**

`difference_type` 也可以被描述为两个指针之间的元素数，因为指向一个元素的指针包含其地址。

[iterator](#iterator) 更常用于访问矢量元素。

### emplace

将就地构造的元素插入到指定位置的向量中。

```cpp
template <class... Types>
iterator emplace(
    const_iterator position,
    Types&&... args);
```

**参数**

*`position`*\
中 [`vector`](../standard-library/vector-class.md) 插入第一个元素的位置。

*`args`*\
构造函数参数。 函数根据所提供的自变量来推断要调用的构造函数重载。

**返回值**

该函数将返回一个指向 `vector` 中新元素的插入位置的迭代器。

**注解**

任何插入操作都可能会消耗大量资源，请参阅[ `vector` 类](../standard-library/vector-class.md)以了解性能的讨论 `vector` 。

### emplace_back

将一个就地构造的元素添加到向量末尾。

```cpp
template <class... Types>
void emplace_back(Types&&... args);
```

**参数**

*`args`*\
构造函数参数。 函数根据所提供的自变量来推断要调用的构造函数重载。

###  empty

测试矢量是否为空。

```cpp
bool empty() const;
```

**返回值**

**`true`** 如果向量为空，则为; 否则为。 **`false`** 如果矢量不为空，则为。

### end

返回后端迭代器，该迭代器指向矢量最后一个元素之后的元素。

```cpp
iterator end();

const_iterator end() const;
```

**返回值**

向量的过去的迭代器。 它指向矢量最后一个元素之后的元素。 该元素是占位符，不应取消引用。 仅将它用于比较。 如果向量为空，则为 `vector::end() == vector::begin()` 。

**注解**

如果将的返回值 `end` 分配给类型的变量 `const_iterator` ，则不能修改矢量对象。 如果将的返回值 `end` 分配给类型的变量 `iterator` ，则可以修改矢量对象。

### erase

从指定位置删除向量中的一个元素或一系列元素。

```cpp
iterator erase(
    const_iterator position);

iterator erase(
    const_iterator first,
    const_iterator last);
```

**参数**

*`position`*\
要从向量中移除的元素的位置。

*`first`*\
要从向量中移除的第一个元素的位置。

*`last`*\
紧接要从向量中移除的最后一个元素的位置。

**返回值**

一个迭代器，它指定已移除的任何元素之外保留的第一个元素或指向向量末尾的指针（若此类元素不存在）。

### front

返回对向量中第一个元素的引用。

```cpp
reference front();

const_reference front() const;
```

**返回值**

对向量对象中第一个元素的引用。 如果向量为空，则返回值不确定。

**注解**

如果将的返回值 `front` 分配给 `const_reference` ，则无法修改矢量对象。 如果将的返回值 `front` 分配给 **`reference`** ，则可以修改矢量对象。

当使用 `_ITERATOR_DEBUG_LEVEL` 定义为1或2的进行编译时，如果尝试访问空向量中的元素，将发生运行时错误。 有关详细信息，请参阅 检查迭代器。

### get_allocator

返回用于构造矢量的分配器对象的一个副本。

```cpp
Allocator get_allocator() const;
```

**返回值**

向量所使用的分配器。

**注解**

矢量类的分配器指定类管理存储的方式。 C++ 标准库容器类提供的默认分配器足以满足大多编程需求。 编写和使用你自己的分配器类是高级 c + + 功能。

### insert

将一个元素或多个元素或一系列元素插入到指定位置的向量中。

```cpp
iterator insert(
    const_iterator position,
    const Type& value);

iterator insert(
    const_iterator position,
    Type&& value);

void insert(
    const_iterator position,
    size_type count,
    const Type& value);

template <class InputIterator>
void insert(
    const_iterator position,
    InputIterator first,
    InputIterator last);
```

**参数**

*`position`*\
向量中插入第一个元素的位置。

*`value`*\
插入到向量中的元素的值。

*`count`*\
插入向量中的元素数目。

*`first`*\
要复制的范围元素中的第一个元素的位置。

*`last`*\
要复制的元素范围以外的第一个元素的位置。

**返回值**

前两个 `insert` 函数返回一个指定新元素插入到向量的位置的迭代器。

**注解**

作为前置条件， *`first`* *`last`* 不能将迭代器放入矢量，或者行为未定义。 任何插入操作都可能会消耗大量资源，请参阅 `vector` 类以了解性能的讨论 `vector` 。

### iterator

一个类型，它提供可读取或修改向量中任何元素的随机访问迭代器。

```cpp
typedef implementation-defined iterator;
```

**注解**

类型 **`iterator`** 可用于修改元素的值。

### max_size

返回向量的最大长度。

```cpp
size_type max_size() const;
```

**返回值**

向量的最大可取长度。

### operator[]

返回对指定位置的矢量元素的引用。

```cpp
reference operator[](size_type position);

const_reference operator[](size_type position) const;
```

**参数**

*`position`*\
矢量元素的位置。

**返回值**

如果指定的位置大于或等于容器大小，则结果为 undefined。

**注解**

如果将的返回值 `operator[]` 分配给 `const_reference` ，则无法修改矢量对象。 如果将 `operator[]` 的返回值分配给引用，则可以修改矢量对象。

当使用 [`_ITERATOR_DEBUG_LEVEL`](../standard-library/iterator-debug-level.md) 定义为1或2的进行编译时，如果尝试访问向量边界之外的元素，将发生运行时错误。 有关详细信息，请参阅 [检查迭代](../standard-library/checked-iterators.md)器。

### operator=

用另一个向量的副本替换该向量中的元素。

```cpp
vector& operator=(const vector& right);

vector& operator=(vector&& right);
```

**参数**

*`right`*\
[`vector`](../standard-library/vector-class.md)要复制到中的 `vector` 。

**注解**

清除中的任何现有元素后 `vector` ，会 `operator=` 将的内容复制或移动 *`right`* 到 `vector` 。

###  pointer

一个类型，提供指向向量中元素的指针。

```cpp
typedef typename Allocator::pointer pointer;
```

**注解**

类型 **`pointer`** 可用于修改元素的值。

### pop_back

删除矢量末尾处的元素。

```cpp
void pop_back();
```

**注解**

有关代码示例，请参阅 [vector::push_back()](#push_back)。

###  push_back

在矢量末尾处添加一个元素。

```cpp
void push_back(const T& value);

void push_back(T&& value);
```

**参数**

*`value`*\
要赋给添加到矢量末尾处的元素的值。

###  rbegin

返回指向反向向量中第一个元素的迭代器。

```cpp
reverse_iterator rbegin();
const_reverse_iterator rbegin() const;
```

**返回值**

发现反向矢量中的第一个元素或发现曾是非反向矢量中的最后一个元素的反向双向迭代器。

**注解**

如果将的返回值 `rbegin` 分配给 `const_reverse_iterator` ，则无法修改矢量对象。 如果将 `rbegin` 的返回值分配给 `reverse_iterator`，则可以修改矢量对象。

### reference

一个类型，它提供对向量中存储的元素的引用。

```cpp
typedef typename Allocator::reference reference;
```



###  rend

返回一个后端反向迭代器，该迭代器指向反向向量的最后一个元素之后的元素。

```cpp
const_reverse_iterator rend() const;
reverse_iterator rend();
```

**返回值**

反向向量的反向结束迭代器。 它指向反向向量的最后一个元素之后的元素，该元素与非反转向量的第一个元素之前的元素相同。 该元素是占位符，不应取消引用。 仅将它用于比较。

**注解**

`rend` 与反向矢量一起使用，就像 [`end`](#end) 与矢量一起使用一样。

如果将的返回值 `rend` 分配给 `const_reverse_iterator` ，则无法修改矢量对象。 如果将 `rend` 的返回值分配给 `reverse_iterator`，则可以修改矢量对象。

`rend` 可用于测试反向迭代器是否已到达其矢量末尾。

返回的值 `rend` 不应被取消引用。 仅将它用于比较。

###  reserve

为向量对象保留最小的存储长度，必要时为其分配空间。

```cpp
void reserve(size_type count);
```

**参数**

*`count`*\
要分配给向量的最小存储长度。

###  resize

为矢量指定新的大小。

```cpp
void resize(size_type new_size);
void resize(size_type new_size, Type value);
```

**参数**

*`new_size`*\
矢量的新大小。

*`value`*\
新大小大于旧大小时添加至矢量的新元素的初始化值。 如果省略该值，则新对象将使用其默认构造函数。

**注解**

如果容器的大小小于请求的大小，则 *`new_size`* `resize` 会将元素添加到向量，直到达到请求的大小。 如果容器的大小大于请求的大小，则 `resize` 删除接近容器末尾的元素，直到达到大小 *`new_size`* 。 如果容器的当前大小与请求的大小相同，则不执行任何操作。

[`size`](#size) 反映矢量的当前大小。

### reverse_iterator

一个类型，它提供可读取或修改反向矢量中的任意元素的随机访问迭代器。

```cpp
typedef std::reverse_iterator<iterator> reverse_iterator;
```

**注解**

`reverse_iterator` 类型用于反向循环访问向量。



###  shrink_to_fit

放弃额外容量。

```cpp
void shrink_to_fit();
```

### size

返回向量中的元素数量。

```cpp
size_type size() const;
```

**返回值**

向量的当前长度。

### size_type

一个类型，它计算矢量中的元素数目。

```cpp
typedef typename Allocator::size_type size_type;
```



###  swap

交换两个向量的元素。

```cpp
void swap(
    vector<Type, Allocator>& right);

friend void swap(
    vector<Type, Allocator>& left,
    vector<Type, Allocator>& right);
```

**参数**

*`right`*\
提供要交换的元素的向量。 或，其元素要与矢量中的元素进行交换的向量 *`left`* 。

*`left`*\
一个向量，其元素将与矢量中的元素进行交换 *`right`* 。

### value_type

一个类型，它代表向量中存储的数据类型。

```cpp
typedef typename Allocator::value_type value_type;
```

**注解**

`value_type` 是模板参数 `Type` 的同义词。

### vector

构造向量。 重载构造特定大小的向量，或具有特定值的元素。 或作为某些其他矢量的全部或部分的副本。 某些重载还允许您指定要使用的分配器。

```cpp
vector();
explicit vector(const Allocator& allocator);
explicit vector(size_type count);
vector(size_type count, const Type& value);
vector(size_type count, const Type& value, const Allocator& allocator);

vector(const vector& source);
vector(vector&& source);
vector(initializer_list<Type> init_list, const Allocator& allocator);

template <class InputIterator>
vector(InputIterator first, InputIterator last);
template <class InputIterator>
vector(InputIterator first, InputIterator last, const Allocator& allocator);
```

**参数**

*`allocator`*\
要用于此对象的分配器类。 [`get_allocator`](#get_allocator) 返回对象的分配器类。

*`count`*\
构造的矢量中的元素数。

*`value`*\
构造的矢量中的元素值。

*`source`*\
要成为副本的构造的矢量中的矢量。

*`first`*\
要复制的元素范围内的第一个元素的位置。

*`last`*\
要复制的元素范围外的第一个元素的位置。

*`init_list`*\
`initializer_list`包含要复制的元素的。

**注解**

所有构造函数都存储分配器对象 (*`allocator`*) 并初始化向量。

前两个构造函数指定一个空初始矢量。 第二个构造函数显式指定要使用) 的分配器类型 (*`allocator`* 。

第三个构造函数指定 (*`count`* 类的默认值元素) 元素的重复 `Type` 。

第四个和第五个构造函数指定 (*`count`*) 的值元素的重复 *`value`* 。

第六个构造函数指定矢量的副本 *`source`* 。

第七个构造函数移动矢量 *`source`* 。

第八个构造函数使用 initializer_list 指定元素。

第九个和第十个构造函数复制矢量的范围（`first`、`last`）。



## list

C + + 标准库列表类是序列容器的类模板，用于在线性排列中维护其元素，并允许在序列内的任何位置高效插入和删除。 序列存储为双向链接的元素列表，每个包含一些 *Type* 类型的成员。

```cpp
template <class Type, class Allocator= allocator<Type>>
class list
```



容器类型选择通常应根据应用程序所需的搜索和插入的类型。 当对任何元素的随机访问超出限制并且仅要求在序列的末尾插入或删除元素时，矢量应作为用于管理序列的首选容器。 当需要随机访问并且在序列起始处和末尾处插入和删除元素已到达极限时，应首选类 deque 容器进行操作。

列表成员函数 [merge](#merge)、[reverse](#reverse)、[unique](#unique)、[remove](#remove) 和 [remove_if](#remove_if) 已针对对列表的操作进行了优化，它们可作为泛型对应函数的高性能替代函数。

当成员函数必须插入或删除列表中的元素时，将发生列表的重新分配。 在所有这类情况下，仅指向受控制序列被消除部分的迭代器或引用将变为无效。

包括 c + + 标准库标准标头 \<list> 以定义 [容器](../standard-library/stl-containers.md) 类模板列表和多个支持模板。



**构造函数**

| 名称          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| [list](#list) | 构造一个列表，它具有特定大小或它的元素具有特定值，或具有特定 `allocator` 或作为某个其他列表副本。 |

**Typedef**

| 名称                                              | 描述                                                         |
| ------------------------------------------------- | ------------------------------------------------------------ |
| [allocator_type](#allocator_type)                 | 表示列表对象的 `allocator` 类的类型。                        |
| [const_iterator](#const_iterator)                 | 一种类型，它提供可读取列表中元素的双向迭代器 **`const`** 。  |
| [const_pointer](#const_pointer)                   | 提供指向列表中元素的指针的类型 **`const`** 。                |
| [const_reference](#const_reference)               | 一种类型，它提供对 **`const`** 存储在列表中用于读取和执行操作的元素的引用 **`const`** 。 |
| [const_reverse_iterator](#const_reverse_iterator) | 一种类型，它提供可读取列表中任何元素的双向迭代器 **`const`** 。 |
| [difference_type](#difference_type)               | 提供引用同一列表中的元素的两个迭代器之间的差异的类型。       |
| [iterator](#iterator)                             | 提供可读取或修改列表中任何元素的双向迭代器的类型。           |
| [pointer](#pointer)                               | 提供指向列表中元素的指针的类型。                             |
| [reference](#reference)                           | 一种类型，它提供对 **`const`** 存储在列表中用于读取和执行操作的元素的引用 **`const`** 。 |
| [reverse_iterator](#reverse_iterator)             | 提供可读取或修改反向列表中的元素的双向迭代器的类型。         |
| [size_type](#size_type)                           | 计算列表中元素的数目的类型。                                 |
| [value_type](#value_type)                         | 表示列表中存储的数据类型的类型。                             |





**函数**

| 名称                            | 描述                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| [assign](#assign)               | 将元素从列表中擦除并将一组新的元素复制到目标列表。           |
| [back](#back)                   | 返回对列表中最后一个元素的引用。                             |
| [back](#back)                   | 返回发现列表中第一个元素的位置的迭代器。                     |
| [cbegin](#cbegin)               | 返回发现列表中第一个元素的位置的常量迭代器。                 |
| [cend](#cend)                   | 返回发现一个列表中最后一个元素之后的位置的敞亮表达式。       |
| [clear](#clear)                 | 消除列表中的全部元素。                                       |
| [crbegin](#crbegin)             | 返回发现反向列表中第一个元素的位置的常量迭代器。             |
| [crend](#crend)                 | 返回用于发现反向列表中最后一个元素之后的位置的常量迭代器。   |
| [emplace](#emplace)             | 将构造的元素插入到列表中的指定位置。                         |
| [emplace_back](#emplace_back)   | 在列表的结尾处添加一个就地构造的元素。                       |
| [emplace_front](#emplace_front) | 在列表的起始位置添加一个就地构造的元素。                     |
| [empty](#empty)                 | 测试列表是否为空。                                           |
| [end](#end)                     | 返回用于发现列表中最后一个元素之后的位置的迭代器。           |
| [erase](#erase)                 | 从列表中的指定位置移除一个或一系列元素。                     |
| [front](#front)                 | 返回对列表中第一个元素的引用。                               |
| [get_allocator](#get_allocator) | 返回用于构造列表的 `allocator` 对象的一个副本。              |
| [insert](#insert)               | 将一个、几个或一系列元素插入列表中的指定位置。               |
| [max_size](#max_size)           | 返回列表的最大长度。                                         |
| [merge](#merge)                 | 将元素从参数列表移除，将它们插入目标列表，将新的组合元素集以升序或其他指定顺序排序。 |
| [pop_back](#pop_back)           | 删除列表末尾的元素。                                         |
| [pop_front](#pop_front)         | 删除列表起始处的一个元素。                                   |
| [push_back](#push_back)         | 在列表的末尾添加元素。                                       |
| [push_front](#push_front)       | 在列表的开头添加元素。                                       |
| [rbegin](#rbegin)               | 返回发现反向列表中第一个元素的位置的迭代器。                 |
| [remove](#remove)               | 清除列表中与指定值匹配的元素。                               |
| [remove_if](#remove_if)         | 将满足指定谓词的元素从列表中消除。                           |
| [rend](#rend)                   | 返回发现反向列表中最后一个元素之后的位置的迭代器。           |
| [resize](#resize)               | 为列表指定新的大小。                                         |
| [reverse](#reverse)             | 反转列表中元素的顺序。                                       |
| [size](#size)                   | 返回列表中元素的数目。                                       |
| [sort](#sort)                   | 按升序或其他顺序关系排列列表中的元素。                       |
| [splice](#splice)               | 将元素从自变量列表中删除或将它们插入目标列表。               |
| [swap](#swap)                   | 交换两个列表的元素。                                         |
| [unique](#unique)               | 从列表中删除满足某些其他二元谓词的相邻重复元素或相邻元素。   |

### 运算符

| 名称                 | 描述                                 |
| -------------------- | ------------------------------------ |
| [operator =](#op_eq) | 用另一个列表的副本替换列表中的元素。 |





## deque

