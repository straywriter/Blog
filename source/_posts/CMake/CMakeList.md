---
title: CMake list 使用
top: false
mathjax: true
date: 2021-05-18 21:59:11
categories:
- CMake
---

-----

## list

```cmake
Reading
  list(LENGTH <list> <out-var>)
  list(GET <list> <element index> [<index> ...] <out-var>)
  list(JOIN <list> <glue> <out-var>)
  list(SUBLIST <list> <begin> <length> <out-var>)

Search
  list(FIND <list> <value> <out-var>)

Modification
  list(APPEND <list> [<element>...])
  list(FILTER <list> {INCLUDE | EXCLUDE} REGEX <regex>)
  list(INSERT <list> <index> [<element>...])
  list(POP_BACK <list> [<out-var>...])
  list(POP_FRONT <list> [<out-var>...])
  list(PREPEND <list> [<element>...])
  list(REMOVE_ITEM <list> <value>...)
  list(REMOVE_AT <list> <index>...)
  list(REMOVE_DUPLICATES <list>)
  list(TRANSFORM <list> <ACTION> [...])

Ordering
  list(REVERSE <list>)
  list(SORT <list> [...])
```



### **LENGTH 获取list长度**

```cmake
list(LENGTH <list> <out-var>)
```

> `<output variable>`为新创建的变量，用于存储列表的长度。

```cmake
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d) # 创建列表变量"a;b;c;d"
list (LENGTH list_test length)
message (">>> LENGTH: ${length}")
```

```
# 输出
>>> LENGTH: 4
```

### **GET 读取列表中指定索引的的元素，可以指定多个索引。**

```
list(GET <list> <element index> [<index> ...] <out-var>)
```

> `<element index>`为列表元素的索引，从0开始编号，索引0的元素为列表中的第一个元素；索引也可以是负数，`-1`表示列表的最后一个元素，`-2`表示列表倒数第二个元素，以此类推。注意：当索引（不管是正还是负）超过列表的长度，运行会报错（`list index: XX out of range`）。
>  `<output variable>`为新创建的变量，存储指定索引元素的返回结果，也是一个列表。

```
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d) # 创建列表变量"a;b;c;d"
list (GET list_test 0 1 -1 -2 list_new)
message (">>> GET: ${list_new}")
```

```
# 输出
>>> GET: a;b;d;c
```

### **JOIN 用于将列表中的元素用连接字符串连接起来组成一个字符串**

- 注意，此时返回的结果已经不是一个列表。

```
list(JOIN <list> <glue> <out-var>)
```

> 将列表中的元素用`<glue>`链接起来，组成一个字符串后，返回给`<output variable>`变量。对于不属于列表的多个字符串的连接操作，可以使用`string()`命令的连接操作。

```cmake
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d) # 创建列表变量"a;b;c;d"
list (JOIN list_test -G- list_new)
message (">>> JOIN: ${list_new}")
```

```
# 输出
>>> JOIN: a-G-b-G-c-G-d
```

### **SUBLIST 子命令SUBLIST用于获取列表中的一部分**

```
list(SUBLIST <list> <begin> <length> <out-var>)
```

> 返回列表`<list>`中，从索引`<begin>`开始，长度为`<length>`的子列表。如果长度`<length>`为0，返回的时空列表。如果长度`<length>`为-1或列表的长度小于`<begin>+<length>`，那么将列表中从`<begin>`索引开始的剩余元素返回。

```cmake
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d) # 创建列表变量"a;b;c;d"
list (SUBLIST list_test 1 2 list_new)
message (">>> SUBLIST: ${list_new}")
list (SUBLIST list_test 1 0 list_new)
message (">>> SUBLIST: ${list_new}")
list (SUBLIST list_test 1 -1 list_new)
message (">>> SUBLIST: ${list_new}")
list (SUBLIST list_test 1 8 list_new)
message (">>> SUBLIST: ${list_new}")
```

```
# 输出
>>> SUBLIST: b;c
>>> SUBLIST:
>>> SUBLIST: b;c;d
>>> SUBLIST: b;c;d
```

### FIND 查找列表是否存在指定的元素

```
list(FIND <list> <value> <out-var>)
```

> 如果列表`<list>`中存在`<value>`，那么返回`<value>`在列表中的索引，如果未找到则返回-1。

```
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d) # 创建列表变量"a;b;c;d"
list (FIND list_test d list_index)
message (">>> FIND 'd': ${list_index}")
list (FIND list_test e list_index)
message (">>> FIND 'e': ${list_index}")
```

```
# 输出
>>> FIND 'd': 3
>>> FIND 'e': -1
```



### APPEND 将元素追加到列表

```
list(APPEND <list> [<element>...])
```

> 此命令会改变原列表的值。

```
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d) # 创建列表变量"a;b;c;d"
list (APPEND list_test 1 2 3 4)
message (">>> APPEND: ${list_test}")
```

```
# 输出
>>> APPEND: a;b;c;d;1;2;3;4
```



### FILTER 用于根据正则表达式包含或排除列表中的元素

```
list(FILTER <list> {INCLUDE | EXCLUDE} REGEX <regex>)
```

> 根据模式的匹配结果，将元素添加（`INCLUDE`选项）到列表或者从列表中排除（`EXCLUDE`选项）。此命令会改变原来列表的值。模式`REGEX`表明会对列表进行正则表达式匹配。（从官方文档目前未找到其他模式）

```cmake
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d 1 2 3 4) # 创建列表变量"a;b;c;d;1;2;3;4"
message (">>> the LIST is: ${list_test}")
list (FILTER list_test INCLUDE REGEX [a-z])
message (">>> FILTER: ${list_test}")
list (FILTER list_test EXCLUDE REGEX [a-z])
message (">>> FILTER: ${list_test}")
```

```
# 输出
>>> the LIST is: a;b;c;d;1;2;3;4
>>> FILTER: a;b;c;d
>>> FILTER: 
```

### INSERT 在指定位置将元素（一个或多个）插入到列表中

```
list(INSERT <list> <index> [<element>...])
```

> `<element_index>`为列表指定的位置，如果元素的位置超出列表的范围，会报错。此命令会改变原来列表的值。

```
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d) # 创建列表变量"a;b;c;d"
list (INSERT list_test 0 8 8 8 8)
message (">>> INSERT: ${list_test}")
list (INSERT list_test -1 9 9 9 9)
message (">>> INSERT: ${list_test}")
list (LENGTH list_test lenght)
list (INSERT list_test ${length} 0)
message (">>> INSERT: ${list_test}")
```

```
# 输出
>>> INSERT: 8;8;8;8;a;b;c;d
>>> INSERT: 8;8;8;8;a;b;c;9;9;9;9;d
>>> INSERT: 8;8;8;8;a;b;c;9;9;9;9;d;0
```



### POP_BACK 将列表中最后元素移除

```
list(POP_BACK <list> [<out-var>...])
```

> `<out-var>`如果未指定输出变量，则仅仅是将原列表的最后一个元素移除。如果指定了输出变量，则会将最后一个元素移入到该变量，并将元素从原列表中移除。如果指定了多个输出变量，则依次将原列的最后一个元素移入到输出变量中，如果输出变量个数大于列表的长度，那么超出部分的输出变量未定义。

```bash
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d) # 创建列表变量"a;b;c;d"
list (POP_BACK list_test)
message (">>> POP_BACK: ${list_test}")
list (POP_BACK list_test outvar1 outvar2 outvar3 outvar4)
message (">>> POP_BACK: ${outvar1}、${outvar2}、${outvar3}、${outvar4}")
```

```ruby
# 输出
>>> POP_BACK: a;b;c
>>> POP_BACK: c、b、a、
```

### POP_FRONT  列表中第一个元素移除

```
list(POP_FRONT <list> [<out-var>...])
```

> `<out-var>`如果未指定输出变量，则仅仅是将原列表的第一个元素移除。如果指定了输出变量，则会将第一个元素移入到该变量，并将元素从原列表中移除。如果指定了多个输出变量，则依次将原列的第一个元素移入到输出变量中，如果输出变量个数大于列表的长度，那么超出部分的输出变量未定义。

```bash
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d) # 创建列表变量"a;b;c;d"
list (POP_FRONT list_test)
message (">>> POP_FRONT: ${list_test}")
list (POP_FRONT list_test outvar1 outvar2 outvar3 outvar4)
message (">>> POP_FRONT: ${outvar1}、${outvar2}、${outvar3}、${outvar4}")
```

```ruby
# 输出
>>> POP_FRONT: b;c;d
>>> POP_FRONT: b、c、d、
```

### PREPEND 将元素插入到列表的0索引位置

```
list(PREPEND <list> [<element>...])
```

> 如果待插入的元素是多个，则相当于把多个元素整体“平移”到原列表0索引的位置。

```bash
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d) # 创建列表变量"a;b;c;d"
list (PREPEND list_test z)
message (">>> PREPEND: ${list_test}")
list (PREPEND list_test p q r s t)
message (">>> POP_FRONT: ${list_test}")
```

```ruby
# 输出
>>> PREPEND: z;a;b;c;d
>>> PREPEND: p;q;r;s;t;z;a;b;c;d
```

### REMOVE_ITEM 将指定的元素从列表中移除

```
list(REMOVE_ITEM <list> <value>...)
```

> 注意：指定的是元素的值，当指定的值在列表中存在重复的时候，会删除所有重复的值。

```bash
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a a b c c d) # 创建列表变量"a;a;b;c;c;d"
list (REMOVE_ITEM list_test a)
message (">>> REMOVE_ITEM: ${list_test}")
list (REMOVE_ITEM list_test b e)
message (">>> REMOVE_ITEM: ${list_test}")
```



```ruby
# 输出
>>> REMOVE_ITEM: b;c;c;d
>>> REMOVE_ITEM: c;c;d
```

### REMOVE_AT 将指定索引的元素从列表中移除

```
list(REMOVE_AT <list> <index>...)
```

> 注意：指定的是元素的索引，当指定的索引不存在的时候，会提示错误；如果指定的索引存在重复，则只会执行一次删除动作。

```bash
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d e f) # 创建列表变量"a;b;c;d;e;f"
list (REMOVE_AT list_test 0 -1)
message (">>> REMOVE_AT: ${list_test}")
list (REMOVE_AT list_test 1 1 1 1)
message (">>> REMOVE_AT: ${list_test}")
```



```ruby
# 输出
>>> REMOVE_AT: b;c;d;e
>>> REMOVE_AT: b;d;e
```

### REMOVE_DUPLICATES

```
list(REMOVE_DUPLICATES <list>)
```

> 移除列表中的重复元素

```bash
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a a a b b b c c c d d d) # 创建列表变量"a;a;a;b;b;b;c;c;c;d;d;d;"
list (REMOVE_DUPLICATES list_test)
message (">>> REMOVE_DUPLICATES: ${list_test}")
set (list_test a a a a) 
list (REMOVE_DUPLICATES list_test)
message (">>> REMOVE_DUPLICATES: ${list_test}")
set (list_test a b c a d a e a f)  # 多个元素重复，只保留第一个 
list (REMOVE_DUPLICATES list_test)
message (">>> REMOVE_DUPLICATES: ${list_test}")
```

```ruby
# 输出
>>> REMOVE_DUPLICATES: a;b;c;d
>>> REMOVE_DUPLICATES: a
>>> REMOVE_DUPLICATES: a;b;c;d;e;f
```

### TRANSFORM  将指定的动作运用到所有或者部分指定的元素，结果可以存到原列表中，或存到指定输出新的变量中。

```
list(TRANSFORM <list> <ACTION> [...])
```

> 选项`ACTION`用于指定应用到列表元素的动作，动作必须在如下中选择一个：`APPEND`/`PREPEND`/`TOUPPER`/`TOUPPER`/`STRIP`/`GENEX_STRIP`/`REPLACE`；选项`SELECTOR`用于指定哪些列表元素会被选择，并执行动作，`SELECTOR`只能从如下中选择一个：`AT`/`FOR`/`REGEX`；选项`OUTPUT_VARIABLE`用于指定新的输出变量。  `TRANSFORM`命令不会改变列表中元素的个数。

```bash
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test a b c d)
list (TRANSFORM list_test APPEND B OUTPUT_VARIABLE list_test_out)
list (TRANSFORM list_test APPEND B)
message (">>> TRANSFORM-APPEND: ${list_test} --- ${list_test_out}")
set (list_test a b c d)
list (TRANSFORM list_test PREPEND F OUTPUT_VARIABLE list_test_out)
list (TRANSFORM list_test PREPEND F)
message (">>> TRANSFORM-PREPEND: ${list_test} --- ${list_test_out}")
```

```ruby
# 输出
>>> TRANSFORM-APPEND: aB;bB;cB;dB --- aB;bB;cB;dB
>>> TRANSFORM-PREPEND: Fa;Fb;Fc;Fd --- Fa;Fb;Fc;Fd
```

### REVERSE 将整个列表反转

```
list(REVERSE <list>)
```

> 反转列表。

```bash
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test aa bb cc dd) 
message (">>> list: ${list_test}")
list (REVERSE list_test)
message (">>> REVERSE: ${list_test}")
```

```ruby
# 输出
>>> list: aa;bb;cc;dd
>>> REVERSE: dd;cc;bb;aa
```

### SORT 对列表进行排序

```
list (SORT <list> [COMPARE <compare>] [CASE <case>] [ORDER <order>])
```

> **`COMPARE`**：指定排序方法。有如下几种值可选：1）`STRING`:按照字母顺序进行排序，为默认的排序方法；2）`FILE_BASENAME`：如果是一系列路径名，会使用basename进行排序；3）`NATURAL`：使用自然数顺序排序。
>
> **`CASE`**：指明是否大小写敏感。有如下几种值可选：1）`SENSITIVE`:按照大小写敏感的方式进行排序，为默认值；2）`INSENSITIVE`：按照大小写不敏感方式进行排序。
>
> **`ORDER`**：指明排序的顺序。有如下几种值可选：1）`ASCENDING`:按照升序排列，为默认值；2）`DESCENDING`：按照降序排列。

```bash
# CMakeLists.txt
cmake_minimum_required (VERSION 3.12.2)
project (list_cmd_test)
set (list_test 3 1 1.1 10.1 3.4 9)
list (SORT list_test) # 以字母顺序，按照大小写不敏感方式，升序排列
message (">>> SORT STRING-SENSITIVE-ASCENDING: ${list_test}")
list (SORT list_test COMPARE NATURAL ORDER DESCENDING) # 以自然顺序，降序排列
message (">>> SORT STRING-SENSITIVE-DESCENDING: ${list_test}")
```

```css
# 输出
>>> SORT STRING-SENSITIVE-ASCENDING: 1;1.1;10.1;3;3.4;9
>>> SORT STRING-SENSITIVE-DESCENDING: 10.1;9;3.4;3;1.1;1
```

## 相关参考

[cmaek list](https://cmake.org/cmake/help/latest/command/list.html)

[Cmake命令之list介绍](https://www.jianshu.com/p/89fb01752d6f)

