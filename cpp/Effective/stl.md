# Effective STL

## 目录

[容器](#容器)  
[vector 和 string](#vector-和-string)  
[关联容器](#关联容器)  
[迭代器](#迭代器)  
[算法](#算法)  
[仿函数、仿函数类、函数等](#仿函数仿函数类函数等)  
[使用 STL 编程](#使用-stl-编程)

## 容器

### 条款 01：慎重选择容器类型

C++ 提供了几种不同的容器供你选择：

- 标准 STL 序列容器：vector、string、deque、list
- 标准 STL 关联容器 ：set、multiset、map、multimap
- 非标准序列容器：slist、rope
- 非标准关联容器：hash_set、hash_multiset、hash_map、hash_multimap

### 条款 02：不要试图编写独立于容器类型的代码

STL 是以泛化（generalization）原则为基础的：数组被泛化为“以其包含的对象的类型为参数”的**容器**；函数被泛化为“以其使用的迭代器的类型为参数”的**算法**；指针被泛化为“以其指向的对象的类型为参数”的**迭代器**。

容器类型被泛化为序列和关联容器，类似的容器被赋予相似的功能。不同的容器是不同的，它们并不是被设计来交换使用的，试图编写对序列容器和关联容器都适用的代码几乎是毫无意义的。

### 条款 03：确保容器中的对象副本正确而高效

复制对象是 STL 的工作方式：进去的是副本，出来的也是副本（copy in, copy out），移动元素也是复制。对象的拷贝构造函数可能会消耗大量时间。通常，用容器保存**指针**比保存对象更好，保存**智能指针**比原生指针好。

### 条款 04：调用 empty 而不是检查 size()是否为 0

empty 对所有的标准容器都是常数时间操作，而对一些 list 实现，size 耗费线性时间。

### 条款 05：区间成员函数优先于与之对应的单元素成员函数

给容器赋值、插入或删除时应该使用 assign、insert 和 erase 这些区间成员函数，而不是循环操纵单元素。

### 条款 06：当心 C++编译器最烦人的分析机制

### 条款 07：如果容器中包含了通过 new 操作创建的指针，切记在容器对象析构前将指针 delete 掉

_注意：更好的做法是使用智能指针。_

### ~~条款 08：切勿创建包含 auto_ptr 的容器对象~~

### 条款 09：慎重选择删除元素的方法

对于不同的类型容器，有不同的删除方法。

循环删除容器中的元素时需要注意：调用`erase(it)`后，迭代器 it 会失效，无法获取下一个元素。

对于关联容器，删除，插入操作会导致指向该元素的迭代器失效，其他元素迭代器不受影响。最简单的办法是，当调用时对 it 使用后缀递增。

```cpp
for(auto it = v.begin(); it != v.end(); ){
    v.erase(it++);
}
```

对于顺序容器，删除、插入操作会导致指向该元素以及后面的元素的迭代器失效。需要利用 erase 的返回值获取下一个元素的迭代器。

```cpp
for(auto it = v.begin(); it != v.end(); ){
    it = v.erase(it);
    // erase()返回指向下一个有效元素的迭代器
}
```

### 条款 10：了解分配器（allocator）的约定和限制

### 条款 11：理解自定义分配器的合理用法

### 条款 12：切勿对 STL 容器的线程安全性有不切实际的依赖

## vector 和 string

### 条款 13：用 vector 和 string 替代动态分配的数组

### 条款 14：使用 reserve 来避免不必要的重新分配

当 STL 容器需要更多的空间时，操作如下：

1. 分配一块大小为当前容量的某个倍数的新内存（通常为 2 倍）；
2. 把容器的所有元素从旧的内存复制到新的内存中；
3. 析构掉旧内存中的对象；
4. 释放旧内存。

为了避免这些非常耗时的步骤，需要使用 reserve()。

### 条款 15：注意 string 实现的多样性

### 条款 16：了解如何把 vector 和 string 数据传给旧 API

### 条款 17：使用“swap 技巧”除去多余的容量

利用利用匿名容器会自动析构这个特点，可以压缩 vector 占用的内存：

```cpp
vector<int> v;
// 容器操作
vector<int>(v).swap(v);
// 利用匿名容器拷贝v的内容，再和v进行交换
```

### 条款 18：避免使用`vector<bool>`

`vector<bool>`不完全满足 STL 容器的要求，可以用`deque<bool>`和 bitset 来替代它。

## 关联容器

### 条款 19：理解相等（equality）和等价（equivalence）的区别

标准关联容器总是保持排列顺序的，所以每个容器必须有一个比较函数（默认为 less）来决定保持怎样的顺序。当 STL 中需要相等判断时，一般的惯
例是直接调用 operator==，比如非成员函数 find。

### 条款 20：为包含指针的关联容器指定比较类型

当创建包含指针的关联容器时，容器将会按照指针的值进行排序，而不是按照指向的对象。因此需要创建自己的函数对象作为该容器的比较函数。

```cpp
auto sortFunc = [](const std::string* pLeft, const std::string* pRight) {return *pLeft < *pRight;  };
std::set<std::string*, sortFunc> datas;
```

### 条款 21：总是让比较函数在等值情况下返回 false

比较函数的返回值表明的是按照该函数定义的排列顺序：一个值是否在另外一个之前。相等的值从来不会有前后顺序关系，所以，对于相等的值，比较函数应该始终返回 false。

### 条款 22：切勿直接修改 set 或 multiset 中的 key

像所有的标准关联容器一样，set 和 multiset 按照一定的顺序来存放自己的元素。如果把关联容器中的一个 key 改变了，这将会打破容器的有序性。修改关联容器中的 key 有以下步骤：

1. 找到你想修改该的容器的元素；
2. 为将要被修改的元素做一份拷贝；
3. 修改该拷贝，使它具有你期望它在容器中的值；
4. 把该元素从容器中删除，通常是通过调用 erase 来进行的；
5. 插入新元素。

```cpp
// 将data中key为old的元素改为new
auto iter = datas.find("old");
if (iter != datas.end())
{
    auto curData = *iter;
    curData.setName("new");
    datas.erase(iter);
    datas.insert(curDatas);
}
```

### 条款 23：考虑用排序的 vector 替代关联容器

如果对于集合的的操作固定，例如在一个阶段都是查找操作，那么可以考虑使用 vector 来存储`std::pair<type1, type2>`（查找前进行排序）。因为关联容器的底层实现使用了红黑树，存储同样元素数量的关联容器占用内存比顺序容器大。排序后进行二分查找速度相近。

如果插入、查找、删除交替进行，那么关联容器更好。

### 条款 24：当效率至关重要时，请在 map::operator[]与 map::insert 之间谨慎作出选择

当用 operator[]向 map 中插入元素时，会默认构造一个对象，再赋值；而 insert 直接在 map 中构造。

因此，当先 map 中添加元素时，要优先选用 insert，而不是 operator[]；当更新已经在 map 中的元素的值时，要优先选择 operator[]（避免歧义）。

### 条款 25：熟悉非标准的哈希容器

注意 hash 容器与关联容器的区别:

- hash 容器是基于 hash 函数的，hash 容器对相同元素的判断是基于相等，默认为 std::equal_to<>。其元素不是以排序方式存放的。
- 关联容器是基于红黑树的，对相同元素的判断基于等价，默认为 std::less<>。元素是基于排序存放的。

## 迭代器

### 条款 26：iterator 优先于 const_iterator、reverse_iterator 以及 const_reverse_iterator

### 条款 27：使用 distance 和 advance 将容器的 const_iterator 转换成 iterator

### 条款 28：正确理解由 reverse_iterator 的 base()成员函数所产生的 iterator 的用法

### 条款 29：对于逐个字符的输入请考虑使用 istreambuf_iterator

## 算法

### 条款 30：确保目标空间足够大

无论何时，如果所使用的算法需要指定一个目标空间，那么必须确保目标空间足够大，或者确保它会随着算法的运行而增大。要在算法执行过程中增大目标区间，请使用插入行迭代器，比如 ostream_iterator 或者有 back_inserter、front_inserter、inserter 返回的迭代器。

当函数参数需要传入一个迭代器以向容器尾部添加数据时，可以使用 std::back_inserter(v)代替 v.end()，它实际调用的是容器的 push_back()；如果要在表头插入，可以使用 std::front_inserter()，它实际调用的是容器的 push_front()。

### 条款 31：了解各种与排序有关的选择

排序算法有如下：

- 完全排序 sort、stable_sort
- 部分排序 partial_sort
- 找到第 k 小（大）的元素 nth_element
- 把满足条件的元素放在不满足的元素之前 partition、stable_partition

sort、stable_sort、partitial_sort、nth_element 算法都要求随机访问迭代器，所以这些算法都只能被应用于 vector、string、deque 和数组。list 是唯一需要排序却无法使用这些排序算法的容器，为此，list 特别提供了 sort 成员函数。

### 条款 32：如果确实需要删除元素，则需要在 remove 这一类算法之后调用 erase

从容器中删除元素，唯一的办法是调用容器的成员函数 erase。remove 算法不会删除元素，而是移动了区间中的元素，将“不用被删除”的元素移动到了区间的前部。它返回一个迭代器指向最后一个“不用被删除”的元素之后的元素。这个返回值相当于该区间的“新的逻辑结尾”。

```cpp
// 删除顺序容器中值为10的元素
std::vector<int> datas;
int destData = 10;
datas.erase(std::remove(datas.begin(), datas.end(), destData), datas.end());

// 删除关联容器中值为10的元素
std::map<int, int> datas;
datas.erase(10);
```

_注意：list 中的 remove 是真正可以将被删除元素从容器中删除的，等同于调用了 erase-remove 方法。_

### 条款 33：对包含指针的容器使用 remove 这一类算法时要特别小心

### 条款 34：了解哪些算法要求使用排序的区间作为参数

要求排序区间的算法会所以有这样的要求是为了提供更好的性能，而对于未排序的区间他们无法保证有这样的性能。要求排序区间的算法有：

```cpp
// 用二分法查找数据
binary_search
lower_bound
upper_bound
equal_range

// 提供了线性时间效率的集合操作
set_union
set_interation
set_difference
set_symmertric_difference

// 线性时间的合并和排序
merge
inplace_merge

// 线性时间内判断一个区间中的所有对象是否都在另外的一个区间中
includes

// 如果不排序，你就无法使容器中的元素唯一
unique
unique_copy
```

### 条款 35：通过 mismatch 或 lexicographical_compare 实现简单的忽略大小写的字符串比较

### 条款 36：理解 copy_if 算法的正确实现

```cpp
template<typename InputIterator, typename OutputIterator, typename Predicate>
OutputIterator copy_if(InputIterator begin, InputIterator end, OutputIterator destBegin, Predicate p)
{
    while (begin != end)
    {
        if (p(*begin)) *destBegin++ == *begin;
        ++begin;
    }
    return destBegin;
}
```

### 条款 37：使用 accumulate 或者 for_each 进行区间统计

## 仿函数、仿函数类、函数等

### 条款 38：遵循按照值传递的原则来设计仿函数

由于函数对象往往会按照值传递和返回，所以，你必须确保你编写的函数对象在经历了传递之后还能正常工作。这意味两件事：

1. 你的函数对象必须尽可能小，否则拷贝的开销会非常昂贵。
2. 函数对象必须是单态的，也就是说不能使用虚函数。这是因为，如果参数的类型是基类类型，而实参是派生类对象，那么在传递过程中会产生剥离问题。在对象拷贝过程中，派生部分可能会被去掉，而仅留下基类部分。

### 条款 39：确保判别式是"纯函数"

判别式(predicate)：返回为 bool 类型(或者可以隐式转换成 bool 类型)的函数。在 STL 中，判别式有着广泛的用途。标准关联容器的比较函数就是判别式：对于像 find_if 以及各种与排序有关的算法，判别式往往也被作为参数来传递。

纯函数(pure function)：返回值仅仅以依赖于其参数的函数。例如，假设 f 是一个纯函数，x 和 y 是两个对象，那么只有当 x 或者 y 的值发生变化的时候，f(x, y)的返回值才可能发生变化。

### 条款 40：若一个类是仿函数，则应该使它可配接

### 条款 41：理解 ptr_fun、mem_fun 和 mem_fun_ref 的来由

### 条款 42：确保`less<T>`与`operator<`具有相同的语义

## 使用 STL 编程

### 条款 43：算法调用优先于手写的循环

任何时候都应该尽量用较高层次的 insert、find 和 for_each 来替换较低层次的 for、while 和 do。因为这样可以提高软件的抽象层次，从而使软件更易于编写，更易于文档化，也更易于扩展和维护。

### 条款 44：容器的成员函数优先于同名的算法

### 条款 45：正确区分 count、find、binary_search、lower_bound、upper_bound 和 equal_range

![stl](img/stlfunc.png)

### 条款 46：考虑使用仿函数而不是函数作为 STL 算法的参数

### 条款 47：避免产生"直写型"(write-only)的代码

### 条款 48：总是包含(#include)正确的头文件

提示：

1. 几乎所有的标准 STL 容器都被声明为与之同名的同文件中；
2. accumulate、inner_product、adjacent_difference、partial_sum 被声明在`<numeric>`中，其他所有 STL 算法都被声明在`<algorithm>`中；
3. 特殊类型的迭代器，包括 istream_iterator 和 istreambuf_iterator，被声明在`<iterator>`中；
4. 标准的仿函数和配接器被声明在`<functional>`中。

### 条款 49：学会分析与 STL 相关的编译器诊断信息

提示：

1. 将冗长的模板类型名替换成短的名字，总是可以简化编译器的诊断信息；
2. vector 和 string 的迭代器通常就是指针，所以当错误地使用了 iterator 的时候，编译器的诊断信息中可能会涉及到指针类型；
3. 如果诊断信息中提到了 back_insert_iterator、front_insert_interator 或者 insert_iterator，则几乎总是意味着错误地调用了 back_inserter、front_inserter 或者 inserter。如果没有直接调用这些函数，一定是你所调用的某个函数直接或者间接调用了这些函数；
4. 如果诊断信息中提到了 bind1st、bind2nd，可能是错误地使用了 bind1st、bind2nd。输出迭代器以及那些由 back_inserter、front_inserter 和 inserter 函数返回的迭代器再赋值操作符内部完成其输出或者插入工作，所以，如果在使用这些迭代器的时候犯错，那么看到的错误消息中可能会提到与赋值操作符相关的内容；
5. 如果得到的错误消息来源于某一个 STL 算法的内部实现，那么也许是在调用算法的时候使用了错误的类型；
6. 如果正在使用一个很常见的 STL 组件，比如 vector、string 或者 for_each 算法，但是从错误消息来源看，编译器好像对此错误一无所知，那么可能是没有包含相应的头文件。

### 条款 50：熟悉与 STL 相关的 Web 站点

```html
SGI STL: http://www.sgi.com/tech/stl/ STLport: http://www.stlport.org Boost:
http://www.boost.org
```
