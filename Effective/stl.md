# Effective STL

## 目录

[容器](#容器)  
[vector和string](#vector和string)  
[关联容器](#关联容器)  
[迭代器](#迭代器)  
[算法](#算法)  
[仿函数、仿函数类、函数等](#仿函数仿函数类函数等)  
[使用STL编程](#使用stl编程)

## 容器

### 条款01：慎重选择容器类型

C++ 提供了几种不同的容器供你选择：

* 标准STL序列容器：vector、string、deque、list
* 标准STL关联容器 ：set、multiset、map、multimap
* 非标准序列容器：slist、rope
* 非标准关联容器：hash_set、hash_multiset、hash_map、hash_multimap

### 条款02：不要试图编写独立于容器类型的代码

STL是以泛化（generalization）原则为基础的：数组被泛化为“以其包含的对象的类型为参数”的**容器**；函数被泛化为“以其使用的迭代器的类型为参数”的**算法**；指针被泛化为“以其指向的对象的类型为参数”的**迭代器**。

容器类型被泛化为序列和关联容器，类似的容器被赋予相似的功能。不同的容器是不同的，它们并不是被设计来交换使用的，试图编写对序列容器和关联容器都适用的代码几乎是毫无意义的。

### 条款03：确保容器中的对象副本正确而高效

复制对象是STL的工作方式：进去的是副本，出来的也是副本（copy in, copy out），移动元素也是复制。对象的拷贝构造函数可能会消耗大量时间。通常，用容器保存**指针**比保存对象更好，保存**智能指针**比原生指针好。

### 条款04：调用empty而不是检查size()是否为0

empty对所有的标准容器都是常数时间操作，而对一些list实现，size耗费线性时间。

### 条款05：区间成员函数优先于与之对应的单元素成员函数

给容器赋值、插入或删除时应该使用assign、insert和erase这些区间成员函数，而不是循环操纵单元素。

### 条款06：当心C++编译器最烦人的分析机制

### 条款07：如果容器中包含了通过new操作创建的指针，切记在容器对象析构前将指针delete掉

*注意：更好的做法是使用智能指针。*

### ~~条款08：切勿创建包含auto_ptr的容器对象~~

### 条款09：慎重选择删除元素的方法

对于不同的类型容器，有不同的删除方法。

循环删除容器中的元素时需要注意：调用`erase(it)`后，迭代器it会失效，无法获取下一个元素。

对于关联容器，删除，插入操作会导致指向该元素的迭代器失效，其他元素迭代器不受影响。最简单的办法是，当调用时对it使用后缀递增。

```cpp
for(auto it = v.begin(); it != v.end(); ){
    v.erase(it++);
}
```

对于顺序容器，删除、插入操作会导致指向该元素以及后面的元素的迭代器失效。需要利用erase的返回值获取下一个元素的迭代器。

```cpp
for(auto it = v.begin(); it != v.end(); ){
    it = v.erase(it);
    // erase()返回指向下一个有效元素的迭代器
}
```

### 条款10：了解分配器（allocator）的约定和限制

### 条款11：理解自定义分配器的合理用法

### 条款12：切勿对STL容器的线程安全性有不切实际的依赖

## vector和string

### 条款13：用vector和string替代动态分配的数组

### 条款14：使用reserve来避免不必要的重新分配

当STL容器需要更多的空间时，操作如下：

1. 分配一块大小为当前容量的某个倍数的新内存（通常为2倍）；
2. 把容器的所有元素从旧的内存复制到新的内存中；
3. 析构掉旧内存中的对象；
4. 释放旧内存。

为了避免这些非常耗时的步骤，需要使用reserve()。

### 条款15：注意string实现的多样性

### 条款16：了解如何把vector和string数据传给旧API

### 条款17：使用“swap技巧”除去多余的容量

利用利用匿名容器会自动析构这个特点，可以压缩vector占用的内存：

```cpp
vector<int> v;
// 容器操作
vector<int>(v).swap(v);
// 利用匿名容器拷贝v的内容，再和v进行交换
```

### 条款18：避免使用`vector<bool>`

`vector<bool>`不完全满足STL容器的要求，可以用`deque<bool>`和bitset来替代它。

## 关联容器

### 条款19：理解相等（equality）和等价（equivalence）的区别

标准关联容器总是保持排列顺序的，所以每个容器必须有一个比较函数（默认为less）来决定保持怎样的顺序。当STL中需要相等判断时，一般的惯
例是直接调用operator==，比如非成员函数find。

### 条款20：为包含指针的关联容器指定比较类型

当创建包含指针的关联容器时，容器将会按照指针的值进行排序，而不是按照指向的对象。因此需要创建自己的函数对象作为该容器的比较函数。

```cpp
auto sortFunc = [](const std::string* pLeft, const std::string* pRight) {return *pLeft < *pRight;  };
std::set<std::string*, sortFunc> datas;
```

### 条款21：总是让比较函数在等值情况下返回false

比较函数的返回值表明的是按照该函数定义的排列顺序：一个值是否在另外一个之前。相等的值从来不会有前后顺序关系，所以，对于相等的值，比较函数应该始终返回false。

### 条款22：切勿直接修改set或multiset中的key

像所有的标准关联容器一样，set和multiset按照一定的顺序来存放自己的元素。如果把关联容器中的一个key改变了，这将会打破容器的有序性。修改关联容器中的key有以下步骤：

1. 找到你想修改该的容器的元素；
2. 为将要被修改的元素做一份拷贝；
3. 修改该拷贝，使它具有你期望它在容器中的值；
4. 把该元素从容器中删除，通常是通过调用erase来进行的；
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

### 条款23：考虑用排序的vector替代关联容器

如果对于集合的的操作固定，例如在一个阶段都是查找操作，那么可以考虑使用vector来存储`std::pair<type1, type2>`（查找前进行排序）。因为关联容器的底层实现使用了红黑树，存储同样元素数量的关联容器占用内存比顺序容器大。排序后进行二分查找速度相近。

如果插入、查找、删除交替进行，那么关联容器更好。

### 条款24：当效率至关重要时，请在map::operator[]与map::insert之间谨慎作出选择

当用operator[]向map中插入元素时，会默认构造一个对象，再赋值；而insert直接在map中构造。

因此，当先map中添加元素时，要优先选用insert，而不是operator[]；当更新已经在map中的元素的值时，要优先选择operator[]（避免歧义）。

### 条款25：熟悉非标准的哈希容器

注意hash容器与关联容器的区别:

* hash容器是基于hash函数的，hash容器对相同元素的判断是基于相等，默认为std::equal_to<>。其元素不是以排序方式存放的。
* 关联容器是基于红黑树的，对相同元素的判断基于等价，默认为std::less<>。元素是基于排序存放的。

## 迭代器

### 条款26：iterator优先于const_iterator、reverse_iterator以及const_reverse_iterator

### 条款27：使用distance和advance将容器的const_iterator转换成iterator

### 条款28：正确理解由reverse_iterator的base()成员函数所产生的iterator的用法

### 条款29：对于逐个字符的输入请考虑使用istreambuf_iterator

## 算法

### 条款30：确保目标空间足够大

无论何时，如果所使用的算法需要指定一个目标空间，那么必须确保目标空间足够大，或者确保它会随着算法的运行而增大。要在算法执行过程中增大目标区间，请使用插入行迭代器，比如ostream_iterator或者有back_inserter、front_inserter、inserter返回的迭代器。

当函数参数需要传入一个迭代器以向容器尾部添加数据时，可以使用std::back_inserter(v)代替v.end()，它实际调用的是容器的push_back()；如果要在表头插入，可以使用std::front_inserter()，它实际调用的是容器的push_front()。

### 条款31：了解各种与排序有关的选择

排序算法有如下：

* 完全排序sort、stable_sort
* 部分排序partial_sort
* 找到第k小（大）的元素nth_element
* 把满足条件的元素放在不满足的元素之前partition、stable_partition

sort、stable_sort、partitial_sort、nth_element算法都要求随机访问迭代器，所以这些算法都只能被应用于vector、string、deque和数组。list是唯一需要排序却无法使用这些排序算法的容器，为此，list特别提供了sort成员函数。

### 条款32：如果确实需要删除元素，则需要在remove这一类算法之后调用erase

从容器中删除元素，唯一的办法是调用容器的成员函数erase。remove算法不会删除元素，而是移动了区间中的元素，将“不用被删除”的元素移动到了区间的前部。它返回一个迭代器指向最后一个“不用被删除”的元素之后的元素。这个返回值相当于该区间的“新的逻辑结尾”。

```cpp
// 删除顺序容器中值为10的元素
std::vector<int> datas;
int destData = 10;
datas.erase(std::remove(datas.begin(), datas.end(), destData), datas.end());

// 删除关联容器中值为10的元素
std::map<int, int> datas;
datas.erase(10);
```

*注意：list中的remove是真正可以将被删除元素从容器中删除的，等同于调用了erase-remove方法。*

### 条款33：对包含指针的容器使用remove这一类算法时要特别小心

### 条款34：了解哪些算法要求使用排序的区间作为参数

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

### 条款35：通过mismatch或lexicographical_compare实现简单的忽略大小写的字符串比较

### 条款36：理解copy_if算法的正确实现

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

### 条款37：使用accumulate或者for_each进行区间统计

## 仿函数、仿函数类、函数等

### 条款38：遵循按照值传递的原则来设计仿函数

由于函数对象往往会按照值传递和返回，所以，你必须确保你编写的函数对象在经历了传递之后还能正常工作。这意味两件事：

1. 你的函数对象必须尽可能小，否则拷贝的开销会非常昂贵。
2. 函数对象必须是单态的，也就是说不能使用虚函数。这是因为，如果参数的类型是基类类型，而实参是派生类对象，那么在传递过程中会产生剥离问题。在对象拷贝过程中，派生部分可能会被去掉，而仅留下基类部分。

### 条款39：确保判别式是"纯函数"

判别式(predicate)：返回为bool类型(或者可以隐式转换成bool类型)的函数。在STL中，判别式有着广泛的用途。标准关联容器的比较函数就是判别式：对于像find_if以及各种与排序有关的算法，判别式往往也被作为参数来传递。

纯函数(pure function)：返回值仅仅以依赖于其参数的函数。例如，假设f是一个纯函数，x和y是两个对象，那么只有当x或者y的值发生变化的时候，f(x, y)的返回值才可能发生变化。

### 条款40：若一个类是仿函数，则应该使它可配接

### 条款41：理解ptr_fun、mem_fun和mem_fun_ref的来由

### 条款42：确保`less<T>`与`operator<`具有相同的语义

## 使用STL编程

### 条款43：算法调用优先于手写的循环

任何时候都应该尽量用较高层次的insert、find和for_each来替换较低层次的for、while和do。因为这样可以提高软件的抽象层次，从而使软件更易于编写，更易于文档化，也更易于扩展和维护。

### 条款44：容器的成员函数优先于同名的算法

### 条款45：正确区分count、find、binary_search、lower_bound、upper_bound和equal_range

![stl](img/stlfunc.png)

### 条款46：考虑使用仿函数而不是函数作为STL算法的参数

### 条款47：避免产生"直写型"(write-only)的代码

### 条款48：总是包含(#include)正确的头文件

提示：

1. 几乎所有的标准STL容器都被声明为与之同名的同文件中；
2. accumulate、inner_product、adjacent_difference、partial_sum被声明在`<numeric>`中，其他所有STL算法都被声明在`<algorithm>`中；
3. 特殊类型的迭代器，包括istream_iterator和istreambuf_iterator，被声明在`<iterator>`中；
4. 标准的仿函数和配接器被声明在`<functional>`中。

### 条款49：学会分析与STL相关的编译器诊断信息

提示：

1. 将冗长的模板类型名替换成短的名字，总是可以简化编译器的诊断信息；
2. vector和string的迭代器通常就是指针，所以当错误地使用了iterator的时候，编译器的诊断信息中可能会涉及到指针类型；
3. 如果诊断信息中提到了back_insert_iterator、front_insert_interator或者insert_iterator，则几乎总是意味着错误地调用了back_inserter、front_inserter或者inserter。如果没有直接调用这些函数，一定是你所调用的某个函数直接或者间接调用了这些函数；
4. 如果诊断信息中提到了bind1st、bind2nd，可能是错误地使用了bind1st、bind2nd。输出迭代器以及那些由back_inserter、front_inserter和inserter函数返回的迭代器再赋值操作符内部完成其输出或者插入工作，所以，如果在使用这些迭代器的时候犯错，那么看到的错误消息中可能会提到与赋值操作符相关的内容；
5. 如果得到的错误消息来源于某一个STL算法的内部实现，那么也许是在调用算法的时候使用了错误的类型；
6. 如果正在使用一个很常见的STL组件，比如vector、string或者for_each算法，但是从错误消息来源看，编译器好像对此错误一无所知，那么可能是没有包含相应的头文件。

### 条款50：熟悉与STL相关的Web站点

```html
SGI STL: http://www.sgi.com/tech/stl/
STLport: http://www.stlport.org
Boost: http://www.boost.org
```
