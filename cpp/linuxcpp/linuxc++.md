# Linux C++开发

## 目录

[shell 命令](#shell-命令)  
[git 常用命令](#git)  
[GCC](#gcc)  
[GDB](#gdb)  
[CMake](#cmake)  
[gtest](#gtest)  
[glog](#glog)  
[正则表达式](#正则表达式)

## shell 命令

### shell 基础命令

#### powershell 常用命令

-   ping
    `$ ping "host"` 验证与主机间的连接
-   ssh
    `$ ssh "host"` 登录远程主机  
    `$ ssh -l "user" "host"` 指定用户登录主机  
    `$ ssh -T "host"` 测试连接
-   scp
    `$ scp "source" "destination"` 复制文件到远程主机
-   ipconfig
    `$ ipconfig` 显示 ip 配置

#### coreutils 命令

-   arch
    `$ arch` 显示主机架构
-   b2sum
    `$ b2sum "filename"` 计算并检查 BLAKE2 校验和
-   base32
    `$ base32 [-d] "filename"` 编码或解码到 Base32 并输出
-   base64
    `$ base64 [-d] "filename"` 编码或解码到 base64 并输出
-   basename
    `$ basename "filepath/filename"` 获取末尾目录名或文件名
-   basenc
    `$ basenc [-option] "filename"`以指定选项编码或解码文件并输出
-   cat
    `$ cat "filename"` 连接并显示文本文件  
    `$ cat  "filename"` 输入保存到文本文件
-   chcon
    `$ chcon -R -t "user" "filename"` 将文件向用户共享
-   chgrp
    `$ chgrp "group" "filename"` 修改文件或目录的所属群组
-   chmod
    `$ chmod u+x "filename"` 文件所有者增加执行权限  
    `$ chmod 755 "filename"` 改变文件权限
    三个数字代表所有者、同组用户、其他用户的权限数值相加  
     执行 1；写 2；读 4
-   chown
    `$ chown "owner:group" "filename"` 改变文件或目录的所有者和群组
-   chroot
    `$ chroot "rootpath" {command}` 改变根目录并在新根目录下执行命令
-   cksum
    `$ cksum "filename"` 检查文件的 CRC 校验和
-   comm
    `$ comm "file1" "file2"` 比较两个文件的内容
-   cp
    `$ cp [-option] "source" "destination"` 复制文件或目录到目标地址
    `-a`=`-dpr`  
     `-s` 符号连接  
     `-l` 硬连接  
     `-p` 保留源文件或目录和属性
    `-r` 递归目录下子目录和文件
-   csplit
    `$ csplit "FILE" "PATTEN"` 将文件分割并输出到 xx00、xx01...中
    `$ csplit a 3 5` 将文件 a 分割为三个部分，第二部分从第 3 行开始，第三部分从第 5 行开始
-   cut
    `$cut [-option] "FILE"` 从一个文本文件或者文本流中提取文本列
    `-b` 以字节为单位分割  
     `-c` 以字符为单位分割  
     `-d` 设置分割字符  
     `-f` 分割后取出第几段
-   date
    `$ date` 显示日期时间
-   dd
    `$ dd if="input" of="output" bs="块大小" conv="关键字"` 读取并转换文件
-   df
    `$ df` 显示文件系统的磁盘使用情况
-   dir
    `$ dir` 显示目录下文件和目录
    ls 展示目录时，目录和可执行文件会用颜色区分，dir 则不会
-   dircolors
    `$ dircolors [色彩配置文件]` 设置 ls 在显示目录或文件时所用的色彩
-   dirname
    `$ dirname "dirpath"` 除去最后一级路径
-   du
    `$ du "dirname/filename"` 显示文件或目录占用的空间
-   echo
    `$ echo -e "text\n"  "filename"` 输出文字到控制台/文件...
-   env
    `$ env` 显示环境变量  
    `$ env -u var` 删除指定的环境变量  
    `$ env var=""` 设置环境变量
-   expand
    `$ expand "FILE"` 将文件的制表符转换为空格，并输出到标准输出
-   expr
    `$ expr 10 + 10` 整数运算  
    `$ expr s1 = s2` 判断数值或字符串是否相等，若相等返回 1
-   factor
    `$ factor n` 分解质因数
-   false
    `$ false` 无事发生
-   fmt
    `$ fmt -w 50 "FILE"` 按指定格式重排文本
-   fold
    `$ flod -b 100 "FILE"` 按列宽重排文本
-   groups
    `$ groups root` 显示用户所在的群组
-   head
    `$ head -n 'x' "FILE"` 显示文本的前 x 行
-   hostid
    `$ hostid` 显示当前主机的 16 进制标识
-   hostname
    `$ hostid` 显示当前主机的主机名
-   id
    `$ id` 显示用户和群组的 id
-   install
    `$ install -g "group" -o "user" "source" "dest"` 复制文件并指定它的群组和用户
-   join
    `$ join FILE1 FILE2` 将两个文件中指定字段的内容相同的行连接起来
-   kill
    `$ kill 12345` 结束指定编号的进程
-   link
    `$ link "FILE" "filename"` 为文件创建一个硬链接
-   ln
    `$ ln "FILE" "filename"` 为文件建立一个硬链接  
    `$ ln -s "FILE" "filename"` 为文件建立一个符号链接
-   logname
    `$ logname"` 显示用户的登录名
-   ls
    `$ ls` 查看当前目录下文件  
    `$ ls -l"` 查看完整的文件信息  
    `$ ln -a"` 查看隐藏文件
-   md5sum
    `$ md5sum FILE` 查看文件的 MD5 加密和
-   mkdir
    `$ mkdir "dirname"` 建立目录  
    `$ mkdir -p "dir1/dir2/dir3"` 建立多级目录
-   mkfifo
    `$ mkfifo "pipe"` 创建命名管道
-   mknod
    `$ mknod "name"` 创建块设备或字符设备文件
-   mktemp
    `$ mktemp -d "dir" tmp.XXX` 在指定目录下创建临时文件
-   mv
    `$ mv "source" "dest"` 重命名或移动文件  
    `$ mv -b "source" "dest"` 若覆盖文件，则进行备份
-   nice
    `$ nice` 查看进程优先级  
    `$ nice -n -20 command &` 设置后台程序运行优先级
    优先级为-20~19，-20 最高，19 最低
-   nl
    `$ nl FILE` 列出文件行号  
    `$ nl -b a FILE` 列出文件行号，包括空行
-   nohup
    `$ nohup command &` 不挂起运行程序，输出将写入 nohup.out
-   nproc
    `$ nproc` 显示可用的处理器数目
-   numfmt
    `$ numfmt --to=iec 2048` 格式化数字并输出
-   od
    `$ od -tx FILE` 以十六进制输出文件
-   paste
    `$ paste FILE1 FILE2` 将多个文件合并并输出  
    `$ paste -s FILE` 将一个文件的多行合并为一行
-   pathchk
    `$ pathchk "path"` 检查路径是否可移植
-   pinky
    `$ pinky` 输出登录用户
-   pr
    `$ pr FILE` 将文件转换为打印格式
    `$ -h "title"` 设置标题  
     `$ -l n` 设置每页的行数
-   printenv
    `$ printenv` 显示所有环境变量
-   printf
    `$ printf %s\n a b c...` 格式化输出文本
-   ptx
    `$ ptx FILE` 生成序列改变的索引
-   pwd
    `$ pwd` 显示当前目录路径
-   readlink
    `$ readlink -f FILE` 显示符号链接指向的位置
-   realpath
    `$ realpath FILE` 显示文件的绝对路径
-   rm
    `$ rm FILE` 删除文件  
    `$ rm -r "dir"` 删除目录及子文件
-   rmdir
    `$ rmdir -p dir1/dir2` 删除多级空目录
-   runcon
    `$ runcon CONTEXT COMMAND` 以给定的上下文执行命令
-   seq
    `$ seq n` 顺序输出一列数字
-   sha1sum
    `$sha1sum FILE` 生成 sha1sum 校验和  
    `$sha1sum -c FILE.sha1` 检验校验和
-   sha224sum
    同上
-   sha256sum
    同上
-   sha384sum
    同上
-   sha512sum
    同上
-   shred
    `$ shred FILE` 覆写文件（彻底删除）
-   shuf
    `$ shuf -i 0-10 -n 4 -o FILE` 生成 4 个 0~10 的伪随机数输出到文件
-   sleep
    `$ sleep 120` 睡眠 120 秒
-   sort
    `$ sort [-option] FILE` 对文件按行升序排序
    `-u` 去除重复行  
     `-r` 降序排序  
     `-o FILE` 将排序结果保存到文件  
     `-n` 按数值排序  
     `-t ':'` 设置分隔符:  
     `-k 'x'` 按第 x 列排序
-   split
    `$ split [-option] FILE` 分割文件为多个小文件
    `-b` 设置子文件大小（字节）  
     `-C` 子文件单行最大字节数  
     `-l` 指定多少行分割成一个小文件
-   stat
    `$ stat FILE` 显示文件状态
-   stdbuf
    `$ stdbuf [-option] COMMAND` 调整标准缓冲区并执行命令
    `-i` 调整输入流缓冲区  
     `-o` 调整输出流缓冲区  
     `-e` 调整错误流缓冲区
-   stty
    `$ stty -a` 打印所有终端设置
-   sum
    `$ sum FILE` 计算文件的校验和和占用磁盘块数
-   sync
    `$ sync FILE` 将缓存数据写入文件
-   tac
    `$ tac FILE` 按行反向输出文件
-   tail
    `$ tail -n 'x' FILE` 显示文本的后 x 行
-   tee
    `$ echo "hello" | tee FILE` 读取标准输入写入标准输出
-   test
    `$ test 表达式` 检查条件是否成立
-   timeout
    `$ timeout 'time' COMMAND` 在指定时间后终止命令
-   touch
    `$ touch -a FILE` 改变文件的存取时间
-   tr
    `$ tr -c -d -s "string1" "string2" FILE` 替换文件中的字符串 string1 为 string2
    `-c` 使用 string1 的补集替换  
     `-d` 删除 string1 输入字符  
     `-s` 删除重复字符串
-   true
    `$ true` 无事发生
-   truncate
    `$ truncate -s 'size' FILE` 设置文件为指定的大小（如果设置字节数小于文件大小，数据会丢失）
-   tsort
    `$ tsort FILE` 对文件中的数据进行拓扑排序
-   tty
    `$ tty` 显示标准输入设备的文件路径和名称
-   uname
    `$ uname -a` 显示系统信息
-   unexpand
    `$ unexpand FILE` 将空格转换为制表符
-   uniq
    `$ uniq -c FILE` 删除文件中重复的行，并在行首添加重复次数
    重复的行不相邻时，uniq 不起作用，通常先使用 sort
-   unlink
    `$ unlink FILE` 删除文件
-   uptime
    `$ uptime` 显示系统运行的时间
-   users
    `$ users` 显示最近登录主机的用户名
-   vdir
    `$ vidr` 类似 `$ls -l`
-   wc
    `$ wc [-option] FILE` 计算文件的长度  
     `-c` 字节数  
     `-l` 行数  
     `-w` 单词数  
     `-m` 字符数
-   who
    `$ who` 显示登录的用户和登录时间
-   whoami
    `$ whoami` 显示当前用户名
-   yes
    `$ yes` 输出一串 y 直到中断

#### 常用命令

-   grep
    `$ grep [-option] pattern [file]` 查找文件里符合条件的字符串或正则表达式
    `-i` 忽略大小写  
    `-v` 反向查找不匹配的行  
    `-n` 显示匹配的行号  
    `-c` 只打印匹配的行数
-   gawk
    `$ gawk '{print $n}' FILE` 输出文本每行的第 n 个字段
    `-F` 指定分隔符
-   find
    `$ find [path] [expression]` 在指定目录下查找文件和目录  
     `-name pattern` 按文件名查找，支持通配符\*?  
    `-type f/d/l` 按类型查找文件/目录/符号链接  
    `-size [+-]size` 按大小查找  
    `-mtime [+-]time` 按修改时间查找
-   sed
    `$ sed <script FILE` 利用脚本处理文本文件  
    `$ sed -e 4a\newLine FILE` 在第 4 行下新增  
    `$ sed -e 2,5d FILE` 删除 2~5 行
    `-e <script` 用选项中脚本  
    `-f <scriptfile` 用脚本文件  
    `a/c/d/i/p/s` 新增/取代/删除/插入/打印/取代
-   Rust 替代命令
-   rg-grep
-   fd-find

### bash 基本语法

-   变量
    `$ var="string"` 定义变量  
    `$ echo "$var"` 查看变量  
    `$ unset "var"` 删除变量
-   字符串
    `$ str="str1 ${str2}"` 字符串拼接  
    `$ echo ${#str}` 获取字符串长度  
    `$ echo ${str:1:4}` 从字符串第 2 个字符开始截取 4 个字符
-   数组
    `$ array=(value0 value1 ...)`/`$ array[n]=valuen` 定义数组  
    `$ echo ${#array[*]}` 查看数组元素个数
-   流程控制
-   分支
-   if 分支  
     <code if [ condition ]  
    then  
    ...  
    elif [ condition ]  
    then  
    ...  
    else  
    ...  
    fi </code
-   case 多选择  
     <code case $n in  
    1\) ...  
    ;;  
    2\) ...  
    ;;  
    \*\) ...  
    ;;  
    esac </code
-   循环
-   for 循环  
     <codefor i in item1 item2 ...  
    do  
    ...  
    done</code
-   while 循环  
     <codewhile [ condition ]  
    do  
    ...  
    done</code
-   until 循环  
     <codeuntil [ condition ]  
    do  
    ...  
    done</code
-   函数
    <codefunc(){  
    ...  
    }</code
-   重定向
    `$ command / FILE` 将输出重定向（追加）到 FILE  
    `$ command < FILE` 将输入重定向到 FILE

## git

### git 常用命令

![git](img/git.jpg)

### github

设置姓名邮箱

```shell
# 设置全局
git config --global user.name Niwenjin
git config --global user.email ni_wenjin@qq.com
# 仅设置当前仓库
git config user.name Niwenjin
git config user.email ni_wenjin@qq.com
```

## GCC

### GCC 命令类型

`gcc FILE.c` C 语言编译器  
`g++ FILE.cpp` C++编译器

### GCC 编译过程

1. 预处理  
   `g++ -E hello.cpp -o hello.i` 对输入文件进行预处理

2. 编译  
   `g++ -S hello.i -o hello.s` 将源代码编译为汇编语言代码

3. 汇编  
   `g++ -c hello.s -o hello.o` 将代码编译为机器语言的目标代码

4. 链接  
   `g++ hello.o -o hello` 生成可执行文件

### GCC 常用选项

-   `-g` 生成带调试信息的可执行文件
-   `-O` 优化源代码（大写字母 o）

    -   `-O=-O1` 默认优化
    -   `-O2` 在 1 的基础上进行一些额外的调整，如指令调整等
    -   `-O3` 在 2 的基础上进行一些额外的调整，如循环展开等

-   `-l/-L` 指定库文件/指定库路径
-   `-I` 指定头文件搜索路径
-   `-Wall` 打印警告信息
-   `-w` 关闭警告信息
-   `-std=c++11` 指定编译标准
-   `-o` 指定输出文件名
-   `-D` 定义宏

## GDB

## 开启 GDB 调试器

`g++ -g test.cpp -o test` 生成可调试的可执行文件  
`gdb test` 调试该可执行文件

## GDB 调试命令

`run=r` 执行文件  
`list=l x` 查看 x 行代码  
`break=b x` 在第 x 行设置断点  
`info=i b` 查看断点  
`print=p x` 查看变量 x 的值  
`continue=c` 继续调试  
`display x` 持续展示 x 的值  
`Enter` 重复上一个命令  
`quit=q` 退出调试器

## CMake

### CMake 编译流程

1. 编写 CMakeLists.txt 文件
2. 执行命令 cmake PATH 生成 Makefile
3. 执行 make 进行编译

### CMake 常用指令

`project(hello)`定义工程名  
`set(var hello.cpp)`显式定义变量  
`SET(CMAKE_BUILD_TYPE "Debug")`生成可调试文件  
`include_directories(dir1 dir2)`定义头文件路径  
`add_library(hello SHARE ${SRC})`生成库文件  
`add_compile_options(-Wall std=c++11 -O2)`添加编译参数  
`add_executable(main main.cpp)`生成可执行文件  
`target_link_libraries`指定链接给定目标或其依赖项时要使用的库  
`add_subdirectory(src)`添加源文件子目录  
`aux_source_directory(. SRC)`将源文件目录存储在一个变量中

### CMake 常用变量

`CMAKE_C_FLAGS`gcc 编译选项  
`CMAKE_CXX_FLAGS`g++编译选项  
`CMAKE_BUILD_TYPE`编译类型

## gtest

### 概念

#### Test Suite

TestSuiteName 用来汇总 test case，相关的 test case 应该是相同的 TestSuiteName。一个文件里只能有一个 TestSuiteName，建议命名为这个文件测试的类名。

TestCaseName 是测试用例的名称。建议有意义，比如“被测试的函数名称”，或者被测试的函数名的不同输入的情况。TestSuiteName_TestCaseName 的组合应该是唯一的。

_注意：GTest 生成的类名是带下划线的，所以上面这些名字里不建议有下划线。_

```cpp
// 命名测试集和测试案例
TEST(TestSuiteName, TestCaseName) {
    // 单测代码
    EXPECT_EQ(func(0), 0);
}
```

#### Test Case

一个 TEST(Foo, Bar){...} 就是一个 Test Case。考虑到构造输入有成本，通常一个 TEST(Foo, Bar) 里会反复修改输入，构造多个 case，测试不同的执行流程。这里建议用大括号分隔不同的 case，整体更条理。另一个好处在于：每个变量的生命周期仅限于大括号内。这样就可以反复使用相同的变量名，而不用给变量名编号。

```cpp
TEST(Foo, bar) {
    // case 1: enable = true
    {
        Context ctx;
        params.enable_refresh = true;
        ASSERT_EQ(ctx->is_enable_fresh(), true);
    }

    // case 2: enable = false
    {
        Context ctx;
        params.enable_refresh = false;
        ASSERT_EQ(ctx->is_enable_fresh(), false);
    }
}
```

如果待测函数十分复杂，建议拆分多个 TEST(Foo, Bar){...}，避免 Test Case 代码膨胀。

```cpp
// 待测函数
int foo(TarAddr ta) {
    if (!ta)
        return -1;
    switch(ta.did) {
        case 0x193b:
            ...
        case 0x1204:
            ...
    }
}

TEST(Foo, IsNil) {
    ...
}

TEST(Foo, 0x193b) {
    ...
}

TEST(Foo, 0x1204) {
    ...
}
```

### 断言

GTest 使用一系列断言的宏来检查返回值是否符合预期、是否抛出预期的异常等。主要分为两类：ASSERT 和 EXPECT。区别在于 ASSERT 不通过的时候会认为是一个 fatal 的错误，退出当前函数（只是函数）。而 EXPECT 失败的话会继续运行当前函数，所以对于函数内几个失败可以同时报告出来。

通常用 EXPECT 级别的断言就好，除非你认为当前检查点失败后函数的后续检查没有意义:

-   如果某个判断不通过时，会影响后续步骤，要使用 ASSERT。常见的是空指针，或者数组访问越界。
-   其他情况，可以使用 EXPECT，尽可能多测试几个用例。

#### 基础的断言

| Fatal assertion              | Nonfatal assertion           | Verifies           |
| ---------------------------- | ---------------------------- | ------------------ |
| `ASSERT_TRUE(_condition_);`  | `EXPECT_TRUE(_condition_);`  | condition is true  |
| `ASSERT_FALSE(_condition_);` | `EXPECT_FALSE(_condition_);` | condition is false |

#### 数值比较

| Fatal assertion              | Nonfatal assertion           | Verifies     |
| ---------------------------- | ---------------------------- | ------------ |
| `ASSERT_EQ(_val1_, _val2_);` | `EXPECT_EQ(_val1_, _val2_);` | val1 == val2 |
| `ASSERT_NE(_val1_, _val2_);` | `EXPECT_NE(_val1_, _val2_);` | val1 != val2 |
| `ASSERT_LT(_val1_, _val2_);` | `EXPECT_LT(_val1_, _val2_);` | val1 < val2  |
| `ASSERT_LE(_val1_, _val2_);` | `EXPECT_LE(_val1_, _val2_);` | val1 <= val2 |
| `ASSERT_GT(_val1_, _val2_);` | `EXPECT_GT(_val1_, _val2_);` | val1 > val2  |
| `ASSERT_GE(_val1_, _val2_);` | `EXPECT_GE(_val1_, _val2_);` | val1 >= val2 |

#### 浮点数比较

| Fatal assertion                             | Nonfatal assertion                          | Verifies                                     |
| ------------------------------------------- | ------------------------------------------- | -------------------------------------------- |
| `ASSERT_FLOAT_EQ(_val1_, _val2_);`          | `EXPECT_FLOAT_EQ(_val1_, _val2_);`          | val1 == val2 (within 4 ULPs)                 |
| `ASSERT_DOUBLE_EQ(_val1_, _val2_);`         | `EXPECT_DOUBLE_EQ(_val1_, _val2_);`         | val1 == val2 (within 4 ULPs)                 |
| `ASSERT_NEAR(_val1_, _val2_, _abs_error_);` | `EXPECT_NEAR(_val1_, _val2_, _abs_error_);` | 判断两个数字的绝对值相差是否小于等于 abs_val |

#### 字符串比较

| Fatal assertion                     | Nonfatal assertion                  | Verifies                                                |
| ----------------------------------- | ----------------------------------- | ------------------------------------------------------- |
| `ASSERT_STREQ(_str1_, _str2_);`     | `EXPECT_STREQ(_str1_, _str_2);`     | the two C strings have the same content                 |
| `ASSERT_STRNE(_str1_, _str2_);`     | `EXPECT_STRNE(_str1_, _str2_);`     | the two C strings have different content                |
| `ASSERT_STRCASEEQ(_str1_, _str2_);` | `EXPECT_STRCASEEQ(_str1_, _str2_);` | the two C strings have the same content, ignoring case  |
| `ASSERT_STRCASENE(_str1_, _str2_);` | `EXPECT_STRCASENE(_str1_, _str2_);` | the two C strings have different content, ignoring case |

### 示例

待测试的目标类：

```cpp
// MyClass.h
class MyClass {
public:
    int Add(int a, int b) {
        return a + b;
    }

    int Subtract(int a, int b) {
        return a - b;
    }
};
```

为 MyClass 创建一个测试文件，例如 MyClassTest.cpp:

```cpp
// MyClassTest.cpp
#include "MyClass.h"
#include <gtest/gtest.h>

TEST(MyClassTest, AddTest) {
    MyClass my_class;
    EXPECT_EQ(my_class.Add(1, 2), 3);
    EXPECT_EQ(my_class.Add(1, 1), 3);
}

TEST(MyClassTest, SubtractTest) {
    MyClass my_class;
    EXPECT_EQ(my_class.Subtract(1, 2), -1);
    EXPECT_EQ(my_class.Subtract(1, 1), 3);
}

int main(int argc, char **argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```

编译运行：`g++ -std=c++11 -pthread MyClassTest.cpp -lgtest -o MyClassTest`

## Glog

Google 的 Glog 是一个应用程序的日志库。它提供基于 C++风格的流的日志 API，以及各种辅助的宏。例如：

```cpp
#include <glog/logging.h>

int main(int argc, char* argv[]) {
  // Initialize Google's logging library.
  google::InitGoogleLogging(argv[0]);

  // ...
  LOG(INFO) << "Found " << num_cookies << " cookies";
}
```

### 日志级别

glog 可以指定级别（按严重性递增）：

-   `INFO`
-   `WARNING`
-   `ERROR`
-   `FATAL`

可以通过设置`FLAG_log_dir`指定 log 输出文件的存储目录。默认情况下，`ERROR`和`FATAL`级别的日志会同时输出到 stderr。打印`FATAL`消息会在打印完成后终止程序。

### flag

一些 flag 会影响 Glog 的输出行为，可以通过修改`FLAGS_*`全局变量来改变 flag 的值。

常用的 flag 有：

-   `logtostderr`(bool, default=false)  
    日志输出到 stderr，不输出到日志文件。
-   `colorlogtostderr`(bool, default=false)  
    输出彩色日志到 stderr。
-   `stderrthreshold`(int, default=2)  
    将大于等于该级别的日志同时输出到 stderr。日志级别 INFO, WARNING, ERROR, FATAL 的值分别为 0、1、2、3。
-   `minloglevel`(int, default=0)  
    打印大于等于该级别的日志。日志级别的值同上。
-   `log_dir`(string, default="")  
    指定输出日志文件的目录。

```cpp
LOG(INFO) << "file";
// Most flags work immediately after updating values.
FLAGS_logtostderr = 1;
LOG(INFO) << "stderr";
FLAGS_logtostderr = 0;
// This won't change the log destination. If you want to set this
// value, you should do this before google::InitGoogleLogging .
FLAGS_log_dir = "/some/log/directory";
LOG(INFO) << "the same file";
```

## 正则表达式

正则表达式(Regular Expression)是一种文本模式，包括普通字符（例如，a 到 z 之间的字母）和特殊字符（称为"元字符"），可以用来描述和匹配字符串的特定模式。

### 语法

**普通字符**包括没有显式指定为元字符的所有可打印和不可打印字符。这包括所有大写和小写字母、所有数字、所有标点符号和一些其他符号。

| 字符       | 描述                                                               |
| ---------- | ------------------------------------------------------------------ |
| **[ABC]**  | 匹配 **[...]** 中的所有字符。                                      |
| **[^ABC]** | 匹配除了 **[...]** 中字符的所有字符。                              |
| **[A-Z]**  | [A-Z] 表示一个区间，匹配所有大写字母，[a-z] 表示所有小写字母。     |
| **.**      | 匹配除换行符（\n、\r）之外的任何单个字符，相等于 `[^\n\r]`。       |
| **[\s\S]** | 匹配所有。\s 是匹配所有空白符，包括换行，\S 非空白符，不包括换行。 |
| **\w**     | 匹配字母、数字、下划线。                                           |
| **\d**     | 匹配任意一个阿拉伯数字（0 到 9）。等价于 **[0-9]**。               |

**非打印字符**也可以是正则表达式的组成部分。下表列出了表示非打印字符的转义序列：

| 字符 | 描述                                                                   |
| ---- | ---------------------------------------------------------------------- |
| \cx  | 匹配由 x 指明的控制字符。例如， \cM 匹配一个 Control-M 或回车符。      |
| \f   | 匹配一个换页符。等价于 \x0c 和 \cL。                                   |
| \n   | 匹配一个换行符。等价于 \x0a 和 \cJ。                                   |
| \r   | 匹配一个回车符。等价于 \x0d 和 \cM。                                   |
| \s   | 匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]。 |
| \S   | 匹配任何非空白字符。等价于 `[^ \f\n\r\t\v]`。                          |
| \t   | 匹配一个制表符。等价于 \x09 和 \cI。                                   |
| \v   | 匹配一个垂直制表符。等价于 \x0b 和 \cK。                               |

**特殊字符**，就是一些有特殊含义的字符。若要匹配这些特殊字符，必须首先使字符"转义"，即，将反斜杠字符 '\\' 放在它们前面。

| 特殊字符 | 描述                                                                                                                       |
| -------- | -------------------------------------------------------------------------------------------------------------------------- |
| $        | 匹配输入字符串的结尾位置。                                                                                                 |
| ( )      | 标记一个子表达式的开始和结束位置。                                                                                         |
| .        | 匹配除换行符 \n 之外的任何单字符。                                                                                         |
| [        | 标记一个中括号表达式的开始。                                                                                               |
| \        | 将下一个字符标记为或特殊字符、或原义字符、或向后引用、或八进制转义符。                                                     |
| ^        | 匹配输入字符串的开始位置，除非在方括号表达式中使用，当该符号在方括号表达式中使用时，表示不接受该方括号表达式中的字符集合。 |
| {        | 标记限定符表达式的开始。                                                                                                   |
| \|       | 指明两项之间的一个选择。                                                                                                   |

**限定符**用来指定正则表达式的一个给定组件必须要出现多少次才能满足匹配。

| 字符  | 描述                                                             |
| ----- | ---------------------------------------------------------------- |
| \*    | 匹配前面的子表达式零次或多次。                                   |
| +     | 匹配前面的子表达式一次或多次。                                   |
| ？    | 匹配前面的子表达式零次或一次。                                   |
| {n}   | n 是一个非负整数。匹配确定的 n 次。                              |
| {n,}  | n 是一个非负整数。至少匹配 n 次。                                |
| {n,m} | m 和 n 均为非负整数，其中 n <= m。最少匹配 n 次且最多匹配 m 次。 |

**定位符**能够将正则表达式固定到行首或行尾。

| 字符 | 描述                                   |
| ---- | -------------------------------------- |
| ^    | 匹配输入字符串开始的位置。             |
| $    | 匹配输入字符串结尾的位置。             |
| \b   | 匹配一个单词边界，即字与空格间的位置。 |
| \B   | 非单词边界匹配。                       |

用圆括号 **()** 将所有选择项括起来，相邻的选择项之间用 **|** 分隔。

标记也称为**修饰符**，正则表达式的标记用于指定额外的匹配策略。

| 修饰符 | 含义                               | 描述                                           |
| ------ | ---------------------------------- | ---------------------------------------------- |
| i      | ignore - 不区分大小写              | 将匹配设置为不区分大小写。                     |
| g      | global - 全局匹配                  | 查找所有的匹配项。                             |
| m      | multi line - 多行匹配              | 使边界字符 ^ 和 $ 匹配每一行的开头和结尾。     |
| s      | 特殊字符圆点 **.** 中包含换行符 \n | 加上 s 修饰符之后, **.** 中匹配包含换行符 \n。 |

### 匹配规则

**基本模式匹配**只包含特殊字符和字面值，可以描述简单的字符串特征。模式可以很简单，由普通的字符串组成，也可以非常复杂，往往用特殊的字符表示一个范围内的字符、重复出现，或表示上下文。

例如：

`^once` 表示该模式只匹配那些以 once 开头的字符串。

`bucket$` 用来匹配那些以 bucket 结尾的字符串。

字符 `^` 和 `$` 同时使用时，表示精确匹配（字符串与模式一样），`^bucket$` 只匹配字符串 "bucket"。

如果一个模式不包括 `^` 和 `$`，那么它与任何包含该模式的字符串匹配。

**字符簇**通常被用于验证字符串是否满足某些条件。

要建立一个表示所有元音字符的字符簇，就把所有的元音字符放在一个方括号里：`[AaEeIiOoUu]`

用连字号可以表示一个字符的范围，如：

```sh
[a-z] // 匹配所有的小写字母
[A-Z] // 匹配所有的大写字母
[a-zA-Z] // 匹配所有的字母
[0-9] // 匹配所有的数字
[0-9\.\-] // 匹配所有的数字，句号和减号
[ \f\r\t\n] // 匹配所有的白字符
```

如果要匹配一个由一个小写字母和一位数字组成的字符串，比如 "z2"、"t6" 或 "g7"，但不是 "ab2"、"r2d3" 或 "b52" 的话，用这个模式：`^[a-z][0-9]$`。当在一组方括号里使用 **^** 时，它表示"非"或"排除"的意思，常常用来剔除某个字符。例如：`^[^0-9][0-9]$`。

更多的情况下，可能要匹配一个单词或一组数字。跟在字符或字符簇后面的花括号`{}`用来确定前面的内容的重复出现的次数。

```sh
a{2}      // 包含连续的刚好两个a
^a{2,}    // 以连续的至少两个a开头
^a{1,3}$  // a, aa, aaa
```

特殊字符 `*` 与 {0,} 是相等的，它们都代表着*0 个或多个前面的内容* 。最后，字符 `+` 与 {1,} 是相等的，表示 _1 个或多个前面的内容_。

圆括号 `()` 是组，主要应用在限制多选结构的范围/分组/捕获文本/环视/特殊模式处理。

```sh
(abc|bcd|cde)  // 表示这一段是abc、bcd、cde三者之一均可，顺序也必须一致。
(abc)?         // 表示这一组要么一起出现，要么不出现，出现则按此组内的顺序出现。
```
