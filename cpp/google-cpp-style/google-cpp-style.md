# Google C++ Style Guide

## 头文件

通常每个`.cc`文件都应该有一个配套的`.h`头文件，常见的例外情况包括单元测试和仅有`main()`函数的`.cc`文件。正确使用头文件会大大改善代码的可读性和执行文件的大小、性能。

### 自给自足的头文件

头文件应该自给自足 (self-contained, 也就是可以独立编译) ，并以`.h`为扩展名。具体来说，头文件应该：

-   包含头文件防护符
-   导入它所需的所有其他头文件

若头文件声明了内联函数 (inline function) 或模版 (template) ，而且头文件的使用者需要实例化 (instantiate) 这些组件时，头文件必须直接或通过导入的文件间接提供这些组件的实现 (definition) 。

少数情况下，用于导入的文件不能自给自足，这类文件不需要头文件防护符，应该使用`.inc`扩展名。尽量少用这种文件。

### #define 防护符

所有头文件都应该用`#define`防护符来防止重复导入。为了保证符号的唯一性，防护符的名称应该基于该文件在项目目录中的完整文件路径，格式是`<项目>_<路径>_<文件名>_H_`。例如，`foo` 项目中的文件 `foo/src/bar/baz.h` 应该有如下防护：

```cpp
#ifndef FOO_BAR_BAZ_H_
#define FOO_BAR_BAZ_H_
...
#endif  // FOO_BAR_BAZ_H_
```

### 导入你的依赖

若代码或头文件引用了其他地方定义的符号 (symbol)，该文件应该直接导入 (include) 提供该符号的声明 (declaration) 或者定义 (definition) 的头文件。不要依赖间接导入，这样，删除不再需要的`#include`语句时，才不会影响使用者。

### 前向声明

尽量避免使用前向声明，而是用导入所需的头文件代替。

前向声明 (forward declaration) 是没有对应定义 (definition) 的声明：

```cpp
class B;
void FuncInB();
extern int variable_in_b;
ABSL_DECLARE_FLAG(flag_in_b);
```

优点：

-   使用前向声明能节约编译时间，因为 `#include` 会迫使编译器打开更多的文件并处理更多的输入。
-   使用前向声明能避免不必要的重复编译. 若使用 `#include`，头文件中无关的改动也会触发重新编译。

缺点：

-   前向声明隐藏了依赖关系，可能会让人忽略头文件变化后必要的重新编译过程。
-   相比 `#include`，前向声明的存在会让自动化工具难以发现定义该符号的模块。
-   修改库 (library) 时可能破坏前向声明。
-   为 `std::` 命名空间的符号提供前向声明会产生未定义行为 (undefined behavior)。
-   前向声明可能改变代码的含义。

### 内联函数

只把 10 行以内的小函数定义为内联 (inline)。

内联函数可以令目标代码 (object code) 更加高效，通常鼓励对存取函数 (accessors)、变异函数 (mutators) 和其它短小且影响性能的函数使用内联展开，但是滥用内联将拖慢程序。内联一个巨大的函数将显著增加代码大小，通常代码越小执行越快，因为指令缓存利用率高。

内联那些有循环或 `switch` 语句的函数通常得不偿失 (除非这些循环或 `switch` 语句通常不执行)。

_注意：即使函数被声明为内联函数，也不一定真的会被内联，比如虚函数和递归函数。_

### #include 的路径及顺序

推荐按照以下顺序导入头文件，并且在每种类别的头文件之间添加空行：

1. 配套的头文件
2. C 语言系统库头文件
3. C++标准库头文件
4. 其他库的头文件
5. 本项目的头文件

_也有人提出把库文件放在最后，这样出错先是项目内的文件，保证内部错误被及时发现。_

头文件的路径应相对于项目源码目录，不能出现 UNIX 目录别名 (alias) `.` (当前目录) 或 `..` (上级目录)。

## 作用域

### 命名空间

命名空间可以将全局作用域 (global scope) 划分为独立的、有名字的作用域，因此可以有效防止全局作用域中的命名冲突 (name collision)。命名空间可以避免大型程序中的命名冲突，同时代码可以继续使用简短的名称。

除了少数特殊情况，应该在命名空间 (namespace) 内放置代码，命名空间应该有独一无二的名字，其中包含项目名称，也可以选择性地包含文件路径。建议按如下方法使用命名空间：

-   遵守命名空间命名规则
-   用注释给命名空间收尾
-   在导入语句之后、gflags 声明/定义及其他命名空间的类的前向声明 (forward declaration) 之后，用命名空间包裹整个源代码文件。

```cpp
// .h 文件
namespace mynamespace {

// 所有声明都位于命名空间中.
// 注意没有缩进.
class MyClass {
    public:
    ...
    void Foo();
};

}  // namespace mynamespace
```

```cpp
// .cc 文件
namespace mynamespace {

// 函数定义位于命名空间中.
void MyClass::Foo() {
    ...
}

}  // namespace mynamespace
```

禁止在命名空间内使用 using 指令 (例如 `using namespace foo`) ；禁止使用内联 (inline) 命名空间；不要在 `std` 命名空间内声明任何东西；不要前向声明 (forward declare) 标准库的类。

### 内部链接

若其他文件不需要使用 `.cc` 文件中的定义，这些定义可以放入匿名命名空间 (unnamed namespace) 或声明为 `static`，以实现内部链接 (internal linkage)。所有放入匿名命名空间中的声明都会内部链接，声明为 `static` 的函数和变量也会内部链接，这意味着其他文件不能访问你声明的任何事物。

建议 `.cc` 文件中所有不需要外部使用的代码采用内部链接：

```cpp
namespace {
...
}  // namespace
```

### 非成员函数、静态成员函数和全局函数

非成员函数和静态成员函数在某些情况下有用。若将非成员函数放在命名空间内，不会污染全局命名空间。建议将非成员 (nonmember) 函数放入命名空间。

尽量不要使用完全全局的函数 (completely global function)。有时我们需要定义一个和类的实例无关的函数，这样的函数可以定义为静态成员函数或非成员函数。非成员函数不应该依赖外部变量，且大部分情况下应该位于命名空间中。

### 局部变量

应该尽可能缩小函数变量的作用域 (scope)，并在声明的同时初始化，且声明离第一次使用的位置越近越好。

_注意：如果变量是一个对象，那么它每次进入作用域时会调用构造函数，退出作用域时会调用析构函数，在循环外声明这类变量更高效。_

### 静态和全局变量

每个对象 (object) 都有与生命周期 (linetime) 相关的储存周期 (storage duration)。静态储存周期对象的存活时间是从程序初始化开始，到程序结束为止。这些对象可能是命名空间作用域内的变量 (全局变量)、类的静态数据成员或者用 `static` 修饰符 (specifier) 声明的函数局部变量。

全局或静态变量对很多场景有帮助：具名常量 (named constants)、编译单元 (translation unit) 内部的辅助数据结构、命令行旗标 (flag)、日志、注册机制、后台基础设施等等。

使用动态初始化或具有非平凡析构函数的全局和静态变量时，会增加代码复杂度，不同编译单元的动态初始化顺序不确定，析构顺序也不确定。平凡的析构函数不受执行顺序影响，因此，只有拥有平凡析构函数的对象才能采用静态储存周期。例如：基本类型、基本类型数组、用`constexpr`修饰的变量。

常用的语法结构：

-   全局字符串：如果需要具名的 (named) 全局或静态字符串常量，可以采用 `constexpr` 修饰的 `string_view` 变量、字符数组或指向字符串字面量 (literal) 的字符指针。
-   字典和集合等动态容器 (container)：若你需要用静态变量储存不会改变的数据 (例如用于搜索的集合或查找表)，不要使用标准库的动态容器，因为这些容器拥有非平凡的析构函数。可以考虑用平凡类型的数组替代。
-   智能指针：智能指针在析构时有释放资源的操作，因此不能作为静态变量。简单的解决方式是，用裸指针 (plain pointer) 指向动态分配的对象，并且永远不删除这个对象。
-   自定义类型的静态变量：如果静态数据或常量数据是自定义类型，请给这一类型设置平凡的析构函数和 `constexpr` 修饰的构造函数。

### thread_local 变量

可以用 `thread_local` 修饰符声明变量：

```cpp
thread_local Foo foo = ...;
```

这样的变量本质上其实是一组不同的对象，不同线程访问该变量时，会访问各自的对象。`thread_local` 变量在很多方面类似于静态存储周期的变量，区别是 `thread_local` 实例会在每个线程启动时初始化，这意味着函数内的 `thread_local` 变量是线程安全的。

那些用于全局/静态变量的、预防野指针的方法不适用于 `thread_local`。这些变量不会随着程序终止而自然结束，必须进行析构（？没看懂）。注意, 线程退出时会销毁 `thread_local` 变量。

位于类或命名空间中的 `thread_local` 变量只能用真正的编译时常量来初始化 (也就是不能动态初始化)，必须用 ABSL_CONST_INIT 修饰来保证这一点 (也可以用 `constexpr` 修饰, 但不常见)。

函数中的 `thread_local` 变量没有初始化的顾虑，但是在线程退出时有释放后使用的风险。

## 类

### 构造函数的内部操作

构造函数 (constructor) 中不得调用虚函数 (virtual method)。在构造函数内调用虚函数时，不会分派 (dispatch) 到子类的虚函数实现 (implementation)。

不要在没有错误处理机制的情况下进行可能失败的初始化。构造函数难以报告错误，如果初始化失败，对象将处于异常状态。若要添加 `Init()` 方法，应该确保可以从对象的状态中得知哪些公用方法是可用的。若在对象未构造完成时调用其方法，很容易导致错误。

### 隐式类型转换

不要定义隐式类型转换。隐式转换可能掩盖类型不匹配错误，有时目标类型与用户预期不符，甚至用户不知道会出现类型转换。隐式转换降低了代码可读性，尤其是存在函数重载的情况下，难以判断实际调用的函数。

```cpp
class Date {
public:
    Date(int year) {
        // 初始化日期
    }
};

Date d = 2024; // 隐式转换，可能产生歧义
```

定义类型转换运算符和单个参数的构造函数时，可以在构造函数或类型转换运算符上添加 `explicit` 关键字，确保使用者必须明确指定目标类型，例如使用转换 (cast) 运算符。

```cpp
class Date {
public:
    explicit Date(int year) {
        // 初始化日期
    }
};

Date d = Date(2024); // 显式转换，代码的意图更加明确
```

### 可拷贝类型和可移动类型

可移动类型 (movable type) 可以用临时变量初始化或赋值。

可拷贝类型 (copyable type) 可以用另一个相同类型的对象初始化 (因此从定义上来说也是可移动的) ，同时源对象的状态保持不变。

编译器会在某些情况下隐式调用拷贝或移动构造函数，例如使用值传递 (pass by value) 传递参数时。

类的公有接口必须明确指明该类是可拷贝的、仅可移动的、还是既不可拷贝也不可移动的，通常应在声明的 `public` 部分显式声明或删除对应的操作。如果该类型的复制和移动操作有明确的语义并且有用，则应该支持这些操作。

```cpp
class Copyable {
 public:
  Copyable(const Copyable& other) = default;
  Copyable& operator=(const Copyable& other) = default;
  // 上面的声明阻止了隐式移动运算符.
  // 您可以显式声明移动操作以支持更高效的移动.
};

class MoveOnly {
 public:
  MoveOnly(MoveOnly&& other) = default;
  MoveOnly& operator=(MoveOnly&& other) = default;

  // 复制操作被隐式删除了, 但您也可以显式删除:
  MoveOnly(const MoveOnly&) = delete;
  MoveOnly& operator=(const MoveOnly&) = delete;
};

class NotCopyableOrMovable {
 public:
  // 既不可复制也不可移动.
  NotCopyableOrMovable(const NotCopyableOrMovable&) = delete;
  NotCopyableOrMovable& operator=(NotCopyableOrMovable&) = delete;

  // 移动操作被隐式删除了, 但您也可以显式声明:
  NotCopyableOrMovable(NotCopyableOrMovable&&) = delete;
  NotCopyableOrMovable& operator=(NotCopyableOrMovable&&) = delete;
};
```

为了避免对象切割的风险，基类最好是抽象 (abstract) 类。若要声明抽象类，可以将构造函数或析构函数声明为 `protected`，或者声明纯虚 (pure virtual) 成员函数。尽量避免继承一个具体类 (concrete class)。

_对象切割：将派生类对象赋值给基类对象时，派生类特有的部分被“切割”掉，只保留基类部分的过程。_

### 结构体还是类

只能用 `struct` 定义那些用于储存数据的被动对象，其他情况应该使用 `class`。

_注意：C++ 中 `struct` 和 `class` 关键字的含义几乎一样。_

### 结构体、数对还是元组

虽然使用数对和元组可以免于定义自定义类型，但是有意义的成员名称比`.first`, `.second` 和 `std::get<X>` 更可读。如果可以给成员起一个有意义的名字，应该用结构体而不是数对 (pair) 或元组 (tuple) 。

### 继承

当子类继承基类时，子类会包含基类定义的所有数据及操作。**接口继承** (interface inheritance) 指从纯抽象基类 (pure abstract base class, 不包含状态或方法定义) 继承；所有其他继承都是**实现继承** (implementation inheritance)。对于实现继承，子类的实现代码散布在父类和子类之间，因此更难理解。子类不能重写 (override) 父类的非虚函数，因此无法修改其实现。多重继承的问题更严重，通常会产生更大的性能开销，且容易产生**菱形继承** (diamond inheritance) 的模式，造成歧义、混乱和严重错误。

所有继承都应该使用 `public` 的访问权限。当不希望您的类被继承时，可以使用 `final` 关键字。

通常情况下，组合 (composition) 比继承 (inheritance) 更合适。尽量只在 “什么是什么” (“is-a”, 其他 “has-a” 情况下请使用组合) 关系的情况下使用继承。

明确使用 `override` 或 `final` (较少使用) 关键字限定重写的虚函数或者虚析构函数。

### 运算符重载

谨慎使用运算符重载 (overload) 。重载运算符应该意义明确，符合常理，并且与对应的内置运算符行为一致。

-   建议将不修改数据的二元运算符定义为非成员函数。如果二元运算符是成员函数，那么右侧的参数可以隐式类型转换，左侧却不能。此时可能 `a + b` 能编译而 `b + a` 编译失败，令人困惑。
-   对可以判断相等性的类型 `T`，请定义非成员运算符 `operator==`，并用文档说明什么条件下认为 `T` 的两个值相等。
-   不要重载 `&&`, `||`, `,` 或一元的 (unary) `&` 运算符。
-   不要重载 `operator""`，即禁止自定义字面量 (user-defined literal) 。

### 访问控制

类的所有数据成员应该声明为私有 (private)，除非是常量。这样做可以简化类的不变式 (invariant) 逻辑，代价是需要增加一些冗余的访问器 (accessor) 代码 (通常是 const 方法)。

### 声明次序

类的定义通常以 `public:` 开头，其次是 `protected:`，最后以 `private:` 结尾。空的部分可以省略。

在各个部分中，应该将相似的声明分组，并建议使用以下顺序：

1. 类型和类型别名 (`typedef`, `using`, `enum`, 嵌套结构体和类, 友元类型)
2. (可选, 仅适用于结构体) 非静态数据成员
3. 静态常量
4. 工厂函数 (factory function)
5. 构造函数和赋值运算符
6. 析构函数
7. 所有其他函数 (包括静态与非静态成员函数, 还有友元函数)
8. 所有其他数据成员 (包括静态和非静态的)

## 函数

### 输入和输出

C++ 函数由返回值提供天然的输出，有时也通过输出参数（或输入/输出参数）提供。我们倾向于使用返回值而不是输出参数：它们提高了可读性，并且通常提供相同或更好的性能。

C/C++ 中的函数参数或者是函数的输入，或者是函数的输出，或兼而有之。非可选输入参数通常是值参或 `const` 引用，非可选输出参数或输入/输出参数通常应该是引用 （不能为空）。对于可选的参数，通常使用 `std::optional` 来表示可选的按值输入；使用 `const` 指针来表示可选的其他输入；使用非常量指针来表示可选输出和可选输入/输出参数。

在排序函数参数时，将所有输入参数放在所有输出参数之前。

### 编写简短函数

使函数尽量简短，以便于他人阅读和修改代码。如果函数超过 40 行，可以思索一下能不能在不影响程序结构的前提下对其进行分割。

### 函数重载

通过重载参数不同的同名函数，可以令代码更加直观。

但是，如果函数单靠不同的参数类型而重载（参数数量不变），调用者可能需要熟悉 C++ 复杂的匹配规则，来了解具体的匹配过程。

如果打算重载一个函数，可以试试改在函数名里加上参数信息。例如，用 `AppendString()` 和 `AppendInt()` 等，而不是一口气重载多个 `Append()`。

### 缺省参数

有些函数一般情况下使用默认参数，但有时需要又使用非默认的参数，缺省参数为这样的情形提供了便利。缺省参数实际上是函数重载语义的另一种实现方式，但是有以下缺点：

-   缺省参数在每个调用点都要进行重新求值， 这会造成生成的代码迅速膨胀。
-   缺省参数会干扰函数指针，导致函数签名与调用点的签名不一致。
-   对于虚函数，不允许使用缺省参数，因为在虚函数中缺省参数不一定能正常工作。

一般情况下建议使用函数重载，除非缺省参数对可读性的提升远远超过了以上提及的缺点。

### 函数返回类型后置语法

可以在函数名前使用 `auto` 关键字，在参数列表之后后置返回类型。只有在常规写法 (返回类型前置) 不便于书写或不便于阅读时使用返回类型后置语法。

```cpp
auto foo(int x) -> int;  // C++11 引入的新形式
```

后置返回类型是显式地指定 Lambda 表达式的返回值的唯一方式。

## 来自 Google 的奇技

Google 用了很多自己实现的技巧 / 工具使 C++ 代码更加健壮。

### 所有权与智能指针

动态分配出的对象最好有单一且固定的所有主，并通过智能指针传递所有权。

_所有权：所有权是一种登记／管理动态内存和其它资源的技术。动态分配对象的所有主是一个对象或函数，后者负责确保当前者无用时就自动销毁前者。_

_智能指针：智能指针是一个通过重载 `_`和`->` 运算符以表现得如指针一样的类。`std::unique_ptr`用来表示动态分配出的对象的独一无二的所有权；`std::shared_ptr`对象的所有权由所有复制者共同拥有，最后一个复制者被销毁时，对象也会随着被销毁。\*

如果必须使用动态分配，那么更倾向于将所有权保持在分配者手中。如果其他地方要使用这个对象，最好传递它的拷贝，或者传递一个不用改变所有权的指针或引用。倾向于使用 `std::unique_ptr` 来明确所有权传递，例如：

```cpp
// 调用者获得所有权
std::unique_ptr<Foo> FooFactory();
// 由于不允许拷贝，智能指针的所有权被传递给函数
void FooConsumer(std::unique_ptr<Foo> ptr);
// 离开函数作用域，智能指针被销毁，管理的对象被析构
```

### Cpplint

使用 `cpplint.py` 检查风格错误。

## 其他 C++ 特性

### 右值引用

右值引用是一种能绑定到右值表达式的引用类型，其语法与传统的引用语法相似。例如，`void f(string&& s);`声明了一个其参数是一个字符串的右值引用的函数。

只在定义移动构造函数与移动赋值操作时使用右值引用，不要使用 `std::forward` 功能函数。你可能会使用 `std::move` 来表示将值从一个对象移动而不是复制到另一个对象。

### 变长数组和 alloca()

变长数组和 `alloca()` 不是标准 C++ 的组成部分，应该改用更安全的分配器（allocator），就像 `std::vector` 或 `std::unique_ptr<T[]>`。

### 友元

友元扩大了 (但没有打破) 类的封装边界。某些情况下，将一个单元测试类声明成待测类的友元会很方便。通常友元应该定义在同一文件内，避免代码读者跑到其它文件查找使用该私有成员的类。

### 异常

异常是处理构造函数失败的唯一途径。但是，异常会彻底扰乱程序的执行流程并难以判断。

尽量不使用 C++异常，而是使用其替代方案，如错误代码、断言。

### 运行时类型识别

RTTI 允许程序员在运行时识别 C++ 类对象的类型。RTTI 有合理的用途但是容易被滥用，在单元测试中可以使用 RTTI，但是在其他代码中请尽量避免。

### 类型转换

不要使用 C 风格类型转换，而应该使用 C++ 风格。

-   用 `static_cast` 替代 C 风格的值转换，或某个类指针需要明确的向上转换为父类指针时。
-   用 `const_cast` 去掉 `const` 限定符。
-   用 `reinterpret_cast` 指针类型和整型或其它指针之间进行不安全的相互转换。

### 流

流用来替代 `printf()` 和 `scanf()`，在打印时不需要关心对象的类型，流的构造和析构函数会自动打开和关闭对应的文件。但是，流使得 `pread()` 等功能函数很难执行，且流不支持字符串操作符重新排序。

不要使用流, 除非是日志接口需要。使用 `printf` 之类的代替。

### 前置自增和自减

对于迭代器和其他模板对象使用前缀形式 (`++i`) 的自增、自减运算符。不考虑返回值的话，前置自增 (`++i`) 通常要比后置自增 (`i++`) 效率更高。因为后置自增 (或自减) 需要对表达式的值 `i` 进行一次拷贝。

### `const` 用法

在声明的变量或参数前加上关键字 `const` 用于指明变量值不可被篡改；为类中的函数加上 `const` 限定符表明该函数不会修改类成员变量的状态。`const` 变量、数据成员、函数和参数为编译时类型检测增加了一层保障，便于尽早发现错误。因此，我们强烈建议在任何可能的情况下使用 `const`：

-   如果函数不会修改你传入的引用或指针类型参数，该参数应声明为 `const`。
-   尽可能将函数声明为 `const`。访问函数应该总是 `const`。其他不会修改任何数据成员、未调用非 `const` 函数、不会返回数据成员非 `const` 指针或引用的函数也应该声明成 `const`。
-   如果数据成员在对象构造之后不再发生变化，可将其定义为 `const`。

### `constexpr` 用法

变量可以被声明成 constexpr 以表示它是真正意义上的常量，即在编译时和运行时都不变。函数或构造函数也可以被声明成 constexpr，以用来定义 constexpr 变量。

### 预处理宏

使用宏时要非常谨慎，尽量以内联函数、枚举和常量代替之。可以用内联函数替代。用宏表示常量可被 `const` 变量代替。如果你要宏，尽可能遵守：

-   不要在 `.h` 文件中定义宏。
-   在马上要使用时才进行 `#define`，使用后要立即 `#undef`。
-   不要只是对已经存在的宏使用#undef，选择一个不会冲突的名称。
-   不要试图使用展开后会导致 C++ 构造不稳定的宏，不然也至少要附上文档说明其行为。
-   不要用 `##` 处理函数，类和变量的名字。

### 0, `nullptr` 和 `NULL`

指针使用 `nullptr`，字符使用 `'\0'` (而不是 `0` 字面值)。

对于指针 (地址值)，使用 `nullptr`，因为这保证了类型安全。

使用 `'\0'` 作为空字符。使用正确的类型使代码更具可读性。

### 列表初始化

C++11 中，列表初始化得到进一步的推广，任何对象类型都可以被列表初始化。示范如下：

```cpp
// Vector 接收了一个初始化列表。
vector<string> v{"foo", "bar"};

// 不考虑细节上的微妙差别，大致上相同。
// 您可以任选其一。
vector<string> v = {"foo", "bar"};

// 可以配合 new 一起用。
auto p = new vector<string>{"foo", "bar"};

// map 接收了一些 pair, 列表初始化大显神威。
map<int, string> m = {{1, "one"}, {2, "2"}};

// 初始化列表也可以用在返回类型上的隐式转换。
vector<int> test_function() { return {1, 2, 3}; }

// 初始化列表可迭代。
for (int i : {-1, -2, -3}) {}

// 在函数调用里用列表初始化。
void TestFunction2(vector<int> v) {}
TestFunction2({1, 2, 3});
```

用户自定义类型也可以定义接收 `std::initializer_list<T>` 的构造函数和赋值运算符，以自动列表初始化：

```cpp
class MyType {
 public:
  // std::initializer_list 专门接收 init 列表。
  // 得以值传递。
  MyType(std::initializer_list<int> init_list) {
    for (int i : init_list) append(i);
  }
  MyType& operator=(std::initializer_list<int> init_list) {
    clear();
    for (int i : init_list) append(i);
  }
};
MyType m{2, 3, 5, 7};
```

不要用列表初始化 auto 变量：

```cpp
auto d = {1.23};        // d 即是 std::initializer_list<double>
auto d = double{1.23};  // 善哉 -- d 即为 double, 并非 std::initializer_list.
```

### Lambda 表达式

Lambda 表达式是创建匿名函数对象的一种简易途径，常用于把函数当参数传，例如：

```cpp
std::sort(v.begin(), v.end(), [](int x, int y) {
    return Weight(x) < Weight(y);
});
```

它们还允许通过名称显式或隐式使用默认捕获从封闭范围中捕获变量。显式捕获要求将每个变量作为值或引用捕获列出：

```cpp
int weight = 3;
int sum = 0;
// Captures `weight` by value and `sum` by reference.
std::for_each(v.begin(), v.end(), [weight, &sum](int x) {
  sum += weight * x;
});
```

默认捕获隐式捕获 lambda 正文中引用的任何变量，包括 this 是否使用了任何成员：

```cpp
const std::vector<int> lookup_table = ...;
std::vector<int> indices = ...;
// Captures `lookup_table` by reference, sorts `indices` by the value
// of the associated element in `lookup_table`.
std::sort(indices.begin(), indices.end(), [&](int a, int b) {
  return lookup_table[a] < lookup_table[b];
});
```

使用 lambda 表达式时需要注意：

-   如果 lambda 可能离开当前作用域，则首选显式捕获。
-   仅当 lambda 的生存期明显短于任何潜在捕获时，才使用默认的引用捕获 （ [&] ）。
-   仅使用默认的按值 （ [=] ） 捕获作为绑定短 lambda 的几个变量的方法，其中捕获的变量集一目了然，并且不会导致隐式捕获 this。（这意味着出现在非静态类成员函数中并在其正文中引用非静态类成员的 lambda 必须显式或 this 通过 [&] 捕获。不建议使用默认的按值捕获来编写长而复杂的 lambda。
-   仅使用捕获来实际捕获封闭范围中的变量。不要将捕获与初始值设定项一起使用来引入新名称，或实质性地更改现有名称的含义。相反，以传统方式声明一个新变量，然后捕获它，或者避免使用 lambda 简写并显式定义函数对象。

### 模板编程

不要使用复杂的模板编程。

### Boost 库

[Boost 库集](http://www.boost.org/) 是一个广受欢迎，经过同行鉴定，免费开源的 C++ 库集。允许使用 Boost 一部分经认可的特性子集。目前允许使用以下库：

-   [Call Traits](http://www.boost.org/doc/libs/1_58_0/libs/utility/call_traits.htm) : `boost/call_traits.hpp`
-   [Compressed Pair](http://www.boost.org/libs/utility/compressed_pair.htm) : `boost/compressed_pair.hpp`
-   [ : `boost/graph`, except serialization (`adj_list_serialize.hpp`) and parallel/distributed algorithms and data structures(`boost/graph/parallel/*` and `boost/graph/distributed/*`)
-   [Property Map](http://www.boost.org/libs/property_map/) : `boost/property_map.hpp`
-   The part of [Iterator](http://www.boost.org/libs/iterator/) that deals with defining iterators: `boost/iterator/iterator_adaptor.hpp`, `boost/iterator/iterator_facade.hpp`, and `boost/function_output_iterator.hpp`
-   The part of [Polygon](http://www.boost.org/libs/polygon/) that deals with Voronoi diagram construction and doesn’t depend on the rest of Polygon: `boost/polygon/voronoi_builder.hpp`, `boost/polygon/voronoi_diagram.hpp`, and `boost/polygon/voronoi_geometry_type.hpp`
-   [Bimap](http://www.boost.org/libs/bimap/) : `boost/bimap`
-   [Statistical Distributions and Functions](http://www.boost.org/libs/math/doc/html/dist.html) : `boost/math/distributions`
-   [Multi-index](http://www.boost.org/libs/multi_index/) : `boost/multi_index`
-   [Heap](http://www.boost.org/libs/heap/) : `boost/heap`
-   The flat containers from [Container](http://www.boost.org/libs/container/): `boost/container/flat_map`, and `boost/container/flat_set`

### C++11

C++11 有众多语言和库上的变革，以下特性最好不要用：

-   尾置返回类型，比如用 `auto foo() -> int` 代替 `int foo()`. 为了兼容于现有代码的声明风格。
-   编译时合数 `<ratio>`, 因为它涉及一个重模板的接口风格。
-   `<cfenv>` 和 `<fenv.h>` 头文件，因为编译器尚不支持。
-   默认 lambda 捕获。

## 命名约定

最重要的一致性规则是命名管理。命名的风格能让我们在不需要去查找类型声明的条件下快速地了解某个名字代表的含义：类型、变量、函数、常量、宏等等。

### 通用命名规则

函数命名、变量命名、文件命名要有描述性；少用缩写。

```cpp
int price_count_reader;    // 无缩写
int num_errors;            // "num" 是一个常见的写法
int num_dns_connections;   // 人人都知道 "DNS" 是什么
```

_注意：一些特定的广为人知的缩写是允许的，例如用 `i` 表示迭代变量和用 `T` 表示模板参数。_

### 文件命名

文件名要全部小写，可以包含下划线 (`_`) 或连字符 (`-`)，依照项目的约定。如果没有约定，那么 “`_`” 更好。

不要使用已经存在于 `/usr/include` 下的文件名。

通常应尽量让文件名更加明确. `http_server_logs.h` 就比 `logs.h` 要好。定义类时文件名一般成对出现，如 `foo_bar.h` 和 `foo_bar.cc`，对应于类 `FooBar`。

内联函数定义必须放在 `.h` 文件中。如果内联函数比较短，就直接将实现也放在 `.h` 中。

### 类型命名

类型名称——类、结构体、类型定义 (`typedef`)、枚举、类型模板参数，的每个单词首字母均大写，不包含下划线。

```cpp
// 类和结构体
class UrlTable { ...
class UrlTableTester { ...
struct UrlTableProperties { ...

// 类型定义
typedef hash_map<UrlTableProperties *, string> PropertiesMap;

// using 别名
using PropertiesMap = hash_map<UrlTableProperties *, string>;

// 枚举
enum UrlTableErrors { ...
```

### 变量命名

变量 (包括函数参数) 和数据成员名一律小写，单词之间用下划线连接。类的成员变量以下划线结尾，但结构体的就不用。

```cpp
string table_name;  // 好 - 用下划线.
string tablename;   // 好 - 全小写.
string tableName;  // 差 - 混合大小写

class TableInfo {
  ...
 private:
  string table_name_;  // 好 - 后加下划线.
  string tablename_;   // 好.
  static Pool<TableInfo>* pool_;  // 好.
};

struct UrlTableProperties {
  string name;
  int num_entries;
  static Pool<UrlTableProperties>* pool;
};
```

### 常量命名

声明为 `constexpr` 或 `const` 的变量，或在程序运行期间其值始终保持不变的，命名时以 “k” 开头，大小写混合。

### 函数命名

常规函数使用大小写混合，取值和设值函数则要求与变量名匹配。一般来说，函数名的每个单词首字母大写 (即 “驼峰变量名” 或 “帕斯卡变量名”)，没有下划线。对于首字母缩写的单词，更倾向于将它们视作一个单词进行首字母大写。

### 命名空间命名

命名空间以小写字母命名，最高级命名空间的名字取决于项目名称，要注意避免嵌套命名空间的名字之间和常见的顶级命名空间的名字之间发生冲突。

### 枚举命名

单独的枚举值应该优先采用常量的命名方式，但宏方式的命名也可以接受。

### 宏命名

通常不应该使用宏。如果不得不用，其命名像枚举命名一样全部大写，使用下划线。

## 注释

记住：注释固然很重要，但最好的代码应当本身就是文档。有意义的类型名和变量名，要远胜过要用注释解释的含糊不清的名字。

### 注释风格

`//` 或 `/* */` 都可以，但 `//` _更_ 常用。要在如何注释及注释风格上确保统一。

### 文件注释

文件注释描述了该文件的内容，如果一个文件只声明、实现、或测试了一个对象，并且这个对象已经在它的声明处进行了详细的注释，那么就没必要再加上文件注释。除此之外的其他文件都需要文件注释。

在每一个文件开头加入版权公告。为项目选择合适的许可证版本 (比如, Apache 2.0, BSD, LGPL, GPL)。

### 类注释

每个类的定义都要附带一份注释，描述类的功能和用法，除非它的功能相当明显。

```cpp
// Iterates over the contents of a GargantuanTable.
// Example:
//    GargantuanTableIterator* iter = table->NewIterator();
//    for (iter->Seek("foo"); !iter->done(); iter->Next()) {
//      process(iter->key(), iter->value());
//    }
//    delete iter;
class GargantuanTableIterator {
  ...
};
```

如果类的声明和定义分开了 (例如分别放在了 `.h` 和 `.cc` 文件中)，此时，描述类用法的注释应当和接口定义放在一起，描述类的操作和实现的注释应当和实现放在一起。

### 函数注释

函数声明处的注释描述函数功能；定义处的注释描述函数实现。

函数声明处注释的内容:

-   函数的输入输出。
-   对类成员函数而言：函数调用期间对象是否需要保持引用参数，是否会释放这些参数。
-   函数是否分配了必须由调用者释放的空间。
-   参数是否可以为空指针。
-   是否存在函数使用上的性能隐患。
-   如果函数是可重入的，其同步前提是什么？

如果函数的实现过程中用到了很巧妙的方式，那么在函数定义处应当加上解释性的注释。

### 变量注释

通常变量名本身足以很好说明变量用途。某些情况下，也需要额外的注释说明。

每个类数据成员 (也叫实例变量或成员变量) 都应该用注释说明用途。

和数据成员一样，所有全局变量也要注释说明含义及用途，以及作为全局变量的原因。

### 实现注释

巧妙或复杂的代码段前要加注释。

比较隐晦的地方要在行尾加入注释。

如果函数参数的意义不明显，考虑用下面的方式进行弥补：

-   如果参数是一个字面常量，并且这一常量在多处函数调用中被使用，用以推断它们一致，你应当用一个常量名让这一约定变得更明显，并且保证这一约定不会被打破。
-   考虑更改函数的签名，让某个 `bool` 类型的参数变为 `enum` 类型，这样可以让这个参数的值表达其意义。
-   如果某个函数有多个配置选项，你可以考虑定义一个类或结构体以保存所有的选项，并传入类或结构体的实例。这样的方法有许多优点，例如这样的选项可以在调用处用变量名引用，这样就能清晰地表明其意义。同时也减少了函数参数的数量，使得函数调用更易读也易写。
-   用具名变量代替大段而复杂的嵌套表达式。
-   万不得已时，才考虑在调用点用注释阐明参数的意义。

如：

```cpp
ProductOptions options;
options.set_precision_decimals(7);
options.set_use_cache(ProductOptions::kDontUseCache);
const DecimalNumber product =
    CalculateProduct(values, options, /*completion_callback=*/nullptr);
```

### 标点、拼写和语法

注释的通常写法是包含正确大小写和结尾句号的完整叙述性语句。

### TODO 注释

对那些临时的、短期的解决方案，或已经够好但仍不完美的代码使用 `TODO` 注释。

```cpp
// TODO(kl@gmail.com): Use a "*" here for concatenation operator.
// TODO(Zeke) change this to use relations.
// TODO(bug 12345): remove the "Last visitors" feature
```

### 弃用注释

通过弃用注释（`DEPRECATED` comments）以标记某接口点已弃用。
