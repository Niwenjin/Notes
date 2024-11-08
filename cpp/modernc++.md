# 现代 C++ 语言核心特性解析

## 第 1 章 新基础类型

**整数类型 long long** 用来表示一个 64 位的整型。另外， long long 是一个有符号类型，对应的无符号类型为 unsigned long long。C++ 标准还为其定义 LL 和 ULL 作为这两种类型的字面量后缀。

```cpp
long long x1 = 65536 << 16;    // 计算得到的x1值为0（这里65536被当作32位整形处理）
long long x2 = 65536LL << 16;  // 计算得到的x2值为4294967296（0x100000000）
```

**新字符类型 char16_t 和 char32_t** ，它们分别用来对应 Unicode 字符集的 UTF-16 和 UTF-32 两种编码方法。Unicode 字符集有 UTF-8、UTF-16 和 UTF-32 这 3 种编码方法。最常用的 UTF-8 编码方法，它采用了一种前缀编码的方法，是一种可变长度的编码方法，常用的字符通常用 1 ～ 2 字节就能表达，其他的字符才会用到 3 ～ 4 字节。对于 UTF-8 编码方法而言，普通类型无法表达变长的内存空间。到了 C++11，char16_t 和 char32_t 的出现打破了这个尴尬的局面。C++11 标准为 3 种编码提供了新前缀用于声明 3 种编码字符和字符串的字面量，它们分别是 UTF-8 的前缀 u8、UTF16 的前缀 u 和 UTF-32 的前缀 U：

```cpp
char utf8c = u8'a';     // C++17标准
//char utf8c = u8'好';  // utf-8存储'好'需要3个字节，无法通过编译
char16_t utf16c = u'好';
char32_t utf32c = U'好';
char utf8[] = u8"你好世界";
char16_t utf16[] = u"你好世界";
char32_t utf32[] = U"你好世界";
```

**char8_t 字符类型** 用于 UTF-8 变长字符的处理，由 C++20 标准引入。为了匹配 char8_t 新字符类型，库函数也有相应的增加：

```cpp
size_t mbrtoc8(char8_t* pc8, const char* s, size_t n, mbstate_t*
ps);
size_t c8rtomb(char* s, char8_t c8, mbstate_t* ps);
using u8string = basic_string;
```

## 第 2 章 内联和嵌套命名空间

**内联命名空间**能够把空间内函数和类型导出到父命名空间中。

```cpp
namespace Parent {
namespace Child1 {
    void foo() { std::cout << "Child1::foo()" << std::endl; }
}
inline namespace Child2 {
    void foo() { std::cout << "Child2::foo()" << std::endl; }
}
}
int main() {
    Parent::Child1::foo();  // 调用Child1命名空间的foo()
    Parent::foo();  // 调用Child2命名空间的foo()而不需要指定子命名空间
}
```

C++17 标准允许一种**嵌套命名空间**的简化语法。

```cpp
namespace A::B::C {
    int foo() { return 5; }
}
// 以上代码等价于
namespace A {
    namespace B {
        namespace C {
            int foo() { return 5; }
        }
    }
}
```

## 第 3 章 auto 占位符

**auto 关键字**是 C++11 引入的，声明变量时根据初始化表达式自动推断该变量的类型、声明函数时函数返回值的占位符。例如：

```cpp
auto i = 5;                    // 推断为int
auto str = "hello auto";       // 推断为const char*
auto sum(int a1, int a2)->int  // 返回类型后置，auto为返回值占位符
{
    return a1+a2;
}
```

用 auto 声明变量时，有 4 点需要注意：

1. 当用一个 auto 关键字声明多个变量的时候，编译器遵从由左往右的推导规则，以最左边的表达式推断 auto 的具体类型：

    ```cpp
    int n = 5;
    auto *pn = &n, m = 10;  // 因为&n类型为int*，所以pn的类型被推导为int*，auto被推导为int，于是m被声明为int类型
    ```

2. 当使用条件表达式初始化 auto 声明的变量时，编译器总是使 用表达能力更强的类型：

    ```cpp
    auto i = true ? 5 : 8.0;  // i的数据类型为double
    ```

3. auto 无法初始化非静态成员变量：

    ```cpp
    struct sometype {
        auto i = 5; // 错误，无法编译通过
    };
    ```

4. 按照 C++20 之前的标准，无法在函数形参列表中使用 auto 声明形参（注意，在 C++14 中，auto 可以为 lambda 表达式声明形参）：

    ```cpp
    void echo(auto str) {…}  // C++20之前编译失败，C++20编译成功
    ```

auto 初始化具有以下**推导规则**：

1. 如果 auto 声明的变量是按值初始化，则推导出的类型会忽略 cv 限定符。进一步解释为，在使用 auto 声明变量时，既没有使用引 用，也没有使用指针，那么编译器在推导的时候会忽略 const 和 volatile 限定符。auto 支持添加 cv 限定符：

    ```cpp
    const int i = 5;
    auto j = i;        // auto推导类型为int，而非const int
    auto &m = i;       // auto推导类型为const int，m推导类型为const int&
    auto *k = i;       // auto推导类型为const int，k推导类型为const int*
    const auto n = j;  // auto推导类型为int，n的类型为const int
    ```

2. 使用 auto 声明变量初始化时，目标对象如果是引用，则引用属性会被忽略：

    ```cpp
    int i = 5;
    int &j = i;
    auto m = j;  // auto推导类型为int，而非int&
    ```

3. 使用 auto 和万能引用声明变量时（见第 6 章），对于左值会将 auto 推导为引用类型：

    ```cpp
    int i = 5;
    auto&& m = i;  // auto推导类型为int& （这里涉及引用折叠的概念）
    auto&& j = 5;  // auto推导类型为int
    ```

4. 使用 auto 声明变量，如果目标对象是一个数组或者函数，则 auto 会被推导为对应的指针类型：

    ```cpp
    int i[5];
    auto m = i;  // auto推导类型为int*
    int sum(int a1, int a2) {
        return a1+a2;
    }
    auto j = sum  // auto推导类型为int (__cdecl *)(int,int)
    ```

5. 当 auto 关键字与列表初始化组合时，C++17 规定：直接使用列表初始化，列表中必须为单元素，否则无法编译，auto 类型被推导为单元素的类型；用等号加列表初始化，列表中可以包含单个或者多个元素，auto 类型被推导为`std::initializer_list<T>`，其中 T 是元素类型。

    ```cpp
    auto x1 = { 1, 2 };    // x1类型为 std::initializer_list<int>
    auto x2 = { 1, 2.0 };  // 编译失败，花括号中元素类型不同
    auto x3{ 1, 2 };       // 编译失败，不是单个元素
    auto x4 = { 3 };       // x4类型为std::initializer_list<int>
    auto x5{ 3 };          // x5类型为int
    ```

简单归纳 auto 的**使用规则**：

1. 当一眼就能看出声明变量的初始化类型的时候可以使用 auto（常见于迭代器）。

2. 对于复杂的类型，例如 lambda 表达式、bind 等直接使用 auto。

    ```
    auto l = [](int a1, int a2) { return a1 + a2; };
    // 这里的l可能是这样的名称<lambda_efdefb7231ea076 22630c86251a36ed4>（不同的编译器命名方法会有所不同），我们根本无法写出其类型，只能用auto来声明。
    ```

在 C++14 标准中，还可以把 auto 写到 lambda 表达式的形参中，这样就得到了一个泛型的 lambda 表达式，例如：

```cpp
auto l = [](auto a1, auto a2) { return a1 + a2; };
auto retval = l(5, 5.0);  // 返回值retval被推导为double类型
```

C++17 标准对 auto 关键字又一次进行了扩展，使它可以作为非类型模板形参的占位符。

```cpp
template<auto N>
void f() {
    std::cout << N << std::endl;
}
int main() {
    f<5>();    // N为int类型
    f<'c'>();  // N为char类型
    f<5.0>();  // 编译失败，模板参数不能为double
}
```

## 第 4 章 decltype 说明符

C++11 标准引入了 decltype 说明符，可以获取对象或者表达式的类型。

## 第 5 章 函数返回类型后置

C++11 标准支持**函数返回类型后置**。

```cpp
auto foo()->int
{
    return 42;
}
```

函数返回类型后置配合 decltype 说明符可以推导函数模板的返回类型。

```cpp
template<class T1, class T2>
auto sum1(T1 t1, T2 t2)->decltype(t1 + t2)
{
    return t1 + t2;
}
// decltype(t1 + t2)不能写在函数声明前，因为编译器在解析返回类型时还没有解析到参数部分，对t1和t2一无所知
```

## 第 6 章 右值引用

在 C++中，所谓的左值一般是指一个指向特定内存的具有名称的值（具名对象），它有一个相对稳定的内存地址，并且有一段较长的生命周期。而右值则是不指向稳定内存地址的匿名值（不具名对象），它的生命周期很短，通常是暂时性的。基于这一特征，我们可以用取地址符&来判断左值和右值，能取到内存地址的值为左值，否则为右值。

顾名思义，**右值引用**是一种引用右值且只能引用右值的方法。在语法方面右值引用可以对比左值引用，在左值引用声明中，需要在类型后添加&，而右值引用则是在类型后添加&&，例如：

```cpp
int i = 0;
int &j = i;    // 左值引用
int &j = 11;   // 编译错误，左值引用无法引用右值
int &&k = 11;  // 右值引用
```

很多情况下右值都存储在临时对象中，当右值被使用之后程序会马上销毁对象并释放内存。右值引用能够延长临时对象生命周期，减少对象复制，提升程序性能。

```cpp
class BigMemoryPool {
public:
    static const int PoolSize = 4096;
    BigMemoryPool() : pool_(new char[PoolSize]) {}
    ~BigMemoryPool() {
        if (pool_ != nullptr) {
            delete[] pool_;
        }
    }
    BigMemoryPool(BigMemoryPool&& other) {  // 移动构造函数能将临时对象的内存移动到构造的对象中，避免内存复制
        std::cout << "move big memory pool." << std::endl;
        pool_ = other.pool_;
        other.pool_ = nullptr;
    }
    BigMemoryPool(const BigMemoryPool& other) : pool_(new char[PoolSize]) {
        std::cout << "copy big memory pool." << std::endl;
        memcpy(pool_, other.pool_, PoolSize);
    }
private:
    char *pool_;
};
```

## 第 7 章 lamdba 表达式

在 STL 中有大量需要传入谓词的算法函数，比如 std::find_if、std::replace_if 等。C++11 标准提供了 lambda 表达式的支持，而且语法非常简单明了：

```cpp
[ captures ] ( params ) specifiers exception -> ret { body }
/*
* captures 捕获列表
* params 参数列表
* specifiers 限定符
* exception 异常说明符
* ret 返回类型
* body 函数体
*/
```

**捕获列表**是 lambda 表达式中最为复杂的一个部分。捕获列表中的变量存在于两个*作用域*——lambda 表达式定义的函数作用域以及 lambda 表达式函数体的作用域。前者是为了捕获变量，后者是为了使用变量。能捕获的变量必须是一个*自动存储类型*（非静态的局部变量），如果要使用静态或全局变量，直接使用即可，不需要捕获。进一步来说，如果将一个 lambda 表达式定义在全局作用域，那么 lambda 表达式的捕获列表必须为空。

捕获列表的捕获方式分为捕获值和捕获引用。*捕获值*是将函数作用域的 x 和 y 的值复制到 lambda 表达式对象的内部，就如同 lambda 表达式的成员变量一样。lambda 表达式的一个特性是*捕获的变量默认为常量*，因此无法改变捕获的值，如果要修改捕获值，需要添加说明符 mutable。*捕获引用*的语法与捕获值只有一个&的区别，在 lambda 表达式内修改捕获的引用变量，外部变量也会被修改。

_注意：和函数传参不同，lambda 表达式的捕获列表在定义时就已经确定了，外部变量修改后再调用 lambda 表达式，也不会影响 lambda 表达式捕获的值。_

lambda 表达式的捕获列表除了指定捕获变量之外还有 3 种特殊的捕获方法：

```cpp
// [this] —— 捕获this指针，捕获this指针可以让我们使用this类型的成员变量和函数。
class A
{
public:
    void print()  { ... }
    void test() {
        auto foo = [this] {
            print();
            x = 5;
        };
        foo();
    }
private:
    int x;
};
// [=] —— 捕获lambda表达式定义作用域的全部变量的值，包括this。
int main() {
    int x = 5, y = 8;
    auto foo = [=] { return x * y; };
    std::cout << foo() << std::endl;
}
// [&] —— 捕获lambda表达式定义作用域的全部变量的引用，包括this。
```

_注意：lambda 表达式其实是一个拥有 operator()自定义运算符的结构体，即函数对象。所以，lambda 表达式是 C++11 提供的一块语法糖，功能完全可以手动实现。_

对于无状态的 lambda 表达式`[]{}`，可以隐式转换为函数指针 `void(*)()`。

标准库 STL 是 lambda 表达式的**常用场合**。对于简单的需求，我们也可能使用 STL 提供的辅助函数，例如 std::less、std::plus 等。对于复杂的需求，可以直接在 STL 算法函数的参数列表中实现辅助函数，例如：

```cpp
std::vector<int> x = {1, 2, 3, 4, 5};
std::cout << *std::find_if(x.cbegin(), x.cend(), [](int i) { return (i % 3) == 0; }) << std::endl;
```

C++14 标准中引入了**初始化捕获**，从而可以自定义捕获变量名及表达式结果，如：

```cpp
int x = 5;
auto foo = [r = x + 1]{ return r; }
std::string x = "hello c++ ";
auto foo = [x = std::move(x)]{ return x + "world"; };
```

C++14 标准让 lambda 表达式具备了模版函数的能力，我们称它为**泛型 lambda 表达式**。只需要使用 auto 占位符即可，例如：

```cpp
auto foo = [](auto a) { return a; };
```

C++17 标准对 lambda 表达式同样有两处增强，一处是常量 lambda 表达式，另一处是对捕获`*this`的增强。其中常量 lambda 表达式的主要特性体现在 constexpr 关键字上；捕获`*this`时，会生成一个`*this`对象的副本并存储在 lambda 表达式内，可以直接访问这个复制对象的成员。

## 第 8 章 非静态数据成员默认初始化

C++11 标准提出了新的初始化方法，即在声明非静态数据成员的同时直接对其使用=或者{}初始化。

```cpp
class X {
public:
	X() {}
	X(int a) : a_(a) {}
	X(double b) : b_(b) {}
	X(const std::string &c) : c_(c) {}
private:
	int a_ = 0;
	double b_{ 0. };
	std::string c_{ "hello world" };
};
```

非静态数据成员在声明时默认初始化需要注意两个问题：

1. 不要使用括号()对非静态数据成员进行初始化，因为这样会造成解析问题，所以会编译错误。
2. 不要用 auto 来声明和初始化非静态数据成员。

## 第 9 章 列表初始化

一般来说，我们称使用括号初始化的方式叫作**直接初始化**，而使用等号初始化的方式叫作**拷贝初始化**（复制初始化）。请注意，这里使用等号对变量初始化并不是调用等号运算符的赋值操作。实际情况是，等号是拷贝初始化，调用的依然是直接初始化对应的构造函数。

C++11 标准引入了列表初始化，它使用大括号`{}`对变量进行初始化，和传统变量初始化的规则一样，它也区分为直接初始化和拷贝初始化，例如：

```cpp
int x = {5};                // 拷贝初始化
int x1{8};                  // 直接初始化
C x2 = {4};                 // 拷贝初始化
C x3{2};                    // 直接初始化
foo({8});                   // 拷贝初始化
foo({"hello", 8});          // 拷贝初始化
C x4 = bar();               // 拷贝初始化
C *x5 = new C{ "hi", 42 };  // 直接初始化，隐式调用多参数的构造函数
```

使用列表初始化标准容器和初始化数组一样简单：

```cpp
int x[] = { 1,2,3,4,5 };
int x1[]{ 1,2,3,4,5 };
std::vector<int> x2 = { 1,2,3,4,5 };
std::vector<int> x3{ 1,2,3,4,5 };
std::map<std::string, int> x4{ {"bear",4}, {"cassowary",2}, {"tiger",7} };
std::map<std::string, int> x5 = { {"bear",4}, {"cassowary",2}, {"tiger",7} };
```

标准容器之所以能够支持列表初始化，离不开编译器支持的同时，它们自己也必须满足一个条件：支持 `std::initializer_list` 为形参的构造函数。编译器负责将列表里的元素（大括号包含的内容）构造为一个 `std::initializer_list` 的对象，然后寻找标准容器中支持`std:: initializer_list`为形参的构造函数并调用它。

使用列表初始化需要注意**隐式缩窄转换**问题。这在传统变量初始化中是没有问题的，代码能顺利通过编译。但是如果采用列表初始化，比如，根据标准，编译器通常会给出一个错误。隐式缩窄转换在 C++标准中包括：

1. 从浮点类型转换整数类型。
2. 从 long double 转换到 double 或 float，或从 double 转换到 float，除非转换源是常量表达式以及转换后的实际值在目标可以表示的值范围内。
3. 从整数类型或非强枚举类型转换到浮点类型，除非转换源是常量表达式，转换后的实际值适合目标类型并且能够将生成目标类型的目标值转换回原始类型的原始值。
4. 从整数类型或非强枚举类型转换到不能代表所有原始类型值的整数类型，除非源是一个常量表达式，其值在转换之后能够适合目标类型。

**列表初始化的优先级问题**。如果有一个类同时拥有满足列表初始化的构造函数，且其中一个是以 `std::initializer_list` 为参数，那么编译器将优先以`std::initializer_ list`为参数构造函数。例如：

```cpp
std::vector<int> x1(5, 5);    // 初始化结果包含5个值为5的元素
std::vector<int> x2{ 5, 5 };  // 初始化结果包含2个元素
```

为了提高数据成员初始化的可读性和灵活性，C++20 标准中引入了**指定初始化**的特性。该特性允许指定初始化数据成员的名称，从而使代码意图更加明确。例如：

```cpp
struct Point3D {
    int x;
    int y;
    int z;
};
Point3D p{ .z = 3 };  // 其他变量调用默认初始化 x = 0, y = 0
```

使用指定初始化需要满足以下条件：

1. 对象必须是一个聚合类型（不能有自定义的构造函数、析构函数、私有成员、基类和虚函数）。

2. 指定的数据成员必须是非静态数据成员。

3. 每个非静态数据成员最多只能初始化一次。

4. 非静态数据成员的初始化必须按照声明的顺序进行。

5. 针对联合体中的数据成员只能初始化一次，不能同时指定。

6. 不能嵌套指定初始化数据成员。

7. 在 C++20 中，指定初始化和其他方法不能混用。

    ```cpp
    Point p{ .x = 2, 3 };  // 编译失败，混用数据成员的初始化
    ```

## 第 14 章 强枚举类型

由于枚举类型存在一些类型安全的问题，C++11 标准增加了强枚举类型。强枚举类型具备以下 3 个新特性。

1. 枚举标识符属于强枚举类型的作用域。
2. 枚举标识符不会隐式转换为整型。
3. 能指定强枚举类型的底层类型，底层类型默认为 int 类型。

定义强枚举类型的方法非常简单，只需要在枚举定义的 enum 关键字之后加上 class 关键字就可以了。

```cpp
enum class HighSchool {
	student,
	teacher,
	principal
};
enum class University {
	student,
	professor,
	principal
};
enum E : unsigned int {  // 指定强枚举类型的底层类型
 e1 = 1,
 e2 = 2,
 e3 = 0xfffffff0
};
int main()
{
	HighSchool x = HighSchool::student;
    HighSchool x = student;  // 编译失败，找不到student的定义
	University y = University::student;
	bool b = x < HighSchool::headmaster;
    bool b = University::student < HighSchool::headmaster;  // 编译失败，比较的类型不同
    int y = University::student;  // 编译失败，无法隐式转换为int类型
	std::cout << std::boolalpha << b << std::endl;
}
```

从 C++17 标准开始，枚举类型对象可以直接使用列表初始化。

```cpp
enum class Color {
	Red,
	Green,
	Blue
};
int main() {
	Color c{ 5 };  // 编译成功
	Color c1 = 5;  // 编译失败
	Color c2 = { 5 };  // 编译失败
	Color c3(5);  // 编译失败
}
```

C++20 标准扩展了 using 功能，它可以打开强枚举类型的命名空间。

```cpp
switch (c) {
	using enum Color;
	case Red: return "Red";
	case Green: return "Green";
	case Blue: return "Blue";
	default:
	return "none";
}
```

## 第 16 章 override 和 final 说明符

重写（override）、重载（overload）和隐藏（overwrite）在 C++中是 3 个完全不同的概念：

1. 重写（override）的意思更接近覆盖，在 C++中是指派生类覆盖了基类的虚函数。覆盖必须满足有相同的函数签名和返回类型，也就是说有相同的函数名、形参列表以及返回类型。
2. 重载（overload），它通常是指在同一个类中有两个或者两个以上函数，它们的函数名相同，但是函数签名不同，也就是说有不同的形参。
3. 隐藏（overwrite）是指基类成员函数，无论它是否为虚函数，当派生类出现同名函数时，如果派生类函数签名不同于基类函数，则基类函数会被隐藏。如果还想使用基类函数，可以使用 using 关键字将其引入派生类。

在编码过程中，重写虚函数很容易出现错误，原因是 C++语法对重写的要求很高，稍不注意就会无法重写基类虚函数，而编译器也可能不会提示任何错误信息。所以 C++11 标准提供了**override 说明符**，它明确告诉编译器这个虚函数需要覆盖基类的虚函数，一旦编译器发现该虚函数不符合重写规则，就会给出错误提示。

```cpp
class Base {
public:
    virtual void some_func() {}
    virtual void foo(int x) {}
    virtual void bar() const {}
    void baz() {}
};
class Derived : public Base {
public:
    virtual void sone_func() override {}
    virtual void foo(int &x) override {}
    virtual void bar() override {}
    virtual void baz() override {}
};
```

C++11 标准引入**final 说明符**来阻止派生类去继承基类的虚函数，它告诉编译器该虚函数不能被派生类重写。

```cpp
class Base {
public:
    virtual void foo(int x) {}
};
class Derived : public Base {
public:
    void foo(int x) final {};
};
class Derived2 : public Derived {
public:
    void foo(int x) {};  // 无法通过编译
};
```

有时候，override 和 final 会同时出现。这种情况通常是中间派生类继承基类后，不希望后续其他派生类修改本类虚函数的行为。

## 第 17 章 基于范围的 for 循环

C++11 标准引入了基于范围的 for 循环特性，该特性隐藏了迭代器的初始化和更新过程，让程序员只需要关心遍历对象本身：

```cpp
for (range_declaration : range_expression) { loop_statement }
```

基于范围的 for 循环不需要初始化语句、条件表达式以及更新表达式，取而代之的是一个**范围声明**和一个**范围表达式**。其中范围声明是一个变量的声明，其类型是范围表达式中元素的类型或者元素类型的引用。而范围表达式可以是数组或对象，对象必须满足以下 2 个条件中的任意一个：

1. 对象类型定义了 begin 和 end 成员函数。
2. 定义了以对象类型为参数的 begin 和 end 普通函数。

```cpp
std::map<int, std::string> index_map{ {1, "hello"}, {2, "world"}, {3, "!"} };
int int_array[] = { 0, 1, 2, 3, 4, 5 };
int main() {
    for (const auto &e : index_map) {
        std::cout << "key=" << e.first << ", value=" << e.second << std::endl;
    }
    for (auto e : int_array) {
        std::cout << e << std::endl;
    }
}
```

一般来说，我们希望*对于复杂的对象使用引用*，而*对于基础类型使用值*，因为这样能够减少内存的复制。如果不会在循环过程中修改引用对象，那么推荐在范围声明中加上 const 限定符以帮助编译器生成更加高效的代码：

```cpp
for (const auto &n : x) {}
```

另外，对于在遍历容器过程中有修改容器的需求，还是需要使用迭代器来处理。

## 第 18 章 支持初始化语句的 if 和 switch

在 C++17 标准中，if 控制结构可以在执行条件语句之前先执行一个初始化语句。语法如下：

```cpp
if (init; condition) {}
```

其中 init 是初始化语句，condition 是条件语句，它们之间使用分号分隔。

if 初始化语句中声明的变量拥有和整个 if 结构一样长的生命周期。从本质上来说初始化语句就是在执行条件判断之前先执行了一个语句，并且语句中声明的变量将拥有与 if 结构相同的生命周期。等价于：

```cpp
{
    init;
    if(condition) { ... }
}
```

利用该特性可以对整个 if 结构加锁，例如：

```cpp
std::mutex mx;
bool shared_flag = true;
int main() {
    if (std::lock_guard<std::mutex> lock(mx); shared_flag) {
        shared_flag = false;
    }
}
```

和 if 控制结构一样，switch 在通过条件判断确定执行的代码分支之前也可以接受一个初始化语句。switch 初始化语句声明的变量的生命周期会贯穿整个 switch 结构，这一点和 if 也相同：

```cpp
std::condition_variable cv;
std::mutex cv_m;
int main() {
    switch (std::unique_lock<std::mutex> lk(cv_m); cv.wait_for(lk, 100ms)) {
        case std::cv_status::timeout:
            break;
        case std::cv_status::no_timeout:
            break;
    }
}
```

_提示：所谓带初始化语句的 if 和 switch 的新特性只是一颗语法糖而已，其带来的功能可以轻易地用等价代码代替。_

## 第 19 章 static_assert 声明

static_assert 声明是 C++11 标准引入的特性，用于在程序编译阶段评估常量表达式并对返回 false 的表达式断言，我们称这种断言为静态断言。使用 static_assert 需要传入两个实参：常量表达式和诊断消息字符串（C++17 支持省略消息字符串）。

```cpp
static_assert(sizeof(int) >= 4, "sizeof(int) >= 4");
```

_注意：第一个实参必须是常量表达式，因为编译器无法计算运行时才能确定结果的表达式。_

## 第 20 章 结构化绑定

C++17 标准引入了**结构化绑定**特性，类似 Python 函数可以有多个返回值。

结构化绑定可以作用于 3 种类型，包括括原生数组、结构体和类对象、元组和类元组的对象。

-   绑定到原生数组

    ```cpp
    // 要求别名的数量与数组元素的个数一致
    int a[3]{ 1, 3, 5 };
    auto[x, y, z] = a;
    ```

-   绑定到结构体和类对象

    ```cpp
    /*
    * 要求：
    * 1. 非静态数据成员个数必须和标识符列表中的别名的个数相同
    * 2. 这些数据成员必须是公有的（C++20修改了这项规则）
    * 3. 这些数据成员必须是在同一个类或者基类中
    * 4. 绑定的类和结构体中不能存在匿名联合体
    */
    class BindBase1 {
    public:
        int a = 42;
        double b = 11.7;
    };
    class BindTest1 : public BindBase1 {};
    class BindBase2 {};
    class BindTest2 : public BindBase2 {
    public:
        int a = 42;
        double b = 11.7;
    };
    class BindBase3 {
    public:
        int a = 42;
    };
    class BindTest3 : public BindBase3 {
    public:
        double b = 11.7;
    };
    int main()
    {
        BindTest1 bt1;
        BindTest2 bt2;
        BindTest3 bt3;
        auto[x1, y1] = bt1; // 编译成功
        auto[x2, y2] = bt2; // 编译成功
        auto[x3, y3] = bt3; // 编译错误
    }
    ```

-   绑定到元组和类元组的对象

    ```cpp
    // std::tuple, std::pair和std::array都能作为绑定目标
    std::map<int, std::string> id2str{ {1, "hello"}, {3, "Structured"}, {5, "bindings"} };
    for (const auto& elem : id2str) {  // C++17前的写法
        std::cout << "id=" << elem.first << ", str=" << elem.second << std::endl;  // map中的元素是pair<int, string>类型
    }
    for (const auto& [id, str] : id2str) {  // 加入结构化绑定简化代码，增加了可读性
        std::cout << "id=" << id << ", str=" << str << std::endl;
    }
    ```

## 第 21 章 noexcept 关键词

由于 C++11 标准引入了移动构造函数，其中包含着一个严重的异常陷阱，即，如果数据的移动发生异常，那么原容器也将无法继续使用。针对这样的问题，C++ 标准委员会提出了 noexcept 说明符。

noexcept 是一个与异常相关的关键字，它既是一个说明符，也是一个运算符。作为说明符，它能够用来说明函数是否会抛出异常，例如：

```cpp
int foo() noexcept { return 42; }
```

另外，noexcept 还能接受一个返回布尔的常量表达式，当表达式评估为 true 的时候，其行为和不带参数一样，表示函数不会抛出异常。反之，当表达式评估为 false 的时候，则表示该函数有可能会抛出异常。这个特性广泛应用于模板当中，例如：

```cpp
template <class T>
T copy(const T & o) noexcept(std::is_fundamental<T>::value) {
	...
}
```

现在 noexcept 运算符可以判断目标类型的移动构造函数是否有可能抛出异常。如果没有抛出异常的可能，那么函数可以选择进行移动操作；否则将使用传统的复制操作。

```cpp
template<typename T>
void swap_impl(T& a, T& b, std::integral_constant<bool, true>) noexcept {
	T tmp(std::move(a));
	a = std::move(b);
	b = std::move(tmp);
}
template<typename T>
void swap_impl(T& a, T& b, std::integral_constant<bool, false>) {
	T tmp(a);
    a = b;
    b = tmp;
}
```

C++11 标准规定下面几种函数会默认带有 noexcept 声明。

1. 默认构造函数、默认复制构造函数、默认赋值函数、默认移动构造函数和默认移动赋值函数。
2. 类型的析构函数以及 delete 运算符。

## 第 22 章 类型别名和别名模板

曾经，为了让代码看起来更加简洁，往往会使用 typedef 为较长的类型名定义一个别名，例如`typedef std::map::const_iterator map_const_iter;`。

C++11 标准提供了一个新的定义类型别名的方法，该方法使用 using 关键字，具体语法如下：

```cpp
using identifier = type-id
```

## 第 23 章 指针字面量 nullptr

在 C++ 标准中有一条特殊的规则，即 0 既是一个整型常量，又是一个空指针常量。NULL 是一个宏，在 C++11 标准之前其本质就是 0。使用 0 代表不同类型的特殊规则给 C++带来了二义性，造成不必要的隐式类型转换。

鉴于 0 作为空指针常量的种种劣势，C++标准委员会在 C++11 中添加**关键字 nullptr** 表示空指针的字面量，它是一个 std::nullptr_t 类型的纯右值。

## 第 24 章 三向比较

C++20 标准新引入了一个名为“太空飞船”（spaceship）的运算符 <=>，它是一个三向比较运算符。该特性的引入为实现比较运算提供了方便，我们只需要实现==和<=>两个运算符函数，剩下的 4 个运算符函数就可以交给编译器自动生成了。

顾名思义，三向比较就是在形如 lhs <=> rhs 的表达式中，两个比较的操作数 lhs 和 rhs 通过<=>比较可能产生 3 种结果，该结果可以和 0 比较，小于 0、等于 0 或者大于 0 分别对应 lhs < rhs、lhs == rhs 和 lhs > rhs。例如：

```cpp
bool b = 7 <=> 11 < 0;    // b == true
bool b = 7 <=> 11 < 100;  // 编译失败，<=>的结果不能与除0以外的数值比较
```

可以看出<=>的返回结果并不是一个普通类型，根据标准三向比较会返回 3 种类型，分别为 std::strong_ordering、std::weak_ordering 以及 std:: partial_ordering，而这 3 种类型又会分为有 3 ～ 4 种最终结果。

`std::strong_ordering`有 3 种比较结果，分别为`std::strong_ ordering::less`、`std::strong_ordering::equal`以及`std::strong_ ordering::greater` ，表达的是一种可替换性，即，若 lhs == rhs，那么在任何情况下 rhs 和 lhs 都可以相互替换。

`std::weak_ordering`类型也有 3 种比较结果，分别为`std::weak_ ordering::less`、`std::weak_ordering::equivalent`以及`std::weak_ordering::greater`。`std::weak_ordering`的含义正好与`std::strong_ ordering`相对，表达的是不可替换性。

`std::partial_ordering`类型有 4 种比较结果，分别为`std::partial_ ordering::less`、`std::partial_ordering::equivalent`、`std::partial_ordering::greater`以及`std::partial_ordering::unordered`。`std::partial_ordering`约束力比`std::weak_ordering`更弱，它可以接受当 lhs == rhs 时 rhs 和 lhs 不能相互替换，同时它还能给出第四个结果`std::partial_ordering::unordered`，表示进行比较的两个操作数没有关系。

这 3 个类只实现了参数类型为自身类型和`nullptr_t`的比较运算符函数，因此只能和 0 及自身类型比较。

C++20 标准规定，如果用户为自定义类型声明了三向比较运算符，那么编译器会为其自动生成<、>、<=和>=这 4 种运算符函数。例如：

```cpp
struct Person {
    std::string name;
    int age;

    // 自定义 <=> 运算符
    std::strong_ordering operator<=>(const Person& other) const {
        // 先比较年龄
        if (auto cmp = age <=> other.age; cmp != 0) {
            return cmp;  // 如果年龄不同，返回年龄的比较结果
        }
        // 如果年龄相同，再比较名字
        return name <=> other.name;
    }

    // 同时还可以定义 operator== 来配合三路比较运算符
    bool operator==(const Person& other) const = default;
};

int main() {
    Person alice{"Alice", 30};
    Person bob{"Bob", 25};

    if (alice < bob) {
        std::cout << "Alice is less than Bob\n";
    } else if (alice == bob) {
        std::cout << "Alice is equal to Bob\n";
    } else {
        std::cout << "Alice is greater than Bob\n";
    }
    return 0;
}
```

## 第 25 章 线程局部存储

C++11 标准中正式添加了新的 thread_local 说明符来声明线程局部存储变量。

```cpp
struct X {
	thread_local static int i;
};
thread_local X a;
```

它能够解决全局变量或者静态变量在多线程操作中存在的问题，一个典型的例子就是 errno。线程间的竞争关系破坏了 errno 的准确性，导致不确定的结果。直到 C++11 之前，errno 都是一个静态变量，而从 C++11 开始 errno 被修改为一个线程局部存储变量。

## 第 26 章 扩展的 inline 说明符

在 C++17 标准之前，**非常量静态成员变量**的声明和定义必须分开进行。因为静态成员变量虽然属于类，但它不属于类的任何特定对象，它实际上是一个全局变量，需要在全局作用域中分配内存。

```cpp
class X {
public:
    static std::string text;    // 非常量静态成员不能同时声明和定义
    static const int num{ 5 };  // 常量静态成员可以同时声明和定义
};

std::string X::text{ "hello" };
```

为了解决上面这些问题，C++17 标准中增强了 inline 说明符的能力，它允许我们内联定义静态变量，例如：

```cpp
class X {
public:
    inline static std::string text{"hello"};
};
```

## 第 27 章 常量表达式

在 C++11 标准以前，没有一种方法能够有效地要求一个变量或者函数在编译阶段就计算出结果，导致很多看起来合理的代码却引来编译错误。这些场景主要集中在需要编译阶段就确定的值语法中，比如 case 语句、数组长度、枚举成员的值以及非类型的模板参数。const 定义的常量和宏并不可靠。因为预处理器对于宏只是简单的字符替换，完全没有类型检查；const 定义的常量可能是一个运行时常量，这种情况下是无法在 case 语句以及数组长度等语句中使用的。

为了解决以上常量无法确定的问题，C++11 标准中定义了一个新的**关键字 constexpr**，它能够有效地定义常量表达式，并且达到类型安全、可移植、方便库和嵌入式系统开发的目的。

constexpr 值即**常量表达式值**，是一个用 constexpr 说明符声明的变量或者数据成员，它要求该值必须在编译期计算。另外，常量表达式值必须被常量表达式初始化。例如：

```cpp
constexpr int x = 42;
char buffer[x] = { 0 };
```

在很多时候，constexpr 值和 const 值是没有区别的，但是 const 并没有确保编译期常量的特性，所以在下面的代码中，它们会有不同的表现：

```cpp
int x1 = 42;
const int x2 = x1;        // 定义和初始化成功
char buffer[x2] = { 0 };  // 编译失败，x2无法作为数组长度
```

constexpr 不仅能用来定义常量表达式值，还能定义一个**常量表达式函数**，即 constexpr 函数，常量表达式函数的返回值可以在编译阶段就计算出来。C++11 对定义常量表达式有非常严格的约束：

1. 返回类型不能是 void
2. 函数体必须只有一条语句：return expr，其中 expr 必须也是一个常量表达式。
3. 函数使用之前必须有定义
4. 函数必须用 constexpr 声明

例如：

```cpp
constexpr int max_unsigned_char() {
    return 0xff;
}
constexpr int square(int x) {
    return x * x;
}
constexpr int abs(int x) {
    return x > 0 ? x : -x;
}
```

这些约束在 C++14 得到了巨大的改善：

1. 函数体允许声明变量，除了没有初始化、static 和 thread_local 变量。
2. 函数允许出现 if 和 switch 语句，不能使用 go 语句。
3. 函数允许所有的循环语句，包括 for、while、do-while。
4. 函数可以修改生命周期和常量表达式相同的对象。
5. 函数的返回值可以声明为 void。
6. constexpr 声明的成员函数不再具有 const 属性。

C++11 中不能通过编译的很多函数在 C++14 中可以使用了，例如：

```cpp
constexpr int abs(int x) {
    if (x > 0) {
        return x;
    } else {
        return -x;
    }  // 规则2
}
constexpr int next(int x) {
    return ++x;  // 规则4
}
constexpr int sum(int x) {
    int result = 0;  // 规则1
    while (x > 0) {
        result += x--;
    }  // 规则3
    return result;
}
```

常量表达式函数的返回值可以在编译期计算出来，但是这个行为并不是确定的。例如，当带形参的常量表达式函数接受了一个非常量实参时，常量表达式函数可能会退化为普通函数：

```cpp
constexpr int square(int x) {
    return x * x;
}
int x = 5;
std::cout << square(x);

```

constexpr 可以声明基础类型从而获得常量表达式值，除此之外 constexpr 还能够声明用户自定义类型。为了能够构造 constexpr 声明的类，需要**常量表达式构造函数**，这个构造函数也需要遵循一些规则：

1. 构造函数必须用 constexpr 声明
2. 构造函数初始化列表中必须是常量表达式
3. 构造函数的函数体必须为空

常量表达式构造函数拥有和常量表达式函数相同的退化特性，当它的实参不是常量表达式的时候，构造函数可以退化为普通构造函数。

_注意：使用 constexpr 声明自定义类型的变量，必须确保这个自定义类型的析构函数是平凡的。_

从 C++17 开始，**lambda 表达式**在条件允许的情况下都会隐式声明为 constexpr。

在 C++17 标准中，constexpr 声明静态成员变量时，也被赋予了该变量的内联属性。

`if constexpr` 是 C++17 标准提出的一个非常有用的特性，可以用于编写紧凑的模板代码，让代码能够根据编译时的条件进行实例化。

C++20 标准允许**constexpr 虚函数**。
