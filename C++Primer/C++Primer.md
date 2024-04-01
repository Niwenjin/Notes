# C++ Primer

## 目录

[第2章 变量和基本类型](#第2章-变量和基本类型)  
[第3章 字符串、向量和数组](#第3章-字符串向量和数组)  
[第4章 表达式](#第4章-表达式)  
[第5章 语句](#第5章-语句)  
[第6章 函数](#第6章-函数)  
[第7章 类](#第7章-类)  
[第8章 IO库](#第8章-io库)  
[第11章 关联容器](#第11章-关联容器)  
[第12章 动态内存](#第12章-动态内存)  
[第14章 重载运算与类型转换](#第14章-重载运算与类型转换)  
[第15章 面向对象程序设计](#第15章-面向对象程序设计)  
[第16章 模板与泛型编程](#第16章-模板与泛型编程)  

## 第2章 变量和基本类型

### 基本类型

```cpp
bool
char
wchar_t
char16_t
char32_t
short
int
long
long long
float
double
long double
```

### 字面值

整型

```cpp
20     // 十进制
0b10   // 二进制
024    // 八进制
0x14   // 十六进制
```

浮点型

```cpp
3.14
3.0e30
.001
```

字符和字符串

```cpp
'a'      // 字符
"hello"  // 字符串
```

布尔型

```cpp
true
false
```

转义序列

```cpp
\a    // 响铃(BEL)
\b    // 退格(BS) ，将当前位置移到前一列
\f    // 换页(FF)，将当前位置移到下页开头
\n    // 换行(LF) ，将当前位置移到下一行开头
\r    // 回车(CR) ，将当前位置移到本行开头
\t    // 水平制表(HT) （跳到下一个TAB位置）
\v    // 垂直制表(VT)
\\    // 代表一个反斜线字符''\'
\'    // 代表一个单引号（撇号）字符
\"    // 代表一个双引号字符
\?    // 代表一个问号字符
\0    // 空字符(NULL)
```

### 前缀和后缀

前缀

```cpp
u     // char16_t
U     // char32_t
L     // wchar_t
u8    // char
```

后缀（整型）

```cpp
u 或 U    // unsigned
l 或 L    // long
ll 或 LL  // long long
```

后缀（浮点型）

```cpp
f 或 F   // float
l 或 L   // long double
```

### 指针和引用

引用

```cpp
int &refval = val;
// 定义引用时，引用被绑定到初始值上，因此必须初始化
```

指针

```cpp
int val = 42;
int *p = &val;   // 用取址符获取地址赋给指针
*p = 0;          // 解引用，操作指针所指的对象
```

### const限定符

const修饰基本数据类型  
`const double pi = 3.14;`  
作为函数参数的const修饰符  
`void print(const string &s);`  
作为函数返回值的const修饰符  
`const int &min(const int &a, const int &b);`  
类中的成员函数  
`void CL::func() const`  
顶层const和底层const
> 顶层const：表示对象本身是常量，对任何数据类型都适用，也包含常量指针，但通常不包括引用。  
> 底层const：表示指针/引用所指的对象是常量，通常是指常量引用和指向常量的指针。
>
### 处理类型

类型别名

```cpp
typedef double wages;
using SI = Sales_item;
```

auto类型  
`auto res = s.size();`  
decltype类型指示符  

```cpp
decltype(f()) sum = x;     // sum类型就是f()返回值
```

### 头文件

头文件保护符

```cpp
#ifndef HELLO_H
#define HELLO_H
......
#endif
```

## 第3章 字符串、向量和数组

### 命名空间

using声明  

```cpp
using namespace::name;
// 如using std::cin;
// 头文件不应包含using声明
```

### string类型

初始化string对象

```cpp
string s1;              // 默认初始化空串
string s2(s1);          // s2是s1的副本
string s2 = s1;         // 等价string s2(s1);
string s3("value");     // s3是字面值"value"的副本
string s3 = "value";    // 等价string s3("value");
string s4(n, 'c');      // s4是n个字符c组成的串
```

随机访问字符

```cpp
string s = "hello";
cout << s[3];
// 下标值必须大于等于0且小于s.size()
```

### 数组

定义数组

```cpp
int arr[10];
// 数组元素被默认初始化为0
string strs[SIZE];
// SIZE必须为常量
```

显式初始化数组

```cpp
int arr[] = {0, 1, 2};    // 含有三个元素0,1,2
int arr[5] = {0, 1, 2};   // 含有五个元素 0,1,2,0,0
int arr[2] = {0, 1, 2};   // 错误，初始值过多
```

指针和数组

```cpp
int arr[5] = {1, 2, 3, 4, 5};
*(arr + 2) = 0;
```

## 第4章 表达式

### 算数运算符

```cpp
+ -                // 一元正负号 
* / % + -
```

### 逻辑和关系运算符

```cpp
! < <= > >= == != && ||
```

### 赋值运算符

```cpp
=
```

### 递增和递减运算符

```cpp
++ --
```

### 成员访问运算符

```cpp
ptr->mem
(*ptr).mem
```

### 条件运算符

```cpp
? :
```

### 位运算符

```cpp
~ << >> & ^ |
```

### sizeof运算符

```cpp
sizeof(type)       // 返回类型所占字节数
sizeof expr        // 返回表达式结果类型的大小
// 返回size_t类型
```

### 逗号运算符

```cpp
先执行左侧表达式，再执行右侧表达式，并返回右侧表达式结果
```

### 类型转换

隐式类型转换

* 比int小的整型提升为int（整型提升）
* 在条件中，非布尔值转换为布尔值
* 初始化中，初始值转换为变量的类型
* 算数运算或关系运算中，转换为同一类型
* 函数调用时，发生类型转换（见第6章）

### 运算符优先级

```cpp
算术 > 位 > 身份 >成员 > 比较 > 逻辑 > 赋值
```

## 第5章 语句

### 条件语句

if语句

```cpp
if(condition)
    statement
```

if else语句

```cpp
if(condition)
    statement
else if(condition2)
    statement2
else
    statement3
```

switch语句

```cpp
switch(c){
    case 'a':
        ...
        break;
    case 'b':
        ...
        break;
    default:
}
```

### 迭代语句

while语句

```cpp
while(condition)
    statement
```

do while语句

```cpp
do{
    statement
}while(condition)
```

传统for语句

```cpp
for(init; condition; expression)
    statement
```

范围for语句

```cpp
for(declaration: expression)
    statement
```

### 跳转语句

break语句  
continue语句  
goto语句

```cpp
goto label;      // 跳转到标签
...
label:
    return;      // 带标签语句
```

### try语句块和异常处理

throw表达式

```cpp
if(condition)
    throw runtime_error("error");
```

try语句块

```cpp
try{
    ...
}catch(exception-declaration){
    ...
}catch(exception-declaration){
    ...
}
```

标准异常

```cpp
exception             // 常见错误
runtime_error         // 运行时错误
range_error           // 范围越界
overflow_error        // 计算上溢
underflow_error       // 计算下溢
logic_error           // 逻辑错误
domain_error          // 参数对应的结果不存在
invalid_argument      // 无效参数
length_error          // 试图创建一个超出该类型最大长度的对象
out_of_range          // 使用一个超出有效范围的值
```

## 第6章 函数

### 参数传递

值传递
> 当初始化一个引用类型的变量时，初始值拷贝给变量  
> 指针形参拷贝的是指针的值  
> 数组形参会转换为指针
>
引用传递
> 通过引用形参，允许函数改变实参的值
>
main：处理命令行选项

```cpp
int main(int argc, int *argv[]);
int main(int argc, int **argv);
```

### 返回类型

返回方式
> 返回非引用类型，函数返回一个副本或临时对象  
> 返回引用类型，返回所引对象的一个别名
>
尾置返回类型

```cpp
// func接受一个int类型实参，返回一个指针，指向有10个int类型元素的数组
auto func(int) -> int(*)[10];
```

使用decltype

```cpp
// 返回一个指针，该指针所指的对象与odd类型一致
decltype(odd) *func(int);
```

### 函数重载

const_cast

```cpp
const string &shorter(const string &s1, const string &s2){...}
string &shorter(string &s1, string &s2){
    auto &r = shorter(const_cast<const string&>(s1),
                      const_cast<const string&>(s2));
    return const_cast<string&>(r);
}
```

### 语言特性

默认实参

```cpp
string screen(int height = 24, int width = 30);
// 该函数既能接受默认值，也能接受用户传入的值
// 一旦某个形参被赋予了默认值，后面的形参必须有默认值
// 通常在函数声明中指定默认实参，并放在头文件中
```

内联函数

```cpp
// 声明为inline的函数将在编译时展开，避免调用函数的开销
inline const string &
shorter(const string &s1, const string &s2){
    return s1.size() <= s2.size() ? s1 : s2;
}
```

constexpr函数

```cpp
// constexpr函数是能用于常量表达式的函数
// 函数的返回类型和形参必须是字面值类型
// 函数体中只能有一个return语句
constexpr int num(){ return 42; }
```

### 函数匹配

1. 选定候选函数
   * 与被调用的函数同名  
   * 其声明在调用点可见
2. 确定可行函数
   * 形参数量与本次调用的实参相等
   * 实参类型与形参相同，或能转换成形参类型
3. 寻找最佳匹配
   * 实参类型与形参越接近越好

### 函数指针

```cpp
bool cmp(const string &, const string &);
// 指针参数类型必须和重载函数的某一个精确匹配
bool (*pf)(const string &, const string &);  // 声明
pf = cmp;              // 赋值（等价于pf = &cmp;）
bool b = pf(s1, s2);   // 使用函数指针调用函数
```

## 第7章 类

### 构造函数

默认构造函数
> 编译器创建的默认构造函数又叫“合成的默认构造函数”  
> 只有当类没有声明任何构造函数时，编译器才会生成默认构造函数  
> 初始化规则：
>
> * 如果存在类内的初始值，用它初始化成员
> * 如果不存在，默认初始化成员
>
自定义默认构造函数

```cpp
Sales_data() = default;
```

初始值列表

```cpp
// 利用初始值列表显式初始化成员
Sales_data(const string &s, unsigned n, double p):
            bookNo(s), units_sold(n), price(p) { };
```

委托构造函数

```cpp
class Sales_data{
public:
    Sales_data(const string &s, unsigned n, double p):
            bookNo(s), units_sold(n), price(p) { };
    // 其他构造函数委托给另一个构造函数
    Sales_data(): Sales_data("", 0, 0){};
}
```

### 访问控制与封装

#### 访问说明符

public
> public说明符后的成员在整个程序内可被访问  
> public成员定义类的接口

private
> private说明符后的成员可被类的成员函数访问  
> private成员封装了类的实现细节
>
class/struct关键字
> class和struct关键字的唯一区别时默认访问权限不同  
>
> * struct定义类默认成员是public
> * class定义类默认成员是private
>
#### 友元
>
> 类允许其他类或者函数访问他的非公有(private)成员，方法是成为友元

```cpp
class Sales_data{
    friend Sales_data add(...);     // 指定函数为友元
    friend class Sales;             // 指定类为友元
    ...
}
```

### 聚合类
>
> 当一个类满足以下条件时，它被成为聚合类：
>
> * 所有成员都是public
> * 没有定义任何构造函数
> * 没有类内初始值
> * 没有基类，也没有virtual函数
>
聚合类的初始化

```cpp
struct Data{
    int val;
    string s;
}
// 用列表初始化聚合类
Data val = { 0, "Hello" };
```

### 静态成员

声明静态成员

```cpp
// 静态成员不与任何对象绑定，只与类关联
class Account{
    static double rate();
    ...
}
```

静态成员初始化

```cpp
// 不应在类内初始化静态成员
class Account{
    static double rate;
    ...
}
double Account::rate = 7.03;
```

访问静态成员

```cpp
double r = CLS::hello();
或
CLS cls;
double r = cls.hello();
```

### 类的其他特性

可变数据成员

```cpp
mutable size_t cnt;    // 即使在const成员内也能被修改
```

返回this

```cpp
Screen &Screen::set(pos r, pos col, pos ch){
    ...
    return *this;
}
```

前向声明

```cpp
class Screen;         // 分离类的声明和定义
// 在它定义前为“不完全类型”
```

## 第8章 IO库

### IO类

```cpp
头文件iostream
类(w)istream读,(w)ostream写,(w)iostream读写

头文件fstream
类(w)ifstream读,(w)ofstream写,(w)fstream读写

头文件sstream
类(w)istringstream读,(w)ostringstream写,(w)stringstream读写
```

fstream和sstream继承自iostream

```cpp
string s1, s2;
getline(cin, s1);            //使用getline读取标准输入
ifstream FILE("hello.txt");
getline(FILE, s2);           //使用getline读取文件

cout << "Hello!" << endl;    //写入到标准输出
ofstream of("hello.txt");
of << "This is a sentence."; //写入到输出文件流

//iostream类的特性同样适用于fstream和sstream
```

IO对象无拷贝或赋值

```cpp
ofstream out1, out2;
out1 = out2;         //错误：不能对流对象赋值

ofstream print(ofstream);
out1 = print(out2); //错误：不能拷贝流对象

//进行IO操作的函数通常以引用方式传递和返回流
ofstream &hello(ofstream &out){
    out << "Hello!" << endl;
    return out;
}

//读写一个IO对象会改变其状态，因此传递和返回的引用不能为const
```

条件状态

```cpp
while(cin >> word){ ... }  //若流保持有效状态，条件为真

cin.rdstate()              //调用rdstate方法查询流的状态
iostate值：
    0：goodbit 无错误
    1：badbit I/O操作上的读/写错误
    2：eofbit 输入操作达到文件结尾
    4：failbit I/O操作出现逻辑错误
```

管理输出缓冲

```cpp
每个输出流管理一个缓冲区，缓冲刷新时，数据才被写到输出设备或文件   
导致缓冲刷新的原因：
    1. 程序正常结束
    2. 缓冲区满
    3. 使用操作符（如endl）显式刷新缓冲
        cout << endl;         //换行并刷新
        cout << ends;         //输出一个空字符并刷新
        cout << flush;        //刷新，不附加额外字符
    4. 流的内部状态被设置为立即刷新
        cout << unitbuf;      //将标准输出设置为立即刷新
    5. 输出流被关联到另一个流
        //cin/cerr默认关联到cout，因此读cin或写cerr都会刷新cout
```

### 文件输入输出

创建文件流对象

```cpp
ifstream in;
in.open(FILE);          //先声明后打开文件
ofstream out(FILE);     //构造文件流并打开文件
if(out){...}            //检查是否成功打开文件
```

关闭文件

```cpp
in.close()              //显式关闭文件
for(auto p = argv + 1; p != argv + argc ; ++p){
    ifstream input(*p);
    ...
}                       //当文件流对象被销毁时，close会被自动调用
```

文件模式

```cpp
in           //以读方式打开
out          //以写方式打开
app          //每次写操作均定位到文件末尾
ate          //每次打开后立即定位到文件末尾
trunc        //截断文件
binary       //以二进制方式进行IO

//ofstream打开文件会默认截断文件，想要保留文件内容并在末尾输出，须在打开时指定文件模式为app
ofstream app("file.txt", ofstream::app);
```

### string流

根据空格断开字符串

```cpp
string line, word;
vector<string> strs;
getline(cin,line);            //用getline读取一行带空格的字符串
istringstream is(line);       //定义字符串流，绑定line字符串
while(is >> word)
    strs.push_back(word);     //根据空格每次读取一个单词
```

格式化输出

```cpp
ostringstream os;
while(const auto &num : nums)
    os << num;          //用标准输出运算符向字符串流添加数据
cout << os.str();       //用str()方法输出字符串流关联的字符串
```

## 第11章 关联容器

两个主要的关联容器类型是`map`和`set`：`map`中的元素是键值对，关键字起到索引的作用；`set`中的元素只包含一个关键字，可以高效查询一个关键字是否在`set`中。

### 关联容器类型

![map&set](img/map&set.png)
无序容器不使用比较运算符来组织元素，而是对关键字使用哈希函数来组织。无序容器提供了与有序容器相同的操作。

### 关联容器操作

#### pair类型

一个pair类型对象保存两个数据成员，分别命名为`first`和`second`。

```cpp
pair<T1, T2> p = {v1, v2};
std::cout << p.first << p.second;
```

定义当`first`和`second`成员分别相等时，两个`pair`相等。

```cpp
p1 == p2;
p1 != p2;
```

#### 元素类型

对于`set`类型，`key_type`和`value_type`是一样的；在`map`中，每个元素是一个`pair`对象。

```cpp
// 对于map<string, int>
// map<string, int>::key_type是string类型
// map<string, int>::value_type是pair<const string, int>类型
```

#### 迭代器

`map`类型的迭代器是一个类型为`pair`的引用：

```cpp
auto map_it = m.begin();
std::cout<< map.it->first;  // 可以获取元素
map_it++;                   // 可以通过迭代器改变元素
```

#### 添加元素

向`map`中插入一个`pair`类型的元素：

```cpp
// 向map中插入pair的四种方法
map<string, int> m;
m.insert({"word", 1});
m.insert(make_pair("word", 1));
m.insert(pair<string, int>("word", 1));
m.insert(map<string, int>::value_type("word", 1));
```

或用下标操作添加元素：

```cpp
m["apple"] = 6;
```

#### 删除元素

关联容器提供`erase`操作：

1. 接受`key_type`类型的元素k，删除所有关键字为k的元素，返回实际删除的元素数量。
2. 接受一个迭代器p，删除迭代器指定的元素，返回指向p之后的迭代器。
3. 接受两个迭代器p和e，删除p和e之间的所有元素，返回迭代器e。

#### 访问元素

1. `find`操作返回一个迭代器：若找到，返回指向指定key的迭代器；否则，返回m.end()。
2. `count`操作返回容器内该关键字数量。
3. 下标操作`m[k]`返回关键字为k的元素；若k不在容器中，添加一个关键字为k的元素并初始化。

## 第12章 动态内存

### 直接管理内存

使用`new`动态分配和初始化对象，将返回一个指向该对象的指针。  
使用`delete`释放一个动态分配的对象或一个空指针。

### 智能指针

1. `shared_ptr`允许多个指针指向同一个对象。
2. `unique_ptr`独占指向的对象。
3. `weak_ptr`是一种弱引用，指向`shared_ptr`管理的对象。

#### 初始化

最安全的分配和使用智能指针的方法是使用`make_shared`函数。

```cpp
shared_ptr<int> p1 = make_shared<int>(42);
shared_ptr<string> p2 = make_shared<string>(10, '9');
// 通常用auto保存分配的对象
auto p3 = make_shared<vector<string>>();
```

用`new`初始化智能指针:

```cpp
// 正确，使用了直接初始化形式
shared_ptr<int> p(new int(42));

// 错误，不能将普通指针隐式转换为智能指针
shared_ptr<int> p = new int(42);
```

使用`reset`将新的指针赋给`shared_ptr`。

```cpp
p.reset(new int(1024));
```

#### 引用计数

当进行拷贝或赋值时，`shared_ptr`对象会记录其引用计数。当引用计数变为0，它就会自动释放管理的对象。

#### 动态数组

用`unique_ptr`管理动态数组：

```cpp
unique_ptr<int[]> p(new int[10]);
p.release();    // 自动用delete[]销毁其指针
```

`shared_ptr`不直接支持管理动态数组，除非提供自己定义的删除器。

### 未构造内存

#### allocator类

allocator会根据对象类型来分配恰当的内存大小和对齐位置。

```cpp
allocator<string> alloc;           // 可以分配string的alloc对象
auto const p = alloc.allocate(n);  // 分配n个未初始化的string
// p指向最后构造的元素的下一个位置
```

用完对象后，必须调用`destroy`销毁它们。

```cpp
auto q = p;
alloc.destory(--q);
```

一旦元素被销毁，可以为他分配其他`string`，也可以将其归还给系统。

```cpp
alloc.deallocate(p, n);
```

## 第14章 重载运算与类型转换

### 运算符

运算符是具有特殊名字的函数  
`int operater+(int, int);`  

```cpp
data1 += data2;  
data1.operator += (data2);
// 这两条语句都调用了成员函数operator+=
```

### 重载运算符

可以被重载的运算符

```cpp
+ - * / % ^ & | ~ ! , = < >  
<= >= ++ -- << >> == != && ||  
+= -= /= %= ^= &= |= *= <<= >>=  
[] () -> ->* new new[] delete delete[]  
```

不能被重载的运算符

```cpp
::   .*   .   ? :
```

当运算符定义为成员函数时，左侧必须是所属类对象  

```cpp
string s = "world";
string t = s + "!";       // 正确，运算符左侧为string
string u = "hello" + s;   // 如果+是string成员，则错误
```

选择作为成员或非成员  

* 赋值=、下标[]、调用()和成员访问->必须是成员  
* 改变对象状态的运算符，如递增、递减和解引用，通常是成员  
* 具有对称性的运算，如算数、相等，通常是非成员  
* 输入输出运算符必须是非成员

重载输出运算符<<

```cpp
ostream &operator<<(ostream &os, const data &item){
    os << item.show1() << " " << item.show2();
    return os;      // 输出运算符一般返回ostream引用
}
```

重载输入运算符>>

```cpp
ostream &operator>>(istream &is, const data &item){
    int weight;
    is >> item.1 >> item.2 >> weight;
    if(is)   // 判断输入是否成功
        item.3 = item.2 * weight;
    else
        item = data();
    return is;
    // 输入运算符必须判断错误的情况，但输出不需要
}
```

算数运算符

```cpp
data operator+(const data &data1, const data &data2){
    data sum = data1;
    sum += data2;  // 利用复合赋值运算符实现算数运算符
    return sum;
}
```

关系运算符

```cpp
bool operator==(const data &data1, const data &data2){
    return data1.a == data2.a &&
    data1.b == data2.b &&
    data1.c == data2.c;
}
```

赋值运算符  

```cpp
data &operator=(const data &a){
    if(this != a)   // 避免自赋值
        ......
    return *this;   // 必须返回左侧运算对象的引用
}
```

下标运算符

```cpp
string &operator[](size_t n)
    { return elements[n]; }   // 返回普通引用
const string &operator[](size_t n) const
    { return elements[n]; }   // 返回常量防止给常量赋值
```

递增和递减运算符

```cpp
data &operator++();     // 前置运算符
data &operator++(int);  // 后置运算符（int不被使用）
```

成员访问运算符

```cpp
class StrBlobPtr
{
public:
    std::string& operator*() const
    {
        auto p = check(curr, "dereference past end");
        return (*p)[curr];   //(*p)是对象所指的vector
    }
    std::string* operator->() const
    {
        //将实际工作委托给解引用运算符
        return & this->operator*();
    }
}
```  

### 函数对象

函数调用运算符  

```cpp
struct absInt {
    int operator()(int val) const {
        return val < 0 ? -val : val;
    }
}
// 如果定义了调用运算符，则该类的对象称为“函数对象”
```

函数对象常常作为泛型算法的实参  

```cpp
class Shorter {
public:
    bool operator()(const string &s1, const string &s2) const {
        return s1.size() < s2.size();
    }
}
sort(words.begin(), words.end(), Shorter());
```

标准库定义的函数对象

```cpp
// 算数
plus<Type>
minus<Type>
multiplies<Type>
divides<Type>
modulus<Type>
negate<Type>
// 关系
equal_to<Type>
not_equal_to<Type>
greater<Type>
greater_equal<Type>
less<Type>
less_equal<Type>
// 逻辑
logical_and<Type>
logical_or<Type>
logical_not<Type>
```

### 类型转换运算符

类型转换运算符是类的特殊成员函数  
`operator type() const;`  
定义含有类型转换符的类

```cpp
class Smallint {
    public:
        // 构造函数定义其他类型向类的转换
        Smallint(int i): val(i){
            if(i < 0 || i > 255)
                throw std::out_of_range("error");
        }
        // 定义类向其他类型的转换
        operator int() const { return val; }
    private:
        std::size_t val;
}
```

显式类型转换运算符

```cpp
// 防止编译器自动调用类型转换符
class Smallint {
    // ......
    explicit operator int() const { return val; }
    // ......
}
```

## 第15章 面向对象程序设计

### 定义基类和派生类

### 虚函数

#### 定义虚函数

`virtual`关键字指定该函数是虚函数。当某个虚函数通过引用或指针调用时，在运行时才被解析（动态绑定/运行时绑定）。

#### 派生类中的虚函数

`final`说明符：指定的函数或类不能被继承。  
`override`说明符：用来说明派生类的函数是对基类虚函数的覆盖（编译器容易发现错误）。

#### 纯虚函数

在函数体的位置书写`=0`可以将虚函数声明为纯虚函数。

#### 抽象基类

含有纯虚函数的类是抽象基类。

### 访问控制与继承

#### 访问控制

`public`/`private`:见第7章。  
`protect`关键字用来声明与派生类分享但不被其他公共访问使用的成员。

#### 继承  

`public`公有继承：成员将遵循其原有的访问说明符。  
`private`私有继承：继承自基类的成员都是私有`private`的。

#### 继承中的类作用域

派生类的作用域嵌套在基类的作用域。如果一个名字在派生类的作用域中无法正确解析，则编译器将在外层基类作用域中继续查找。

## 第16章 模板与泛型编程

### 函数模板

#### 模板定义

模板定义以`template`开始，后跟一个模板参数列表。

```cpp
template <typename T>
int func(const T &v1, const T &v2) {
    return v1 < v2;
}
```

#### 实例化

当调用函数模板时，编译器推断模板实参来实例化一个特定版本的函数，这个函数被称为模板的实例。

#### 模板类型参数

在模板参数列表中，关键词`typename`和`class`含义相同，关键词后跟模板类型参数。类型参数可以像内置类型和类型说明符一样使用。

```cpp
template <typename T, U>
T func(U* p) {
    T tmp = *p;
    return tmp;
}
```

#### 非类型模板参数

非类型参数表示一个值而非一个类型，可以通过特定的类型名来指定非类型参数。

```cpp
template <unsigned N, unsigned M>
int cmp(const char (&p1)[N], const char (&p2)[M]) {
    // 第一个参数表示数组p1的长度，第二个参数表示数组p2的长度。
    return strcmp(p1, p2);
}
```

当调用`cmp("hi", "mom");`时，编译器会实例化如下版本：

```cpp
int cmp(const char (&p1)[3], const char (&p2)[4]);
```

#### 重要原则

1. 模板中的函数参数是`const`的引用
2. 函数体中的条件判断仅使用`<`比较运算。

### 类模板

类模板用于生成类的蓝图。由于编译器不能为类模板推断模板参数类型，必须在模板名后尖括号中提供额外信息。

#### 类模板定义

类似函数模板，类模板以`template`开始，后跟模板参数列表。

```cpp
template <typename T>
class Cls {
  public:
    typedef T value_type;
    T& func();
  private:
    void push_back(const T &t);
    std::shared_ptr<std::vector<T>> data;
};
```

#### 实例化类模板

当使用类模板时，必须提供显式模板实参。`Cls<int> cls;`
