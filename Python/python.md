# Python

## 目录

[Python 语法](#python-语法)  
[Jupyter Notebook](#jupyter-notebook)  
[NumPy](#numpy)  
[Matplotlib](#matplotlib)

## Python 语法

### 变量和简单数据类型

变量是可以赋值的标签。

Python 通过把花括号内的变量替换为其值。

Python 通过变量名可以直接输出对应的值：

```py
>>> message = "hello world!"
>>> message
'hello world!'
```

#### 字符串

在 Python 中，用引号括起的都是字符串，其中的引号可以是单引号，也可以是双引号。这种灵活性让你能够在字符串中包含引号和撇号：

```py
'I told my friend, "Python is my favorite language!"'
"The language 'Python' is named after Monty Python, not the snake."
"One of Python's strengths is its diverse and supportive community."
```

**修改字符串的大小写**

```py
name = "ada lovelace"
name.title()  # 将每个单词的首字母改为大写
name.upper()  # 将每个字母改为大写
name.lower()  # 将每个字母改为小写
```

**f 字符串**

f 是 format（设置格式）的简写，使用 f 字符串可格式化字符串：

```py
first_name = "ada"
last_name = "lovelace"
full_name = f"{first_name} {last_name}"  # 合并字符串
message = f"Hello, {full_name.title()}!"  # 格式化字符串
print(message)
```

**使用制表符或换行符来添加空白**

要在字符串中添加制表符，可使用字符组合\t；要在字符串中添加换行符，可使用字符组合\n ：

```py
>>> print("Languages:\n\tPython\n\tC\n\tJavaScript")
Languages:
    Python
    C
    JavaScript
```

**删除空白**

Python 能够找出字符串开头和末尾多余的空白。

```py
>>> favorite_language = ' python '
>>> favorite_language.rstrip()  # 删除字符串右边的空格
' python'
>>> favorite_language.lstrip()  # 删除字符串左边的空格
'python '
>>> favorite_language.strip()  # 删除字符串两边的空格
'python'
```

#### 数

**整数**

在 Python 中，可对整数执行加（+ ）减（- ）乘（\* ）除（/ ）运算：

```py
>>> 2 + 3
5
>>> 3 - 2
1
>>> 2 * 3
6
>>> 3 / 2
1.5
```

Python 使用两个乘号表示乘方运算：

```py
>>> 10 ** 6
1000000
```

使用圆括号来修改运算次序；空格不影响 Python 计算表达式的方式。

```py
>>> 2 + 3*4
14
>>> (2 + 3) * 4
20
```

**浮点数**

Python 将所有带小数点的数称为浮点数。从很大程度上说，使用浮点数时无须考虑其行为。你只需输入要使用的数，Python 通常会按你期望的方式处理它们。

**整数和浮点数**

将任意两个数相除时，结果总是浮点数，即便这两个数都是整数且能整除；在任何运算中，只要有操作数是浮点数，结果总是浮点数：

```py
>>> 4/2
2.0
>>> 1 + 2.0
3.0
>>> 2 * 3.0
6.0
>>> 3.0 ** 2
9.0
```

**数中的下划线**

书写很大的数时，可使用下划线将其中的数字分组，使其更清晰易读。存储这种数时，Python 会忽略其中的下划线。这种表示法适用于整数和浮点数。

```py
>>> universe_age = 14_000_000_000
```

**同时给多个变量赋值**

可用逗号将变量名分开，在一行代码中给多个变量赋值，这有助于缩短程序并提高其可读性。

```py
>>> x, y, z = 0, 0, 0
```

**常量**

常量类似于变量，但其值在程序的整个生命周期内保持不变。Python 没有内置的常量类型，但 Python 程序员会使用全大写来指出应将某个变量视为常量，其值应始终不变：

```py
MAX_CONNECTIONS = 5000
```

#### 注释

在 Python 中，注释用井号'#'标识。井号后面的内容都会被 Python 解释器忽略。

### 列表

列表由一系列按特定顺序排列的元素组成。在 Python 中，用方括号[]表示列表，并用逗号分隔其中的元素（也可以只用[]表示创建空列表）。

**访问列表元素**

列表是有序集合，因此可以用索引访问列表的任意元素。

```py
bicycles = ['trek', 'cannondale', 'redline', 'specialized']
print(bicycles[0])
print(bicycles[0].title())  # 调用字符串方法格式化输出
```

#### 修改、添加和删除元素

**修改列表元素**

可以用索引修改任意元素的值。

```py
motorcycles = ['honda', 'yamaha', 'suzuki']
motorcycles[0] = 'ducati'
```

**在列表中添加元素**

Python 提供了多种在既有列表中添加新数据的方式。

```py
motorcycles = ['honda', 'yamaha', 'suzuki']
motorcycles.append('ducati')  # 将元素附加在列表末尾
motorcycles.insert(0, 'ducati')  # 将元素插入到列表索引位置
```

**在列表中删除元素**

可以根据位置或值来删除列表中的元素。

```py
motorcycles = ['honda', 'yamaha', 'suzuki']
del motorcycles[0]  # 删除指定索引的元素
popped_motorcycle = motorcycles.pop()  # 弹出栈顶（末尾）的值并赋值给其他变量
first_owned = motorcycles.pop(0)  # 弹出任意位置的值并赋值给其他变量
# 如果你要从列表中删除一个元素，且不再以任何方式使用它，就使用del语句；如果你要在删除元素后还能继续使用它，就使用方法pop()。

motorcycles = ['honda', 'yamaha', 'suzuki', 'ducati']
motorcycles.remove('ducati')   # 根据值删除元素
# 方法remove()只删除第一个指定的值。如果要删除的值可能在列表中出现多次，就需要使用循环来确保将每个值都删除。
```

#### 组织列表

**列表排序**

列表方法 sort() 让你能够对列表进行永久性排序，方法 sorted() 则对列表临时排序。

```py
cars = ['bmw', 'audi', 'toyota', 'subaru']
cars.sort()  # 升序排序列表
print(sorted(cars))  # 临时排序列表，不改变列表元素顺序
```

**反转列表**

可使用列表方法 reverse() 反转列表元素的排列顺序。

```py
cars = ['bmw', 'audi', 'toyota', 'subaru']
cars.reverse()
```

**获取列表长度**

使用列表方法 len() 可快速获悉列表的长度。

```py
>>> cars = ['bmw', 'audi', 'toyota', 'subaru']
>>> len(cars)
4
```

试图访问超过列表长度的索引将导致索引错误。

```py
>>> motorcycles = ['honda', 'yamaha', 'suzuki']
>>> print(motorcycles[3])
Traceback (most recent call last):
  File "motorcycles.py", line 2, in <module>
    print(motorcycles[3])
IndexError: list index out of range
```

#### 遍历列表

使用 for 循环遍历列表中的元素：

```py
magicians = ['alice', 'david', 'carolina']
for magician in magicians:
    print(f"{magician.title()}, that was a great trick!")
    print(f"I can't wait to see your next trick, {magician.title()}.\n")
```

_注意：Python 用缩进表示代码块。_

#### 数值列表

列表非常适合用于存储数字集合。

**创建数字列表**

Python 函数 range() 让你能够轻松地生成一系列数，使用函数 list() 可将 range() 的结果直接转换为列表。

```py
# 打印一系列数（通常用于指定步数的循环）
for value in range(1, 5):
    print(value)

# 将range()产生的一系列数转换为列表
>>> numbers = list(range(1, 6))
>>> print(numbers)
[1, 2, 3, 4, 5]
```

使用函数 range() 时，还可指定步长。

```py
>>> even_numbers = list(range(2, 11, 2))
>>> print(even_numbers)
[2, 4, 6, 8, 10]
```

**数值统计**

min、max 和 sum 是专门用于处理数字列表的 Python 函数。

```py
>>> digits = [1, 2, 3, 4, 5, 6, 7, 8, 9, 0]
>>> min(digits)
0
>>> max(digits)
9
>>> sum(digits)
45
```

**列表解析**

列表解析将 for 循环和创建新元素的代码合并成一行，并自动附加新元素。

```py
>>> squares = [value**2 for value in range(1, 11)]
>>> print(squares)
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

#### 列表切片

可指定索引创建指定的列表切片。

```py
>>> players = ['charles', 'martina', 'michael', 'florence', 'eli']
>>> print(players[0:3])  # 指定起始和终止索引（不包含终止索引）
['charles', 'martina', 'michael']
>>> print(players[2:])  # 指定起始索引
['michael', 'florence', 'eli']
>>> print(players[:4])  # 指定终止索引
['charles', 'martina', 'michael', 'florence']
>>> print(players[-3:])  # 负数索引返回离列表末尾相应距离的元素
['michael', 'florence', 'eli']
```

可在遍历中使用切片：

```py
players = ['charles', 'martina', 'michael', 'florence', 'eli']
for player in players[:3]:
    print(player.title())
```

**复制列表**

要复制列表，可创建一个包含整个列表的切片，即整个列表的副本。

```py
# 产生一个列表的副本并赋值给新列表
my_foods = ['pizza', 'falafel', 'carrot cake']
friend_foods = my_foods[:]
# 错误，这会让两个变量名指向同一个列表
friend_foods = my_foods
```

#### 元组

不可变的列表被称为元组，使用圆括号而非中括号来标识。

```py
dimensions = (200, 50)

print(dimensions[0])  # 访问元组元素的操作与列表相同
dimensions[0] = 250  # 错误！试图修改元组的操作是被禁止的

# 遍历元组的操作也与列表相同
for dimension in dimensions:
    print(dimension)

# 虽然不能修改元组的元素，但可以给存储元组的变量赋值
dimensions = (200, 50)
dimensions = (400, 100)  # 把新的元组赋值给相同的变量
```

_注意：严格地说，元组是由逗号标识的，圆括号只是让元组看起来更整洁、更清晰。如果要定义只包含一个元素的元组，必须在这个元素后面加上逗号：`my_t = (3,)`_

### 分支

Python 用 if 来执行分支语句，每条 if 语句的核心都是一个值为 True 或 False 的表达式，这种表达式称为**条件测试**。如果条件测试的值为 True，就执行紧跟在 if 语句后面的代码；如果为 False，就忽略这些代码。

#### 条件测试

**测试运算符**

| 测试运算符     | 作用         |
| -------------- | ------------ |
| `==`           | 检查是否相等 |
| `!=`           | 检查是否不等 |
| `<, <=, >, >=` | 数值比较     |

**检查多个条件**

| 关键字 | 作用                 |
| ------ | -------------------- |
| `and`  | 两个条件都满足       |
| `or`   | 只要至少一个条件满足 |

**检查特定值是否包含在列表中**

可使用关键字 in 和 not in 检查特定值是否包含在列表中：

```py
>>> requested_toppings = ['mushrooms', 'onions', 'pineapple']
>>> 'mushrooms' in requested_toppings
True
>>> 'pepperoni' not in requested_toppings
True
```

#### if 语句

最简单的 if 语句只有一个测试和一个操作：

```py
if conditional_test:
    do something
```

如果需要在条件测试通过时执行一个操作，在没有通过时执行另一个操作，使用 if-else 语句：

```py
age = 17
if age >= 18:
    print("You are old enough to vote!")
    print("Have you registered to vote yet?")
else:
    print("Sorry, you are too young to vote.")
    print("Please register to vote as soon as you turn 18!")
```

需要检查超过两个的情形，使用 if-elif-else 语句：

```py
age = 12
if age < 4:
    price = 0
elif age < 18:
    price = 25
elif age < 65:
    price = 40
else:
    price = 20
print(f"Your admission cost is ${price}.")
```

_if-elif-else 语句中，可以没有 else。_

### 字典

Python 用花括号{}表示字典，能够将相关信息关联起来（键值对）。字典的存储依赖于哈希表，不允许重复的键。

#### 使用字典

字典是一系列键值对 。每个键都与一个值相关联，你可使用键来访问相关联的值。与键相关联的值可以是数、字符串、列表乃至字典。

```py
alien_0 = {'color': 'green', 'points': 5}
```

要获取与键相关联的值，可依次指定字典名和放在方括号内的键。要修改字典中的值，可依次指定字典名、用方括号括起的键，以及与该键相关联的新值。

```py
alien_0 = {'color': 'green'}
print(alien_0['color'])
alien_0['color'] = 'yellow'
```

字典是一种动态结构，可随时在其中添加键值对。要添加键值对，可依次指定字典名、用方括号括起的键和相关联的值。

```py
alien_0 = {'color': 'green', 'points': 5}
alien_0['x_position'] = 0
alien_0['y_position'] = 25
```

_注意：在 Python 3.7 中，字典中元素的排列顺序与添加顺序相同。_

对于字典中不再需要的信息，可使用 del 语句将相应的键值对彻底删除。使用 del 语句时，必须指定字典名和要删除的键。

```py
alien_0 = {'color': 'green', 'points': 5}
del alien_0['points']
```

可使用方法 get() 在指定的键不存在时返回一个默认值。

```py
# 如果指定的键有可能不存在，应考虑使用方法get()，而不要使用方括号表示法。
alien_0 = {'color': 'green', 'speed': 'slow'}
point_value = alien_0.get('points', 'No point value assigned.')
```

#### 遍历字典

用 for 循环遍历字典，可用方法 items()返回键值对，并声明两个变量，用于存储键值对中的键和值。

```py
for k, v in user_0.items()
# 字典方法items()返回一个键值对列表
```

在不需要使用字典中的值时，方法 keys()很有用。

```py
for name in favorite_languages.keys():
```

_注意：遍历字典时，会默认遍历所有的键。显式地使用方法 keys()可让代码更容易理解，但是也可以省略它。_

如果需要按特定顺序遍历字典中的所有键，可以使用函数 sorted() 来获得按特定顺序排列的键列表：

```py
for name in sorted(favorite_languages.keys()):
```

如果主要对字典包含的值感兴趣，可使用方法 values() 来返回一个值列表，不包含任何键。

```py
for language in favorite_languages.values():
```

为剔除重复项，可使用**集合**（set）。集合中的每个元素都必须是独一无二的：

```py
for language in set(favorite_languages.values()):
```

### 循环

Python 中的循环语句有 for 和 while。

#### while 循环

while 语句的一般形式：

```py
while 判断条件(condition)：
    执行语句(statements)
```

while 循环使用 else 语句

_用途：通常配合 break 使用。当 while 循环 break 后，就不再执行 else 代码块中的语句。_

```py
while <expr>:
    <statement(s)>
else:
    <additional_statement(s)>
```

#### for 循环

Python for 循环可以遍历任何可迭代对象，如一个列表或者一个字符串。

```py
for <variable> in <sequence>:
    <statements>
else:
    <statements>
```

遍历列表中的元素：

```py
sites = ["Baidu", "Google","Runoob","Taobao"]
for site in sites:
    print(site)
```

遍历字符串中的每一个字符：

```py
word = 'runoob'
for letter in word:
    print(letter)
```

整数范围值可以配合 range() 函数使用：

```py
#  1 到 5 的所有数字：
for number in range(1, 6):
    print(number)
```

和 while 类似，for...else 语句用于在循环结束后执行一段代码。

```py
for x in range(6):
    print(x)
else:
    print("Finally finished!")
```

用 break 退出循环，用 continue 跳到下一步循环，用 pass 占位。

### 函数

定义一个由自己想要功能的函数，以下是简单的规则：

1. 函数代码块以 def 关键词开头，后接函数标识符名称和圆括号 ()。
2. 任何传入参数和自变量必须放在圆括号中间，圆括号之间可以用于定义参数。
3. 函数内容以冒号 : 起始，并且缩进。
4. return [表达式] 结束函数，选择性地返回一个值给调用方，不带表达式的 return 相当于返回 None。

```py
def 函数名（参数列表）:
    函数体
```

#### 可更改对象和不可更改对象

在 python 中，strings, tuples, 和 numbers 是不可更改的对象，而 list,dict 等则是可以修改的对象。

- 不可变类型：变量赋值 a=5 后再赋值 a=10，这里实际是新生成一个 int 值对象 10，再让 a 指向它，而 5 被丢弃，不是改变 a 的值，相当于新生成了 a。
- 可变类型：变量赋值 la=[1,2,3,4] 后再赋值 la[2]=5 则是将 list la 的第三个元素值更改，本身 la 没有动，只是其内部的一部分值被修改了。

python 函数的参数传递：

- 不可变类型：类似 C++ 的值传递，如整数、字符串、元组。如 fun(a)，传递的只是 a 的值，没有影响 a 对象本身。如果在 fun(a) 内部修改 a 的值，则是新生成一个 a 的对象。
- 可变类型：类似 C++ 的引用传递，如 列表，字典。如 fun(la)，则是将 la 真正的传过去，修改后 fun 外部的 la 也会受影响。

_python 中一切都是对象，严格意义我们不能说值传递还是引用传递，我们应该说传不可变对象和传可变对象。_

#### 参数

以下是调用函数时可使用的正式参数类型：

- 必需参数
- 关键字参数
- 默认参数
- 不定长参数

必需参数须以正确的顺序传入函数。调用时的数量必须和声明时的一样。

关键字参数和函数调用关系紧密，函数调用使用关键字参数来确定传入的参数值。

```py
def printinfo( name, age ):
   "打印任何传入的字符串"
   print ("名字: ", name)
   print ("年龄: ", age)
   return

#调用printinfo函数
printinfo(age=50, name="runoob")  # 可以不按顺序传入参数
```

调用函数时，如果没有传递参数，则会使用默认参数。

```py
def printinfo( name, age = 35 ):
   "打印任何传入的字符串"
   print ("名字: ", name)
   print ("年龄: ", age)
   return

#调用printinfo函数
printinfo( age=50, name="runoob" )
print ("------------------------")
printinfo( name="runoob" )
```

你可能需要一个函数能处理比当初声明时更多的参数。这些参数叫做不定长参数，声明时不会命名。

```py
# 加了星号 * 的参数会以元组(tuple)的形式导入，存放所有未命名的变量参数。
def functionname([formal_args,] *var_args_tuple ):
   "函数_文档字符串"
   function_suite
   return [expression]

# 加了两个星号 ** 的参数会以字典的形式导入。
def functionname([formal_args,] **var_args_dict ):
   "函数_文档字符串"
   function_suite
   return [expression]
```

声明函数时，参数中星号 _ 可以单独出现，如果单独出现星号 _，则星号 \* 后的参数必须用关键字传入。

```py
>>> def f(a,b,*,c):
...     return a+b+c
...
>>> f(1,2,3)   # 报错
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: f() takes 2 positional arguments but 3 were given
>>> f(1,2,c=3) # 正常
6
```

Python3.8 新增了一个函数形参语法 / 用来指明函数形参必须使用指定位置参数，不能使用关键字参数的形式。

```py
def f(a, b, /, c, d, *, e, f):
    print(a, b, c, d, e, f)
# 形参 a 和 b 必须使用指定位置参数，c 或 d 可以是位置形参或关键字形参，而 e 和 f 要求为关键字形参
```

#### 匿名函数

Python 使用 lambda 来创建匿名函数。

```py
lambda [arg1 [,arg2,.....argn]]:expression
```

```py
sum = lambda arg1, arg2: arg1 + arg2

# 调用sum函数
print ("相加后的值为 : ", sum( 10, 20 ))
print ("相加后的值为 : ", sum( 20, 20 ))
```

### 模块

模块是一个包含所有你定义的函数和变量的文件，其后缀名是.py。模块可以被别的程序引入，以使用该模块中的函数等功能。这也是使用 python 标准库的方法。想使用 Python 源文件，只需在另一个源文件里执行 import 语句，语法如下：

#### import 语句

```py
# 导入模块
import support

# 现在可以调用模块里包含的函数了
support.print_func("Runoob")
```

当使用 import 语句的时候，Python 解释器从搜索路径的目录中去寻找所引入的模块。搜索路径被存储在 sys 模块中的 path 变量中。

```py
>>> import sys
>>> sys.path
['', '/usr/lib/python3.4', '/usr/lib/python3.4/plat-x86_64-linux-gnu', '/usr/lib/python3.4/lib-dynload', '/usr/local/lib/python3.4/dist-packages', '/usr/lib/python3/dist-packages']
# ''代表当前路径
```

#### from … import 语句

Python 的 from 语句让你从模块中导入一个指定的部分到当前命名空间中，语法如下：

```py
from modname import name1[, name2[, ... nameN]]
```

这个声明不会把整个模块导入到当前的命名空间中，它只会将模块里的某个函数/变量引入进来。

```py
>>> from fibo import fib, fib2
>>> fib(500)
1 1 2 3 5 8 13 21 34 55 89 144 233 377
```

把一个模块的所有内容全都导入到当前的命名空间也是可行的，只需使用如下声明：

```py
# 这个声明不该被过多地使用
from modname import *
```

导入不同目录下的模块：

```py
# 引入testFile/test_c.py文件
from testFile.test_c import c

c()
```

#### 模块与主程序

一个模块被另一个程序第一次引入时，其主程序将运行。如果我们想在模块被引入时，模块中的某一程序块不执行，我们可以用**name**属性来使该程序块仅在该模块自身运行时执行。

```py
if __name__ == '__main__':
   print('程序自身在运行')
else:
   print('我来自另一模块')
```

_说明： 每个模块都有一个`__name__`属性，当其值是`__main__`时，表明该模块自身在运行，否则是被引入。_

#### 包

包是一种管理 Python 模块命名空间的形式，采用“点模块名称”。在导入一个包的时候，Python 会根据 sys.path 中的目录来寻找这个包中包含的子目录。目录只有包含一个叫做`__init__.py`的文件才会被认作是一个包。

```py
sound/                          顶层包
      __init__.py               初始化 sound 包
      formats/                  文件格式转换子包
              __init__.py
              wavread.py
              wavwrite.py
              aiffread.py
              aiffwrite.py
              auread.py
              auwrite.py
              ...
      effects/                  声音效果子包
              __init__.py
              echo.py
              surround.py
              reverse.py
              ...
      filters/                  filters 子包
              __init__.py
              equalizer.py
              vocoder.py
              karaoke.py
              ...
```

一种导入子模块的方法是:

```py
from sound.effects import echo

echo.echofilter(input, output, delay=0.7, atten=4)
```

还可以直接导入一个函数或者变量：

```py
from sound.effects.echo import echofilter

echofilter(input, output, delay=0.7, atten=4)  # 变量被引入当前模块
```

_注意：当使用 from package import item 这种形式的时候，对应的 item 既可以是包里面的子模块（子包），或者包里面定义的其他名称，比如函数，类或者变量。_

### 面向对象

#### 类和对象

类(Class)是用来描述具有相同的属性和方法的对象的集合。它定义了该集合中每个对象所共有的属性和方法。对象是类的实例。

类定义的语法如下：

```py
class ClassName:
    <statement-1>
    .
    .
    .
    <statement-N>
```

实例化后，类命名空间中所有的命名都是有效属性名。

```py
class MyClass:
    """一个简单的类实例"""
    i = 12345
    def f(self):
        return 'hello world'

# 实例化类
x = MyClass()

# 访问类的属性和方法
print("MyClass 类的属性 i 为：", x.i)
print("MyClass 类的方法 f 输出为：", x.f())
```

类有一个名为 **init**() 的特殊方法（构造方法），该方法在类实例化时会自动调用。

```py
class Complex:
    def __init__(self, realpart, imagpart):
        self.r = realpart
        self.i = imagpart
x = Complex(3.0, -4.5)
print(x.r, x.i)   # 输出结果：3.0 -4.5
```

类的方法与普通的函数只有一个特别的区别——它们必须有一个额外的第一个参数名称, 按照惯例它的名称是 self。在 Python 中，self 是一个惯用的名称，用于表示类的实例（对象）自身。它是一个指向实例的引用，使得类的方法能够访问和操作实例的属性。

```py
class MyClass:
    def __init__(self, value):
        self.value = value

    def display_value(self):
        print(self.value)

# 创建一个类的实例
obj = MyClass(42)

# 调用实例的方法
obj.display_value() # 输出 42
```

#### 继承

子类（派生类 DerivedClassName）会继承父类（基类 BaseClassName）的属性和方法。派生类的定义如下所示：

```py
class DerivedClassName(BaseClassName):
    <statement-1>
    .
    .
    .
    <statement-N>
```

#### 类属性与方法

Python 用两个下划线开头，声明该属性/方法为私有不能在类的外部被使用或直接访问。

```py
class JustCounter:
    __secretCount = 0  # 私有变量
    publicCount = 0    # 公开变量

    def count(self):
        self.__secretCount += 1
        self.publicCount += 1
        print (self.__secretCount)

counter = JustCounter()
counter.count()
counter.count()
print (counter.publicCount)
print (counter.__secretCount)  # 报错，实例不能访问私有变量
```

**运算符重载**

可以对类的专有方法进行重载，类的专有方法：

- `__init__` : 构造函数，在生成对象时调用
- `__del__` : 析构函数，释放对象时使用
- `__repr__` : 打印，转换
- `__setitem__` : 按照索引赋值
- `__getitem__`: 按照索引获取值
- `__len__`: 获得长度
- `__cmp__`: 比较运算
- `__call__`: 函数调用
- `__add__`: 加运算
- `__sub__`: 减运算
- `__mul__`: 乘运算
- `__truediv__`: 除运算
- `__mod__`: 求余运算
- `__pow__`: 乘方

## Jupyter Notebook

Jupyter Notebook 是集代码和可视化内容为一体的工具。

- 支持多语言：支持 Python, MATLAB, R 语言等，可以导出为 Markdown, pdf 等文档。
- 远程运行：可以通过网络链接远程服务器来实现运算。
- 交互式展现：可以输出图片、视频、数学公式，甚至可以呈现一些互动的可视化内容。

## NumPy

NumPy(Numerical Python) 是 Python 语言的一个扩展程序库，支持大量的维度数组与矩阵运算，此外也针对数组运算提供大量的数学函数库。

### 创建 Ndarray 对象

NumPy 最重要的一个特点是其 N 维数组对象 ndarray，它是一系列同类型数据的集合，以 0 下标为开始进行集合中元素的索引。

ndarray 内部由以下内容组成：

- 一个指向数据（内存或内存映射文件中的一块数据）的指针。
- 数据类型或 dtype，描述在数组中的固定大小值的格子。
- 一个表示数组形状（shape）的元组，表示各维度大小的元组。
- 一个跨度元组（stride），其中的整数指的是为了前进到当前维度下一个元素需要"跨过"的字节数。

创建一个 ndarray 只需调用 NumPy 的 array 函数即可：

```py
numpy.array(object, dtype = None, copy = True, order = None, subok = False, ndmin = 0)
# 参数说明
# object: 数组或嵌套的数列
# dtype: 数组元素的数据类型，可选
# copy: 对象是否需要复制，可选
# order: 创建数组的样式，C为行方向，F为列方向，A为任意方向（默认）
# subok: 默认返回一个与基类类型一致的数组
# ndmin: 指定生成数组的最小维度
```

numpy 的数值类型实际上是 dtype 对象的实例，并对应唯一的字符，包括 np.bool\_，np.int32，np.float32，等等。下表列举了常用 NumPy 基本类型。

| 名称       | 描述                                                                       |
| ---------- | -------------------------------------------------------------------------- |
| bool\_     | 布尔型数据类型（True 或者 False）                                          |
| int\_      | 默认的整数类型（类似于 C 语言中的 long，int32 或 int64）                   |
| intc       | 与 C 的 int 类型一样，一般是 int32 或 int 64                               |
| intp       | 用于索引的整数类型（类似于 C 的 ssize_t，一般情况下仍然是 int32 或 int64） |
| int8       | 字节 (-128 to 127)                                                         |
| int16      | 整数 (-32768 to 32767)                                                     |
| int32      | 整数 (-2147483648 to 2147483647)                                           |
| int64      | 整数 (-9223372036854775808 to 9223372036854775807)                         |
| uint8      | 无符号整数 (0 to 255)                                                      |
| uint16     | 无符号整数 (0 to 65535)                                                    |
| uint32     | 无符号整数 (0 to 4294967295)                                               |
| uint64     | 无符号整数 (0 to 18446744073709551615)                                     |
| float\_    | float64 类型的简写                                                         |
| float16    | 半精度浮点数，包含：1 个符号位，5 个指数位，10 个尾数位                    |
| float32    | 单精度浮点数，包含：1 个符号位，8 个指数位，23 个尾数位                    |
| float64    | 双精度浮点数，包含：1 个符号位，11 个指数位，52 个尾数位                   |
| complex\_  | complex128 类型的简写，即 128 位复数                                       |
| complex64  | 复数，表示双 32 位浮点数（实数部分和虚数部分）                             |
| complex128 | 复数，表示双 64 位浮点数（实数部分和虚数部分）                             |

NumPy 的数组中比较重要 ndarray 对象属性有：

| 属性             | 说明                                         |
| ---------------- | -------------------------------------------- |
| ndarray.ndim     | 秩，即轴的数量或维度的数量                   |
| ndarray.shape    | 数组的维度，对于矩阵，n 行 m 列              |
| ndarray.size     | 数组元素的总个数，相当于 .shape 中 n\*m 的值 |
| ndarray.dtype    | ndarray 对象的元素类型                       |
| ndarray.itemsize | ndarray 对象中每个元素的大小，以字节为单位   |
| ndarray.flags    | ndarray 对象的内存信息                       |
| ndarray.real     | ndarray 元素的实部                           |
| ndarray.imag     | ndarray 元素的虚部                           |
| ndarray.data     | 包含实际数组元素的缓冲区                     |

ndarray 数组除了可以使用底层 ndarray 构造器来创建外，也可以通过以下方式来创建。

| 函数       | 原型                                                                       | 描述                                                         |
| ---------- | -------------------------------------------------------------------------- | ------------------------------------------------------------ |
| empty      | numpy.empty(shape, dtype = float, order = 'C')                             | 创建一个指定形状（shape）、数据类型（dtype）且未初始化的数组 |
| ones       | numpy.ones(shape, dtype = None, order = 'C')                               | 创建指定形状的数组，数组元素以 1 来填充                      |
| zeros      | numpy.zeros(shape, dtype = float, order = 'C')                             | 创建指定大小的数组，数组元素以 0 来填充                      |
| zeros_like | numpy.zeros_like(a, dtype=None, order='K', subok=True, shape=None)         | 创建一个与给定数组具有相同形状的数组，数组元素以 0 来填充    |
| ones_like  | numpy.ones_like(a, dtype=None, order='K', subok=True, shape=None)          | 创建一个与给定数组具有相同形状的数组，数组元素以 1 来填充    |
| asarray    | numpy.asarray(a, dtype = None, order = None)                               | 从已有的列表或元组创建数组                                   |
| frombuffer | numpy.frombuffer(buffer, dtype = float, count = -1, offset = 0)            | 以流的形式读入转化成 ndarray 对象                            |
| fromiter   | numpy.fromiter(iterable, dtype, count=-1)                                  | 从可迭代对象中建立 ndarray 对象，返回一维数组                |
| arange     | numpy.arange(start, stop, step, dtype)                                     | 创建数值范围并返回 ndarray 对象                              |
| linspace   | np.linspace(start, stop, num=50, endpoint=True, retstep=False, dtype=None) | 创建一个等差数列构成的一维数组                               |
| logspace   | np.logspace(start, stop, num=50, endpoint=True, base=10.0, dtype=None)     | 创建一个等比数列                                             |

### 切片和索引

ndarray 对象的内容可以通过索引或切片来访问和修改。

可以通过冒号分隔切片参数 start:stop:step 来进行切片操作：

```py
import numpy as np

a = np.arange(10)
b = a[2:7:2]   # 从索引 2 开始到索引 7 停止，步长为 2
print(b)
```

除了整数和切片的索引外，数组还可以由整数数组索引、布尔索引及花式索引。

整数数组索引是指用一个数组来访问另一个数组的元素：

```py
# 整数数组索引
x = np.array([[1,  2],  [3,  4],  [5,  6]])
y = x[[0,1,2],  [0,1,0]]  # 获取数组中 (0,0)，(1,1) 和 (2,0) 位置处的元素

a = np.array([[1,2,3], [4,5,6],[7,8,9]])
b = a[1:3, 1:3]
c = a[1:3,[1,2]]
d = a[...,1:]
```

布尔索引通过布尔运算（如：比较运算符）来获取符合指定条件的元素的数组：

```py
x = np.array([[  0,  1,  2],[  3,  4,  5],[  6,  7,  8],[  9,  10,  11]])
print (x[x > 5])
```

花式索引根据索引数组的值作为目标数组的某个轴的下标来取值：

```py
x = np.arange(9)
x2 = x[[0, 6]] # 使用花式索引取下标为0和6的元素构成新数组
```

### 广播

如果两个数组 a 和 b 形状相同，即满足 a.shape == b.shape，那么 a\*b 的结果就是 a 与 b 数组对应位相乘。

```py
>>> a = np.array([1,2,3,4])
>>> b = np.array([10,20,30,40])
>>> c = a * b
>>> print (c)
[ 10  40  90 160]
```

当运算中的 2 个数组的形状不同时，numpy 将自动触发广播机制。广播的规则：

- 让所有输入数组都向其中形状最长的数组看齐，形状中不足的部分都通过在前面加 1 补齐。
- 输出数组的形状是输入数组形状的各个维度上的最大值。
- 如果输入数组的某个维度和输出数组的对应维度的长度相同或者其长度为 1 时，这个数组能够用来计算，否则出错。
- 当输入数组的某个维度的长度为 1 时，沿着此维度运算时都用此维度上的第一组值。

### 数组操作

修改数组形状

| 函数    | 原型                                    | 描述                       |
| ------- | --------------------------------------- | -------------------------- |
| reshape | numpy.reshape(arr, newshape, order='C') | 不改变数据的条件下修改形状 |
| flat    | np.flat                                 | 数组元素迭代器             |
| flatten | ndarray.flatten(order='C')              | 返回一份数组拷贝           |
| ravel   | numpy.ravel(a, order='C')               | 返回展开数组               |

翻转数组

| 函数      | 原型                              | 描述             |
| --------- | --------------------------------- | ---------------- |
| transpose | numpy.transpose(arr, axes)        | 对换数组的维度   |
| ndarray.T | ndarray.T(axes)                   | 同上             |
| rollaxis  | numpy.rollaxis(arr, axis, start)  | 向后滚动指定的轴 |
| swapaxes  | numpy.swapaxes(arr, axis1, axis2) | 对换数组的两个轴 |

修改数组维度

| 函数         | 原型                                    | 描述                           |
| ------------ | --------------------------------------- | ------------------------------ |
| broadcast    | numpy.broadcast(x, y)                   | 对 y 广播 x                    |
| broadcast_to | numpy.broadcast_to(array, shape, subok) | 将张量广播到新形状             |
| expand_dims  | numpy.expand_dims(arr, axis)            | 增加张量的维度                 |
| squeeze      | numpy.squeeze(arr, axis)                | 减少张量中所有单维度，缩减尺寸 |

连接数组

| 函数        | 原型                                   | 描述                           |
| ----------- | -------------------------------------- | ------------------------------ |
| concatenate | numpy.concatenate((a1, a2, ...), axis) | 沿着现有轴连接数组的序列       |
| stack       | numpy.stack(arrays, axis)              | 沿着新轴连接数组的序列         |
| hstack      | numpy.hstack(arrays, axis)             | 水平堆叠序列中的数组（列方向） |
| vstack      | numpy.vstack(arrays, axis)             | 垂直堆叠序列中的数组（行方向） |

分割数组

| 函数   | 原型                                         | 描述                                 |
| ------ | -------------------------------------------- | ------------------------------------ |
| split  | numpy.split(ary, indices_or_sections, axis)  | 将一个数组分割为多个子数组           |
| hsplit | numpy.hsplit(ary, indices_or_sections, axis) | 将一个数组水平分割为多个子数组(按列) |
| vsplit | numpy.vsplit(ary, indices_or_sections, axis) | 将一个数组垂直分割为多个子数组(按行) |

数组元素的添加与删除

| 函数   | 原型                                                           | 描述                                     |
| ------ | -------------------------------------------------------------- | ---------------------------------------- |
| resize | numpy.resize(arr, shape)                                       | 返回指定形状的新数组                     |
| append | numpy.append(arr, values, axis=None)                           | 将值添加到数组末尾                       |
| insert | numpy.insert(arr, obj, values, axis)                           | 沿指定轴将值插入到指定下标之前           |
| delete | Numpy.delete(arr, obj, axis)                                   | 删掉某个轴的子数组，并返回删除后的新数组 |
| unique | numpy.unique(arr, return_index, return_inverse, return_counts) | 查找数组内的唯一元素                     |

### 数学函数

三角函数

| 函数   | 原型            | 描述                     |
| ------ | --------------- | ------------------------ |
| sin    | numpy.sin(a)    | 返回给定弧度的 sin 值    |
| cos    | numpy.cos(a)    | 返回给定弧度的 cos 值    |
| tan    | numpy.tan(a)    | 返回给定弧度的 tan 值    |
| arcsin | numpy.arcsin(a) | 返回给定值的的反三角函数 |
| arccos | numpy.arccos(a) | 返回给定值的的反三角函数 |
| arctan | numpy.arctan(a) | 返回给定值的的反三角函数 |

_注意：三角函数和反三角函数使用的都是弧度制，要转换为角度需要使用`numpy.pi/180`。_

取整函数

| 函数   | 原型                        | 描述                                                                                           |
| ------ | --------------------------- | ---------------------------------------------------------------------------------------------- |
| around | numpy.around(a, decimals=0) | 返回指定数字的四舍五入值。decimals: 保留几位小数。如果为负，整数将四舍五入到小数点左侧的位置。 |
| floor  | numpy.floor(a)              | 向下取整                                                                                       |
| ceil   | numpy.ceil(a)               | 向上取整                                                                                       |

算数函数

| 函数       | 原型                 | 描述                   |
| ---------- | -------------------- | ---------------------- |
| add        | numpy.add(a, b)      | 两个数组相加           |
| subtract   | numpy.subtract(a, b) | 两个数组相减           |
| multiply   | numpy.multiply(a, b) | 两个数组相乘           |
| divide     | numpy.divide(a, b)   | 两个数组相除           |
| reciprocal | numpy.reciprocal(a)  | 逐个元素取倒数         |
| power      | numpy.power(a, b)    | 计算 a^b               |
| mod        | numpy.mod(a,b)       | 计算 a 对 b 取模的余数 |

统计函数

| 函数       | 原型                                                                                          | 描述                                                         |
| ---------- | --------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| amin       | numpy.amin(a, axis=None, out=None, keepdims=<no value>, initial=<no value>, where=<no value>) | 计算数组中的元素沿指定轴的最小值                             |
| amax       | numpy.amax(a, axis=None, out=None, keepdims=<no value>, initial=<no value>, where=<no value>) | 计算数组中的元素沿指定轴的最大值                             |
| ptp        | numpy.ptp(a, axis=None, out=None, keepdims=<no value>, initial=<no value>, where=<no value>)  | 计算数组中元素最大值与最小值的差                             |
| percentile | numpy.percentile(a, q, axis)                                                                  | 百分位数是统计中使用的度量，表示小于这个值的观察值的百分比   |
| median     | numpy.median(a, axis=None, out=None, overwrite_input=False, keepdims=<no value>)              | 计算数组 a 中元素的中位数（中值）                            |
| mean       | numpy.mean(a, axis=None, dtype=None, out=None, keepdims=<no value>)                           | 计算数组中元素的算术平均值                                   |
| average    | numpy.average(a, axis=None, weights=None, returned=False)                                     | 根据在另一个数组中给出的各自的权重计算数组中元素的加权平均值 |
| std        | numpy.std(a)                                                                                  | 计算数组的标准差                                             |
| var        | numpy.var(a)                                                                                  | 计算数组的方差                                               |

## Matplotlib

Matplotlib 是 Python 的绘图库，它能让使用者很轻松地将数据图形化，并且提供多样化的输出格式。

### Pyplot

Pyplot 是 Matplotlib 的子库，提供了和 MATLAB 类似的绘图 API。

使用的时候，我们可以使用 import 导入 pyplot 库，并设置一个别名 plt：

```py
from matpoltlib import pyplot as plt
# 或 import matplotlib.pyplot as plt
```

plot() 是绘制二维图形的最基本函数。它可以绘制点和线，语法格式如下：

```py
# 画单条线
plot([x], y, [fmt], *, data=None, **kwargs)
# 画多条线
plot([x], y, [fmt], [x2], y2, [fmt2], ..., **kwargs)
# 参数说明
# x, y：点或线的节点，x 为 x 轴数据，y 为 y 轴数据，数据可以列表或数组。
# fmt：可选，定义基本格式（如颜色、标记和线条样式）。
# **kwargs：可选，用在二维平面图上，设置指定属性，如标签，线的宽度等。
```

**颜色字符**：'b' 蓝色，'m' 洋红色，'g' 绿色，'y' 黄色，'r' 红色，'k' 黑色，'w' 白色，'c' 青绿色，'#008000' RGB 颜色符串。多条曲线不指定颜色时，会自动选择不同颜色。

**线型参数**：'‐' 实线，'‐‐' 破折线，'‐.' 点划线，':' 虚线。

**标记字符**：'.' 点标记，',' 像素标记(极小点)，'o' 实心圈标记，'v' 倒三角标记，'^' 上三角标记，'>' 右三角标记，'<' 左三角标记...等等。

使用Pycharm或Jupyter Notebook时图像会交互式显示，其他时候需要调用 plt.show() 显示图像：

```py
import matplotlib.pyplot as plt
import numpy as np

xpoints = np.array([1, 2, 6, 8])
ypoints = np.array([3, 8, 1, 10])

plt.plot(xpoints, ypoints)
plt.show()
```